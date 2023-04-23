---
title: \[Data\] ElasticSearch의 ILM
excerpt: "ElasticSearch, Index Life Management, 인덱스 생명 주기 설정"
categories:
   - data
---

<br />
<br />


검색팀 뿐만 아니라 데이터 파이프라인에서도 많이 사용되는 ElasticSearch(es) 기본 개념 정리합니다.

<br />

상황은 이렇습니다.

카프카에서 es로 싱크하는 컨슈머 어플리케이션 + kibana 를 구성하던 중에 

디스크 용량에 집착을 할 계기가 있었습니다.

es에 새롭게 쌓기 시작한 데이터의 양이

(편차는 있지만) 가장 많은 날짜 기준, 하루 100GB 정도가 쌓이고 있었습니다.😮

<img src="https://user-images.githubusercontent.com/42775225/233843858-bc9c5f53-d4a9-43d9-9269-ccf32530c7da.png">

<br />

보통 es의 장애라고 하면 CPU spike, Network bandwidth explode 쪽이 

주된 관심사로 보이지만, 지금 제게는 용량 쪽 관리를 먼저 해야했습니다.

그래서 es의 ILM(Index Life Management) 설정 방법을 정리합니다.😊

<br />
<br />

# 1. ElasticSearch

ElasticSearch는 정형 및 비정형 데이터에 대한 검색엔진 입니다.

검색엔진이라고 하면, 겉으로 드러나는 부분은 

검색창에 검색어를 입력해서 나오는 결과를 받을 수 있는 것이지만, 

이를 위해서 검색 대상을 수집, 정제, 인덱싱하는 과정이 있어야 합니다.

<br />

쉽게 생각하면,

es는 DB이고, DB이기 때문에 당연히 데이터를 넣는 행위가 있으며, 이를 ‘**인덱싱’** 이라고 합니다.

(컬럼이나 id값으로 쿼리를 빠르게 해주는 도구인, RDBMS의 인덱스와 다릅니다.)

실제 코드에서도 index 라는 메서드를 사용하는데, es에 데이터를 저장한다는 말입니다.

아 참고로, HLRC 는 7.15 이후 deprecated 예정이기 때문에 사용을 추천 드리지 않습니다.

(Low Level Rest Client API 혹은 RESTful API 를 추천)

<br />

ElasticSearch 기본 개념을 다음과 같이 정리할 수 있습니다.

- 클러스터(Cluster) : 노드의 집합
- 노드(Node) : DB서버
- 샤드(Shard) : 파티션
- 인덱스(Index) : 테이블
- 문서(Document) : 행
- 필드(Field) : 열
- 맵핑(Mapping) : 스키마


<br />
<br />
<br />

# 2. 기본 환경 설정

es의 환경 설정은 elasticsearch.yaml 로 할 수 있습니다.

'$' 와 함께 환경 변수명을 가져와서 사용이 가능합니다.

- `cluster.name` : 클러스터 명
- `cluster.initial_master_nodes` : 마스터 후보 노드를 지정
- `network.host` : 지정된 IP만 es에 접근하기 위해 IP 주소 입력 → 이거 설정하면 운영모드
    - `_local_` : 127.0.0.1과 같음. 로컬 주소
    - `_site_` : 로컬 네트워크 주소로 설정됨. 서버가 재시작되어도 yml 변경하지 않아도 됨
    - `_global_` : 네트워크 외부에서 바라보는 주소
- `http.port` : 클라이언트와 통신하는 포트
- `transport.port` : 내부 노드들끼리 통신하는 포트
- `discovery.seed_hosts` : 활성화 된 다른 서버를 찾는 설정

cf) 운영 모드로 하면 bootstrap check를 합니다. (같은 설정인데 에러를 만나게 될 수 있음)

<br />
<br />
<br />

# 3. ILM(Index Lifecycle Management)

ILM은 임계 크기, 문서 수, 보관 기간을 **조건**으로 

새 인덱스 **생성**하거나 오래된 인덱스 **삭제**하는 설정입니다.

<br />

다만, 조건에 맞춰서 트리거되는 구조가 아니라 정해진 polling 시간 단위에 따라 적용됩니다.

kibana로 쉽게 설정 관리가 가능하다는 장점이 있습니다.

<br />

시작 전에, 사용되는 개념은 다음과 같습니다.

### 1) tier

인덱스 사용 빈도에 따라 다르게 보관을 합니다.

- hot : 업데이트 질의 빈번. SSD
- warm : 업데이트 없지만 질의 빈번. HDD
- cold : 업데이트 없고 간헐적 질의.
- frozen : 업데이트 없고 드물게 질의. Cheapest
- delete : 더이상 필요하지 않음.

<br />

### 2) action

- rollover : 특정 조건에 도달하면 새로운 인덱스 생성 (log-001, log-002, …)
- shrink : primary shard의 수를 줄임
- force merge : index shard의 segments 수 줄임
- freeze : index 읽기 전용으로 전환
- delete : 오래된 index 영구 삭제


<br />
<br />
<br />

# 4. ILM 설정 방법

이번에 저는 es, kibana를 7.x 버전으로 사용했고, 해당 UI를 기준으로 방법을 정리합니다.

또한 아래에서 API들을 사용할텐데,

kibana → Management → Dev Tools 의 콘솔에서 사용 가능합니다.

<br />

**요약)**

1. Policy 생성
2. Index Template 생성
3. 기존 Index 설정 수동 Policy 적용
4. 새로 생기는 Index들 자동 Policy 적용

<br />

### 1) Policy 생성

저는 rollover(조건에 맞으면 인덱스 새로 생성)를 할 필요없이 

delete phase(삭제) 설정만 하면 되는 상황이라 다음과 같이 설정을 해주었습니다.

rollover가 필요하신 분들을 위해 설명이 잘 되어 있는 글을 공유드립니다.

⇒ <a href="https://medium.com/nerd-for-tech/data-rollover-in-elasticsearch-b809bb9f150a" target="_blank">Data rollover in Elasticsearch</a>


```
PUT _ilm/policy/<policyName>
{
  "policy": {
    "phases": {
      "hot": {
        "actions": {
          "set_priority": {
            "priority": 100
          }
        },
        "min_age": "0ms"
      },
      "delete": {
        "min_age": "7d",
        "actions": {
          "delete": {}
        }
      }
    }
  }
}
```

<br />

kibana의 UI로 보면 다음과 같습니다.

<img src="https://user-images.githubusercontent.com/42775225/233845252-44b3ad8e-fff1-4c27-98c8-a85fad879c93.png">

<img src="https://user-images.githubusercontent.com/42775225/233845118-13c63ace-8b75-47e0-b844-628876dc6e78.png">

<br />

그러면 생성한 Policy를 목록에서 확인할 수 있으며, 

Linked indices에 연결된 인덱스는 없는 것을 볼 수 있습니다.

<img src="https://user-images.githubusercontent.com/42775225/233845553-c1ffc152-6468-4066-b421-f817085e441c.png">

<br />

### 2) Index Template 생성

**인덱스 이름 패턴** 을 기반으로 새로 생성되는 인덱스에 

자동으로 settings와 mapping을 적용하기 위해 템플릿을 생성해줍니다.

<br />

인덱스 템플릿은 Index patterns 와 Settings 만 설정을 했습니다.


```
PUT _index_template/pageid_detector_gmkt_template
{
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 1,
      "lifecycle": {
        "name": "<policyName>"
      }
    }
  },
  "index_patterns": [
    "index명*"
  ]
}
```

<img src="https://user-images.githubusercontent.com/42775225/233846087-65af171d-f214-4205-a04c-79f289ec71a6.png">

<img src="https://user-images.githubusercontent.com/42775225/233847998-d4e547db-9d64-42b7-a2a6-c281d11e2426.png">


<br />

### 3) 기존 Index Policy 수동 설정

인덱스 템플릿은 기존 인덱스에는 영향을 주지 않기 때문에 수동으로 설정을 해주어야 합니다.

```
The Index Templates view lists your templates and lets you examine, 
edit, clone, and delete them. Changes made to an index template do not 
affect existing indices.
```

source. <a href="https://www.elastic.co/guide/en/elasticsearch/reference/7.12/index-mgmt.html#manage-index-templates" target="_blank">공식 문서</a>

<br />

인덱스 선택 → Manage index → Add lifecycle policy → <policyName>

으로 수동 설정해줍니다.

<br />

Linked indices 에 수동으로 연결된 인덱스의 값이 올라감을 확인할 수 있습니다.

<img src="https://user-images.githubusercontent.com/42775225/233846362-c3785cd6-7860-40eb-a241-0251b982b09f.png">

<br />

그리고 인덱스가 삭제로 변함을 볼 수 있습니다.

<img src="https://user-images.githubusercontent.com/42775225/233847028-a4133550-3144-4ce4-911e-ebd18af237b0.png">


<br />

만약 삭제가 안된다면, 인덱스의 ILM 상태를 보고 트러블 슈팅을 해보면 됩니다.

```
GET 인덱스명/_ilm/explain
```

<br />

혹시 트러블 슈팅하시는데 필요할까봐 공식문서도 킵해두었습니다.

⇒ <a href="https://www.elastic.co/guide/en/elasticsearch/reference/7.17/index-lifecycle-error-handling.html" target="_blank">Troubleshooting index lifecycle management errors</a>


<br />

그런데 rollover는 alias가 있어야 하고, alias는 아래와 같이 추가해줄 수 있습니다!

⇒ <a href="https://www.elastic.co/guide/en/elasticsearch/reference/7.12/indices-aliases.html#indices-aliases" target="_blank">Aliases API</a>


```
POST /_aliases
{
  "actions": [
    {
      "add": {
        "index": "인덱스명",
        "alias": "alias명",
        "is_write_index": true
      }
    }
  ]
}
```

<br />

### 4) 새로 생기는 Index Policy 설정

템플릿에서 설정한 인덱스 패턴에 잡히기 때문에 새로 생기는 인덱스는 걱정 끝~~

<br />
<br />
<br />


검색 팀이 아니라서 딱 필요한 기능만(es 및 kibana) 찾아서 쓰는 정도이지만,

오늘도 고생쓰~~🚀🚀



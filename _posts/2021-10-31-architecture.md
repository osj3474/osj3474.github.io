---
title: \[Web\] 서비스의 큰 그림, 아키텍처
excerpt: "👨‍💻 백엔드 개발자가 이해한 인프라 이야기"
categories:
   - web
---

<br><br>


# 시작하며

----------------------------------------------

훌륭한 백엔드 개발자👨‍💻 되기 위해 공부하면서 **인프라**와 **아키텍처**에 대한 역량이 정말 "중요하다" 는 것을 느끼고 있습니다. 

다양한 이유가 있겠지만, 가장 먼저는 클라우드 서비스가 발전하면서 개발자가 다양한 인프라 자원을 손 쉽게 사용할 수 있게 되었습니다.

또한 규모 있는 시스템에서 발생할 수 있는 문제들이 많은 경우 인프라로 해결되는 모습을 많이 봤습니다. (다중화라던지, 트래픽 대응이라던지)

'대규모 시스템 설계 기초' 라는 책을 통해 공부한 내용과 더불어 지금까지 알고 있는 지식을 정리 해봤습니다.

<br>

열심히 공부하고 있는 내용이라 틀린 부분이 있다면 댓글로 가감 없이 지적해주시면 감사하겠습니다! ✋

그럼 시작합니다.🚀 🚀 🚀 🚀



<br><br>


# 아키텍처 진화 과정

----------------------------------------------

아래 시나리오는 제가 임의로 <u>가정한 상황</u>으로, 아키텍처가 항상 이렇게 변화하는 것은 아닙니다.



### 1) 노트북에서 서비스 개발

서비스를 기획해서 노트북에 웹서버를 구축하였습니다. 

![image](https://user-images.githubusercontent.com/42775225/139639793-e4154a01-507a-4f96-8002-757f66775b41.png)

하지만 혼자만 사용하는 서비스가 아니라면 노트북에서는 한계가 있습니다. 

(물론, 방화벽을 직접 열고 Wifi를 사용하는 노트북으로 포트포워딩을 하는 작업을 거쳐서 서버로 만들 수도 있지만, 저는 지금이 <u>클라우드 서버가 등장할 타이밍</u>이라고 생각합니다.)

<br /><br />

### 2) 외부 사용자 서비스

구축한 웹서버를 통해 서비스 첫 런칭을 시작했습니다.

![image](https://user-images.githubusercontent.com/42775225/139639806-31002a32-38a7-4882-9cac-addcabf9e58b.png)

그런데 반응이 좋아서 사용자가 많아지게 되었습니다.

![image](https://user-images.githubusercontent.com/42775225/139639819-378d1483-b3a4-4b21-aca3-f26ae73a5794.png)

단일 웹서버이다 보니 부하로 인해, 서비스의 속도가 너무 느려졌습니다. 

그래서 서버의 스펙보다는 서버의 갯수를 늘리는 방법을 선택했습니다.

<br /><br />





### 3) 서버증설 + DB분리

DB서버를 분리해서 하나의 통합 DB를 두고 WAS를 두개를 두었습니다. (+로드 밸런싱)

![image](https://user-images.githubusercontent.com/42775225/139639836-0a5da064-dd42-4b0c-a9a7-8a85b84fd02c.png)

그런데 사용자를 통해 이런 문의가 들어옵니다. 

"자꾸 다시 로그인 하라고 해요..."

쿠키/세션 방식으로 로그인 처리했는데 WAS가 두개니까 다른 WAS로 붙을 때마다 세션에 기록이 없는 문제가 발생했습니다.

그래서 세션 스토리지를 하나 두기로 합니다.

<br /><br />

### 4) 세션 스토리지 추가

세션 스토리지로 Redis를 선택해서 스토리지를 추가하니 더이상 그런 문의는 발생하지 않았습니다.

(스프링을 사용한다면 의존성 추가로 쉽게 Redis를 사용할 수 있답니다~!)

![image](https://user-images.githubusercontent.com/42775225/139639848-0022dc2f-d1da-45b7-b9ee-43e111766e1b.png)

그런데 이제는 DB서버에서 CPU사용량 이슈가 반복적으로 발생했습니다.

![image](https://user-images.githubusercontent.com/42775225/139639862-4d9365d7-4e6b-4e55-a0cf-bcf4302c4df0.png)

정적 컨텐츠는 따로 빼서 DB접근을 줄이고, DB도 "작업" 을 기준으로 분리하기로 합니다.

<br /><br />



### 5) CDN, CQRS패턴

정적 컨텐츠 캐시를 처리할 CDN를 추가하고, DB는 CQRS 패턴으로 read/write 용을 나눕니다. 

read용 DB는 검색 엔진(Elastic Search 등)을 사용했습니다.

또한 분리된 DB는 메세지 큐(kafka)를 통해 동기화를 해줍니다.

![image](https://user-images.githubusercontent.com/42775225/139639885-71383867-38b2-4862-b0b4-2da9a480b64f.png)

그리고 Redis도 클러스터링을 통해 장애를 대응합니다.

![image](https://user-images.githubusercontent.com/42775225/139639916-cb46227f-7de5-4646-832b-25efaa2da6d9.png)

참고로 데이터 베이스 다중화 방법으로

- clustering

  : DB서버가 죽었을 때 대처하는 방법으로, Active-Active 혹은 Active-Standby 구조

- replication

  : 데이터 저장소를 복제하는 것으로, Master-Slave구조로 Slave를 데이터 백업 용도 혹은 조회 용도로 사용

- sharding

  : 데이터가 많아서 검색일 느린 상황을 극복하는 방법으로 테이블을 나눔

가 있습니다.



<br />

그런데... 사용자가 갑자기 많아지면서 로드밸런서가 죽었고, 서비스 장애가 발생했습니다..

![image](https://user-images.githubusercontent.com/42775225/139639941-942043fe-3a3e-4e11-b2bf-93863c4d1b6c.png)

급하게 재기동은 했지만, 단일 로드 밸런서로 SPOF를 방치한 것이 화근이 되었습니다.

<br /><br />





### 6) LB 클러스터링

부 로드밸런서를 두어 혹시나 Active가 죽더라도 Passive가 바로 대응할 수 있게 합니다.

![image](https://user-images.githubusercontent.com/42775225/139639961-f82a7e9c-f8ed-4203-9a55-1fbfc7c5abdf.png)



<br />

서비스는 날이 갈수록 커졌고, 결국 사용자들은 모바일 런칭을 기다리게 되었습니다.

![image](https://user-images.githubusercontent.com/42775225/139639980-9c7f4f19-5f6e-4c72-9db6-974fc4861fa2.png)

웹과 모바일을 동시에 서비스 하려면 어떻게 구조를 잡아야 할지 생각해봤습니다.

<br /><br />

모바일 클라이언트 서버는 swift와 kotlin(물론 서버로도 쓰지만)으로 구축하되,

API서버는 그대로 사용할 것이기 때문에 백엔드 구조를 다시 잡아야 했습니다.

![image](https://user-images.githubusercontent.com/42775225/139639998-b9d8843a-13a9-4d55-b3f3-3ff74768db37.png)

<br /><br />





### 7) 모바일 지원, API게이트웨이

그래서 API게이트웨이를 2개를 두고 서비스하기 시작했습니다.

모바일과 웹의 API게이트웨이를 분리한 이유는 보여줄 데이터가 다르고, 

API게이트웨이에 모니터링을 달아서 사용할 때, 모바일과 웹의 이슈 조건에도 차이가 있기 때문입니다.

(e.g. 반응 지연 이라는 이슈로 alert를 받을 때, 모바일에서의 지연 시간과 웹에서의 지연 시간이 같을 수 없습니다.)

cf) <a href="https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern" target="_blank">[API게이트웨이를 분리하는 이유]</a>



<br />

![image](https://user-images.githubusercontent.com/42775225/139640021-185fcd2c-010c-4196-9159-aaafed75e24d.png)

또한 사용자에게 제공하는 기능이 너무 많이 추가되면서 도메인을 분리해야 했고, MSA로 개편하였습니다.

그리고 그림에 담지는 않았지만, MSA에서 장애 전파를 막기 위해 Kafka 를 이용할 수 있습니다.

cf) <a href="https://www.youtube.com/watch?v=BnS6343GTkY&t=1252s" target="_blank">배달의민족 마이크로서비스 여행기</a>



<br /><br />

### 8) DNS로 로드밸런싱

끝으로 로드밸런서를 로드밸런싱 할 수 있는 DNS를 사용하여 

API게이트웨이를 DNS로 로드밸런싱합니다. 

![image](https://user-images.githubusercontent.com/42775225/139640063-54ccf4e5-9ad0-444b-90d1-712f6d5a4af3.png)

<br /><br /><br />



이렇게 아키텍처 진화 과정이 종료되었습니다.

배운 개념을 어떻게든 연결해보고자 억지스러운 면이 있을지도 모르겠습니다.

<br />

다만, 인프라를 확장해나가는 플로우와 키워드를 얻어가실 수 있는 분이 있다면 정말 좋겠습니다.

또 1년 뒤에 다시 제가 이 글을 본다면 "정말 많이 성장했다!!!" 라는 말을 하지 않을까 하는 마음으로

부족하지만 포스팅합니다!!



<br /><br />



cf) 알아두면 좋을 키워드

- ssl 오프로딩 : API게이트웨이에 ssl을 위임해서 로드 밸런서는 바이패스와 로드밸런싱만 하는 것
- GSLB : 글로벌 리전에 있는 서버를 클라이언트가 찾아가게 하는 방식



<br /><br /><br />

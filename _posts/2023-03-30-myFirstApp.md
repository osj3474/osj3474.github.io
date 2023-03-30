---
title: \[Project\] 모바일 런칭 (App/Play Store)
excerpt: "한 번 더 큰 유입을 위한 앱 출시. 개발자 인생의 첫 앱"
categories:
   - project
---

<br />
<br />

# 1. 행복한 소식, 새로운 시작

사이드 프로젝트가 토이 프로젝트로 끝나지 않고, 웹에 이어 앱으로까지 출시하게 되었습니다.🎉

제게 엄청 뜻 깊은 순간이기도 하면서, 새로운 시작점이기도 합니다.

<br />

저는 누군가 사용할 수 있는 수준(웹이나 앱을 출시해서 누군가에게 소개할 정도)의 서비스를 만든다면, 

그 때는 분명 엄청난 자신감이 생길 것이라 생각했었습니다. 😎

그런데, 출시를 하고 한 동안 왠지 모를 공허한? 감정이 느껴졌습니다.

하나의 Product를 만들 수 있게 되었으니 빵!하고 점프가 있을 줄 알았던 것 같습니다.

<br />

아직도 회사 선배님들의 퍼포먼스를 동경의 눈으로 보고 있는 주니어일 뿐이지만,

지금이 분명 제게 새로운 시작점이 될 것입니다.

<br />
<br />
<br />

# 2. 서비스 2막이 올랐다.

사용자 유입에 있어서 웹의 한계는 명확했습니다.

접근성이 무엇보다 중요함을 인정했고, (저의 짝사랑) flutter로 모바일 서비스를 런칭했습니다.

### 앱 스토어

=> <a href="https://apps.apple.com/kr/app/whynothere/id1665014097" target="_blank">WhyNotHere 사람을 모으기 쉬워지는 곳 _ why not here</a>

<img width="856" src="https://user-images.githubusercontent.com/42775225/228871220-501f4178-e1d4-4eed-b9cf-d8ef4e379b17.png">

<br />

### 플레이 스토어

=> <a href="https://play.google.com/store/apps/details?id=com.sangjin.whynot" target="_blank">WhyNotHere - 한동대 대표 커뮤니티</a>

<img width="856" src="https://user-images.githubusercontent.com/42775225/228608241-93f79905-edff-4000-b2ef-5285bd6dd8a5.png">

<br />

앞으로 대대적인 홍보 방법으로는,

- 학교 단체 카톡방
- 포스터
- 인스타
- 스토리 영상
- 자체 이벤트 등 

다양한 홍보 방법을 고려하고 있으며, 서비스 고도화 작업도 지속적으로 진행하고 있습니다.

<br />

다음 이야기가 어떻게 펼쳐질지 함께 기대해주세요~~~🥹

마지막으로 근황 업데이트하고 글을 마무리하겠습니다.

<br />
<br />
<br />

# 3. 근황

요즘 저희 팀은 Consumer 앱을 시작으로, SCDF(spring cloud data flow)로 많이 전환을 합니다.

저도 이번에 @KafkaListener 로 되어 있던 방식을 Consumer 객체를 Bean으로 등록하는 방식으로 변경했었습니다.

다른 부분은 어렵지 않았는데, 카프카 멀티 바인딩을 설정 못해서 한참 애를 먹었습니다.

혹여 시행 착오를 줄이실 분들이 있을까봐 설정 예시를 공유합니다.

<br />

**kafka multi bindings**

```yaml
spring:
  cloud:
    stream:
      function:
        definition: 컨슈머1;컨슈머2;
      bindings:
        컨슈머1-in-0:
          destination: [토픽명]
          content-type: application/json
          group: [Consumer Group Id]
          binder: 카프카1
        컨슈머2-in-0:
          destination: [토픽명]
          content-type: application/json
          group: [Consumer Group Id]
          binder: 카프카2
      binders:
        카프카1:
          type: kafka
          environment:
            spring.cloud.stream:
              kafka:
                binder:
                  auto-create-topics: false
                  brokers: [브로커 주소들]
                  consumer-properties:
                    auto.offset.reset: latest
        카프카2:
          type: kafka
          environment:
            spring.cloud.stream:
              kafka:
                binder:
                  auto-create-topics: false
                  brokers: [브로커 주소들]
                  consumer-properties:
                    auto.offset.reset: latest
```

<br />

sink앱 하나를 만들어두고 모듈 형식으로 재사용하면서 재미를 봤습니다.

카프카 토픽명만 환경 변수로 바꿔서 훅훅 배포~

그리고 임박한 databricks와 Spark의 만남을 준비하며,

오늘도 코딩중.

<br />
<br />
<br />




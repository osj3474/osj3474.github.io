---
title: \[Web\] 서비스의 큰 그림, 아키텍처
excerpt: "👨‍💻 백엔드 개발자가 이해한 인프라 이야기"
categories:
   - web
---

<br><br>


# 시작하며

----------------------------------------------











<br><br>


# 아키텍처 진화 과정

----------------------------------------------



1. 노트북 위에 스프링 프로젝트

   

   항상 켜둘 수가 없다.

2. EC2위에 스프링

   

   사용자가 많아졌다.

3. DB서버를 분리하고 WAS 두개를 두자. 여기서 로드밸런서를 두자.

   

   오잉. 근데 WAS가 두개니까 쿠키/세션으로 로그인 처리했는데 문제가 발생하네.

4. Redis를 세션 스토리지로 쓰자

   

   그런데 이제는 DB에서 불이 나네

5. WAS 앞 단에는 정적 컨텐츠를 처리할 웹서버를 추가하고, CQRS 패턴으로 read/write 나눈다.

   

   로드밸런서에서 불이 나서 SPOF가 되었다.

6. 부 로드밸런서를 두고, 로드밸런서가 ssl을 해줬는데 이걸 API 게이트 웨이로 위임한다.

   

   모바일로도 런칭할 필요를 느끼고, 모바일을 지원하기 시작했다.

7. API게이트웨이를 2개를 두고 서비스하기 시작했다.

   

   원하는 기능이 너무 많이 추가되면서 MSA환경을 가기로 마음을 먹었다.

   또한, RDB의 스케일 업도 한계에 부딪히면서 

8. WAS서버가 여러개의 도메인을 가지도록 했다. 그리고 DB는 topic server 형태를 띄도록 했다.

   

   그런데 병목지점이 발생하고, 장애전파가 너무 심하다.

9. Kafka 띄워서 메세지큐 방식으로 전환하자.

   

   사용자가 많아졌다.

10. 로드밸런서를 DNS에서 로드밸런싱 한다.

    

    사용자가 더 많아졌고, 글로벌로도 진출했다.

11. 정적 컨텐츠는 스토리지+CDN을 사용한다. 글로벌 리전에 서버를 둔다. 이 때 GSLB가 사용된다.

    















<br /><br /><br />
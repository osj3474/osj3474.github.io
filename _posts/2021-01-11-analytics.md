---
title: 내 블로그를 보는 사람이 있을까?
categories:
   - web
---





<br>

혼자 공부한 거를 정리하려고 블로그를 운영한다고 해도 *<u>혹시 몇명이라도 내 블로그를 보지는 않을까</u>* 하는 기대를 하게 됩니다. 그럴 때 필요한 것이 **애널리틱스** 입니다.

<br>






# 애널리틱스 툴

사이트에 발생하는 트랙픽 정보를 기반으로 효과적으로 의사결정을 할 수 있게 하는 도구입니다. 대표적으로 구글 애널리틱스가 있지만, 네이버 애널리틱스도 비교대상이 되는 것 같습니다. 둘다 무료라는 점이 좋고(물론, raw data가 필요한 경우에는 GA360유료버전을 사용해야 합니다.), 운영하는 사이트에 대한 디벨롭에 상당히 도움이 됩니다. 

저는 구글 애널리틱스, 네이버 애널리틱스를 둘 다 연결했고, 어떤 차이점이 있는지는 잘 정리된 내용이 있어 첨부합니다.

<a href="https://polygonstudio.tistory.com/158">[구글 애널리틱스와 네이버 애널리틱스의 차이점] </a>

<br>





# 몇 명이 방문했을까

운영하는 사이트를 애널리틱스와 연결하는 가장 첫번째 이유는 '사용자가 몇 명인가' 일 것이라 생각합니다. 저도 그게 궁금해서 연결한 것이기 때문입니다. 연결 방법은 잠시 뒤에 소개하는 걸로 하고, 연결을 하고 나면 다음과 같이 확인할 수 있습니다.<br>

### [ 구글 애널리틱스 ]

![image](https://user-images.githubusercontent.com/42775225/104138738-72984800-53e9-11eb-9599-7e66a818c928.png)



### [네이버 애널리틱스]

![image](https://user-images.githubusercontent.com/42775225/104138747-82179100-53e9-11eb-8fff-736f95f9eeb0.png)



단순히 방문자 수 뿐만 아니라, 어느 사이트에서 넘어왔는지나 어떤 버튼을 누르는지까지 설정할 수 있습니다. 이것을 설정하고 이해하기 위해서는 보고서의 구성하는 Dimesions와 Metric, 기본적인 지표인 pageview, event 같은 것을 알아야 할 필요성이 있습니다.

이런 지표들만 보면, 데이터 분석의 욕구가 막 오릅니다. (못 다 이룬 꿈 ㅎㅎㅎ,,)

<br><br>

<br>

# 연결 방법



### [구글 애널리틱스]

1. 구글 로그인 후 https://analytics.google.com/  에서 애널리틱스에 가입합니다.

   ![image](https://user-images.githubusercontent.com/42775225/104186274-88455600-5459-11eb-87c6-994a94dbabcc.png)

   

2. 그리고 필요한 서비스를 선택합니다. (저는 웹 입니당)

   ![image](https://user-images.githubusercontent.com/42775225/104186352-9eebad00-5459-11eb-83f1-3f5bb06a139f.png)

   

   

3. 그리고 추적코드를 저희 블로그에 할당해야 하는데, 추적코드를 설치하는 방법은 3가지가 있습니다.

   - gtag.js
   - GTM (Google Tag Manager)
   - gtag.js + GTM

   저희는 가장 간단하며, \<head>에만 넣으면 설치가 되는, gtag.js로 하겠습니다. 

   ![image](https://user-images.githubusercontent.com/42775225/104186661-115c8d00-545a-11eb-92a6-3645e9b5cf03.png)

   ![image](https://user-images.githubusercontent.com/42775225/104186686-21746c80-545a-11eb-9466-f99188aee84c.png)

   위의 내용을 자신의 블로그의 \<head>에 넣으면 됩니다. 



* jekyll 깃블로그를 사용하시는 분들만 따라오세요.

  https://mmistakes.github.io/minimal-mistakes/docs/configuration/

  에 공식문서가 있습니다. 

  일단 _config.yml에서 다음과 같이 해줍니다.

  ![image](https://user-images.githubusercontent.com/42775225/104187083-b11a1b00-545a-11eb-9071-ff414dd4794c.png)

  그리고 저희 블로그의 모든 페이지에 적용되는 default.html의 head부분에 gtag.js를 추가해줍니다. 

  ![image](https://user-images.githubusercontent.com/42775225/104196559-46231100-5467-11eb-8ea7-5e8ca21deca4.png)

custom.html에 그 내용을 추가해줄게요.

![image](https://user-images.githubusercontent.com/42775225/104187528-43222380-545b-11eb-9a5e-d697c6a7637b.png)



<br><br>

### [네이버 애널리틱스]

1. 네이버 로그인 후 https://analytics.naver.com/ 에 들어갑니다.

2. 구글 애널리틱스는 웹을 등록하면, 동일하게 script를 넣어줍니다. 

3. body에 넣으라고 되어 있었는데, head에 넣어도 동작합니다.

   <img src="https://user-images.githubusercontent.com/42775225/104188146-20dcd580-545c-11eb-9d78-9f712f36b45e.png" alt="image" style="zoom:50%;" />





<br>

앞으로 애널리틱스로 사이트 운영에 좋은 사이클을 만들어 보겠습니다. 오늘도 수고했당:)


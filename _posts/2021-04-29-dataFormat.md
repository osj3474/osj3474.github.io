---
title: \[Data\] 메세지 표준 규격
excerpt: "JSON, yaml, xml를 알아봅시다~!"
categories:
   - data
---

<br><br>

# 글을 시작하며

오늘은 JSON, yaml, xml에 대해서 이야기를 하려고 합니다. 관심이 있으시다면, 끝까지 읽어주세요~!!

<br />



<div align=center>
  <img src="/assets/images/0429-1.png">
</div>

A/와 B는 팀 프로젝트를 마치고 함께 보고서를 쓰게 되었습니다. 하나의 보고서를 내야하는데, 분량이 많아서 나눠서 쓰기로 합니다. 

각자가 쓰고 하나로 합치려고 보니, A는 워드로 작성을 했고, B는 한글로 작성을 했던 것입니다. 한 파일로 합쳐보았지만, 용지 규격도, 폰트도 달라서 그림과 표의 위치가 엉망이 되었습니다.

어떤 방식으로 보고서를 쓸지 미리 정한다면, 위와 같은 상황을 막을 수 있을 것입니다. 

즉, 데이터를 전달할 일이 있다면 기준이 되는 **'규격'** 이 있다면 좋을 것입니다.



<br />

# 메세지 표준 규격

**클라이언트**와 **서버**가 메세지를 주고 받을 때도, 데이터를 어떻게 주고 받는지가 중요합니다.



<div align=center>
  <img src="/assets/images/0429-2.png">
</div>


<br />

그래서 이들 간에도 규격이 있습니다. 

그것은 오늘의 주인공이 **JSON, yaml, xml** 입니다. 





<br />

# JSON, yaml, xml

이 규격들은 <u>모양</u>, <u>허용되는 인코딩</u>, <u>주석 가능 여부</u> 등에 차이가 있습니다. 

표를 보고 조금 더 자세히 알아봅시다.

<br />

|                              | <center>JSON</center>                                        | <center>yaml</center>                                        | <center>xml</center>                                         |
| ---------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img width=100/>             | <center>'키'와 '값'으로 된 객체<br />(중괄호{ } 사용)</center> | <center>JSON과 유사<br />(스페이스로 값 구분, 탭X)</center>  | <center>html와 유사<br />(태그< > 사용)<br /></center>       |
| <center>모양</center>        | {<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;key : value,<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;key : value<br />} | key : value<br />key :<br />&nbsp;&nbsp;&nbsp;&nbsp;key : value | <?xml version="1.0" encoding="UTF-8"?><br /><br />\<key> value </key> |
| <center>인코딩 허용</center> | UTF-8                                                        | UTF-8, UTF-16<br />(자바 설정 파일이<br />application.yml 인 이유~!) | 문자 인코딩 직접 지정<br />(위의 예시처럼 인코딩을 <br />코드에서 직접 지정) |
| <center>주석 기능</center>   | ❌                                                            | ⭕️<br />(#)                                                   | ⭕️<br />(\<!-\- -\->)                                         |
| <center>사용 예시</center>   | - 서버/클라이언트 간 대표적인 메세지 규격                    | - 자바 application.yml과 같은 설정 파일                      | - AWS의 RESTful API 응답 형식<br />- Mybatis 사용 시 Mapper  |
| <center>널 값</center>       | null                                                         | ~                                                            |                                                              |
| <center>한계</center>        | - 읽기는 쉽지만, 표현 비용 큼<br />&nbsp;&nbsp;(텍스트라서)<br />- 호환성 유지 어려움<br />&nbsp;&nbsp;(서버에서 key변경 시)<br /><br />🎯 Graphql은 왜 등장했을까요~!<br />- 주석 불가 | - JSON만큼 범용적이지 않음<br />- 간격 중요 (탭 허용 X)      | - 단순 데이터 전달 용으로 쓰기엔 복잡                        |



<br /><br />

이번에도 느낀 것이지만, 새로운 개념을 공부할 때, 기존에 알던 것을 기반으로 이해하게 됩니다. 가령,



1. 내가 경험했던 yaml파일이 무엇이 있었지? 전 application.yml이 떠올랐습니다.

   => 아 그리고 자바 설정 파일이라서!! JSON이 아닌 yaml이구나. 

   &nbsp;&nbsp;&nbsp;(<a href="https://osj3474.github.io/data/encoding/">자바의 경우, UTF-16 인코딩이 필요합니다~!</a>)

   

2. xml은 Mybatis로 프로젝트 할 때, Mapper로 썼었는데!

   => xml 사용 예시로 적으면 되겠다!

   

이렇게 기존 개념들이 연결되었을 때의 기분은 최곱니다. 🚀🚀🚀 



<br />


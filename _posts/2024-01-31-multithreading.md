---
title: \[Web\] UDF로 접하게 된 멀티쓰레딩 이슈
excerpt: "hive, databricks, ThreadLocal, synchronized"
categories: 
   - web
---

<br />
<br />

# 1. UDF 는 무엇일까

저는 hive에서 트래픽 데이터의 메타 정보를 사용할 수 있게 해주는, java 기반 UDF를 개발 및 유지 보수하고 있습니다. 

UDF란 java를 이용해 만든 클래스를 쿼리에서 사용할 수 있도록 만든 함수인데요.

single row 를 input으로 받아서 single row를 output으로 반환합니다.

실제 구현은 GenericUDF 를 상속받아서 initialize 함수와 evaluate 함수를 오버라이딩해서 사용합니다.

<br />
<br />
<br />


# 2. 멀티 쓰레딩 이슈 발견

기존에는 hive에서 UDF를 사용하고 있었지만, 

회사에서 databricks 라는 플랫폼을 도입하면서 databricks 환경에서도 해당 UDF를 사용해야 했습니다.

<br />

그런데 hive에서 UDF를 사용해서 집계한 결과와 databricks에서 UDF를 사용해서 집계한 결과가 

다르게 나오는 문제가 발견되었습니다.

<br />

원인은 hive와 databricks의 <b>동작 방식 차이</b>에 있었는데요.

hive는 분산처리 방식이 프로세스 단위라서 worker node의 pid가 여러개 생기는 반면,

databricks는 멀티 쓰레드 방식으로 동작하였습니다.

<br />

UDF가 하는 역할이 트래픽 데이터의 버전에 맞는 meta 정보를 매핑해주는 역할을 수행하는데,

meta 정보 변수가 <b>클래스의 필드 영역에 선언</b>되었고, 

이는 jvm의 method area 이라서 쓰레드가 공유하는 영역이었습니다.

즉 어플리케이션 메모리 상에 올려둔 meta 정보 변수는 critical section 으로 처리가 필요했습니다.🥲

<br />

이해를 돕기 위한 임의의 예를 들면,

어떤 클릭에 대한 이벤트가 지난 주에는 메인 프로모션에 해당되어 가중치가 5였지만,

이번 주는 프로모션이 끝나서 가중치가 1이 되었다면 분석가 분들이 분석을 하실 때

각 버전에 맞는 가중치가 매핑되어야 정확한 레포트가 나올 수 있는 것입니다.



<br />
<br />
<br />


# 3. 해결 방법

웹 개발을 하면서 멀티 쓰레딩 이슈를 다뤄본 적은 <b>처음 있는 일</b>이었습니다.

선배 개발자 분의 단비같은 조언✨을 바탕으로 여러 방법으로 해결해볼 수 있었습니다.

<br />

<b>첫 번째</b>는, databricks의 spark.executor.cores 값을 1로 설정하는 것입니다.

각 executor가 실행하는 task를 1로 설정해서 싱글 쓰레드 방식으로 사용하는 것입니다.

실제로 속도상 이슈가 없어서 일정 기간 동안 이 방법으로 사용하였었습니다.

하지만 1로 설정해서 사용한다면 속도 면에서의 장점을 살릴 수가 없었습니다.

<br />

<b>두 번째</b> 방법으로는, ThreadLocal을 사용하는 방법입니다.

ThreadLocal은 각 쓰레드마다 독립적인 데이터를 보유하게 해주는 Java 클래스이기 때문에,

meta 정보를 공유 자원으로 사용하지 않도록 해서 해결하는 것입니다. 

단, 메모리 누수의 주범이 되니 주의해서 사용해야 한다는 점이 있습니다.

(참고 블로그 <a href="https://javacan.tistory.com/entry/ThreadLocalUsage" target="_blank">바로가기</a>)

<br />

<b>마지막</b> 방법으로는, 현재 변수에 값을 write하는 부분을 synchronized 처리합니다. (lock 을 겁니다.) 

멀티 쓰레드에서 문제가 되는건 읽고 쓰는 것 때문이라서,

쓰는 동안은 lock을 걸고, 읽기 전용 객체처럼 사용하면 멀티 쓰레드 문제가 발생하지 않습니다.

<br />

결국, 코드 레벨에서 멀티 쓰레딩 문제를 해결하여

databricks에서 UDF 동작하는 속도를 2~3배 정도 개선하였습니다.🥳


<br />
<br />
<br />

# 4. IT 용어 메모

- 동시성 : 일을 주고 쉬는 시간에 다른일을 함. 제한된 자원내에서 성능 좋음

- 병렬성 : 일을 쪼개서 함. 자원을 높이면 성능도 올라감

- Webflux(Reactive Stack) : 비동기 및 논블로킹 방식으로 동작. db도 non-blocking 지원해야 함. (MongoDB)

- MVC(Servlet Stack) : 전통적인 동기 및 블로킹 방식으로 동작. one-request-per-thread. (대부분의 RDBMS)

<br />
<br />
<br />
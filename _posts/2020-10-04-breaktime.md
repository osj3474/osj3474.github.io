---
title: \[데이터\] 새로운 크롤링 방법
categories:
   - data
---

<br>

# 요즘의 일상

2020년도 하반기 공채 시즌이 되면서,

- 수업&과제
- 자소서 쓰기

가 하루의 전부가 되었습니다. 취업을 준비하시는 분들 모두 파이팅 ㅠㅜ

<br>



# 교수님의 노크

저는 교수님 맞은편에 있는 학회실에서 주로 공부를 합니다. ICT융합 특론 수업 TA라 시키실 일이 있으면 바로 하곤 합니다.

이번에는 수업 때 사용할 데이터를 모아달라고 말씀해 주셨는데, 해당 데이터는 코로나 관련 재난 문자 데이터였습니다.

링크 : http://www.safekorea.go.kr/idsiSFK/neo/sfk/cs/sfc/dis/disasterMsgList.jsp?emgPage=Y&menuSeq=679

우선, robots.txt로 크롤링 가능 여부를 확인해봤는데, 확인이 되지는 않았습니다.

(웹사이트 최상위 경로에서 확인하는거 아닌가요? 아시는 분,,,)

<img src="https://user-images.githubusercontent.com/42775225/95009872-2312e500-0660-11eb-8834-eb44279f5cd8.png" alt="image" style="zoom:50%;" />

일단, 나쁜 용도로 사용하는 것이 아니기 때문에 크롤링을 시작했습니다.

<br><br>

# 새로운 크롤링 방법

원래라면 URL 기반으로 크롤링이 안 되는건 없다고 생각했었는데, 해당 사이트는 URL이 전혀 바뀌지 않았습니다.

(이 때까지는, '페이지 소스'에 직접적으로 원하는 text가 없더라도 text가 있는 URL이 소스 안에 있기 마련이었습니다.)

![image](https://user-images.githubusercontent.com/42775225/95010029-47bb8c80-0661-11eb-99db-21fda0f28aeb.png)

<br><br>

그래서 개발자 도구로 이전 글과 다음 글로 넘어갈 때 어떤 것이 바뀌는지 확인해 본 결과, session storage의 값을 변경해가면서 페이지를 바꾸는 것이었습니다.

![image](https://user-images.githubusercontent.com/42775225/95010165-1ee7c700-0662-11eb-9c03-423fa4ea6cac.png)

<br><br>

해당 사실을 확인하고는 selenium으로 session storage 값을 변경해가면서 데이터 수집을 완료하였습니다.

✅ 최종 결과

![image](https://user-images.githubusercontent.com/42775225/95010227-8d2c8980-0662-11eb-81b5-8565fed38ef9.png)

교수님께서 1월 달부터 데이터를 요구하셔서 약 35000row가 나왔습니다. (utf-8, cp949, euc-kr 세 가지 인코딩 방식으로 넘겨드렸습니다.)

<br><br>

# 이번 학기가 끝나면

어떤 곳에서 어떤 일을 하게 될지 궁금하기도 하고, 기대가 되기도 합니다. 고등학교 때 대학을 기대하는 것과는 또 다른! 마음이랄까,,

최선을 다하는 마지막 학기를 보내겠습니다~~~~ 바쁘다고 주변의 소중한 사람들에게 소홀하지 않기를 🤗

<br><br>

# 사용했던 코드들!

1. 이번 크롤링에서 사용했던 모듈은 다음과 같습니다. selenium을 사용하기 위해서는 크롬 드라이버를 다운로드 받아야 합니다. (본인이 사용하는 크롬 브라우저의 버전과 동일한 버전으로 다운받아야 합니다.)

   cf) 크롬 드라이버 설치 링크 : https://chromedriver.chromium.org/downloads

   ![image](https://user-images.githubusercontent.com/42775225/95010534-0200c300-0665-11eb-9085-064827124f22.png)

   ```python
   from selenium import webdriver
   import pandas as pd
   import re
   import time
   import random
   from selenium.webdriver.common.by import By
   ```

2. 세션 스토리지의 값을 변경하기 위해서는 자바스크립트를 사용해야 합니다. 아래의 window.~~ 코드가 해당 코드입니다. 우선 드라이버로 브라우저를 엽니다.

   ```python
   class SessionStorage:
       def __init__(self, driver) :
           self.driver = driver
       def set(self, key, value):
           self.driver.execute_script("window.sessionStorage.setItem(arguments[0], arguments[1]);", key, value)
       def __setitem__(self, key, value):
           self.set(key, value)

   driver = webdriver.Chrome('/Users/osangjin/Desktop/dev/Algo/chromedriver')
   url = 'http://www.safekorea.go.kr/idsiSFK/neo/sfk/cs/sfc/dis/disasterMsgView.jsp?menuSeq=679'
   driver.get(url)
   ```

3. 그 다음 세션 스토리지 값을 변경하고, 새로고침 하면서 데이터를 받아왔습니다. (text가 아예 없는 경우도 있어서 try, except로 처리해줍니다.) 시간이 엄청 많이 걸립니다..

   ```python
   for i in range(START, END):
       storage["bbs_ordr"] = i
       driver.execute_script("window.location.reload();")
       time.sleep(random.random()+0.3)

       search_result = driver.find_element(By.ID, 'sj')
       txt = search_result.text

       p = re.compile('(\d{4}/\d{2}/\d{2} \d{2}:\d{2}:\d{2})')
       try:
           date = re.findall(p, txt)[0]
       except:
           date = ''

       search_result = driver.find_element(By.ID, 'cn')
       txt = search_result.text

       try:
           content = txt[txt.index('['):txt.index('-송출지역-')]
       except:
           content = ''

       p = re.compile('\-송출지역\-\n(.*)')
       try:
           start_char = re.findall(p, txt)[0]
           broadcast = txt[txt.index(start_char):]
       except:
           broadcast = ''
       data_lst[idx] = [date, content, broadcast]
       idx+=1
       print("{}번째 완료!".format(i))
   ```

4. pandas로 csv파일로 저장합니다.

   ```python
   df = pd.DataFrame(data_lst,columns=['시간', '내용', '송출지역'])
   df.to_csv("utf.csv", encoding='utf-8')
   df.to_csv("cp.csv", encoding='cp949')
   df.to_csv("euc.csv", encoding='euc-kr')

   driver.close()
   ```

5. 터미널을 여러개 켜서 병렬적으로 데이터를 얻습니다.
   ![image](https://user-images.githubusercontent.com/42775225/95010683-4345a280-0666-11eb-9a69-f037ca4b23e1.png)

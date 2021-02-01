---
title: \[웹\] 웹사이트 호스팅 (AWS편)
categories:
   - web
---

# 웹사이트 만들어줄 수 있을까..?

'블로깅을 매일 해야지!' 했는데, 계절학기 기말고사에 집중하기 위해서 잠깐 내려놓고, 다시 오늘 했던 일을 정리합니다 :)

창업학부에서 공부하다가 보면, 아이디어를 테스트해보고 싶은 선후배님들이 '웹사이트(혹은 모바일 어플리케이션) 만들어 줄 수 있어?' 이런 부탁을 받는 경우가 많은 것 같습니다.

최근에 받은 부탁으로 빠르게 전자 서명 기능이 가능한 웹사이트를 만들었어야 했는데, 그림판 기능을 가진, 웹사이트를 AWS로 빠르게 publish해서 넘겨주었습니다. 그 과정을 정리합니다.

## 이게 너무 불편했어..!

선배가 가진 고민은, 기말고사 파이팅(학부 차원에서 기말고사를 응원하는 제도)을 하면서 발생했던 문제를 제게 토로해주었습니다. (자세한 내용은 선배가 사업까지 생각하기 때문에 패스..)

## 웹사이트 구현

우선 웹사이트의 내용과 기능은 _노마드 코더의 그림판 만들기_ 를 참고하였고, 배포에만 집중해서 전달합니다.

## 1단계 (소스 코드 준비)

웹사이트를 채우는 소스코드들을 로컬에서 완성합니다. 저의 경우 파일 트리는 다음과 같았습니다.

<img src="https://user-images.githubusercontent.com/42775225/87038461-baff5f00-c228-11ea-8f01-879552dc41e5.png" alt="image" style="zoom:50%;" />

## 2단계 (AWS EC2 생성)

그리고, AWS EC2 인스턴스를 생성합니다. 스펙은 다음과 같습니다.

- Ubuntu Server 18.04 LTS
- t2.micro

![image](https://user-images.githubusercontent.com/42775225/87038983-79bb7f00-c229-11ea-92c6-4213ea8e3436.png)

cf) 접속하는데 혹시나!
WARNING: UNPROTECTED PRIVATE KEY FILE!
이러한 에러가 뜬다면

=> chmod 400 [파일명].pem

**_그리고 인바운드 규칙에서 반드시 HTTP(port80)를 추가해주세요~!_**

## 3단계 (소스 코드 올리기)

이제 로컬에 있는 소스 코드를 EC2 서버로 올리겠습니다. 소스코드가 있는 곳으로 이동해서 다음의 명령어를 작성합니다.

```shell
scp -i ~/Documents/dev/key/[파일명].pem -r sign-test ubuntu@ec2-00-00-000-000.ap-northeast-2.compute.amazonaws.com:~/
```

여기서 잘 안되는 경우가 생길 수 있을 것 같아서 추가 설명합니다!

1. 우선, sign-test라는 폴더를 로컬에서 서버로 올리는 작업을 하는 것입니다.

2. 터미널을 이용해서 sign-test가 있는 곳으로 이동해주세요!

3. 그리고 -i 다음에 나오는 곳은 pem파일이 저장되어 있는 곳을 적을 것입니다.

4. ubuntu@ec2 다음에 00-00-000-000으로 되어 있는 곳은, 여러분의 EC2 서버를 접속할 때의 ip주소에 해당됩니다. (AWS console에서 '연결'을 선택하면 나오는 그 주소 전체를 복붙하고 끝에 :~/를 붙여주세요!)

## 4단계 (Apache 서버)

이제 서버를 설치합니다. Apache, nginx 등이 있지만, 저는 생활코딩에서 재밌게 배워서 거기서 소개해준 Apache를 설치하려고 합니다.
궁금하신 분들은 다음의 링크를 참고하세요~:)

<a href="https://opentutorials.org/course/2598/14446" target="_blank"> 생활코딩 Apahce </a>

```shell
sudo apt-get update
sudo apt-get install apache2
sudo service apache2 start
```

여기까지 했다면, 본인의 EC2 IP로 접속하면,

<img src="https://user-images.githubusercontent.com/42775225/87041137-bd63b800-c22c-11ea-8d78-8e3897814af8.png" alt="image" style="zoom:50%;" />

이런 화면이 떳다면 거의 다 성공했습니다.

## 5단계 (홈 디렉토리 변경)

이제 저희의 리눅스 서버의 홈 디렉토리로 아파치가 링크할 수 있도록 설정해줍시다. 현재는 /var/www/html 으로 연결되어 있습니다.

```shell
pwd
```

해서 얻은 정보(/home/ubuntu/)로 설정해줄 겁니다.

```shell
cd
cd /etc/apache2/sites-available
sudo vi 000-default.conf
```

여기로 들어가서 DocumentRoot의 /var/www/html를 /home/ubuntu/ 로 변경합니다.

![image](https://user-images.githubusercontent.com/42775225/87042029-0e27e080-c22e-11ea-9b37-42b37abd6e0a.png)

그리고,

```shell
cd /etc/apache2
sudo vi apache2.conf
```

여기로 들어가서 Require all denied를 granted로 변경합니다.
![image](https://user-images.githubusercontent.com/42775225/87042333-7e366680-c22e-11ea-841a-fdf526551160.png)

여기까지 끝났다면,

```shell
sudo service apache2 restart
```

를 하고 다시 본인의 EC2 IP주소를 브라우저에서 입력해봅니다.

## 완성

그러면 저희가 로컬에서 보던 index.html을 다른 사람들도 브라우저를 통해서 접속할 수 있도록 세팅이 끝났습니다.

웹사이트는 이제 막 공부를 시작해서 더 좋은 방법이 있을 지도 모릅니다. 그리고 이런 경우, github pages를 이용하는 것이 더 간단할 수도 있겠습니다.

인스타그램이 AWS를 이용해서 단 3명의 엔지니어 만으로 1400만 개의 계정의 억 단위 트래픽을 견딜 수 있도록 했다는 글을 봤습니다. 앞으로 AWS IAM에 대해서 공부할 텐데, 너무 매력적인 서비스인 것 같습니다.

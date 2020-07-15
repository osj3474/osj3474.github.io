---
title: 웹사이트 호스팅 (Github pages편)
---

# 정적 웹사이트 호스팅하기

## 배움의 동기

웹의 중요성을 알면서도 자꾸만 미루고 미루다 대학생으로서 마지막 방학이 되어서야 공부하기 시작했습니다.

장기적인 관점에서 **_데이터 분석가_** 로 성장하기 위해서는 데이터 전처리 코스트를 얼마나 줄일 수 있는지가 관건이라고 생각합니다.

그 중 데이터 확보(수집) 단계에서 '로그 데이터' 를 다룰 줄 아는 능력, 또 더 나은 '크롤링' 을 위한 발판이 되기를 바랍니다.

마지막으로, 제가 만든 웹사이트에서 데이터 파이프라인 구축을 **_airflow_** 를 활용해서 해보려고 합니다.

_cf) 나중에 지금까지 해왔던 공부와 진로 계획도 포스팅 하겠습니다._

## 1단계 (소스 코드 준비)

이번에도 역시 로컬에 작업했던 웹사이트 소스가 있다고 하고 시작하겠습니다. 저의 경우 파일 트리는 다음과 같습니다.

![image](https://user-images.githubusercontent.com/42775225/87558140-b7ba1680-c6f3-11ea-8725-af0929881158.png)

## 2단계 (github repository)

새로운(로컬에서 작업할 때부터 버전 관리를 하는 것을 추천합니다. 그 경우 기존의 저장소를 사용하면 됩니다.) 깃허브 저장소와 해당 로컬 폴더를 연동합니다.

## 3단계 (gh-pages 브랜치 만들기)

저희가 사용하려고 하는 Github Pages는 Github 내에 있는 저장소를 무료로 호스팅하게 해주는 서비스입니다.
(이 때, front-end만 있는 정적 웹사이트만 해당됩니다.)

### 3-1 딘계 (브랜치 생성)

저장소에서 master branch탭을 누르고

**_gh-pages_** 라는 브랜치를 만듭니다. (브랜치명은 꼭 gh-pages여야 합니다.)

![image](https://user-images.githubusercontent.com/42775225/87559587-7460a780-c6f5-11ea-810e-bedb9d63ae8b.png)

### 3-2단계 (setting탭에서 확인)

그러면, setting탭으로 가서 publish가 되었는지 확인하고, 접속해봅니다.

![image](https://user-images.githubusercontent.com/42775225/87560666-ac1c1f00-c6f6-11ea-821f-d1e69b99e250.png)

## 완성

이전에 사용했던 AWS와 Apache를 이용하는 것보다 훨씬 간편하게 퍼블리쉬하였습니다.
(단, github pages는 지켜야하는 규칙이 몇가지 있는데, 예로, index.html 파일은 반드시 있어야 합니다.)
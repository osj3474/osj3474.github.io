---
title: \[Project\] 추천 시스템
categories:
   - project
---

# 나에게 딱 맞는 식당은 어딜까?

거의 3주 동안 블로그에 공부한 내용조차 올리지 못했는데, <a href="https://github.com/osj3474/Recommendation-system" target="_blank">드디어 결과물</a> 을 들고 왔습니다! 저는 2년 전, 정말 아무것도 모를 때 처음 추천 시스템을 배웠습니다. 처음 배웠던 추천 시스템은 당시 제겐 너무 어려웠습니다. 물론 지금도 공부하면 할수록 더 어려워지는 것 같습니다.

우선 개념 자체는 다른 블로그들에서 쉽게 찾아볼 수 있는데, 사실 저는 계산이 직접 어떻게 되는지가 이해되어야 하는 사람이라 각각을 저만의 그림으로 만들어 봤습니다. 익숙한 collaborative부터 보겠습니다!

## 1. Collaborative filtering 개념

아래의 그림들 처럼 collaborative filtering은 user_item 테이블이 기반이 됩니다. 여기서 user-based는 사용자들끼리의 유사도를 구하고, item-based는 아이템들끼리의 유사도를 구해서 filtering을 진행합니다.

### 1) User-based collaborative filtering

먼저, 첫 번째 보이는 user_item 테이블에는 사용자가 아이템에 내린 평점이 값으로 들어가 있는 테이블입니다. 그리고, 추천을 하려는 대상은 u3(3번째 사용자) 라고 합시다. 사용자 기반은 사용자들끼리의 유사도를 구해야 합니다. 유사도를 구하는 방법은 여러가지가 있는데 현재는 그냥, -1 ~ 1 사이의 값을 가지며, 1에 가까울수록 비슷하다고 생각하겠습니다. (통상적으로 피어슨 상관계수나 코사인 유사도를 사용합니다.)

![image](https://user-images.githubusercontent.com/42775225/89480561-a20aaf00-d7d0-11ea-9e11-e9c0b29d1a0b.png)

지금은 u3에게 추천을 해준다고 하면, u3과 다른 사용자들끼리의 유사도를 구해서 높은 순서대로 나열합니다. (이 때, k개만 남깁니다.) 값을 구했더니 u3과의 **유사도 수치** 는 위와 같았습니다. 그러면 사용자들이 내린 평점과 유사도로 가중 평균합니다. 그러면, u3과 유사한 사용자일수록 유사도 수치가 높고, 그 사용자가 내린 평점에 더 힘을 실어줄 수 있습니다. 그래서 i1에 내릴 평점을 구했습니다. (i2, i3, i4도 모두 동일합니다.)

### 2) Item-based collaborative filtering

아이템 기반 역시, 첫 번째 보이는 user_item 테이블에서 시작합니다. 추천을 하려는 대상은 u3(3번째 사용자) 라고 합시다. 아이템 기반은 아이템들끼리의 유사도를 구해야 합니다. 여기서도 유사도 값은 -1 ~ 1 사이의 값을 가지며, 1에 가까울수록 비슷하다고 생각하겠습니다.

![image](https://user-images.githubusercontent.com/42775225/89481568-fc0c7400-d7d2-11ea-900b-49b9f16a9f81.png)

우선, item들끼리의 유사도 매트릭스를 만듭니다. 그리고 u3이 내린 평점을 아이템의 유사도로 가중 평균합니다. 그러면, u3이 높은 평점을 준 아이템과 유사한 아이템일수록 더 높은 점수를 받을 수 있게 됩니다. 그래서 i1에 내릴 평점을 구했습니다. (i2, i3, i4도 모두 동일합니다.)

##### 번외

여기서 한 가지를 생각해 봅시다. 새로운 신규 사용자가 들어왔다고 하겠습니다. 그러면, user-based의 경우는 새로운 사용자와 기존의 모든 사용자들과의 유사도를 구해서 가중평균을 해야합니다. 반면, item-based 같은 경우는 item 유사도 매트릭스는 계산해둔거 그대로 두고, 새로운 사용자의 평점 정보만 받아서 바로 계산해서 줄 수 있습니다. 그래서 item-based가 계산량에서는 우위에 있습니다. 단, 두 가지 방법 모두 다 사용자가 내린 평점 점수에 의존하고 있기 때문에 평점 정보가 없는 사용자나 아이템의 경우 제약이 생깁니다. (사실 이 경우에는 popularity로 해결하거나, 가상평가, 평가단 이용 등의 방법을 쓰기도 합니다.)

## 2. Content based filtering

컨텐츠 기반은 다음과 아이템을 설명하는 feature들을 가지고 아이템을 벡터로 표현하고, 사용자가 이 feature들에 대해 내린 선호도를 기반으로 추천하는 것입니다. 그려본 그림과 행렬 곱이 되는 방식은 다음과 같을 것입니다.

![image](https://user-images.githubusercontent.com/42775225/89487554-c3739700-d7e0-11ea-8472-eaa6e6972cf2.png)

유투브 추천 알고리즘도 살펴봤는데, 상당히 흥미로웠습니다. 나중에 페이퍼를 제대로 읽게 되면 이것도 정리해서 올리겠습니다.

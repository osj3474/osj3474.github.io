---
title: 알고리즘과 친해지기
---

# 백준, 프로그래머스

취업을 준비하면서 데이터 엔지니어에 대한 매력을 느낄 때 쯤, 해당 직무를 뽑을 때, 어떤 점을 보는지 확인했습니다.
데이터 사이언티스트 신입의 경우에도 코딩테스트를 보다는, 시각화와 모델링 능력에 초점을 맞춰서 본다면, 엔지니어는 확실히 컴퓨터 공학 지식이 기반이 되어야 했습니다. 더 나은 코드를 위해 알고리즘 공부 시작!

아직까지는 제대로 코딩테스트를 준비해본 적이 없기 때문에, 제가 감당할 수 있는 분량으로 시작했습니다.

매일매일

- 백준 구현 2문제

- 프로그래머스 고득점 kit 2문제

다음은 오늘 문제를 풀면서 배운 점을 정리해보았습니다.

# Set자료형 & import sys 과의 만남 (백준 1764번)

교집합을 구하는 문제였는데, 어떻게 할까 고민하다가 dictionary를 사용하였습니다.

#### [ 첫 내 코드 ] - 1단계

```python
N, M = map(int, input().split())
no_hear_lst = [input() for _ in range(N)]
no_hear_dic = dict(zip(no_hear_lst, [0]*N))  # search O(1)로 하기 위해 dictionary

cnt = 0
total_lst = [''] * min(N, M)   # append, pop 쓰지 않기 위해 리스트 할당

for _ in range(M):
    temp = input()
    try:
        ck = no_hear_dic[temp] # try, except 유도
        total_lst[cnt] = temp
        cnt += 1
    except:
        pass

print(cnt)
temp_lst = total_lst[:cnt]
total_lst = sorted(temp_lst)
for total in total_lst:
    print(total)
```

![image](https://user-images.githubusercontent.com/42775225/87614955-743fc680-c74c-11ea-8a88-b3cbeaf768bf.png)

그런데, 결과는 무려 시간이 3988ms. 잘못되었다고 생각하고, 100ms 대로 푸신 다른 사람들의 풀이를 본 결과! 자료형을 set을 사용해서, intersection으로 쉽게 교집합을 구했습니다.

#### [ set을 활용 ] - 2단계

```python
N, M = map(int, input().split())

a = set([input() for _ in range(N)])
b = set([input() for _ in range(M)])

c = a&b
print(len(c))
for item in sorted(list(c)):
    print(item)
```

코드길이가 무지하게 짧아졌습니다. 그런데 결과는...

![image](https://user-images.githubusercontent.com/42775225/87615322-6f2f4700-c74d-11ea-9434-86c9efa64f8a.png)

시간은 3904ms로 거의 줄어들지 않았습니다. (심히 충격적이었습니다.)

#### [ sys모듈 사용] - 3단계

이 문제는 입력을 처리하는 시간이 길었던 것이었습니다. 그래서 input()을 sys.stdin.readline()로 바꾸었더니 시간이 엄청 단축되었습니다.

```python
import sys

N, M = map(int, sys.stdin.readline().split())

a = set([sys.stdin.readline() for _ in range(N)])
b = set([sys.stdin.readline() for _ in range(M)])

c = a&b
print(len(c))
for item in sorted(list(c)):
    print(item, end='')
```

![image](https://user-images.githubusercontent.com/42775225/87618023-c801de00-c753-11ea-9891-8830eb7d0ba5.png)





#### **_더 나은 코드를 위해 알고리즘 공부 열심히!!_**


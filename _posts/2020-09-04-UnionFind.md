---
title: 알고리즘과 친해지기 2
---

# 지나왔던 시간을 되돌아보며

몇 달간 코딩테스트를 준비하면서 겪은 단계를 정리해봤습니다.

### 1. 문제를 읽고 구현을 할 수 있는지!

이 단계는 구현을 할 수 있는 능력이 자신감으로 이어집니다. (코딩이 즐거운 단계)

하지만, 정답을 제출하면 테스트 케이스가 거의 다 틀립니다. 그럼에도 저는 그 당시, 구현을 하고 있는 본인에게 뿌듯했습니다.

### 2. 효율적으로 풀었는지!

시간복잡도를 생각하기 시작하는 단계입니다. 반복문을 줄이는 것부터 시작됩니다. 그 말고도, 파이썬 List를 사용할 때, append 혹은 pop이 얼마나 많은 시간을 잡아먹게 하는지 알게 되었습니다. (저는 코딩테스트를 **파이썬**으로 준비합니다.)

그래서 저는 List를 사용할 때, 대부분의 경우, 리스트의 공간을 미리 잡아두고, **할당** 하는 방법으로 사용합니다. List의 resize가 내부적으로 어떻게 구현되어 있는지 확인해봅시다.

![image](https://user-images.githubusercontent.com/42775225/92188906-ca053500-ee98-11ea-8f8a-d6804d0661ce.png)

List는 링크드리스트처럼 element들이 링크로 연결되어 있는 것이 아닙니다. 연속된 메모리 블록을 받아서 element들을 저장하는 것입니다. C로 짜여진 내부를 보면(PyListObject), element도 pointer로 가지고 있습니다.

아무튼! 인덱스가 주어지면, 인덱싱하는 경우는 constant time이 걸립니다. 하지만, append나 pop과 같은 경우는, 내부적으로 공간 재할당(list_resize)을 필요로 합니다. 이는 n의 값이 커짐에 따라 연산량이 부담되게 됩니다.

효율성을 생각하며 짜기 시작하더라도, 정답을 제출하면 통과하지 못하는 테스트 케이스가 있습니다.

### 3. 반례를 찾을 수 있는지!

아마 가장 핵심적인 역량이지 않을까 생각이 됩니다. 저는 분명 예외 케이스를 다 잡았다고 생각하는데, 테스트 케이스에서는 반례를 찾으라고 합니다. 엄청난 센스로 예외가 딱 보이면 좋겠지만, 현재로서는 다음 단계로 확인하는 편입니다.

**경계값** 이 되는 부분부터 빠르게 확인하고, **정렬** 을 한 부분, 자료형 중에 **딕셔너리** 를 사용한 부분, **인덱스** 와 관련된 부분 순으로 확인합니다.

### 4. 이해하기 쉬운 코드를 구현했는지!

제가 짠 코드를 누군가에게 설명하면서, **정말 필요한 역량** 이라는 것을 체감했습니다. 파이썬다운 코드를 짜고 싶어서 학교 도서관에서 파이썬 클린코드 라는 책을 빌렸지만, 생각보다 진도가 나가지 않습니다. 같이 스터디 할 사람을 구해야 할 것 같습니다.ㅠㅜ;

### 5. 유형별 노트 만들기!

분명 필요한 단계지만, 코딩을 시험처럼 대하게 되어 아직 거부감이 드는 것 같습니다. 하지만, 빨리 풀어야 하는데 어쩔 수 있을까 싶습니다. 최근에 많은 시간을 투자했던 Union Find 알고리즘을 정리하며, 블로깅을 마칩니다.

바로 들어가기 전에, 먼저 간단하게 설명을 하면, Union Find 알고리즘은 Disjointset을 찾는 알고리즘입니다. 즉, 그룹을 나누되, 특정 원소가 A그룹에도 있고, B그룹에도 있는 상황이 **_발생하지 않게_** 그룹을 나누는 것입니다.

먼저, parent와 rank라고 하는 딕셔너리를 만듭니다.

- **parent** : 해당 노드의 부모노드를 value로 가집니다. ex) { 0:0, 1:0, 2:0}

- **rank** : 해당 노드에 연결된 노드의 갯수를 value로 가집니다. ex) { 0:2, 1:0, 2:0}

일단 다음과 같이 초기화 합니다.

```python
parent = {}
rank = {}
# Make set
for i in range(n) :
    parent[i] = i
    rank[i] = 0
```

그리고 다음 2 function을 사용합니다.

- **Find** : 첫 번째 인자의 부모 노드가 누구인지 반환해주는 함수

- **Union** : 첫 번째, 두 번째 노드를 연결하고, parent, rank를 업데이트해주는 함수

```python
def Find(v, parent) :
    if parent[v] != v :
        parent[v] = Find(parent[v], parent)
    return parent[v]

def Union(x, y, parent, rank) :
    if rank[x] > rank[y] :
        parent[y] = x
    elif rank[x] < rank[y] :
        parent[x] = y
    else :
        parent[y] = x
        rank[x] += 1
    return parent, rank
```

# Union Find 사용시 주의사항

프로그래머스 고득점 kit <a href="https://programmers.co.kr/learn/courses/30/lessons/42861">Greedy 5번</a> 을 풀다가, <a href='https://programmers.co.kr/learn/courses/30/lessons/43162'>DFS 2번 </a>으로 가서 복습한 내용입니다.

### [ Greedy 5번 접근 ]

위의 그리디 문제는 최소 신장 트리를 구하는 문제입니다.

Kruscal이나 Prim 알고리즘을 써야한다는 것은 알지만, 왠지 Union Find말고 그냥 풀고 싶어서 Kruscal을 위키백과로 찾아봤습니다.

![image](https://user-images.githubusercontent.com/42775225/92196269-d80f8180-eea9-11ea-818a-777f228eb5d7.png)

```python
def solution(n, costs):
    tree = set()
    costs = sorted(costs, key=lambda x:x[2])
    total = 0
    for cost in costs:
        if cost[0] in tree and cost[1] in tree:
            continue
        tree.add(cost[0])
        tree.add(cost[1])
        total += cost[2]
    return total
```

![image](https://user-images.githubusercontent.com/42775225/92196360-14db7880-eeaa-11ea-89aa-077219bf99c2.png)

어디서 문제가 발생했는지 확인했습니다. 입력이 다음과 같다면, 그래프가 각각 생겨서 이어질 때는 이미 tree라는 set 안에 A, B, C, D, E가 모두 들어가 있는 상황입니다.

```python
costs = [[0,1,1],[0,2,2],[1,3,4],[3,4,3],[1,2,5],[2,4,6]]
```

![image](https://user-images.githubusercontent.com/42775225/92197734-9254b800-eead-11ea-8012-4f3e8fc59c6c.png)

더 고민해보면 좋은 돌파구가 있을 것 같지만, 결국, 부모노드 정보를 가지고 set을 구분하는 Union Find 로 넘어갔습니다.

### [ DFS 2번 복습 ]

Union Find로 문제를 해결하는 방법은 <a href="https://programmers.co.kr/learn/courses/30/lessons/43162">프로그래머스 고득점 kit DFS 2번 네트워크</a> 문제와 유사합니다. 뜻 밖의 수확이 있었습니다. adjacency matrix 로 노드를 방문하는 경우, 반례를 하나 찾았습니다.

```python
def Find(v, parent) :
    if parent[v] != v :
        parent[v] = Find(parent[v], parent)
    return parent[v]
def Union(x, y, parent, rank) :
    if rank[x] > rank[y] :
        parent[y] = x
    elif rank[x] < rank[y] :
        parent[x] = y
    else :
        parent[y] = x
        rank[x] += 1
    return parent, rank

# settings
parent = {}
rank = {}

for i in range(n) :
  parent[i] = i
  rank[i] = 0
```

위의 Union Find 코드를 가지고 다음의 입력을 넣었을 때, 문제가 발생합니다.

```python
computers = [[1,0,1,0,1],[0,1,0,1,0],[1,0,1,0,0],[0,1,0,1,1],[1,0,0,1,1]]
```

![image](https://user-images.githubusercontent.com/42775225/92199199-6b988080-eeb1-11ea-9509-d41af4d9d414.png)

이 케이스 역시, 그래프가 각각 생기고 뭉치는 과정에서 발생합니다. 위의 경우, A, E, C가 한 그룹, D, B가 한 그룹으로 묶이게 됩니다. 그리고 마지막으로, D와 E가 묶이게 됩니다. 마지막 상황에서의 parent와 rank의 상황을 봅시다.

```python
parent = {0: 0, 1: 1, 2: 0, 3: 1, 4: 0}
rank = {0: 1, 1: 1, 2: 0, 3: 0, 4: 0}
```

D의 rank와 E의 rank 모두 0이기 때문에, Union함수의 마지막으로 이동하고, 두번째 세트 중에, D만이 parent를 A로 가집니다. (B가 업데이트 되는 부분 없음.)

![image](https://user-images.githubusercontent.com/42775225/92200595-460d7600-eeb5-11ea-98ef-a9403ca7675d.png)

그래서 B를 업데이트 해주기 위해서 다음의 코드가 추가적으로 필요하게 됩니다.

```python
for i in range(n) :
  root = Find(i, parent)
  parent[i] = root
```

[ DFS 2번 전체 코드 ]

```python
def Find(v, parent) :
    if parent[v] != v : parent[v] = Find(parent[v], parent)
    return parent[v]
def Union(x, y, parent, rank) :
    if rank[x] > rank[y] :
        parent[y] = x
    elif rank[x] < rank[y] :
        parent[x] = y
    else :
        parent[y] = x
        rank[x] += 1
    return parent, rank
def solution(n, computers):
    parent = {}
    rank = {}
    for i in range(n) :
        parent[i] = i
        rank[i] = 0

    for i in range(len(computers)) :
        for edge in range(len(computers[i])) :
            if edge == i :
                continue
            if computers[i][edge] == 1 :
                v1 = i
                v2 = edge
                x = Find(v1, parent)
                y = Find(v2, parent)
                if x != y :
                    parent, rank = Union(x, y, parent, rank)

    for i in range(n) :
        root = Find(i, parent)
        parent[i] = root

    value = []
    value = list(parent.values())
    value_set = len(set(value))
    return value_set
```

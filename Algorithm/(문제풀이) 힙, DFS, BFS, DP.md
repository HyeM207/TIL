# [1] 힙 (Heap)
## 📌 문제 1. 더 맵게
### ✅ (1) 코드
▶ 내 풀이 모음(Github) : [[Prg] 더 맵게](https://github.com/HyeM207/Algorithm/blob/main/Heap/%5BPrg%5D%20%EB%8D%94%20%EB%A7%B5%EA%B2%8C.py)

#### __v1: 강의 보고 혼자 코딩__  
```python
import heapq

def solution(scoville, k):
    answer = 0
    heapq.heapify(scoville)
    while len(scoville) > 0 : 
        if len(scoville) == 1 and scoville[0] < k : 
            return -1 
        if scoville[0] >= k :
            return answer 
        x1 = heapq.heappop(scoville)
        x2 = heapq.heappop(scoville)
        heapq.heappush(scoville, x1 + (x2*2))
        answer += 1
    return answer
```


#### __v2: 강의 모범답안__  
```python
import heapq

def solution(scoville, K):
    answer = 0
    heapq.heapify(scoville)
    while True: 
        mini = heapq.heappop(scoville)
        if mini >= K : 
            break
        elif len(scoville) == 0 :
            answer = -1 
            break
        min2 = heapq.heappop(scoville)
        new_scoville = mini + 2 * min2
        heapq.heappush(scoville, new_scoville)
        answer += 1

    return answer
```
-  heap을 사용하라는 팁을 얻어, 파이썬 라이브러리 heapq를 이용하여 간단하게 풀이하였다. 
- 로직은 맛을 계산하는 수식을 이용하여 순서대로 짜면 된다. 

<br>

### ✅ (2) 풀이법 및 복잡도 분석 
위의 풀이대로면 최악의 경우 n회 반복문 돌며, heapq.heappop()이 O(logN)복잡도를 가지니, 전체 복잡도는 `O(NlogN)` 이다.   
만약 heap을 사용하지 않고 반복문을 통해 맛 맵기 계산을하면 복잡도가 `O(N^2)`이므로, heap 사용하는 것을 익혀두자

<br>

### ✅ (3) 새로 알게된 점
> 힙의 계산 복잡도

- 힙 구성 (heapify) : O(NlogN)
- 힙 삽입 (insert) :  O(logN)
- 힙 삭제 (remove) :  O(logN)


<br><br>

# [2] 깊이/너비 우선 탐색 (DFS/BFS)
## 📌 개념 (DFS/ BFS)
### #️⃣ 깊이 우선 탐색 (DFS; Depth-First Search)
한 정점에서 __인접한 방문하지 않은 모든 정점을 방문__ 하되,   
각 인접 정점을 기준으로 __`깊이 우선 탐색`__ 을 끝낸 뒤 다음 정점으로 진행함 
- __스택__ 을 이용하여 어느 정점에서 DFS하고 있는지를 기억함

### #️⃣ 넓이 우선 탐색 (BFS; Breadth-First Search)
한 정점에서 __인접한 방문하지 않은 모든 정점을 방문__ 하되,   
__방문한 각 인접 정점__ 을 기준으로 __방문한 순서에 따라 다시 너비 우선 탐색__ 을 진행함


## 📌 문제 2. 여행경로
### ✅ (1) 코드
> (강의 설명) 해당 문제는 '재귀적인 성질을 가진 한 붓 그리기 문제' 로 재귀 성질을 가진 DFS를 응용하여 해결할 수있다.

▶ 내 풀이 모음(Github) : [[Prg] 여행경로](https://github.com/HyeM207/Algorithm/blob/main/Graph/%5BPrg%5D%20%EC%97%AC%ED%96%89%EA%B2%BD%EB%A1%9C.py)

#### __v1: 강의 보고 혼자 코딩__  
```python
from collections import defaultdict
def solution(tickets):
    route = []
    graph = defaultdict(list)
    
    # 여행 경로 리스트를 value로 하는 딕셔너리화 작업 & 역순 정렬
    for t in sorted(tickets, reverse=True):
        graph[t[0]].append(t[1])

    stack = ['ICN']

    while stack : 
        # 마지막 원소를 기준으로 다음 방문지가 있으면 while문으로 반복 
        while graph[stack[-1]] : 
            destination = graph[stack[-1]].pop()
            stack.append(destination)
        # 없으면 나와서 여행 경로에 추가함
        route.append(stack.pop())
     
    return route[::-1] # 역순으로 return 
```
1.  `DFS` 탐색하기 위해, 문제에서 주어진 항공편을 defauldict으로 리스트 원소를 가진 자료형으로 변환한다.
    - 이때 알파벳 순으로 방문해야되는 것을 고려하여 pop하도록 __역순으로 sort__ 해준다.
2. dfs 탐색을 할때는 `stack`을 이용하여 graph를 탐색하는데, __stack에 마지막 원소를 기준으로 다음 방문지를 정하고 방문할 것이니 stack에 append__ 한다.
    - 이때 만약 stack의 마지막 원소와 연결된 다음 목적지가 없다면(`while graph[stack[-1]]`) 해당 장소를 pop()하여 route에 append해준다.
<br>

#### __v2: 강의 모범답안__  
```python
def solution(tickets):
    graph = {}
    
    # 여행 경로 리스트를 value로 하는 딕셔너리화 작업
    for t in tickets:
        graph[t[0]] = graph.get(t[0], []) + [t[1]]
    # 그래프 정렬
    for g in graph:
        graph[g].sort(reverse=True)
    
    stack = ["ICN"]
    route = []
    while stack : 
        top = stack[-1]
        if top not in graph or len(graph[top]) == 0 : 
            route.append(stack.pop())
        else : 
            stack.append(graph[top][-1])
            graph[top] = graph[top][:-1]

    return route[::-1]
```
- v1와 알고리즘은 유사하여 알고리즘 설명은 생략함
- v1과 차이점은 v1은 defaultdict을 활용한점, v2는 딕셔너리를 활용하였다는 점이다. 
    - defaultdict을 이용하면 `graph[top] = graph[top][:-1]` 코드 대신 pop()이 가능하여 __코드를 간단히__ 할 수 있다. 
<br>

### ✅ (2) 복잡도 분석 
stack에 들어가고 나오는게 표 개수에 비례함  
하지만 위에서 graph를 정렬`O(NLogN)`했기에 계산은 상수시간이 걸리고, 전체적으로 while문의 복잡도는 `O(N)`이 되게 된다.

<br>

### ✅ (3) 새로 알게된 점
> 재귀적인 성질을 가진 한 붓 그리기 문제'는 재귀 성질을 가진 DFS로 풀 수 있다. 


<br><br>

# [3] 동적계획법 (Dynamic Programming)
## 📌 개념 (Dynamic Programming)
주어진 __'최적화 문제'__ 를     
-> 재귀적인 방식이 아닌 `작은 부분 문제`로 나누어  
-> __부분 문제를 풀고 이 해를 조합하여__   
-> 전체 문제 해답에 도달하는 방식

- 탐색해야할 범위를 `동적`으로 결정하여 탐색 범위를 한정함
- 예시로 피보나치 수열을 DP로 풀 수 있다. 
    - 재귀함수로 풀면 똑같은 과정을 반복한다는 특징이 있다 
    - 이를 DP로 풀면 부분문제로 접근하여, 똑같은 계산 없이 부분 문제의 답을 활용하여 복잡도를 `선형 함수 형태`로 줄일 수 있다.
- 다른 예시로는 DP문제로 유명한 'Knapsack Problem'문제로, 가장 높은 값을 가지도록 물건을 골라 배낭에 담는 문제이다.  

<br>

## 📌 문제 3. N으로 표현
### ✅ (1) 코드
> (강의 설명) N을 x번 사용해서 만들 수 있는 수들을 이용하여 수식을 만듦  
    >>  ex. (N=5)   
    >> 1 =>  5  
    >> 2 =>  55 와 (1로 만든 수) +-/* (1로 만든 수)  
    >> 3 => 555와  
    >> (1로 만든 수) +-/* (2로 만든 수)  
    >> (12로 만든 수) +-/* (1로 만든 수)
    >> n => (1~ n-1로 만든 수)  +-/* (n-1 ~ 1로 만든 수)



▶ 내 풀이 모음(Github) : [[Prg] N으로 표현](https://github.com/HyeM207/Algorithm/blob/main/Dynamic%20Programming/%5BPrg%5D%20N%EC%9C%BC%EB%A1%9C%20%ED%91%9C%ED%98%84.py)

#### __v1: 강의 보고 혼자 코딩__  
```python
def solution(N, number):
    s= [ set() for _ in range(10)] #  인덱스1부터 값을 담음
    x = 1 # N을 사용하는 횟수
    
    while x < 9 :  
        tmp = int(str(N) * x)
        if tmp == number : 
               return x 
        s[x].add(tmp)
        
        for i in range(1, x) : # 조합의 개수 
            # (x로 만든 수) +-/* (x-i로 만든 수) 계산
            # print(f"s[{i}] : {s[i]} s[{x-i}] : {s[x-i]}")
            for p1 in s[i]: 
                for p2 in s[x-i] :
                    if p1 + p2 == number : 
                        return x
                    s[x].add(p1 + p2)
                    if p1 - p2 == number : 
                        return x
                    s[x].add(p1 - p2)
                    if p1 * p2 == number : 
                        return x
                    s[x].add(p1 * p2)
                    if p1!= 0 and p2 != 0 : # zerodivision 에러 방지
                        if p1// p2 == number : 
                            return x
                        s[x].add(p1 // p2)
        x += 1   
    
    return -1
```
- 강의 보고 `DP`문제이고 __N을 x번 사용해서 만들 수 있는 수들을 이용하여 수식을 만드는 것__ 이 포인트라는 것을 알게되었다.  
또한  __n => (1~ n-1로 만든 수)  +-/* (n-1 ~ 1로 만든 수)__  인것을 활용하여 코드를 짰다.
- 상세 설명 : 
    1. while문을 돌며 N을 사용하는 횟수를 관리한다.
    2. for i in range(1, x) 를 이용하여 조합을 만든다. 
            - 이때 이중 for문을 돌며 (x로 만든 수) +-/* (x-i로 만든 수) 계산한다.

<br>

#### __v2: 강의 모범답안__  
```python 
def solution(N, number):
    s = [ set() for _ in range(8)] 

    # 미리 한 자리수를 만들어 추가함
    for i, x in enumerate(s, start=1) : 
        x.add(int(str(N) * i))

    for i in range(1, len(s)):
        for j in range(i) : 
            for op1 in s[j] : 
                for op2 in s[i-j-1] : 
                    s[i].add(op1 + op2)
                    s[i].add(op1 - op2)
                    s[i].add(op1 * op2)
                    if op2 != 0 :
                        s[i].add(op1 // op2)
        if number in s[i] :
            answer = i + 1
            break
    else :
        answer = -1
    
    return answer  
```
- 내가 직접 짠 v1과 비교하면 로직은 동일하지만 코드가 더 직관적이고 깔끔하다.   
또한 나는 연산 후 해당 값이 number와 동일한지 바로바로 비교해주었는데, 위의 코드는 계산 후  in을 사용하여 number가 있는지 확인하는 부분이 다르다. 

<br>

### ✅ (2) 새로 알게된 점
> DP란 주어진 __'최적화 문제'__ 를     
-> 재귀적인 방식이 아닌 `작은 부분 문제`로 나누어  
-> __부분 문제를 풀고 이 해를 조합하여__   
-> 전체 문제 해답에 도달하는 방식이다
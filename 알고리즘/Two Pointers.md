## 1. Two Pointers란?
  - 두 개의 포인터를 사용하는 것

## 2. 간단한 문제
  ```
  [6, 3, 2, 4, 9, 1] 와 같이 숫자들이 주어졌을 때, 
  특정 구간을 잘 골라 구간 내 숫자의 합이 10을 넘지 않으면서
  가장 큰 구간의 크기를 구하는 프로그램을 작성하시오.
  ```
### 나이브한 풀이방법
  - 나이브한 풀이 방법은 주어진 배열의 길이가 n이라고 할 때 i = 0 부터 i = n-1 까지 한칸씩 늘리고 값을 더하면서 구간 내 숫자의 합이 10이 되면 멈추고 가장 큰 구간인지 현재까지 가지고 있는 큰 구간의 길이와 비교와 비교하면 된다
### 나이브한 풀이방법 시각화
  - <img width="200" alt="image" src="https://user-images.githubusercontent.com/61530368/185727172-c7dd83db-76f7-46e8-9382-1eaf4e06ce4c.png">
  - <img width="200" alt="image" src="https://user-images.githubusercontent.com/61530368/185727204-a3a74816-ec25-496e-8fd7-093385455cb6.png">
  - 배열 i = 0부터 i = n-1까지 하나하나 늘려가면서 늘릴 때 마다 배열의 합을 구한다. 이때, 구간의 합이 s가 되면 초록색, 파란색 배열을 늘리는 것을 그만한다.
  - 시간복잡도는 나이브하게 구해보면 각 인덱스에서 길이를 늘려야하기때문에 n이 걸리고 늘릴 때 마다 구간의 합을 구해야하기 때문에 n이 걸려 총 O(n^2)이 걸린다. 
### 나이브한 풀이방법 소스코드
  ``` python
  n, s = map(int, input().split())
  a = list(map(int, input().split()))
  ans = 1e9
  for i in range(n):
      total = 0
      c = 0
      for j in range(i, n):
          total += a[j]       
          c += 1      
          if total >= s:
              ans = min(c, ans)
              break
  ```
  - 위는 나이브한 풀이의 소스코드이다.
## 위 방법에서 개선시킬 방법은 없는가? 
### 그림으로 개선시킬 방법 찾기
  - <img width="400" alt="image" src="https://user-images.githubusercontent.com/61530368/185727506-6dfd2966-d690-4c18-8e97-b498a7310be1.png">
  - 위 그림을 보면 알 수 있듯이 초록색 부분에서 구했던 값들을 파란색 배열에서도 똑같이 합을 구하는 것을 볼 수 있다.
  - 이부분을 없애면 조금 더 빠르게 이 문제를 해결할 수 있지 않을까?
### 소스코드로 개선시킬 방법 찾기
  - 앞서 적은 파이썬 코드를 다시 보겠다.
    ``` python
    n, s = map(int, input().split())
    a = list(map(int, input().split()))
    ans = 1e9
    for i in range(n):
        total = 0
        c = 0
        ############################
        #for j in range(i, n):     #
        #    total += a[j]         #
        #    c += 1                #
        #    if total >= s:        #
        #        ans = min(c, ans) #
        #        break             #
        ############################
    ```
    - 위 코드에서 네모박스 쳐진 부분이 부분의 합을 구하는 코드인데 이부분을 중복되는 합을 없애는 식으로 개선시켜주면 좀 더 빠른 코드를 만들 수 있을 것 같다.
### 방법 생각하기
  - 그럼 어떻게 더 빠르게 찾을 수 있을까?
  - <img width="621" alt="image" src="https://user-images.githubusercontent.com/61530368/185727841-d2bc4494-92e5-4cb6-915e-8b2bb3bef5ea.png">
  - 위 그림에 step1번인 상황에서 우리는 이미 초록색 부분의 구간 배열의 합이 10을 넘은 것을 알고있다고 가정하자.
  - 이때 우리는 다음 step2로 넘어갈 때 이미 [6, 3, 2] 의 합이 11이라는 걸 알고있고 step2에서 화살표 1번이 오른쪽으로 한칸 올 때 [3, 2]의 구간합을 구하기위해 앞서 [6, 3, 2]의 합에서 6을 빼주면 3, 2의 합을 바로 구할 수 있다.
  - 하지만 [3, 2]의 합이 10을 넘지않기 때문에 화살표 2를 오른쪽으로 한칸 늘려줄 필요가 있다. 
  - 그렇게 step3, step4까지 오게되면 [3, 2, 4, 9]의 합은 18이 되고 이 때 10을 넘게 된다. 
  - 이렇게 두 개의 포인터를 이용하면 중복되는 구간합을 구하는 코드를 개선시킬 수 있다. 
### 슈도코드 
  - 앞으로 화살표 1번을 L포인터, 화살표 2번을 R포인터라고하겠다.
  ``` python
  ans = 1e9
  R = -1 # R포인터의 초기 위치 
  cnt = 0 # 현재 구간의 합 
  for L in range(n):
    while R 포인터가 이동할 수 있고(즉, 배열을 넘어가지않고) and 이동해야 할 때:
      # R포인터 이동 
      # R포인터가 이동했으니 현재 구간 합도 갱신 
    
    if 현재 구간합이 조건에 만족 될 때:
      # 현재 구간의 길이를 구한다 
    
    L을 오른쪽으로 한칸 이동시켜주니까 현재 구간의 합에서 현재 L포인트의 위치에 배열값을 빼주기 
   
  ```
### 소스코드
  ``` python
  and = 1e9
  R = -1 # R포인터 위치 초기화
  cnt = 0 # 현재 구간의 합
  
  for L in range(n):
    while R + 1 < n and cnt < s: # R포인터가 이동할 수 있고, 이동해야 할 때
      R += 1 # R포인터 이동
      cnt += a[R] # R포인터를 이동했으니 구간의 합 갱신
  
  if cnt >= s: # 현재 구간합이 조건에 만족 될 때
    ans = min(ans, R - L + 1) # 현재 구간의 길이를 구한다
  
  cnt -= a[L] # 다음 for문에서 L 포인터를 한칸 오른쪽으로 이동시켜주니까 현재 구간의 합에서 현재 위치의 L포인터 배열 값을 빼주기     
  ```
### Two Pointers를 사용한 시간복잡도 분석
  - 시간복잡도는 O(n)이다 
  - 왜 For문안에 while문이 돌아가는데 O(n)일까?
  - 그것은 바로 L포인터를 움직이는 for문과 R포인터를 움직이는 while문이 따로따로 n번씩 돌아가기 때문이다 
  - 앞서 설명한 그림을 다시 생각해보면 L포인터와 R포인터는 n개의 배열에서 움직이는 횟수는 각각 n번 n번이다. 때문에 총 n + n인데 이걸 빅오표기법으로 나타내면 O(n)이다 
  - 때문에 시간복잡도는 O(n)이다.
  
  
  
  
  
  

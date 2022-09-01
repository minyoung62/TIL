## LIS란?
  - 가장 긴 증가 부분 수열이다 

## 간단한 문제
  ```
  [10, 30, 25, 40, 28, 45]
  가장 긴 증가 부분 수열의 길이를 구하라
  ```
### 나이브한 풀이 
  - 배열의 길이가 6이고 배열의 첫 번째 인덱스 0부터 마지막 인덱스 5까지 한 칸씩 잡고 잡은 인덱스에서 왼쪽으로 늘리면서 증가하는 부분 수열을 확인한다.
  - 배열의 길이가 n일 때 n개의 인덱스를 잡는 O(n)
  - 그리고 왼쪽으로 늘리는 경우 O(n)
  - 증가하는 부분 수열인지 확인하는 경우 O(n)이다.
  - 때문에, 총 O(n^3)의 시간복잡도가 나온다.
### 개선 방법
  - dp테이블을 활용하여 현재 위치를 가장 긴 증가하는 부분 수열의 증가 횟수로 생각하면 증가하는 부분 수열인지 확인하는 경우가 걸리는 O(n)을 O(1)로 줄일 수 있다. 

### 개선된 코드(dp활용)
  ``` python
  n = int(input())

  a = list(map(int, input().split()))

  #dp[i]:= i번째 위치는 가장 긴 증가하는 부분 수열의 증가 횟수 
  dp = [0] * n
  
  # dp table init(자기자신은 증가 횟수가 1로 초기화)
  for i in range(n):
    dp[i] = 1

  # dp table 채우기
  for i in range(1, n):
      for j in range(i):
          if a[i] > a[j]:
              dp[i] = max(dp[i], dp[j] + 1)

  print(max(dp))
  ```
  - 총 시간복잡도 O(n^2)

### 더더더 개선 
  - dp 테이블을 이용하여 O(n^2)까지 만들었지만 추가로 더 개선할 수 있다.  
  ``` python
  for j in range(i):
    if a[i] > a[j]:
      dp[i] = max(dp[i], dp[j] + 1)
  ```
  - 위 코드는 dp를 활용한 코드에서 발취한 코드이다.
  - 이 코드는 현재 위치에서 이전 위치의 증가하는 부분수열인지 판단하고 그게 맞다면 dp테이블을 갱신시켜주는 코드이다. 
  - 그런데 이 부분에서 굳이 또 봤던 것을 계속 볼 필요가 있을까? 
  - 가령 a배열에 [4, 3, 2, 1]이 있고 현재 i의 인덱스가 2라면 다시 4를 볼 필요가 있을까? 
  - 이 부분을 빠르게 해결하기 위해 증가하는 부분수열을 담고있는 dp테이블을 만들고 그 테이블은 오름차순 정렬이 되어있으므로 이분탐색으로 O(LogN)에 탐색할 수 있다.
### 더더더 개선된 코드
  ```
  n = int(input())
  a = list(map(int, input().split()))

  dp = [a[0]]
  tmp = []

  def bisectLeft(target):
    l = 0
    r = len(dp) - 1
    idx = len(dp)
    while l <= r:
      mid = (l + r) // 2

      if dp[mid] >= target:
        idx = mid
        r = mid - 1
      else:
        l = mid + 1

    return idx



  for i in range(1, n):
    idx = bisectLeft(a[i])

    if idx == len(dp):
      dp.append(a[i])
    else:
      dp[idx] = a[i]

  print(len(set(dp)))
  ```
  - 출력값에 set을 한 이유는 [1, 2, 1, 2]의 문제가 들어왔을 때 dp테이블에 [1, 1, 2]가 들어가기 때문이다 때문에 중복되는 것은 제거해주기 위해 마지막에 set을 진행했다.(물론 앞에서 따로 처리는 할 수 있다...)

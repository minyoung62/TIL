## 다익스트라 알고리즘
### 다익스트라 알고리즘이란?
  - 특정 시작점에서 다른 모든 정점으로 가는 최단거를 각각 구해주는 알고리즘
  - 즉, 5개의 정점이 있고 1번 정점에서 출발한다고 가정하면, 1번에서 2~5번으로 가는 최단거리를 구해주는 것
### 아이디어
  - 다른 지점까지의 거리는 모르지만, A라는 지점까지 가는 최단거리는 확실히 안다고 가정했을 때 A를 거쳐 다른 지점을 갈 수 있다면 아래와 같이 표현 가능
  - 현재 위치에서 다른 위치까지 가는 최소 거리를 먼저 선택하기위해 배열을 사용해도 되지만 heapq를 사용하면 logN의 시간복잡도에 최소 거리를 구할 수 있다
  ```
  특정 지점까지 거리 = A까지 가는 거리 + A에서 특정 지점까지 소요되는 거리
  ```
### 다익스트라 특징
  - 가중치가 무조건 양수
    - 가중치가 음수이면 위의 아이디어로 가중치를 개산했을 때 확정한 최단거리가 음수로 가중치로 갱신될 수 있기 때문이다. 
### 다익스트라 코드
  ``` python
  import sys
  import heapq

  n, m = map(int, input().split())
  K = int(input())
  con = [[] for _ in range(n + 1)] # 인접리스트; con[x] := x에서 나가는 간선의 정보들
  for _ in range(m):
      u, v, cost = map(int, input().split())
      con[u].append((v, cost))
      con[v].append((u, cost))

  # 다익스트라
  # dist[i] := 시작점에서 i번 정점까지 알려진 최단 거리
  dist = [1e9 for _ in range(n + 1)]
  dist[K] = 0
  pq = [(0, K)]

  while pq:

      # 시작점에서 min_index 정점까지 min_dist만에 올 수 있다
      min_dist, min_index = heapq.heappop(pq)

      # 주변 간선을 확인할 가치가 없으면 
      if min_dist > dist[min_dist]:
          continue

      for target_index, target_cost in con[min_index]:
          # min_index ----(target_cost)----> target_index
          new_dist = min_dist + target_cost

          # 더 좋은 거리가 있으면 
          if new_dist < dist[target_index]: 
              dist[target_index] = new_dist
              heapq.heappush(pq, (new_dist, target_index))

  for i in range(1, n + 1):
      if dist[i] == 1e9
          print(-1)
      else:
          print(dist[i])

  ```
  ```
  5 6
  1
  5 1 1
  1 2 2
  1 3 3
  2 3 4
  2 4 5
  3 4 6
  ```
  - 예제 입력 데이터

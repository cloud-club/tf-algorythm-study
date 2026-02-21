# 11 주차

---

## 문제 링크 🔗

- 문제 1: [[PCCP 기출문제] 2번 / 석유 시추](https://school.programmers.co.kr/learn/courses/30/lessons/250136)

---

## 문제 요약

- 이차원 배열에 석유가 있는 곳에 1의 값이 들어있다.
- 석유 시추를 수직으로 진행했을 때(즉, 한 열로 쭉 들어갔을 때) 가장 많은 석유를 시추할 수 있는 경우를 구한다.
- 나는 이차원 배열을 순회하며 최초 석유를 만난 지점에서 BFS를 통해 바로 주변에 위치한 석유들의 수를 구했다.
  - result : 각 열에서 시추할 수 있는 석유의 수가 담긴 배열
  - visited : 이미 BFS 처리를 했는 지에 대해 체크하는 배열
  - cols : 해당 덩어리가 걸쳐 있는 열들(중복 제거한 set 배열에 저장)
- BFS 탐색이 모두 완료된 후에는 result에서 최댓값을 골라 출력한다.

---

## 나의 풀이 ✏️

```python
  from collections import deque

  def solution(land):
      n = len(land)
      m = len(land[0])
      
      # 각 열마다 얻을 수 있는 총 석유량을 저장할 배열
      result = [0] * m
      # 방문 여부 체크
      visited = [[0] * m for _ in range(n)]
      
      for i in range(n):
          for j in range(m):
              # 석유가 있고 아직 방문하지 않은 곳이라면 BFS 시작
              if land[i][j] == 1 and not visited[i][j]:
                  # 1. BFS로 석유 덩어리 탐색
                  q = deque([(i, j)])
                  visited[i][j] = True
                  
                  count = 0        # 현재 덩어리의 석유량
                  
                  # 덩어리가 포함된 열들을 중복 없이 저장하기 위한 set
                  cols = set()
                  
                  while q:
                      r, c = q.popleft()
                      count += 1
                      cols.add(c) # 시추관이 통과하는 열 저장
                      
                      for dr, dc in [(0, 1), (0, -1), (1, 0), (-1, 0)]:
                          nr, nc = r + dr, c + dc
                          
                          # 범위 내에 있고, 석유이며, 방문하지 않은 경우
                          if 0 <= nr < n and 0 <= nc < m:
                              if land[nr][nc] == 1 and not visited[nr][nc]:
                                  visited[nr][nc] = True
                                  q.append((nr, nc))
                  
                  # 2. 탐색이 끝난 후, 해당 덩어리가 속한 모든 열에 석유량 합산
                  for col in cols:
                      result[col] += count
      
      # 3. 모든 열 중 가장 많은 석유량 반환
      return max(result)
```

### 접근 방식

- 아이디어: 방문 처리, 열 마다 시추할 수 있는 석유의 수를 저장
- 사용한 자료구조/알고리즘: BFS
- 선택 이유: 큐에 담긴 인덱스 위치에서 주변을 탐색하며 전체를 탐색해야 한다고 생각해서 BFS를 떠올렸다.

### 복잡도 (나의 풀이)

- 시간 복잡도: O(n^2). n과 m의 범위가 각각 최대 500으로 500 * 500 = 250,000 이면 O(n^2)이어도 10초 내에 충분히 코드가 동작할 것이라 판단

### 실행 결과 (나의 풀이)

- 통과 여부: Y
- 실행 시간/메모리: 효율성 테스트 5 〉 통과 (287.90ms, 12.9MB)

### 개선할 점

O(n^2)이 케이스에 따라 비효율 적일 수 있는 경우인 만큼, 좀 더 실행 시간을 낮출 수 있는 알고리즘을 사용해보면 좋겠다. -> 근데 다른 사람들의 코드를 조사해보니 이미 이게 최적의 풀이인 듯

---

## 비교 풀이 ⚠️

```Python
  def solution_dfs(land):
    n, m = len(land), len(land[0])
    visited = [[0]*m for _ in range(n)]
    result = [0]*m

    def dfs(r, c):
        stack = [(r, c)]
        count = 0
        cols = set()

        while stack:
            x, y = stack.pop()
            if visited[x][y]:
                continue
            visited[x][y] = 1

            count += 1
            cols.add(y)

            for dx, dy in [(0,1),(0,-1),(1,0),( -1,0)]:
                nx, ny = x+dx, y+dy
                if 0 <= nx < n and 0 <= ny < m:
                    if land[nx][ny] == 1 and not visited[nx][ny]:
                        stack.append((nx,ny))
        return count, cols

    for i in range(n):
        for j in range(m):
            if land[i][j] == 1 and not visited[i][j]:
                count, cols = dfs(i, j)
                for col in cols:
                    result[col] += count

    return max(result)

```

### 변경 사항 요약

- 변경 내용: DFS 이용. 근데 이 문제는 DFS의 특성을 살릴 수 없는 문제라(경로가 특성을 가지는 경우) 사실상 BFS랑 동일하다
- 개선 효과: 딱히 없음. 파이썬에서는 DFS 구현 시에 스택을 사용하기에 BFS보다 시간이 오래 걸림

### 복잡도 (개선된 풀이)

- 시간 복잡도: O(n^2)

### 실행 결과 (개선된 풀이)

- 통과 여부: Y
- 실행 시간/메모리: 테스트 5 〉 통과 (308.54ms, 29MB)

---

## 결론

- BFS vs DFS 사용 상황 비교
  1. 경로가 특성을 지니는 경우(경로의 숫자들이 겹치면 안된다던가) -> DFS
     1. BFS는 경로의 특성을 가리지 못함
  2. 모든 경로를 방문해야 하는 경우 -> DFS & BFS
  3. 최단 경로 -> BFS
     1. DFS 사용시 최악의 경우 정말 마지막에 답을 찾을 수 있음
     2. BFS는 인접한 곳부터 탐색하기에 최단 경로를 먼저 찾을 확률이 더 높음

---

## 배운 점 🌱

DFS와 BFS의 특성과 사용처 이해하기

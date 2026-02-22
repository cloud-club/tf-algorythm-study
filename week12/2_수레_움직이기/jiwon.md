# 11 주차

---

## 문제 링크 🔗

- 문제 2: [[PCCP 기출문제] 4번 / 수레 움직이기](https://school.programmers.co.kr/learn/courses/30/lessons/250134)

---

## 문제 요약

- 빨간 수레와 파란 수레가 본인들의 도착지점에 가장 빠르게 도착할 수 있는 방법 구하기
  - 불가능한 경우 0 출력
- 빨간 수레와 파란 수레를 동시에 상하좌우 인접한 칸 중 하나로 움직여야 한다.
- 기존에 방문했던 경로는 방문하지 못한다. -> visited 기록 필요
- 자신의 도착지점에 도달한 수레는 움직이지 않는다 -> 장애물로 전환됨
- 수레들의 위치는 동시에 겹칠 수 없음
- 수레들이 서로의 위치를 swap 하는 것도 불가

---

## 나의 풀이 ✏️

```python
  from collections import deque

def solution(maze):

    def bfs(start, end):
        q = deque()
        q.append([start, [start]])
        paths = []
        
        while q:
            now, path = q.popleft()
            if end == now:
                paths.append(path)
                continue
            if len(path) > 16:
                continue
            for move in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                current = [now[0] + move[0], now[1] + move[1]]
                if 0 <= current[0] < n and 0 <= current[1] < m:
                    if current not in walls and current not in path:
                        q.append([current, path+[current]])
        return paths

    n = len(maze)
    m = len(maze[0])
    walls = []

    for i in range(n):
        for j in range(m):
            check = maze[i][j]
            if check == 1:
                red_start = [i, j]
            elif check == 2:
                blue_start = [i, j]
            elif check == 3:
                red_end = [i, j]
            elif check == 4:
                blue_end = [i, j]
            elif check == 5:
                walls.append([i,j])

    answer = 17

    # 빨강과 파랑의 모든 경로를 BFS로 탐색해서 저장해둔다
    red_paths = bfs(red_start, red_end)
    blue_paths = bfs(blue_start, blue_end)

    if not red_paths or not blue_paths:
        return 0

    # 조건들을 비교
    for red_path in red_paths:
        for blue_path in blue_paths:
            if len(red_path) > len(blue_path):
                long_path = red_path
                short_path = blue_path
            else:
                long_path = blue_path
                short_path = red_path

            for lp in range(len(long_path) - 1):
                if lp < len(short_path)-1:
                    sp = lp
                else: 
                    sp = -1

                if long_path[lp] == short_path[sp]:
                    break

                if sp != -1:
                    if long_path[lp + 1] == short_path[sp] and long_path[lp] == short_path[sp+1]:
                        break
            # 정상적으로 for을 모두 통과한 경우에만 answer 갱신
            else:
                answer = min(answer, len(long_path)-1)
    return answer if answer != 17 else 0
```

### 접근 방식

- 아이디어: 빨강, 파랑 수레가 도착지점에 도달하는 모든 경로를 구하고, 같은 시간동안의 경로를 추적하며 조건에 부합하는지 확인한다
- 사용한 자료구조/알고리즘: BFS
- 선택 이유: n과 m의 범위가 작았기에 모든 경우를 구하고 조건을 확인해도 된다고 판단

### 복잡도 (나의 풀이)

- 시간 복잡도: 최대 16개 칸에서 4방향을 탐색하지만, 조건문에서 필터링 하는 부분들이 많아서 실제로는 O(4^16)보다는 훨씬 작게 나온다.

### 실행 결과 (나의 풀이)

- 통과 여부: Y (지정된 시간 안에는 못 풂)
- 실행 시간/메모리: 테스트 9 〉 통과 (21.62ms, 9.23MB)

### 개선할 점

BFS로 갈 수 있는 경로를 다 구해서 비교하는 것이 너무 많은 연산 과정을 거치는 것 같은데, 시간을 기준으로 두 수레의 경로를 탐색할 수 있는 방법을 찾아보면 더욱 성능을 개선할 수 있을 것 같다.

---

## 개선된 풀이 👍

```Python
  from collections import deque

def solution(maze):
    n, m = len(maze), len(maze[0])

    for i in range(n):
        for j in range(m):
            if maze[i][j] == 1: red_start = (i, j)
            elif maze[i][j]  == 2: blue_start = (i, j)
            elif maze[i][j] == 3: red_end = (i, j)
            elif maze[i][j] == 4: blue_end = (i, j)

    q = deque([(red_start, blue_start, {red_start}, {blue_start}, 0)])

    while q:
        r_now, b_now, r_visited, b_visited, cnt = q.popleft()

        if r_now == red_end and b_now == blue_end:
            return cnt

        r_candidates = [r_now] if r_now == red_end else []
        if not r_candidates:
            for dx, dy in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                nr, nc = r_now[0] + dx, r_now[1] + dy
                if 0 <= nr < n and 0 <= nc < m and maze[nr][nc] != 5 and (nr, nc) not in r_visited:
                    r_candidates.append((nr, nc))

        b_candidates = [b_now] if b_now == blue_end else []
        if not b_candidates:
            for dx, dy in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                nr, nc = b_now[0] + dx, b_now[1] + dy
                if 0 <= nr < n and 0 <= nc < m and maze[nr][nc] != 5 and (nr, nc) not in b_visited:
                    b_candidates.append((nr, nc))
                    
        for r_next in r_candidates:
            for b_next in b_candidates:
                if r_next == b_next: continue
                if r_next == b_now and b_next == r_now: continue
                
                new_r_v = r_visited | {r_next}
                new_b_v = b_visited | {b_next}
                
                q.append((r_next, b_next, new_r_v, new_b_v, cnt+1))
    return 0
```

### 변경 사항 요약

- 변경 내용: 모든 bfs를 저장하기 전에 조건 체크를 수행
- 개선 효과: 불필요한 연산을 줄인다.

### 복잡도 (개선된 풀이)

- 시간 복잡도: O(16 * 16 * 4)

### 실행 결과 (개선된 풀이)

- 통과 여부: Y
- 실행 시간/메모리: 테스트 9 〉 통과 (8.40ms, 10.9MB)

---

## 결론

경우의 수가 작아서 모든 경우를 구하고 조건을 확인하는 것도 시간초과가 나지는 않았지만, 순회 과정에서 조건을 확인하는 방법을 구현하면 확실히 훨씬 적은 경우만 연산해도 된다는 것을 알 수 있었다.

---

## 배운 점 🌱

최대한 적은 횟수 순회할 방법을 항상 고려하자
BFS, DFS 사용 경우 구별하기

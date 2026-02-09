# 11 주차

---

## 문제 링크 🔗

- 문제 1: [[PCCP 기출문제] 2번 / 퍼즐 게임 챌린지](https://school.programmers.co.kr/learn/courses/30/lessons/340212)

---

## 문제 요약

1. 숙련도에 따라 퍼즐을 풀이하는 데에 소요되는 시간이 상이하다.
2. limit 시간 내에 퍼즐들을 풀이하기 위해 필요한 숙련도의 최솟값을 구한다.

---

## 나의 풀이 ✏️

```python
  def solution(diffs, times, limit):
    def check(level):
        temp = times[0]
        for i in range(1, len(diffs)):
            if diffs[i] > level:
                cal = diffs[i] - level
                temp += (cal+1) * times[i]
                temp += cal * times[i-1]
            else:
                temp += times[i]
            if temp > limit:
                return False
        return True
    
    l, r = 1, max(diffs)

    while l < r:
        mid = (l + r) // 2
        if check(mid):
            r = mid
        else:
            l = mid + 1

    return l
```

### 접근 방식

- 아이디어: 숙련도를 x로 잡고, 만약 레벨보다 숙련도가 낮을 경우, 그렇지 않을 경우를 잘 분기처리 하는 것이 중요
  - 시간 초과가 발생하므로 숙련도의 최솟값을 찾는 과정에서 이분 탐색을 사용해야 한다.
- 사용한 자료구조/알고리즘: 이분 탐색
- 선택 이유: 시간 초과

### 복잡도 (나의 풀이)

- 시간 복잡도: O(nlongn)
  - 절반을 탐색하고, 그 범위에서 또 절반을 탐색하고... -> O(logn)
  - check(level) -> O(n)

### 실행 결과 (나의 풀이)

- 통과 여부: Y
- 실행 시간/메모리: 테스트 19 〉 통과 (1448.81ms, 33.1MB)

### 개선할 점

좀 더 간결하게 작성할 방법?
사실 다른 풀이를 탐색해봐도 명확하게 잘 짰다고 생각한다.

---

## 개선된 풀이 👍

```Python
  def solution(diffs, times, limit):

    answer, left, right = 1, 1, 100_000

    while left <= right:

        level = (left + right) // 2

        if sum(times[i] if diffs[i] <= level else (diffs[i] - level) * (times[i] + times[i - 1]) + times[i]

               for i, _ in enumerate(diffs)) > limit:

            left = level + 1

        else:

            answer, right = level, level - 1

    return answer
```

### 변경 사항 요약

- 변경 내용: 내 코드의 check 부분을 이분 탐색에 적용시켜서 한 번에 처리하는 방법
- 개선 효과: 좀 더 간결한 코드를 작성할 수 있다.

### 복잡도 (개선된 풀이)

- 시간 복잡도: O(nlogn)
  - 다만, 내 풀이에는 조기 종료 조건이 있기 떄문에 나의 풀이가 특정 조건에서는 더 빠를 수 있다.

### 실행 결과 (개선된 풀이)

- 통과 여부: Y
- 실행 시간/메모리: 테스트 19 〉 통과 (1173.61ms, 33MB)

---

## 결론

조기 종료 조건을 통해 코드의 실행 시간을 최적화 할 수 있다.

---

## 배운 점 🌱

짧고 간결한 코드와 성능은 상관 관계는 있을 수 있지만, 기본적으로 독립 시행이다.
그리고 해당 문제를 풀이하면서 문제 해석에 집중한다면 빠르게 정답 코드를 작성할 수 있음을 알 수 있었다.
실제로 전체 풀이 시간의 2/3을 문제 파악에 소모하고 굉장히 빠른 시간 만에 코드를 완성하는 경험을 했다.

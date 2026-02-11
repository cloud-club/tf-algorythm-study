# 11 주차

## 문제 링크 🔗

- 문제 1: https://school.programmers.co.kr/learn/courses/30/lessons/340212

---

## 문제 요약

n개의 퍼즐 풀어야 하며 숙련도와 퍼즐 난이도 관계에 따라 계산 로직이 다름

난이도 <= level인 경우, times[i]만큼의 시간 소요

난이도 > level인 경우, (times[i-1] + times[i]) * (diffs[i]-level) + times[i] 소요

제한 시간 내 퍼즐을 모두 해결하기 위한 숙련도의 최솟값 구하기

---

## 나의 풀이 ✏️

```python
import sys

input = sys.stdin.readline

def solv(N,diffs,times,limit,level):

    ans = 0
    
    #로직
    for i in range(N):
        
        #1. diff<=level인 경우, dp 업데이트  
        if diffs[i]<=level:
            ans += times[i]
        #2. diff>level인 경우
        #diffs[0]이 항상 1이기 때문에 가능
        else:
            ans += (times[i-1] + times[i]) * (diffs[i]-level) + times[i]
        
        #3. 조기 탈출. for문을 다 돌지 않아도 limit을 초과하는 경우 조기 종료
        if ans>limit:
            return float("inf")
            
    return level
    
#이분탐색
def solution(diffs, times, limit):
    answer = max(diffs)
    
    #최대 숙련도는 diffs
    left = 1
    right = max(diffs)
    N = len(diffs)
    
    while left<=right:
        #mid가 level 의미
        mid = (left+right)//2
        
        ans = solv(N,diffs,times,limit,mid)
        #print("ans",ans)
        #level이 answer보다 작은 경우 최솟값 탐색: 더 작은 값 탐색
        if ans<answer:
            answer=ans
            right = mid-1
        #level이 answer보다 큰 경우 level 높이기
        else:
            left = mid+1
        
        
    return answer
```

### 접근 방식

- 아이디어: 숙련도 결정 후 문제에 주어진 계산 방식으로 제한 시간 내 가능 여부 확인
- 사용한 자료구조/알고리즘: 이분탐색
- 선택 이유: 숙련도를 이분탐색으로 결정해야 시간복잡도 줄일 수 있음

### 복잡도 (나의 풀이)

- 시간 복잡도: N log (max(diffs))
- 공간 복잡도: N

### 실행 결과 (나의 풀이)

- 통과 여부: 통과

### 개선할 점

변수명 및 함수명을 통해 어떤 작업을 할 지 이해할 수 없음

---

## 개선된 풀이 👍

```Python
import sys

def solution(diffs, times, limit):
    # 1. 검증 함수 최적화 (True/False 반환으로 변경)
    def is_possible(level):
        total_time = 0
        for i in range(len(diffs)):
            if diffs[i] <= level:
                total_time += times[i]
            else:
                # 이전 퍼즐 시간(times[i-1])과 현재 시간 활용
                # diffs[0]은 항상 1이므로 i=0일 때는 이 조건에 걸리지 않음
                fail_count = diffs[i] - level
                total_time += fail_count * (times[i] + times[i-1]) + times[i]
            
            # 중간에 limit을 넘으면 즉시 중단 (Early Exit)
            if total_time > limit:
                return False
        return True

    # 2. 이분 탐색 범위 설정
    # 최소 숙련도는 1, 최대는 diffs 중 최댓값
    left = 1
    right = max(diffs)
    answer = right
    
    while left <= right:
        mid = (left + right) // 2
        
        if is_possible(mid):
            # 성공하면 더 낮은 숙련도가 있는지 탐색
            answer = mid
            right = mid - 1
        else:
            # 실패하면 숙련도를 높여야 함
            left = mid + 1
            
    return answer
```

### 변경 사항 요약

- 변경 내용: 함수명 및 변수명 변경, is_possible() 반환값을 int형에서 boolean으로 변경
- 개선 효과: 가독성 증대, 직관적

### 복잡도 (개선된 풀이)

- 시간 복잡도: N log (max(diffs))
- 공간 복잡도: N

### 실행 결과 (개선된 풀이)

- 통과 여부: 통과

---

## 결론

함수와 변수명 작성 시 직관적이고 이해하기 쉽도록 작성할 것

---

## 배운 점 🌱

로직 구현해도 시간복잡도를 줄일 수 있는 방법 끊임없이 생각할 것
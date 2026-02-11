# 11 주차

---

## 문제 링크 🔗

- 문제 2: https://school.programmers.co.kr/learn/courses/30/lessons/340210

---

## 문제 요약

수식을 통해 몇 진법인지 탐색 후 지워진 결과값 채우기

---

## 나의 풀이 ✏️

```Python
#N진법 수를 10진법으로 변환
#n진법의 수 num을 10진법으로 변환
def NToTen(n, num):
    if len(num)==1: return int(num)
 
    number = 0
    for idx in range(len(num)):
        number += int(num[idx])*(n**(len(num)-1-idx))

    return number
 
#10진법 수를 N진법으로 변환
def TenToN(n, num):
    if num==0: return "0"
    answer = ""

    for idx in range(2,-1,-1):
        div = num // (n**idx)
        if answer or div: answer += str(div)
        num = num % (n**idx)

    return answer

def solution(expressions):
    #answer: 답변이 X인 수식. 계산해야 하는 수식
    #answer_format: 가능한 진법 리스트
    answer, answer_format = [], []
    #max_format: 수식에 존재하는 수 중 가장 큰 수(15의 경우, 1과 5로 나누어 max_format 계산)
    #hint: 답변이 X가 아닌 수식 전체 
    max_format, hint = 0, []
    
    #1. expressions 내 수를 통해 가능한 가장 작은 진법 계산
    for e in expressions:
        #expression 내 식 분리
        num1, func, num2, _, ans = e.split(" ")
        
        for idx in range(len(num1)): max_format = max(max_format, int(num1[idx]))
        for idx in range(len(num2)): max_format = max(max_format, int(num2[idx]))
        
        #ans가 X가 아닌 경우, hint 삽입 + max_format 업데이트
        if ans != "X": 
            hint.append(e)
            for idx in range(len(ans)): max_format = max(max_format, int(ans[idx]))
        #ans가 X인 경우, answer에 넣어 추후 계산
        else: answer.append(e)
    
    #2. (max_format+1)진법부터 9진법까지 탐색 
    for n in range(max_format+1, 10):
        #check를 통해 n진법 가능 여부 체크. check=1은 진법으로 계산 가능, check=0은 불가능
        check = 1
        #hint를 통해 진법 계산
        for h in hint:
            num1, func, num2, _, ans = h.split(" ")
            #num1,num2,ans가 n진법으로 되어 있는 상황에서 각각을 10진법으로 변환한 num1,num2,ans를 사용해 계산
            num1, num2, ans = NToTen(n, num1), NToTen(n, num2), NToTen(n, ans)
            #10진법으로 계산한 num1,num2,ans가 func에 따라 수식에 부합하는지 확인
            #수식에 따라 계산할 때 좌항과 우항이 일치하지 않으면 check=0으로 n진법이 될 수 없음-> break로 hint 반복문 탈출
            if (func == '+') and (num1+num2!=ans): 
                check = 0
                break
            if (func == '-') and (num1-num2!=ans): 
                check = 0
                break
        
        #check=1인 경우, hint를 모두 돈 결과로 n진법이 가능함을 의미하여 answer_format에 추가
        if check: 
            answer_format.append(n)
    
    #3. answer는 계산해야 하는 수식. 즉 ans가 X인 수식 계산한 값 구한 후 answer 업데이트
    for idx in range(len(answer)):
        num1, func, num2, _, ans = answer[idx].split(" ")
        #가능한 진법 n 세트
        ans_set = set()
        for a in answer_format:
            #n진법 num_1과 num_2를 10진법으로 변환
            num_1, num_2 = NToTen(a, num1), NToTen(a, num2)
            
            #func에 따라 num_1과 num_2의 계산 결과를 n진법으로 변환해 ans_set에 저장
            if func=="+": ans_set.add(str(TenToN(a, num_1+num_2)))
            if func=="-": ans_set.add(str(TenToN(a, num_1-num_2)))
        
        #ans_set 개수가 1인 경우 ans_set 값 출력. set을 list로 변환해 출력
        #그렇지 않은 경우, "?" 출력
        if len(ans_set)==1:
            answer[idx] = ' '.join([num1, func, num2, _, list(ans_set)[0]])
        else: answer[idx] = ' '.join([num1, func, num2, _, "?"])
            
    return answer
```

### 접근 방식

- 아이디어: 가능한 모든 진법 탐색 후 해당 진법으로 지워진 결과값 채우기
- 사용한 자료구조/알고리즘: 구현
- 선택 이유: 문제에 주어진대로, 진법 계산 방법에 따라 순차적으로 진행해야 함

### 복잡도 (나의 풀이)

- 시간 복잡도: len(expressions) *  100 (수식의 최대 길이 의미)
- 공간 복잡도: len(expressions)

### 실행 결과 (나의 풀이)

- 통과 여부: 통과

## 결론

문제에 주어진대로 차근차근 해결하는 것이 중요
파이썬 함수를 적절히 사용해 짧은 코드로 해결할 것

---

## 배운 점 🌱

문자열 분리 방법, 진법 계산 방법에 대해 알게 됨

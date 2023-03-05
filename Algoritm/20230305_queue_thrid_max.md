:calendar: 2023. 03. 05. Sun.

# :memo:  5. Queue  by. Python
## 정렬된 Queue 이용해서 3번 째로 큰 수 찾기

1. 주어진 배열을 역순으로 정렬한다
2. 가장 큰 수를 차례대로 변수에 저장한다
3. 배열을 순회한다
   1. 해시 테이블(set)을 사용하여 중복된 숫자는 다음으로 넘긴다
   2. 중복된 숫자를 제외하고, set에 담긴 갯수가 3개가 되면, 그 마지막 것을 반환한다
4. 순회 과정에서 중복된 숫자를 제외하고 3번째 큰 숫자를 찾지 못했다면 2번에서 저장한 가장 큰 수를 반환한다.


```python
def thirdMax(nums: []) -> int:
  cnt = 0
  third_max = 0

  # 해시 테이블 : 중복 제거
  check_dup = set()
  nums.sort(reverse=True) # 내림차순 정렬
  third_max = nums[0] # 가장 큰 수를 저장

  for num in nums:
    if num in check_dup:
      continue
  
    check_dup.add(num)

    if cnt == 2: # cnt가 1씩 증가해서 (0, 1, 2) -> 2가 된 경우는 3번째로 큰 수가 됨
      third_max = num
      break
  
    cnt += 1

  return third_max

print(thirdMax([14, 42, 35, 33, 31, 19, 27, 20])) # 33
```
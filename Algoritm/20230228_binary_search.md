:calendar: 2023. 02. 28. Tue.

# :memo:  1. 이진 탐색(binary search) by. Python
## 정렬된 배열에서 삽입된 위치 찾기

* 이진 탐색의 범위는, `low`, `hight` 중 목푯값이 `mid` 값보다 작으면 `high`를 `mid` -1로 업데이트하고, `mid` 값보다 크면 `low` 값을 `mid` + 1로 업데이트하여 실제 검색의 범위를 반으로 줄이는 형식이다.
* 이렇게 계속 찾다 보면 `low` 값이 `high`와 같거나 커지는 경우가 발생하는데, 이런 경우는 범위 내에서 찾고자 하는 요소를 찾지 못하는 경우이다. 이때는 `low` 값을 반환한다.
* 참고로, mid 값을 계산하는 과정은 `(low + high) / 2` 로 진행하는데, 파이썬에서는 해당 숫자가 무수히 커져도 문제가 되지 않지만 C / C++ / Java의 경우 `low`와 `high`를 정수형으로 선연했을 경우 문제가 발생할 수 있다. (오버플로)
* 이때 C / C++ / Java에서 이진 탐색을 순조롭게 하기 위해서는 `mid` 를 `(low + high) / 2` 가 아니라 `low + (high - low) / 2` 로 진행한다.
* python의 경우 정수형을 반환받기 위해 `int` 클래스를 사용하여 반환 값을 정수형으로 변환해 주어야 한다

<br>

```python
# 선형순회
def searchNum(nums: List[int], target: int) -> int:
  index = 0

  # 인덱스가 리스트의 길이보다 작을 때까지 반복한다
  # 찾고자 하는 target이 리스트의 인덱스(최초 0)보다 작거나 같으면 빠져 나온다.
  while index < len(nums):
    if target <= nums[index]:
      break
    
    index += 1
  
  return index
```

```python
# 이진탐색

def searchNum(nums: List[int], target: int) -> int:
  low = 0
  high = len(nums) -1

  while low <= high:
    mid = int((low + high) / 2)

    # 찾고자 하는 타겟이 mid (리스트의 중간값)과 같다면
    if target == nums[mid]:
      return mid
    # 타겟이 mid(리스트의 중간값) 보다 크다면 최솟값에 인덱스 + 1을 해준다
    if target > nums[mid]:
      low = mid + 1
    else:
    # 타겟이 mid(리스트의 중간값) 보다 큰 경우라면 최댓값을 중간값 - 1 해준다
      high = mid - 1
  
  return low

```
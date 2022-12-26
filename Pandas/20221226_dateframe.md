Date: 2022.12.26.Mon.

# :memo: 3. 데이터 프레임과 시리즈

## 3-1. 데이터 프레임 만들기

<br/>

데이터프레임을 만드는 방법은 `Pandas` `DataFrame` 클래스에 만들고자 하는 형태의 딕셔너리를 전달해야 한다. (딕셔너리는 데이터의 순서가 보장되지 않는다)

<br/>

```python
import pandas as pd

students = pd.DataFrame({
  'name': ['Jenny', 'Jemma', 'William', 'Rosalin'],
  'grade': [3, 1, 2, 2],
  'class': ['A', 'B', 'A', 'C'],
  'stNo': ['312', '145', '255', '282'],
})

```

<br/>

* 데이터프레임을 만들 때, 별도로 인덱스를 지정하지 않으면 자동으로 0부터 인덱스를 생성한다. <br/>

* 생성하는 컬럼을 내가 원하는 대로 배치하고 싶다면? 데이터프레임을 생성할 때, 인자로 `columns` 에서 원하는 순서대로 열 이름을 배치해 주면 된다 <br/>

```python

students = pd.DataFrame(
  data = {
    'name': ['Jenny', 'Jemma', 'William', 'Rosalin'],
    'grade': [3, 1, 2, 2],
    'class': ['A', 'B', 'A', 'C'],
    'stNo': ['312', '145', '255', '282'],
  },
  columns = ['stNo', 'name', 'grade', 'class']
  # index = {} 이 부분을 설정하면 인덱스를 0, 1, 2 가 아닌 원하는 값으로 채울 수 있다
)

```

<br/>

위에서도 언급했듯, 딕셔너리 자료형은 데이터의 순서를 보장하지 않는다. 따라서 키(Key)와 값(Value)로 이루어진 순서가 보장된 딕셔너리를 전달하기 위해서는 `OrderedDict`로 딕셔너리를 한 번 감싼 후에 인자로 전달해야 한다. <br/><br/>

`orderedDict`는 `collections` 에서 import 해올 수 있다.

<br/>

```python
from collections import OrderDict
import pandas as pd

students = pd.DataFrame(OrderedDict([
  ('name', ['Jenny', 'Jemma', 'William', 'Rosalin']),
  ('grade', [3, 1, 2, 2]),
  ('class', ['A', 'B', 'A', 'C']),
  ('stNo', ['312', '145', '255', '282']),
]))

```

<br/><br/>

## 3-2. 시리즈 메소드 알아보기

<br/>

시리즈는 데이터프레임의 한 열을 구성하는 기본 단위이다. 데이터프레임에서 특정 시리즈를 추출하기 위해서는 `index`, `keys`, `values` 등 메소드를 사용할 수 있다. <br/><br/>

### 1) keys()로 시리즈 추출
```python

key = students.keys()
print(key)
>>> Index(['name', 'grade', 'class', 'stNo'], dtype='object')
```
<br/>

`key()` 메소드를 이용하면 현재 데이터프레임의 모든 열들을 리스트 형태로 받을 수 있다. <br/>

### 2) 열 이름으로 시리즈 추출
```python

name = students['name'] # keys()로 원하는 열의 이름을 얻은 후, 열 이름을 얻어 뽑을 수 있음 
```

<br/>

### 3) 기타 시리즈 메소드
1. `append` : 두 개 이상의 시리즈 연결
2. `drop_duplicates` : 중복값이 없는 시리즈 반환
3. `equals` : 시리즈에 인자로 보낸 값이 있는지 확인
4. `isin` : 시리즈의 값  출력
5. `values` : 시리즈의 값 출력
6. `max` : 시리즈를 구성하는 데이터가 정수일 경우 최댓값
7. `min` : 시리즈를 구성하는 데이터가 정수일 경우 최솟값
8. `mean` : 시리즈를 구성하는 데이터가 정수일 경우 평균값
9. `median` : 시리즈를 구성하는 데이터가 정수일 경우 중간값
10. `replace` : 특정 값을 가진 시리즈를 교체
11. `sort_values` : 시리즈의 값을 정렬
12. `to_frame` : 시리즈를 데이터프레임으로 변경


<br/><br/>


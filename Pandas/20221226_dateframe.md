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

위에서도 언급했듯, 딕셔너리 자료형은 데이터의 순서를 보장하지 않는다. 따라서 키(Key)와 값(Value)로 이루어진 순서가 보장된 딕셔너리를 전달하기 위해서는 `OrderedDict`로 딕셔너리를 한 번 감싼 후에 인자로 전달해야 한다. <br/>
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

## 3-2. 시리즈 속성 알아보기

<br/>

|---|---|
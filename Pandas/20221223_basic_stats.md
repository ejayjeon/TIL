Date: 2022.12.23.Fri.

# :memo: 2. 기초 통계 계산
## 1. 그룹화한 데이터 통계 구하기

<br/>

### 1-1. 그룹화한 데이터 평균 구하기
(자료는 이지스퍼블리싱에서 제공하는 'gapminder'를 이용함) <br/><br/>

1. 데이터 프레임 중 `lifeExp` 열을 `year`(연도별)로 그룹화하여 평균 구하기


```python
import pandas as pd

# data 폴더 아래의 gapminder.tsv 파일을 불러와서, tap을 기준으로 정렬
df = pd.read_csv('./data/gapminder.tsv', sep='\t')

avg = df.groupby('year')['lifeExp'].mean()
```
<br/>

* 데이터의 그룹화는 `groupby` 메소드를 이용할 수 있다.
* 이 `groupby` 메소드는 그룹화하고자 하는 열을 인자로 받는다. 제시된 조건을 보면, 연도별로 그룹화하고자 했으므로 연도를 표기한 `year` 열을 `groupby` 메소드의 인자로 넣어준다.
* 다음으로는 그룹화를 통해 구하고자 하는 열의 이름을 표기한다. 여기서는 `lifeExp` 열을 입력해준다.
* 마지막으로, 평균을 구해주는 메소드인 `mean` 을 추가한다.

<br/><br/>

2. lifeExp, gdpPercap 열의 평균값을 연도, 지역별로 그룹화하여 한 번에 계산하기 <br/><br/>

```python
multi_group = 
  df.groupby(['year', 'continent'])[['lifeExp', 'gdpPercap']].mean()
```
<br/>

* 방식은 1번과 동일하다. 열의 이름을 나열하기 위해 리스트 형태를 사용했다.
* 그룹화 하고자 하는 열을 리스트 형태로 `groupby` 메소드에 전달한다.
* 값을 구하고자 하는 열을 리스트 형태로 표기한다.
* 평균을 구하는 `mean` 메소드로 최종적으로 나온 값의 평균값을 구한다.

<br/><br/>

### 1-2. 그룹화한 데이터의 갯수 세기
그룹화한 데이터의 갯수를 통계에서는 '**빈도 수**'라고 한다. `pandas`에는 빈도 수를 쉽게 구할 수 있는 메소드로, `nunique` 가 있다.

<br/>

```python
frequency = df.groupby('continent')['country'].nunique()
```

<br/>

* 위와 같이 구하면, `continent` 열을 기준으로 그룹화한 `country` 의 빈도 수를 계산하여 값을 추출할 수 있다.

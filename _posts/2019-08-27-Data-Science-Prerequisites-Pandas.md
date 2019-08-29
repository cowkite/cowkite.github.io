----

title: "Data Science - Pandas 기초"

date: 2019-08-27 17:16

categories: development python data-science

------



## 1. Pandas Basics 

- Wes McKinney에 의해 개발 되었음 (https://wesmckinney.com/)

- Pandas = Data Analytics(데이터 분석) 을 위한 Python 라이브러리

- Data = [Structured Data] + [Unstructured Data]
  Data = [Tabuler Data] + [Image, Text, Audio, Video]
  Data = [20%] + [80%]

- Pandas 가 다루는 Data = 정형 데이터(Structured Data) = Table 형태 데이터

- Pandas의 두가지 데이터 타입: DataFrame, Series

  ### 1-1. DataFrame: Matrix with labels

  ```pythond
  import pandas as pd
  unames = ['user_id', 'gender', 'age', 'occupation', 'zip'] 
  users = pd.read_table('./data/pandas/users.dat', sep='::', header=None, names=unames, engine='python', index_col='user_id')
  users.head()
  ```

  ### 1-2. Series: Vector with lables

  ```python
  users['age'].head()
  ```

  ```python
  users.loc[3]
  ```



## 2. Indexing

- 데이터에서 원하는 부분만 추출하는 것 (ex: 남자만, 30대만, 맨 앞의 20줄만, 3번 user만...)
- `head()`: 맨 앞의 몇 줄만 인덱싱 (ex: `users.head(10)`)
- `tail()`: 맨 뒤의 몇 줄만 인덱싱
- `age`: 열 하나만 인덱싱
- `loc`: 행 하나만 인덱싱
- Filtering: 조건에 맞는 데이터만 인덱싱
  - 람다 함수 사용 (ex: `users[lambda x: x.gender == 'F']`)
  - 람다 사용 이유: DataFrame 이름을 다시 쓰지 않고 `x`로 표현이 가능해서 코드가 심플해짐 (`users[users.gender == 'F']` -> `users[lambda x: x.gender == 'F']`)



## 3. Groupby

- 그룹 별로 비교해보는 것 (성별 별로, 직업 별로, 나이대 별로...)

- 내부적으로 Split-Apply-Combine 수행

- .groupby(`key`)[`column`].`operation`()

  ex: `users.groupby('gender')['age'].mean()`: gender 별로 age의 값을 combine

- `users[age].groupby(users.gender).mean()` -> `users.groupby(gender)[age].mean()`

- groupby 결과를 DataFrame으로 보려면 []를 한번 더 해주면 됨: `.groupby(key)[[column]].operation()`

  ex: `users.groupby('gender')[['age']].mean()`

- groupby와 함께 쓰이는 함수들

  - 갯수 .size()
  - 합 .sum()
  - 평균 .mean()
  - 중앙값 .median()
  - 표준편차 .std()
  - 최대값 .max()
  - 최소값 .min()
  - 내가 만든 함수 .apply(내가 만든 함수명)



## 4. Assign

- 파생변수 (=새로운 변수)를 쓰는 것.. 즉, column 추가하는 것

- **방법1**: `['new_column'] = column`

  ex: `age_range`라는 `age`의 평균 column 추가:  `users1['age_range'] = users1['age'] // 10 * 10`

- **방법2**: `.assign(new_column = column)`

  ex: `age_range`라는 `age`의 평균 column 추가: `users2.assign(age_range = lambda x: x.age //10 * 10)`

- 방법1은 값이 바뀌기 때문에 방법2를 사용하는 것이 좋음



## 5. Method Chaining

- 전체를 소괄호로 묶어야함

- **Without Method Chaining**

  ```python
  a = data.this_method()
  b = a.that_method()
  c = b.another_method()
  ```

  - 새로운 변수 이름을 생각해 내기 힘들다.
  - 이름만 보고 무슨 변수인지 예측하기 어렵다.
  - 나중에 쓰이지 않을 변수가 너무 많다.

- **With Method Chaining**

  ```python
  data_new = (data.this_method()
              .that_method()
              .another_method()
             )
  ```

  

## 6. Pandas Heuristics

- **의미 있는 이름을 사용할 것**

  1. 직관적인 변수 사용
  2. 축약어 지양
  3. 같은 이름 다시 사용하지 않기

- **분석의 담계를 남길것**

  ```
  분석단계1 : users
  분석단계2 : users_cleaned
  분석단계3 : users_sampled
  분석단계4 : users_added
  ```

  1. 원본 데이터는 건드리지 말 것
  2. 위에서부터 차례대로 실행가능하도록 할 것
  3. 덜 중요한 과정은 Method Chaining으로 처리 할 것

- **for loop를 사용하지 말것**

  다음 method로 대체

  - map
  - apply
  - applymap
  - add
  - sub
  - mul
  - div


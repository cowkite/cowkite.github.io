----

title: "Data Science - Classification"

date: 2019-08-28 15:18

categories: development python data-science

------



타이타닉 침몰 사고의 실제 데이터를 통해, 생존유무를 예측하는 문제를 Predictive Analytics 기법으로 풀어보기



## 1. Classification Overview

* **이진분류 (Binary Classification)**

  * 이메일 : 스팸 / 스팸 아님
  * 온라인 거래 : 도용 / 도용 아님
  * 종양 : 심각함(Malignant) / 심각하지 않음(Benign)
  * 특징: 종속변수(y)가 0 또는 1의 값을 가진다.

* **유방암 데이터 예제**

  * 데이터 가져오기

    ```python
    from sklearn.datasets import load_breast_cancer
    
    breast_cancer = load_breast_cancer()
    
    print(breast_cancer.DESCR)
    
    import pandas as pd
    
    data = pd.DataFrame(breast_cancer.data, columns=breast_cancer.feature_names)
    data['malignant'] = 1 - breast_cancer.target # 0 for malignant and 1 for benign in the original dataset
    ```

  * 그래프 그리기

    ```python
    %matplotlib inline
    from matplotlib import pyplot as plt
    
    data.plot(x='mean radius', y='malignant', kind='scatter', ylim=[-1,2])
    ```

    ![](D:\soyeon.kim\2. 개인\images\12.PNG)

  * 선형 회귀(Linear Regression)으로 분류하면 어떨까?

    ```python
    from sklearn.linear_model import LinearRegression
    
    lr = LinearRegression()
    lr.fit(data[['mean radius']], data['malignant'])
    
    data.plot(x='mean radius', y='malignant', kind='scatter', ylim=[-1,2])
    plt.plot(data['mean radius'], lr.predict(data[['mean radius']]), 'skyblue');
    ```

    ![](D:\soyeon.kim\2. 개인\images\13.PNG)
    * 선형회귀로 모델을 만든 다음에 ℎ𝜃(𝑥)=0.5hθ(x)=0.5보다 작으냐 크냐로 y값을 예측한다.
    * 못 할 건 없지만
    * y는 0 또는 1이다.
    * ℎ𝜃(𝑥)hθ(x)이 0보다 작거나 1보다 큰 것은 이상하다.
    * 그래서 1≥ℎ𝜃(𝑥)≥01≥hθ(x)≥0인 모델이 필요하다.
    * 로지스틱 회귀 (Logistic Regreesion) 는 1≥ℎ𝜃(𝑥)≥01≥hθ(x)≥0 로 만들어준다.

  * 가설 (Hypothesis)

    * Goal: 1≥ℎ𝜃(𝑥)≥0

    * Solution: 𝑔(𝜃𝑇𝑥)

    * **Sigmoid function (Logistic function)**: 𝑔(𝑧)=11+𝑒−𝑧

      ```python
      import numpy as np
      z = np.linspace(-4, 4)
      plt.plot(z, [1/(np.e**(-i) + 1) for i in z])
      ```

      ![](D:\soyeon.kim\2. 개인\images\14.PNG)

      -> Sigmoid function의 특징: 항상 0보다 크고 1보다 작은 값을 리턴함

* **Dicistion boundary**

  * 로지스틱 회귀 (Logistic regression)는 ℎ𝜃(𝑥)hθ(x) >= 0.5일 때 y=1로 예측한다.

    이는 𝑧=𝜃𝑇𝑥z=θTx > 0일 때와 같다.

    즉, 𝜃𝑇𝑥θTx = 0 일 때를 기준으로 class 가 나뉘게 된다.

* **Cost function**

  * ℎ𝜃(𝑥)=11+𝑒−𝜃𝑇𝑥

  * 매개변수 (parameters) 𝜃θ를 어떻게 구할까?

    * 𝐶𝑜𝑠𝑡(ℎ𝜃(𝑥(𝑖)),𝑦(𝑖)))={−log(ℎ𝜃(𝑥(𝑖))),−log(1−ℎ𝜃(𝑥(𝑖))),if 𝑦=1if 𝑦=0Cost(hθ(x(i)),y(i)))={−log⁡(hθ(x(i))),if y=1−log⁡(1−hθ(x(i))),if y=0

    * y = 1일 때, ℎ𝜃(𝑥)hθ(x) = 1이면 cost가 0이고, ℎ𝜃(𝑥)hθ(x) = 1에서 멀어질 수록 무한대에 가까워진다.

      ```python
      h_range = np.linspace(0.001, 0.999, num=1000)
      plt.plot(h_range, -np.log(h_range))
      ```

      ![](D:\soyeon.kim\2. 개인\images\15.PNG)

    * y = 0일 때, ℎ𝜃(𝑥)hθ(x) = 0이면 cost가 0이고, ℎ𝜃(𝑥)hθ(x) = 0에서 멀어질 수록 무한대에 가까워진다.

      ```python
      plt.plot(h_range, -np.log(1 - h_range))
      ```

      ![](D:\soyeon.kim\2. 개인\images\16.PNG)

    * 예측값이 정답과 맞으면 cost가 0, 많이 틀릴 수록 cost를 많이 준다는 것을 알 수 있다.

  * **Simplified cost function**

    * 𝐽(𝜃)=1𝑛∑𝑖=1𝑛𝐶𝑜𝑠𝑡(ℎ𝜃(𝑥(𝑖),𝑦(𝑖)))
    * 𝐶𝑜𝑠𝑡(ℎ𝜃(𝑥(𝑖)),𝑦(𝑖)))={−log(ℎ𝜃(𝑥(𝑖))),−log(1−ℎ𝜃(𝑥(𝑖))),if 𝑦=1if 𝑦=0
    * 𝐶𝑜𝑠𝑡(ℎ𝜃(𝑥(𝑖)),𝑦(𝑖)))=−𝑦log(ℎ𝜃(𝑥(𝑖)))−(1−𝑦)𝑙𝑜𝑔(1−ℎ𝜃(𝑥(𝑖)))





## 1-1. 나무 기반 모델 (Tree-Based Models)

* 일반적으로 성능이 더 좋은건 아니나, 앙상블과 같이 사용했을 때 성능이 높아짐

* 회귀와 분류 문제에 모두 사용 가능

* 예측변수 공간(predictor space)을 여러 개의 구역으로 구획(stratifying or segmenting)한다.

* 새로운 데이터에 대해 예측하기 위해서는 그 데이터가 속한 구역에 있는 학습 데이터셋의 평균이나 최빈값을 이용한다.

* 구역을 나누는 규칙을 나무(tree)로 표현할 수 있기 때문에, 이런 접근을 의사 결정 나무(decision tree)로 부른다.

* 의사 결정 나무(decision tree)는 여러 가지 규칙을 순차적으로 적용하면서 독립 변수 공간을 분할하는 분류 모형이다. 분류(classification)와 회귀 분석(regression)에 모두 사용될 수 있기 때문에 CART(Classification And Regression Tree)라고도 한다.

* **의사 결정 나무 (Decistion Trees)의 특징**

  * 간단하고 해석하기 쉽다.
  * 정확도는 다소 낮다.
  * 배깅(bagging), 랜덤 포레스트(random forests), 부스팅(boosting) 등 앙상블 기법과 함께 자주 사용된다.
  * 수많은 의사 결정 나무를 결합하는 것이 성능을 크게 향상시킬 수 있다는 것을 보게 될 것이다.
  * 단, 의사 결정 나무의 갯수가 많으면 모델의 해석은 더 어려워진다.

  ### 1-2-1. 회귀 트리 (Regression Tree)

  * 예시 : 회귀 트리를 이용해서 야구 선수의 연봉을 예측하기

    * 야구 선수의 경력과 작년에 몇 번 공을 쳤는지를 이용해서 타자의 연봉을 예측하는 모델
    * Hitters : Baseball Data : https://www.kaggle.com/floser/hitters
    * 변수 설명 : https://rdrr.io/cran/ISLR/man/Hitters.html

    ![](D:\soyeon.kim\2. 개인\images\17.PNG)
    * 해석: 경력이 4.5년이 안되는 야구선수는 연봉을 5.11을 받고, 경력이 4.5년보다 많은 야구 선수는 작년 타수가 117.5보다 작으면 6.00, 크면 6.74를 받는다.
      - 정확히는 연봉의 log를 취한 값을 예측
      - 그래서 1,000×𝑒6.740=1,000×e6.740=845,346
      - 잎 노드에 있는 숫자는 학습 데이터 중에 해당 구역으로 떨어진 야구 선수들 연봉의 평균이다.

    ![](D:\soyeon.kim\2. 개인\images\18.PNG)

    * 세 구역은 다음과 같이 수학적으로 표현 가능하다.
      - R1 ={X | Years<4.5}
      - R2 ={X | Years>=4.5, Hits<117.5}
      - R3 ={X | Years>=4.5, Hits>=117.5}
    *   해석
      * 경력은 연봉을 결정하는 데 가장 중요한 요소이다.
      * 경력이 적으면 연봉이 평균적으로 낮다.
      * 야구선수의 경력이 적으면, 작년에 몇번 공을 쳤는지는 중요하지 않다.
      * 그러나 야구선수의 경력이 5년 이상이면, 작년에 기록한 타수가 연봉에 영향을 미친다.
      * 타수가 높은 타자는 다른 타자들보다 평균적으로 연봉을 많이 받는다.

  * **회귀 트리 (regression tree) 만들기**

    ![](D:\soyeon.kim\2. 개인\images\19.PNG)

  * **Splitting**

    * 예측 변수 공간(predictor space)을 J개의 서로 다른 구역으로 나눈다.
    * 예측 변수 공간(predictor space)이란 예측 변수 X1, X2, …, Xp가 취할 수 있는 값들의 조합을 말한다.
    * 우리의 목적은 RSS를 최소화하는 R1, …, RJ를 찾는 것이다.
    * ∑𝑗=1𝐽∑𝑖∈𝑅𝑗(𝑦𝑖−𝑦̂ 𝑅𝑗)2∑j=1J∑i∈Rj(yi−y^Rj)2
    * 우리는 예측 변수 공간을 고차원의 직사각형들(high dimensional rectanngles), 즉 박스들(boxes)로 나눈다.

  * **Pruning**

    * Spliting 방식은 training data에는 잘 적합할 수 있으나, overfit의 위험이 있다. 지나치게 세분화된 tree는 train set에서만의 특징마저 반영 해버릴 수 있기 때문이다. 이는 결과적으로 test set에서는 좋지 못한 성능을 냄을 나타낸다. 따라서 Pruning 알고리즘을 적용하면 split을 제한하여 약간의 bias가 생기더라도 variance가 높지 않은, ‘지나치게 세분화되지 않은 tree’를 만들수 있다.
    * 이 때 사용하는 Pruning 알고리즘이 Cost complexity pruning 이다. 이는 새로운 split을 함으로써 생기는 RSS의 ‘이득’이 α|T| 라는 penalty보다 더 크지 못하다면 split을 안하는 알고리즘이다.

  * **회귀 트리 알고리즘 정리**

    * Xj를 선택하고 나누는 기준(cutpoint) s를 선택해서 두 개의 구역으로 나눈다.
    * 그 구역에서 위에서 언급한대로 평균값을 이용해 예측을 수행한다.
    * 모든 예측 변수와 모든 가능한 나누는 기준(cutpoint)에 대해서 RSS를 구한다.
    * 이 때 기존보다 가장 RSS를 크게 낮추는 Xj와 s를 선택한다.
    * 그 다음에는, 나눠진 구역 중 하나에서 같은 일을 반복한다.
    * 이 일을 미리 정한 멈추는 기준(stoppinng criterion)이 도달할 때까지 반복한다.
    * 예컨대, 모든 구역(region)에 학습 데이터가 5개 미만으로 남았을 때 그만둘 수 있다.

  

  ### 1-2-2. 분류 트리 (Classification Tree)

    * 회귀 트리와 거의 유사하다.

  * 양적인(quantitative) 반응변수가 아닌 질적인(qualitative) 반응 변수를 사용한다.

  * 분류 트리에서, 예측은 그 구역에서 평균이 아닌 **가장 자주 등장하는 클래스**로 결정된다.

    * **분류 트리 만들기**

        * Heart Disease UCI : https://www.kaggle.com/ronitf/heart-disease-uci
      * 변수 설명 : https://archive.ics.uci.edu/ml/datasets/Heart+Disease![](D:\soyeon.kim\2. 개인\images\20.PNG)
      * 회귀 트리와 거의 유사하다.
      * 다만 RSS는 더이상 쓸 수 없다.

  * **분류 트리에서 RSS 대신 사용하는 것들**

    * Classification error rate

      * 예측이 틀리는 비율
      * 가장 자주 등장하는 클래스로 예측하기 때문에, 단순히 그 구역에서 가장 자주 등장하는 클래스가 아닌 학습 데이터의 비율이 된다.

    * Gini index

      * 𝐺=∑𝑘=1𝐾𝑝̂ 𝑚𝑘(1−𝑝̂ 𝑚𝑘)

    * Entropy

      * 0≤−𝑝̂ 𝑚𝑘log𝑝̂ 𝑚𝑘

        

  ### 1-2-3. 트리 모델(Trees Models)과 선형 모델(Linear Models)의 비교

  - 어떤 모델이 더 좋을지는 주어진 문제에 달려있다.

  - 특징 변수(features)와 반응 변수(responnse)의 관계가 선형적이면, 선형 모델이 잘 동작한다.

  - 특징 변수(features)와 반응 변수(responnse)의 관계가 심하게 비선형적이면, 트리 모델이 더 잘할 수 있다.

    ![](D:\soyeon.kim\2. 개인\images\21.PNG)

  

  ### 1-2-4. 의사 결정 나무 (Decision Tree) 요약

  * *장점*
    * 설명하기 쉽다. 심지어 선형 회귀보다도 설명하기 쉽다.
    * 사람이 의사 결정하는 방식을 잘 표현한다.
    * 나무를 그려볼 수 있다.
    * 질적 변수(카테고리 변수)를 더미화하지 않고도 자연스럽게 다룰 수 있다.
  * *단점*
    * 의사 결정 나무는 보통 다른 회귀나 분류 모델에 비해서 정확도가 떨어진다.



## 2. Exploratory Data Analytics (EDA)

* *(간단히 짚고 넘어가기)*

  ### 2-1. 문제 정의하기

  * 타이타닉 침몰 사고의 실제 데이터를 통해, 생존유무를 예측합니다.

  ### 2-2. 훈련, 테스트 데이터 확인하기

  * **데이터 확인**

    ```python
    import numpy as np 
    import pandas as pdhttp://localhost:8888/notebooks/step3%20-%20Classification.ipynb#3-2-2-:-%ED%9B%88%EB%A0%A8,-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0
    import matplotlib.pyplot as plt
    import seaborn as sns
    plt.style.use('fivethirtyeight')
    import warnings
    warnings.filterwarnings('ignore')
    %matplotlib inline
    
    data=pd.read_csv('./data/titanic/train.csv')
    
    data.head()
    ```

    ![](D:\soyeon.kim\2. 개인\images\22.PNG)

  * **값이 없는 데이터 확인하기**

    ```python
    data.isnull().sum() #checking for total null values
    ```

    ![](D:\soyeon.kim\2. 개인\images\23.PNG)
    -> 관찰 결과: `Age`, `Cabin`, `Embarked` 가 null value 를 가지고 있음

  * **데이터로부터 1차적인 insight 알아내기: 생존자는 몇명?**

    ```python
    f,ax=plt.subplots(1,2,figsize=(18,8))
    data['Survived'].value_counts().plot.pie(explode=[0,0.1],autopct='%1.1f%%',ax=ax[0],shadow=True)
    ax[0].set_title('Survived')
    ax[0].set_ylabel('')
    sns.countplot('Survived',data=data,ax=ax[1])
    ax[1].set_title('Survived')
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\24.PNG)
    -> **관찰 결과**

    - 많은 승객이 Tatanic 충돌 사고로부터 살아남지 못했습니다.

    - Training Data Set을 보면, 탑승한 891 명의 승객 중 350 명만이 살아 남았습니다. 즉, 전체 Training Data Set의 승객중 **38.4%** 만이 충돌에서 살아 남았습니다. 데이터에서 더 나은 통찰력을 얻고 어떤 카테고리의 승객이 생존했는지, 생존하지 못했는지를 알아 내기 위해 더 깊게 분석해야 할 필요가 있습니다.

      

  ### 2-3. Features 분석하기

  * **Types of Features**

    * **Categorical Features**

      * A categorical variable is one that has two or more categories and each value in that feature can be categorised by them.For example, gender is a categorical variable having two categories (male and female). Now we cannot sort or give any ordering to such variables. They are also known as **Nominal Variables**.
      * 범주형 변수(categorical variable)는 두 개 이상의 범주(Category)가 있고 해당 feature의 각 값을 범주별로 나눌 수 있습니다. 예를 들어 성별(Sex)은 두 범주(남성과 여성)가 있는 범주형 변수입니다. 범주형 변수는 정렬(Sorting)하거나 순서부여(Ordering)를 할 수 없습니다. 범주형 변수는 **Nominal Variables(명목 변수)** 라고도 합니다.
      * **Categorical Features in the dataset: Sex,Embarked.**

    * **Ordinal Features**

      * An ordinal variable is similar to categorical values, but the difference between them is that we can have relative ordering or sorting between the values. For eg: If we have a feature like **Height** with values **Tall, Medium, Short**, then Height is a ordinal variable. Here we can have a relative sort in the variable.
      * 순서형 변수는 범주형 변수과 유사하지만 차이점은 값 사이의 정렬(Sorting)하거나 순서부여(Ordering)를 할 수 있다는 것입니다. 예 : **Height(키=신장)** 변수인 경우에 **Tall, Medium, Short** 값이 순서(Order)가 있습니다.
      * **Ordinal Features in the dataset: PClass**

    * **Continous Features**

      * A feature is said to be continous if it can take infinate values between any two points or between the minimum or maximum values in the features column.
      * 연속형 변수는 임의의 두 점 혹은 최소값과 최대값 사이에 무한 값을 취할 수 있는 경우를 말한다.
      * **Continous Features in the dataset: Age**  

    * **Discrete Features**

      * A feature is said to be discrete if it can take countable values between any two points or between the minimum or maximum values in the features column.

      * 이산형 변수는 임의의 두 점 혹은 최소값과 최대값 사이에 유한 값을 취할 수 있는 경우를 말한다.

      * **Discrete Features in the dataset: SibSp, Parch**

        

    ####  2-3-1. Categorical Feature: Sex

    ```python
    f,ax=plt.subplots(1,2,figsize=(18,8))
    data[['Sex','Survived']].groupby(['Sex']).mean().plot.bar(ax=ax[0])
    ax[0].set_title('Survived vs Sex')
    sns.countplot('Sex',hue='Survived',data=data,ax=ax[1])
    ax[1].set_title('Sex:Survived vs Dead')
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\25.PNG)

    -> 관찰결과: 살아남은 여성의 수가 살아남은 남성의 수의 2배 정도 됩니다. 또한 **여성의 생존율은 거의 75% 인데 반해, 남성의 생존율은 거의 18-19% 정도 됩니다.**

    

    #### 2-3-2. Ordinal Feature: Pclass

    ```python
    f,ax=plt.subplots(1,2,figsize=(18,8))
    data['Pclass'].value_counts().plot.bar(color=['#CD7F32','#FFDF00','#D3D3D3'],ax=ax[0])
    ax[0].set_title('Number Of Passengers By Pclass')
    ax[0].set_ylabel('Count')
    sns.countplot('Pclass',hue='Survived',data=data,ax=ax[1])
    ax[1].set_title('Pclass:Survived vs Dead')
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\26.PNG)

    * **관찰 결과**
      * 그래프를 통해 Pclass1의 승객들이 구조 작업시에 높은 우선 순위를 부여 받았음을 유추해 볼 수 있습니다. Pclass3의 승객 수는 훨씬 많았지만 생존율은 약 **25%** 로 매우 낮습니다.
      * Pclass1은 **63%** 인 반면 Pclass2는 **48%** 입니다. 이를 통해 돈과 지위가 생존율에 영향을 주었음을 알 수 있습니다.

    

    #### 2-3-3. Continous Feature: Age

    ```python
    f,ax=plt.subplots(1,2,figsize=(18,8))
    sns.violinplot("Pclass","Age", hue="Survived", data=data,split=True,ax=ax[0])
    ax[0].set_title('Pclass and Age vs Survived')
    ax[0].set_yticks(range(0,110,10))
    sns.violinplot("Sex","Age", hue="Survived", data=data,split=True,ax=ax[1])
    ax[1].set_title('Sex and Age vs Survived')
    ax[1].set_yticks(range(0,110,10))
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\27.PNG)

    * **관찰 결과**

      1) 어린이의 수는 Pclass의 번호가 증가에 맞추어 함께 중가함. 10세 이하 어린이의 생존율이 Pclass와 관계없이 양호한 것으로 보입니다.

      2) Pclass1에서 20 세에서 50 세 사이의 Passenegers에 대한 생존 기회는 높으며 여성에게는 훨씬 더 좋습니다.

      3) 남성의 경우 생존율은 연령이 증가함에 따라 감소합니다.

    

    #### 2-3-4. Categorical Value: Embarked

    ```python
    sns.factorplot('Embarked','Survived',data=data)
    fig=plt.gcf()
    fig.set_size_inches(5,3)
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\28.PNG)

    * **관찰결과**

      포트C 에서 승선한 승객들의 생존율이 약 0.55로 가장 높고 포트S에 승선한 승객들의 생존율이 가장 낮음.

    

    #### 2-3-5. Discrete Feature: SibSp

    ```python
    f,ax=plt.subplots(1,2,figsize=(20,8))
    sns.barplot('SibSp','Survived',data=data,ax=ax[0])
    ax[0].set_title('SibSp vs Survived')
    sns.factorplot('SibSp','Survived',data=data,ax=ax[1])
    ax[1].set_title('SibSp vs Survived')
    plt.close(2)
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\29.PNG)

    * **관찰 결과**
      * barplot 와 factorplot 은 가족없이 혼자 승선한 승객에 대해서 34.5%의 생존율을 보여줍니다. SibSp 가 증가하면 그래프가 대략적으로 감소합니다. 일반적으로 가족이 있다면, 가족을 구하려고 더 노력할 것입니다. 놀라운 사실은 5-8 명으로 구성된 가족의 생존율은 **0%** 입니다.

      * 이유는 **Pclass** 에 있었습니다. crosstab 은 SibSp > 3 인 승객이 모두 Pclass3에 있음을 보여줍니다. Pclass3(>3)인 승객은 모두 살아남지 못했습니다.

        

    #### 2-3-6. Discrete Feature: Parch

    ```python
    f,ax=plt.subplots(1,2,figsize=(20,8))
    sns.barplot('Parch','Survived',data=data,ax=ax[0])
    ax[0].set_title('Parch vs Survived')
    sns.factorplot('Parch','Survived',data=data,ax=ax[1])
    ax[1].set_title('Parch vs Survived')
    plt.close(2)
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\30.PNG)

    * **관찰 결과**

      * SibSp 와 결과가 매우 비슷합니다. 가족이 함께 탑승한 승객은 생존 기회가 더 큽니다. 그러나 parch 숫자가 올라감에 따라 감소합니다.

      * 1-3 명의 가족(부모 혹은 자식)을 가진 승객은 혼자 승선한 승객보다 생존율이 높습니다. 4명 이상의 가족(부모 혹은 자식)을 가진 승객의 생존율은 낮습니다.

        

    #### 2-3-7. Continous Feature: Fare

    ```python
    f,ax=plt.subplots(1,3,figsize=(38,8))
    sns.distplot(data[data['Pclass']==1].Fare,ax=ax[0])
    ax[0].set_title('Fares in Pclass 1')
    sns.distplot(data[data['Pclass']==2].Fare,ax=ax[1])
    ax[1].set_title('Fares in Pclass 2')
    sns.distplot(data[data['Pclass']==3].Fare,ax=ax[2])
    ax[2].set_title('Fares in Pclass 3')
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\31.PNG)

    * **관찰 결과**

      Pclass1 의 분포가 Fare 값으로 봤을 때 가장 넓게 분포되어 있음.

      

  * **전체 Feature 들에 대한 관찰 결과**

    * `Sex:` 남자보다 여자일 때 생존율이 높음.
    * `Pclass:` **1등석 승객**의 생존율이 매우 높음. **Pclass3의 생존율은 매우 낮음**. **여성**의 경우 **Pclass1** 의 생존율이 1이며 **Pclass2** 의 생존율도 높음. **생존하기 위해서 돈과 지위가 매우 중요했다는 사실을 보여줌.**.
    * `Age` 5-10세 미만의 어린이는 생존율이 높음. 15~35세 사이의 승객이 많이 사망함.
    * `Embarked` : 흥미로운 사실을 볼수 있음 **Pclass1 승객의 대다수가 S에서 일어났음에도 불구하고 C에서의 생존율이 더 높음.** Q에서의 생존한 승객은 모두 **Pclass3** 에서 나왔음.
    * `Parch+SibSp`: 1-2명의 형제자매/배우자 혹은 1-3명의 부모/자식과 함께 승선한 승객이 혼자 승선했거나 대가족과 함께 승선한 승객보다 생존율이 높음.





## 3. Preprocessing

* *위에서 보정한 데이터를 한번 더 보정하기*

  ### 3-1. Feature Engineering

  * 다수의 feature들이 주어졌을 때, 모든 feature가 모두 중요하지는 않습니다. feature들 중에는 제거되어야 하는 redundent feature이 존재하고 때로는 다른 feature들에서 부터 새로운 feature를 추가할 수도 있습니다.

  * 한 예로 앞에서 'Name' feature에서 'Initial' feature를 뽑아내어 새로운 feature로 추가한 경우를 보았습니다. 이 처럼 새로운 feature를 추가하거나 몇가지 feature는 제거하는 것이 필요합니다. 결국 Feature Engineering은 주어진 Dataset에 존재하는 feature를 예측 모델링에 적합한 형태의 Feature로 변환하는 과정이라고 보아야 할 것입니다.

    #### 3-1-1. Age_band

    * **Age Feature 의 문제점**

      - **Age는 Continous Feature입니다**. 이것이 문제가 될 수 있습니다.
      - 예들들어 스포츠 선수를 **Sex(성별)** 로 묶어보면, 쉽게 '남'과 '여' 두가지 그룹으로 나누어 묶을 수 있습니다.
      - 만약 스포츠 선수를 **Age** 로 묶어본다면 어떻까요? 만약 30명이 있다면, 이 경우 30개의 서로 다른 Age 값이 있을 것이고 이것이 Continous Feature의 문제점이 됩니다.

    * **Continous Feature는 Categorical Feature** 로 변경할 필요가 있습니다. 이 때 사용되는 기법이 Binning 혹은 Normalisation 입니다. 여기서는 Binning을 사용해 봅니다.

    * 승객의 나이중 최대값이 80이므로... 0-80 구간을 5개의 bin으로 나누어봅니다. 80/5=16. 하나의 bin의 사이즈는 16이 됩니다.

      ```python
      data['Age_band'].value_counts().to_frame().style.background_gradient(cmap='summer')#checking the number of passenegers in each band
      
      sns.factorplot('Age_band','Survived',data=data,col='Pclass')
      plt.show()
      ```

      ![](D:\soyeon.kim\2. 개인\images\32.PNG)

      * **관찰 결과**

        Pclass와 관계없이 Age_band가 증가함에 따라 생존율이 감소합니다.

        

    #### 3-1-2. Family_Size and Alone

    * "Family_size" 와 "Alone" 이라는 feature를 추가해서 분석해 봅시다. "Family_size" 와 "Alone"는 "Parch" 과 "SibSp"를 이용해서 만듭니다. "Family_size"의 경우는 생존율이 승객의 "Family Size"와 관련이 있는지를 확인할 수 있도록 "Parch" 과 "SibSp"를 합한 feature를 새로 만든 것입니다. "Alone"은 승객이 혼자 승선했는지 여부를 나타내는 새로운 feature 입니다.

      ```python
      data['Family_Size']=0
      data['Family_Size']=data['Parch']+data['SibSp']#family size
      data['Alone']=0
      data.loc[data.Family_Size==0,'Alone']=1#Alone
      
      f,ax=plt.subplots(1,2,figsize=(18,6))
      sns.factorplot('Family_Size','Survived',data=data,ax=ax[0])
      ax[0].set_title('Family_Size vs Survived')
      sns.factorplot('Alone','Survived',data=data,ax=ax[1])
      ax[1].set_title('Alone vs Survived')
      plt.close(2)
      plt.close(3)
      plt.show()
      ```

      ![](D:\soyeon.kim\2. 개인\images\33.PNG)

      * **관찰 결과**

        **Family_Size=0 는 가족없이 혼자 승선한 승객이라는 뜻입니다.** family_size=0인 경우 생존율니 낮습니다. family_size > 4 인 경우 생존율이 급격히 떨어집니다. 따라서 이 feature도 예측 모델에 유입될 수 있는 중요한 feature라고 보여집니다.

      ```python
      sns.factorplot('Alone','Survived',data=data,hue='Sex',col='Pclass')
      plt.show()
      ```

      ![](D:\soyeon.kim\2. 개인\images\34.PNG)

      * **관찰 결과**

        혼자 승선한 승객은 "Sex", "Pclass"와 상관없이 생존율이 낮습니다. 단 예외적으로 Pclass3 이면서 여성인 승객인 경우는 혼자 승선한 승객인 경우가 가족과 함께 승선한 승객보다 생존율이 높습니다.

        

  ### 3-2. Cleaning

  * **Converting String Values into Numeric**: 일반적으로...머신러닝 모델의 입력으로 String 값을 넣을 수 없으므로, "Sex", "Embarked" 같은 문자형 feature을 숫자형 feature들로 변환해야만 합니다.

    ```python
    data['Sex'].replace(['male','female'],[0,1],inplace=True)
    data['Embarked'].replace(['S','C','Q'],[0,1,2],inplace=True)
    data['Initial'].replace(['Mr','Mrs','Miss','Master','Other'],[0,1,2,3,4],inplace=True)
    ```

  * **Dropping UnNeeded Features**

    * `Name`--> We don't need name feature as it cannot be converted into any categorical value.
    * `Age`--> We have the Age_band feature, so no need of this.
    * `Ticket`--> It is any random string that cannot be categorised.
    * `Fare`--> We have the Fare_cat feature, so unneeded
    * `Cabin`--> A lot of NaN values and also many passengers have multiple cabins. So this is a useless feature.
    * `Fare_Range`--> We have the fare_cat feature.
    * `PassengerId`--> Cannot be categorised.

    ```python
    data.drop(['Name','Age','Ticket','Fare','Cabin','Fare_Range','PassengerId'],axis=1,inplace=True)
    sns.heatmap(data.corr(),annot=True,cmap='RdYlGn',linewidths=0.2,annot_kws={'size':20})
    fig=plt.gcf()
    fig.set_size_inches(18,15)
    plt.xticks(fontsize=14)
    plt.yticks(fontsize=14)
    plt.show()
    ```





## 4. Model Selection

* 알고리즘 돌리기..!

  ### 4-1. Running Basic Algorithms

  * EDA만으로 문제(Titanic 승객들의 사고에서 살아남을지 죽을지 여부를 예측)에 대한 모델의 성능을 높힐 수 없으므로 여러가지 Classification 알고리즘을 통해 어떻게 문제를 풀수 있을지 알아보자...

    1. Logistic Regression

    2. Support Vector Machines(Linear and radial)

    3. Random Forest

    4. K-Nearest Neighbours

    5. Naive Bayes

    6. Decision Tree

  * **Classification 의 종류**

    1. **판별함수(Discriminant Function) 모델**

       하나는 주어진 데이터를 카테고리에 따라 서로 다른 영역으로 나누는 경계면(decision boundary)을 찾아낸 다음 이 경계면으로부터 주어진 데이터가 어느 위치에 있는지를 계산하는 판별함수(discriminant function)를 이용하는 판별함수 모델이다.

    2. **확률적 모델 = 확률적 판별(Discriminative) 모델 | 확률적 생성(Generative) 모델**

       또 다른 하나는 주어진 데이터에 대해(conditionally) 각 카테고리 혹은 클래스가 정답일 조건부확률(conditional probability)를 계산하는 확률적 모델이다. 조건부확률 기반 방법은 조건부확률을 계산하는 방법에 따라 직접 조건부확률 함수를 추정하는 확률적 판별(discriminative) 모델과 베이즈 정리를 사용하는 확률적 생성(generative) 모델으로 나누어진다.

       

  ### 4-2. Cross Validation

  * *대부분의 경우 데이터는 불균형합니다. 특히 데이터셑의 크기가 작은 경우 테스트셑에 대한 성능 평가의 신뢰성이 떨어지게 됩니다. 테스트셑을 어떻게 잡느냐에 따라 성능이 매우 다르게 측정될수 있고 이를 해결하기 위해 Cross Validation은 최소 한 번은 테스트셋으로 쓰이도록 합니다. 아래의 그림을 보면, 데이터를 5개로 쪼개 매번 테스트셋을 바꿔나가는 것을 볼 수 있습니다. 첫 번째 Iteration에서는 BCDE를 트레이닝 셋으로, A를 테스트셋으로 설정한 후, 성능을 평가합니다. 두 번째 Iteration에서는 ACDE를 트셋으로, B를 테스트셋으로하여 성능을 평가합니다. 그러면 총 5개의 성능 평가지표가 생기게 되는데, 보통 이 값들을 평균을 내어 모델의 성능을 평가하게 됩니다.*

  * **K-Fold Cross Validation**

    * *K-Fold Cross Validation은 먼저 데이터 집합을 k개의 부분 집합으로 나눕니다. 2) 데이터 세트를 (k = 5) 부분으로 나누어 봅시다. 우리는 테스트를 위해 1 부분을 예약하고 4 부분에 걸쳐 알고리즘을 훈련시킵니다. 3) 각 Iteration 에서 테스트 부분을 변경하고 학습하는 프로세스를 계속 진행합니다. 4) 모든 Iteration이 끝나고 총 5개의 성능 평가지표가 생기게 되는데, 보통 이 값들을 평균을 내어 모델의 성능을 평가합니다.*
    * *각 Iteration에서 평가된 모델은 어떤 경우는 underfit 어떤 경우는 overfit 될 수 있습니다. 이들에 대한 평균을 구하므로...Cross Validation을 통해 좀 더 일반화된 모델을 얻을 수 있습니다.*

  * **Confustion Matrix**

    * *정확도(Accuracy)가 가장 일반적으로 쓰이는 성능평가 지표이고 직관적으로 이해하기 쉽다는 장점이 있지만...데이터의 분균형으로 인해 클래스의 비율이 한 쪽으로 치우쳐 있을 경우는 평가가 어렵다.(Imbalanced dataset problem)*

    * *혼동행렬(Confusion Matrix)은 정확도보다 더 자세한 정보를 보여준다.*

      * **Hyper-Parameters Tuning**

      * *기계 학습 모델은 블랙박스와 같습니다. 이 블랙박스에는 몇 가지 기본 매개변수 값이 있고 더 나은 모델을 얻기 위해 조정하거나 변경할 수 있습니다. 예를들어 SVM 모델의 C와 Gamma와 같은 것입니다. 이것을 우리는 hyper-parameter라고 부릅니다. 우리는 hyper-parameter 조정을 통해 알고리즘의 학습 속도를 변경하고 더 나은 모델을 얻을 수 있습니다. 이것을 Hyper-Parameter Tuning이라고 합니다.*

      * SVM와 RandomForests 2가지 알고리즘에 대해서 각각 Hyper-Parameter Tuning을 진행

        * **SVM**

          ```python
          from sklearn.model_selection import GridSearchCV
          C=[0.05,0.1,0.2,0.3,0.25,0.4,0.5,0.6,0.7,0.8,0.9,1]
          gamma=[0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9,1.0]
          kernel=['rbf','linear']
          hyper={'kernel':kernel,'C':C,'gamma':gamma}
          gd=GridSearchCV(estimator=svm.SVC(),param_grid=hyper,verbose=True)
          gd.fit(X,Y)
          print(gd.best_score_)
          print(gd.best_estimator_)
          ```

        * **Random Forests**

          ```python
          n_estimators=range(100,1000,100)
          hyper={'n_estimators':n_estimators}
          gd=GridSearchCV(estimator=RandomForestClassifier(random_state=0),param_grid=hyper,verbose=True)
          gd.fit(X,Y)
          print(gd.best_score_)
          print(gd.best_estimator_)
          ```

        -> Rbf-Svm에 대한 최고의 Accuracy 스코어는 **82.82% 이고 이때 Hyper-Parameter는 C=0.05 이고 gamma=0.1** 이다.

        -> RandomForest에 대한 최고의 Accuracy 스코어는 **81.8% 이고 이때 Hyper-Parameter는 n_estimators=900**.





## 5. Model Evaluation & Prediction

* 데이터 성능 높이기

  ### 5-1. Ensembling

  * 앙상블(Ensemble) 학습은 여러 개의 학습 알고리즘을 사용하고, 그 예측을 결합함으로써 보다 정확한 최종 예측을 도출하는 기법입니다. 하나의 강한 머신러닝 알고리즘보다 여러 개의 약한 머신러닝 알고리즘이 낫다 라는 아이디어를 가지고 이해하면 좋습니다.

  * 이미지, 영상, 음성 등의 비정형 데이터의 분류는 딥러닝이 뛰어난 성능을 보이고 있지만, 대부분의 정형 데이터 분류 시에는 앙상블이 뛰어난 성능을 나타내고 있습니다.

  * 문제와 데이터에 따라 단일 모델의 성능이 더 좋은 경우도 있습니다. 하지만 앙상블 기법을 사용하면 더 유연성있는 모델을 만들며 더 좋은 예측 결과를 기대할 수 있습니다.

  * Classification에서 앙상블 학습의 유형은 가장 많이 알려진 Voting, Bagging, Boosting, Stacking 등이 있으며, 그 외에도 다양한 앙상블 학습의 유형이 있습니다.

    #### 5-1-1. Voting

    * 보팅(Voting) 또는 에버리징(Averaging) 은 가장 쉬운 앙상블 기법입니다. 이 둘은 서로 다른 알고리즘을 가진 분류기를 결합하는 방식입니다.

    * 다음과 같은 과정으로 진행이 됩니다.

      1. 일정 수의 base model과 predict를 만듭니다.
         - Training 데이터를 나누어 같은 알고리즘을 사용하거나
         - Training 데이터는 같지만 다른 알고리즘을 사용하거나
      2. Voting을 진행합니다.
         - Majority Voting (Hard Voting) : 각 모델은 test 데이터셋(또는 인스턴스)의 결과를 예측합니다. 그리고 예측값들의 다수결로 예측값을 정합니다.
         - Weighted Voting (Soft Voting) : 각 모델은 Test 데이터셋의 결과 가능성을 예측합니다. 그리고 이 가능성에 특정 연산을 하여 분류 label의 확률값을 계산합니다. 이 방법에서 가중치의 연산은 원하는 방식으로 할 수 있고, 보통 평균을 사용합니다. 일반적으로 Majority Voting보다 유연한 결과를 얻을 수 있으며, 예측 성능이 좋아 더 많이 사용합니다.

      ```python
      from sklearn.ensemble import VotingClassifier
      ensemble_lin_rbf=VotingClassifier(estimators=[('KNN',KNeighborsClassifier(n_neighbors=10)),
                                                    ('RBF',svm.SVC(probability=True,kernel='rbf',C=0.5,gamma=0.1)),
                                                    ('RFor',RandomForestClassifier(n_estimators=500,random_state=0)),
                                                    ('LR',LogisticRegression(C=0.05)),
                                                    ('DT',DecisionTreeClassifier(random_state=0)),
                                                    ('NB',GaussianNB()),
                                                    ('svm',svm.SVC(kernel='linear',probability=True))
                                                   ], 
                             voting='soft').fit(train_X,train_Y)
      print('The accuracy for ensembled model is:',ensemble_lin_rbf.score(test_X,test_Y))
      cross=cross_val_score(ensemble_lin_rbf,X,Y, cv = 10,scoring = "accuracy")
      print('The cross validated score is',cross.mean())
      ```

    #### 5-1-2. Bagging

    * 배깅(Bagging) 은 Bootstrap Aggregating의 약자입니다. 배깅의 핵심은 평균을 통해 분산(variance)값을 줄여 모델을 더 일반화시킨다는 점입니다. 배깅은 Voting과 유사한 방식으로 진행이 됩니다. 정확히는 최종적으로는 Voting을 사용합니다.

      - 일정 수의 base model을 만듭니다.

      - 모델들의 알고리즘은 모두 같습니다.

      - 각각의 모델은 훈련데이터셋에서 랜덤으로 만든 서브 데이터셋을 각각 사용합니다.

      - 서브 데이터셋을 만드는 과정을 부트스트래핑(Bootstrapping) 분할 방식이라고 합니다. 각각의 서브 데이터셋은 중첩이 가능합니다.

      - 배깅의 경우에는 데이터 생성과 훈련이 개별 모델에서 진행되므로, 병렬 연산이 가능합니다.

      - **Bagged KNN**

        배깅(Bagging)은 high variance을 가진 모델에서 잘 작동합니다. 예를들어 Decision Tree 입니다. 또한 작은 **n_neighbours** 값을 가진 KNN 에서도 사용가능합니다.

        ```python
        from sklearn.ensemble import BaggingClassifier
        model=BaggingClassifier(base_estimator=KNeighborsClassifier(n_neighbors=3),random_state=0,n_estimators=700)
        model.fit(train_X,train_Y)
        prediction=model.predict(test_X)
        print('The accuracy for bagged KNN is:',metrics.accuracy_score(prediction,test_Y))
        result=cross_val_score(model,X,Y,cv=10,scoring='accuracy')
        print('The cross validated score for bagged KNN is:',result.mean())
        ```

      - **Bagged Decision Tree**

        ```python
        model=BaggingClassifier(base_estimator=DecisionTreeClassifier(),random_state=0,n_estimators=100)
        model.fit(train_X,train_Y)
        prediction=model.predict(test_X)
        print('The accuracy for bagged Decision Tree is:',metrics.accuracy_score(prediction,test_Y))
        result=cross_val_score(model,X,Y,cv=10,scoring='accuracy')
        print('The cross validated score for bagged Decision Tree is:',result.mean())
        ```

    #### 5-1-3. Boosting

    * 부스팅(Boosting) 알고리즘은 여러 개의 약한 학습기(weak learner = estimater)를 순차적으로 학습-예측하며 잘못 예측한 데이터에 가중치 부여를 통해 오류를 개선해 나가면서 학습하는 방식입니다.
    * 계속해서 분류기에게 가중치를 부스팅하면서 학습을 진행하기에 부스팅 방식으로 불립니다.
    * 기존 Boosting 방법은 순차적인 연산이 필수적이므로 병렬 연산이 불가능합니다. 그렇기에 대용량 데이터셋에서는 학습 시간이 매우 많이 필요할 수 있습니다.
    * 부스팅의 대표적인 알고리즘은 AdaBoost 와 Gradient Boost 가 있고, 최근 성능면에서 인정을 받아 가장 많이 사용하는 부스팅 계열 알고리즘으로 XGBoost 와 LightGBM 이 있습니다. 그 외에도 CatBoost와 같은 알고리즘이 있습니다.
    * **AdaBoost(Adaptive Boosting)**

    * **Stochastic Gradient Boosting**

    * **XGBoost**

    * **LightGBM(Light Gradient Boosting Methods)**

      

    #### 5-1-4. Stacking

    * stacked generalization 으로 알려진 기법입니다.

    * 현실 모델에 적용하는 경우는 적으나, Kaggle대회에서 높은 순위를 얻기 위해 많이 사용됩니다.

    * 가장 핵심 아이디어는 머신러닝 알고리즘으로 훈련 데이터셋을 통해 새로운 데이터셋을 만들고, 이를 데이터셋으로 사용하여 다시 머신러닝 알고리즘을 돌리는 것입니다. 보통은 서로 다른 타입의 모델들 을 결합합니다.

    * 스태킹에는 총 2가지 종류의 모델이 필요합니다.
      * 개별적인 기반 모델 : 성능이 비슷한 여러 개의 모델
      * 최종 메타 모델 : 기반 모델이 만든 예측 데이터를 학습 데이터로 사용할 최종 모델

    * 여러 개의 개별 모델들이 생성한 예측 데이터를 기반으로 최종 메타 모델이 학습할 별도의 학습 데이터 세트와 예측할 테스트 데이터 세트를 재 생성하는 기법입니다.

    * 모델을 통해 input을 만들고, 다시 모델에 넣는 구조때문에 meta-model 이라고도 부릅니다.

    

    #### 5-1-5. Hyper-Parameter Tuning for AdaBoost

    * AdaBoost 알고리즘이 가장 높은 Accuracy를 가진 Classifier로 선택되었으니 Hyper-Parameter Tuning 을 통해서 조금 더 성능 높혀보기

      ```python
      n_estimators=list(range(100,1100,100))
      learn_rate=[0.05,0.1,0.2,0.3,0.25,0.4,0.5,0.6,0.7,0.8,0.9,1]
      hyper={'n_estimators':n_estimators,'learning_rate':learn_rate}
      gd=GridSearchCV(estimator=AdaBoostClassifier(),param_grid=hyper,verbose=True)
      gd.fit(X,Y)
      print(gd.best_score_)
      print(gd.best_estimator_)
      ```

      

      

    ### 5-2. Feature Importance

  * 어떤 변수가 가장 영향을 많이 주었나?

    ```python
    f,ax=plt.subplots(2,2,figsize=(15,12))
    model=RandomForestClassifier(n_estimators=500,random_state=0)
    model.fit(X,Y)
    pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[0,0])
    ax[0,0].set_title('Feature Importance in Random Forests')
    model=AdaBoostClassifier(n_estimators=200,learning_rate=0.05,random_state=0)
    model.fit(X,Y)
    pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[0,1],color='#ddff11')
    ax[0,1].set_title('Feature Importance in AdaBoost')
    model=GradientBoostingClassifier(n_estimators=500,learning_rate=0.1,random_state=0)
    model.fit(X,Y)
    pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[1,0],cmap='RdYlGn_r')
    ax[1,0].set_title('Feature Importance in Gradient Boosting')
    model=xg.XGBClassifier(n_estimators=900,learning_rate=0.1)
    model.fit(X,Y)
    pd.Series(model.feature_importances_,X.columns).sort_values(ascending=True).plot.barh(width=0.8,ax=ax[1,1],color='#FD0F00')
    ax[1,1].set_title('Feature Importance in XgBoost')
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\35.PNG)

    * **관찰 결과**

      1. 공통적인 important feature는 'Initial', 'Fare_cat', 'Pclass', 'Family_Size' 입니다.

      2. 'Sex' feature 중요성이 높지 않아보이는데, 'Sex'는 'Pclass'와 함께 고려되었을 때 매우 좋은 차별적인 feature로 관찰되었었던 결과로 보았을 때 약간 놀랍습니다. 'Sex' feature는 Random Forests에서만 중요해 보입니다.

         'Initial'이 모든 Classifier의 상위 Importance feature로 올라와 있는 것을 볼 수 있는데, 이것은 'Sex'와 'Initial' 간에 Positive Relation이 있음이 이미 관찰되었기 때문에 'Initial'과 'Sex' feature 는 모두 의미적으로 성별을 나타낸다고 봐야 합니다.

      3. 'Pclass'와 'Fare_cat'은 의미적으로 승객의 상태를 나타내고, 'Family_Size'는 'Alone', 'Parch' 및 'SibSp'와 같은 의미를 나타낸다고 봐야 합니다.

  

  ### 5-3. Prediction

   * Making Prediction Result into CSV File

     ```python
     result = pd.DataFrame({
             "PassengerId": test_X.index,
             "Survived": Y_pred
         })
     result.to_csv('./data/titanic/result.csv', index=False)
     ```


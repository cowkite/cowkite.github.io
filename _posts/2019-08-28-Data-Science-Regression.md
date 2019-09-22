---
title: "Data Science - Regression (선형 회귀)"
date: 2019-08-28 10:18
categories: development python data-science
---



- Regression을 이용한 데이터 분석의 전체 workflow
- ML - Regression 알고리즘
- Ames 지역의 집 값을 예측해보자 (https://www.kaggle.com/c/house-prices-advanced-regression-techniques/overview)

## 1. Regression Overview

- 보스턴 데이터 가져오기 (보스턴 집값 구하는 로직은 가장 기존적인 예제라서 `sklearn` 패키지에 포함이 되어있음)

  ```python
  from sklearn.datasets import load_boston
  boston = load_boston()
  ```

- Import pandas 후 DataFrame에 넣어줌

  ```python
  import pandas as pd
  data = pd.DataFrame(boston.data, columns=boston.feature_names)
  data['MEDV'] = boston.target
  ```

- 25%는 테스트 데이터

  ```python
  from sklearn.model_selection import train_test_split
  train_data, test_data = train_test_split(data, test_size=0.25)
  ```

- 데이터를 새로운 파생변수에 넣어준다

  ```python
  RM_mean = train_data.RM.mean()
  RM_std = train_data.RM.std()
  
  MEDV_mean = train_data.MEDV.mean()
  MEDV_std = train_data.MEDV.std()
  
  processed_data = (train_data
                    .assign(RM_standard = lambda x: (x.RM - RM_mean)/RM_std)
                    .assign(MEDV_standard = lambda x: (x.MEDV - MEDV_mean)/MEDV_std)
                   )
  processed_data.head()
  ```

- 테이블 결과 확인

  ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/01.PNG?raw=true)

- 그래프 그리기

  ```python
  %matplotlib inline
  from matplotlib import pyplot as plt
  import numpy as np
  ```

  

  ### 1-1. 선형 회귀

  - 𝜃를 직접 정하지 않고 머신 러닝 알고리즘을 통해 학습하는 방법을 알아볼 것이다. 이 경우 가장 많이 쓰이는 알고리즘은 선형 회귀(linear regression)이다.

  - 설명변수(explanatory variable)와 목적변수(target variable) 사이에 선형 관계가 있다.

    - 독립변수(independent variable) = 설명변수(explanatory variable) = 입력값이나 원인을 나타낸다.
    - 종속변수(dependent variable) = 목적변수(target variable) = 결과값이나 효과를 나타낸다.

  - **가정**: RM(방의 갯수)와 MEDV(집값) 사이에 선형 관계가 있다.

    ```python
    def plot_h(theta_1):
        x = np.linspace(-3, 3)
        y_hat = theta_1 * x
        plt.scatter(x=processed_data.RM_standard, y=processed_data.MEDV_standard)
        plt.plot(x, y_hat, 'r');
    ```

    #### 1-1-1. Cost function

    - 좋은 𝜃θ를 찾기 위해서는, 어떤 𝜃θ가 좋은 𝜃θ인지 평가할 수 있어야 한다. Cost function은 모델이 얼마나 틀렸는지를 측정하는 함수이다. Loss function, Object function이라고 불리며 𝐽(𝜃)J(θ)로 표기한다.

    - 𝐽(𝜃)=12𝑛∑𝑖=1𝑛(ℎ𝜃(𝑥(𝑖))−𝑦(𝑖))2

      ```pythond
      def RSS(theta_1):
          y_hat = theta_1 * processed_data.RM_standard
          residual = processed_data.MEDV_standard - y_hat
          n = len(residual)
          rss = 1/(2*n) * (residual ** 2).sum()
          return rss
      ```

    #### 1-1-2. ℎ𝜃(𝑥)hθ(x)와 𝐽(𝜃)J(θ)의 차이

    - ℎ𝜃(𝑥)hθ(x)의 함수값은 𝜃θ가 고정되어 있고 𝑥x 따라 변한다. 이에 비해 𝐽(𝜃)J(θ)는 𝑥x와 𝑦y가 주어진 데이터로 고정되어 있고 𝜃θ에 따라 값이 변한다.

    - **𝐽(𝜃)J(θ)의 그래프**

      - 𝜃θ에 따라 변하는 𝐽(𝜃)J(θ)의 그래프를 그려보면 다음과 같다.

        ```python
        thetas = np.linspace(-3, 3)
        plt.plot(thetas, [RSS(theta) for theta in thetas]);
        ```

        ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/02.PNG?raw=true)

        ​	-> 0.7 ~ 0.8 사이에서 최소값이 있다는 것을 직관적으로 알 수 있다.

        - validation 데이터를 통해 하이퍼 파라미터를 구하고, training 데이터를 통해 파라미터를 구한다.

        - 위 그래프에서 정확한 최솟값을 구하기 위해서는 경사 하강법(Gradient Descent)을 사용해야한다.

          

    #### 1-1-3. Gradient Descent

    - 𝐽(𝜃)J(θ)를 극소화하는 𝜃θ를 자동으로 찾아주는 알고리즘이 경사 하강법 (Gradient Descent)이다.

    - **𝜃𝑗:=𝜃𝑗−𝛼∂∂𝜃𝑗𝐽(𝜃)**

      - 𝛼α : 학습 속도 (learning rate) 
        - 얼만큼 갈 것인지: 이걸 몇으로해야 (얼마나 작게줘야) 데이터가 최솟값에 수렴할지 고민하는 값임
        - 경사 하강법의 하강 속도를 조절하는 역할을 한다.
        - 이 값은 분석가가 직접 지정해주는 `hyperparameter`이다.
          - 𝛼α가 너무 클 때 : 경사 하강법이 수렴하지 못하고 발산하거나 진동한다.
          - 𝛼α가 너무 작을 때 : 경사 하강법이 수렴하는 데 너무 긴 시간이 걸린다. (=학습 속도가 느려진다.)
      - ∂∂𝜃𝑗𝐽(𝜃)∂∂θjJ(θ) : 기울기 (derivative) -> 방향

    - **극소점(Local Minimum)에 빠질 위험**

      - 경사 하강법은 최솟값을 찾는 절대적인 알고리즘은 아니다. 최솟값이 아닌 극소점 (local minimum), 즉 주변보다는 낮지만 가장 낮은 값은 아닌 지점에 빠질 위험이 있다.![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/03.PNG?raw=true)
      - 해결을 위해선 알파값을 크게 잡고, 점점 줄여나가는 방식을 사용
      - 수학적으로 역행렬을 사용하면 theta를 구할 수 있으나(𝜃=(𝑋𝑇𝑋)−1𝑋𝑇𝑦), 역행렬이 존재하지 않을 수도 있음.
        - 역행렬 (inverse matrix) 연산은 느리다.
        - (𝑋𝑇𝑋)(XTX)의 역행렬이 존재하지 않을 수 있다.
          - 쓸모 없는 (선형종속인) 변수들
          - 변수들이 너무 많을 때 (𝑛<𝑚n<m)

    - **선형 회귀를 위한 경사 하강법 (Gradient descent for linear regression)**

      ℎ𝜃(𝑥)=𝜃1𝑥hθ(x)=θ1x

      𝐽(𝜃)=𝐽(𝜃1)=12𝑛∑𝑖=1𝑛(ℎ𝜃(𝑥(𝑖))−𝑦(𝑖))2=12𝑛∑𝑖=1𝑛(𝜃1𝑥(𝑖)−𝑦(𝑖))2J(θ)=J(θ1)=12n∑i=1n(hθ(x(i))−y(i))2=12n∑i=1n(θ1x(i)−y(i))2

      ∂∂𝜃1𝐽(𝜃1)=1𝑛∑𝑖=1𝑛(𝜃1𝑥(𝑖)−𝑦(𝑖))𝑥(𝑖)∂∂θ1J(θ1)=1n∑i=1n(θ1x(i)−y(i))x(i)

      𝜃1:=𝜃1−𝛼∂∂𝜃𝑗𝐽(𝜃)=𝜃1−𝛼1𝑛∑𝑖=1𝑛(𝜃1𝑥(𝑖)−𝑦(𝑖))𝑥(𝑖)

      ```python
      x = processed_data.RM_standard
      y = processed_data.MEDV_standard
      n = len(x)
      alpha = 0.3
      theta_1 = initial_theta_1 = -0.2
      
      ```

      ```python
      plot_h(theta_1)
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/04.PNG?raw=true)

    - 다회 시도하여 수렴하는지 테스트

      ```python
      costs = []
      iterations = []
      for i in range(20):
          y_hat = theta_1 * x
          residual = y_hat - y
      
          cost = (residual ** 2).sum()/(2*n)
      
          gradient = 1/n * (residual*x).sum()
      
          theta_1 -= alpha * gradient
          
          print('theta_1 :', theta_1, 'Cost :', cost, 'gradient :', gradient)
          plot_h(theta_1)
          plt.show()
          
          iterations.append(i)
          costs.append(cost)
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/05.PNG?raw=true)

      -> *(이미지는 잘렸지만)* **theta 값이 점점 0.7에 가까워짐**

      ```python
      plt.plot(iterations, costs)
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/06.PNG?raw=true)

  

  ### 2-2. EDA

  - 통계적으로 얼마나 예측치가 있는지. 데이터를 시각화하는 과정.

  - 결국은 Preprocessing을 하기 위한 과정임

    #### 2-2-1. 문제 정의

    - 아이오와 주 에임스(Ames)에 있는 주거용 주택을 설명하는 79가지 변수로 각 주택의 최종 가격을 예측하기

    #### 2-2-2. 훈련, 테스트 데이터 확인하기

    - 데이터 확인

      ```python
      import numpy as np # linear algebra
      import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
      %matplotlib inline
      import matplotlib.pyplot as plt  # Matlab-style plotting
      import seaborn as sns
      color = sns.color_palette()
      sns.set_style('darkgrid')
      import warnings
      def ignore_warn(*args, **kwargs):
          pass
      warnings.warn = ignore_warn #ignore annoying warning (from sklearn and seaborn)
      
      
      from scipy import stats
      from scipy.stats import norm, skew #for some statistics
      
      
      pd.set_option('display.float_format', lambda x: '{:.3f}'.format(x)) #Limiting floats output to 3 decimal points
      
      
      from subprocess import check_output
      
      #Now let's import and put the train and test datasets in  pandas dataframe
      train = pd.read_csv('./data/ames_house_prices/train.csv')
      test = pd.read_csv('./data/ames_house_prices/test.csv')
      
      ##display the first five rows of the train dataset.
      train.head(5)
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/07.PNG?raw=true)

      -> *테스트 데이터는 레이블이 없어서 데이터 사이즈가 1 적음*

  

  ### 2-3. Preprocessing

  - Preprocessing 과정

    #### 2-3-1. Outliers (이상치)

    - Training 과정에서 성능에 영향을 주기 때문에 제거해야함

    - 데이터 관측

      ```python
      fig, ax = plt.subplots()
      ax.scatter(x = train['GrLivArea'], y = train['SalePrice'])
      plt.ylabel('SalePrice', fontsize=13)
      plt.xlabel('GrLivArea', fontsize=13)
      plt.show()
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/08.PNG?raw=true)

    - Training 데이터의 GrLivArea 중 오른쪽 하단에 이상치(outlier) 2개 삭제

      ```python
      #Deleting outliers
      train = train.drop(train[(train['GrLivArea']>4000) & (train['SalePrice']<300000)].index)
      
      #Check the graphic again
      fig, ax = plt.subplots()
      ax.scatter(train['GrLivArea'], train['SalePrice'])
      plt.ylabel('SalePrice', fontsize=13)
      plt.xlabel('GrLivArea', fontsize=13)
      plt.show()
      
      ```

      ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/09.PNG?raw=true)

    #### 2-3-2. Target Variable

    - 우편향 되어있는 변수 조정

      ```python
      #We use the numpy fuction log1p which  applies log(1+x) to all elements of the column
      train["SalePrice"] = np.log1p(train["SalePrice"])
      
      #Check the new distribution 
      sns.distplot(train['SalePrice'] , fit=norm);
      
      # Get the fitted parameters used by the function
      (mu, sigma) = norm.fit(train['SalePrice'])
      print( '\n mu = {:.2f} and sigma = {:.2f}\n'.format(mu, sigma))
      
      #Now plot the distribution
      plt.legend(['Normal dist. ($\mu=$ {:.2f} and $\sigma=$ {:.2f} )'.format(mu, sigma)],
                  loc='best')
      plt.ylabel('Frequency')
      plt.title('SalePrice distribution')
      
      ```

      

    #### 2-3-3. Features Engineering

    - **Missing Data**

      1. **Missing ratio 확인**

         ```python
         all_data_na = (all_data.isnull().sum() / len(all_data)) * 100
         all_data_na = all_data_na.drop(all_data_na[all_data_na == 0].index).sort_values(ascending=False)[:30]
         missing_data = pd.DataFrame({'Missing Ratio' :all_data_na})
         missing_data.head(20)
         
         ```

         ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/10.PNG?raw=true)

         -> 99%의 missing ratio를 가진 항목은 의미가 없는 데이터기 때문에, 특정 미싱률을 가지는 데이터는 삭제가 필요하다.

      2. **Data Correlation**

         종속 변수간의 어떤 relation ship이 있는지 확인

         ```python
         #Correlation map to see how features are correlated with SalePrice
         corrmat = train.corr()
         plt.subplots(figsize=(12,9))
         sns.heatmap(corrmat, vmax=0.9, square=True)
         
         ```

         ![](https://github.com/cowkite/cowkite.github.io/blob/master/_posts/assets/images/11.PNG?raw=true)

         -> 관련이 있으면 밝은색, 관련이 없으면 어두운색.

         -> 흰색의 대각선은 본인 데이터기 때문임

      3. **Missing values** 처리하기

         - 전체 데이터를 `N/A`로 처리하기

           ```python
           all_data["PoolQC"] = all_data["PoolQC"].fillna("None")
           
           ```

         - 중간 값으로 처리하기

           ```python
           all_data["LotFrontage"].fillna(all_data.groupby("Neighborhood")["LotFrontage"].transform("median"), inplace=True)
           
           ```

         - 데이터 제거하기 (데이터의 의미가 없는 경우)

           ```python
           all_data = all_data.drop(['Utilities'], axis=1)
           
           ```

         - 특정 열의 값으로 처리하기 (결측치가 적은 경우)

           ```python
           all_data['Electrical'] = all_data['Electrical'].fillna(all_data['Electrical'].mode()[0])
           
           ```

    - **Transforming some numerical variables to categorical**

      ```python
      #MSSubClass=The building class
      all_data['MSSubClass'] = all_data['MSSubClass'].apply(str)
      
      
      #Changing OverallCond into a categorical variable
      all_data['OverallCond'] = all_data['OverallCond'].astype(str)
      
      
      #Year and month sold are transformed into categorical features.
      all_data['YrSold'] = all_data['YrSold'].astype(str)
      all_data['MoSold'] = all_data['MoSold'].astype(str)
      
      ```

    - **Transforming some categorical variables to numerical**

      ```python
      from sklearn.preprocessing import LabelEncoder
      cols = ('FireplaceQu', 'BsmtQual', 'BsmtCond', 'GarageQual', 'GarageCond', 
              'ExterQual', 'ExterCond','HeatingQC', 'PoolQC', 'KitchenQual', 'BsmtFinType1', 
              'BsmtFinType2', 'Functional', 'Fence', 'BsmtExposure', 'GarageFinish', 'LandSlope',
              'LotShape', 'PavedDrive', 'Street', 'Alley', 'CentralAir', 'MSSubClass', 'OverallCond', 
              'YrSold', 'MoSold')
      # process columns, apply LabelEncoder to categorical features
      for c in cols:
          lbl = LabelEncoder() 
          lbl.fit(list(all_data[c].values)) 
          all_data[c] = lbl.transform(list(all_data[c].values))
      
      # shape        
      print('Shape all_data: {}'.format(all_data.shape))
      
      ```

    - **Normalizeing skewd features**

      ```python
      skewness = skewness[abs(skewness) > 0.75]
      print("There are {} skewed numerical features to Box Cox transform".format(skewness.shape[0]))
      
      from scipy.special import boxcox1p
      skewed_features = skewness.index
      lam = 0.15
      for feat in skewed_features:
          #all_data[feat] += 1
          all_data[feat] = boxcox1p(all_data[feat], lam)
          
      #all_data[skewed_features] = np.log1p(all_data[skewed_features])
      
      ```

      -> *Box-Cox Transformation of (highly) skewed features* (http://onlinestatbook.com/2/transformations/box-cox.html)https://datascienceschool.net/view-notebook/3f485c426a4b49fc9de95a02137ca6b4/)

    - **Scaling features**

    - **Add/Drop more features**

      - Check correlation.
      - Adding one more important feature.
      - Getting dummy categorical features.
      - Getting the new train and test sets.

      

  ### 2-4. Model Selection

  - 어려운.. 과정이.. 아니랍니다.. import 받은 것 호출만 하면 됨

    #### 2-4-1. Import libratries

    - 필요한 항목들 import

      ```python
      from sklearn.linear_model import ElasticNet, Lasso,  BayesianRidge, LassoLarsIC
      from sklearn.ensemble import RandomForestRegressor,  GradientBoostingRegressor
      from sklearn.kernel_ridge import KernelRidge
      from sklearn.pipeline import make_pipeline
      from sklearn.preprocessing import RobustScaler
      from sklearn.base import BaseEstimator, TransformerMixin, RegressorMixin, clone
      from sklearn.model_selection import KFold, cross_val_score, train_test_split
      from sklearn.metrics import mean_squared_error
      import xgboost as xgb
      import lightgbm as lgb
      
      ```

    #### 2-4-2. Define a cross validation strategy

    - 훨씬 성능이 좋아짐

    - `rms`: 성능을 측정하는데 가장 많이 사용. 여기서는 cross validation의 성능을 측정하기 위해 사용한다.

      ```python
      #Validation function
      n_folds = 5
      
      def rmsle_cv(model):
          kf = KFold(n_folds, shuffle=True, random_state=42).get_n_splits(train.values)
          rmse= np.sqrt(-cross_val_score(model, train.values, y_train, scoring="neg_mean_squared_error", cv = kf))
          return(rmse)
      
      ```

    #### 2-4-3. Making Base Regression Model

    - **LASSO Regression**

      ```python
      lasso = make_pipeline(RobustScaler(), Lasso(alpha =0.0005, random_state=1))
      
      ```

    - **Eleastic Net Regression**

      ```python
      ENet = make_pipeline(RobustScaler(), ElasticNet(alpha=0.0005, l1_ratio=.9, random_state=3))
      
      ```

    - *(이하 생략)*

      

  ### 2-5. Model Evaluation & Prediction

  - 데이터 성능 향상 시키기

    #### 2-5-1. Base model Evaluation

    - **LASSO Regression**

      ```python
      score = rmsle_cv(lasso)
      print("\nLasso score: {:.4f} ({:.4f})\n".format(score.mean(), score.std()))
      
      ```

      *-> Lasso score: 0.1172 (0.0076)*

    - **Eleastic Net Regression**

      ```python
      score = rmsle_cv(ENet)
      print("ElasticNet score: {:.4f} ({:.4f})\n".format(score.mean(), score.std()))
      
      ```

      *-> ElasticNet score: 0.1172 (0.0077)*

    - *(이하 생략)*

    #### 2-5-2. Emsembling

    - *앙상블(Ensemble) 학습은 여러 개의 학습 알고리즘을 사용하고, 그 예측을 결합함으로써 보다 정확한 최종 예측을 도출하는 기법입니다. 하나의 강한 머신러닝 알고리즘보다 여러 개의 약한 머신러닝 알고리즘이 낫다 라는 아이디어를 가지고 이해하면 좋습니다. 이미지, 영상, 음성 등의 비정형 데이터의 분류는 딥러닝이 뛰어난 성능을 보이고 있지만, 대부분의 정형 데이터 분류 시에는 앙상블이 뛰어난 성능을 나타내고 있습니다. 문제와 데이터에 따라 단일 모델의 성능이 더 좋은 경우도 있습니다. 하지만 앙상블 기법을 사용하면 더 유연성있는 모델을 만들며 더 좋은 예측 결과를 기대할 수 있습니다. Regression에서 앙상블 학습의 유형은 가장 많이 알려진 Averaging, Stacking 등이 있으며, 그 외에도 다양한 앙상블 학습의 유형이 연구되고 있습니다.*

    - 실제 서비스할 때는 의미 없는 (0.0001%) 성능 차이임. *...강사님 피셜 kaggel 대회용으로만 사용할듯한다고함...*

    - **Averaging**

      *에버리징(Averaging)은 가장 쉬운 앙상블 기법입니다. 서로 다른 알고리즘을 가진 Regression 모델를 결합하는 방식입니다.*

      1. *Simple Averaging*

         *회귀 문제에서 사용하는 방법으로, 각 예측값을 평균내어 사용합니다. 이 방법은 경우에 따라 과대적합을 줄여주고, 더 부드러운 회귀모델을 만들어줍니다.*

      2. *Weighted Averaging*

         *위에서 평균을 낼 때, 각 모델별 가중치를 두어 평균내는 방식입니다.*

    - **Stacking**

      *In this approach, we add a meta-model on averaged base models and use the out-of-folds predictions of these base models to train our meta-model.*

      *The procedure, for the training part, may be described as follows:*

      *Split the total training set into two disjoint sets (here train and .holdout )*

      *Train several base models on the first part (train)*

      *Test these base models on the second part (holdout)*

      *Use the predictions from 3) (called out-of-folds predictions) as the inputs, and the correct responses (target variable) as the outputs to train a higher level learner called meta-model.*

      *The first three steps are done iteratively . If we take for example a 5-fold stacking , we first split the training data into 5 folds. Then we will do 5 iterations. In each iteration, we train every base model on 4 folds and predict on the remaining fold (holdout fold).*

      *So, we will be sure, after 5 iterations , that the entire data is used to get out-of-folds predictions that we will then use as new feature to train our meta-model in the step 4.*

      *For the prediction part , We average the predictions of all base models on the test data and used them as meta-features on which, the final prediction is done with the meta-model.*

    #### 2-5-3. Prediction

    - Ensemble prediction
    - Making Prediction Result into CSV File

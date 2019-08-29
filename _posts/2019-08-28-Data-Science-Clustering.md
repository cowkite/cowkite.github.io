----

title: "Data Science - Clustering"

date: 2019-08-28 16:20

categories: development python data-science

------



## 1. Clustering Overview

* Clustering 개요

  ### 1-1. 클러스터링이란?

  * **클러스터 (Cluster)**

    * 데이터 포인트들(data objects)의 모임
      - 같은 그룹(or cluster) 안에서는 서로 비슷하고
      - 서로 다른 그룹 사이에는 비슷하지 않다

  * **클러스터링 (Clustering, cluster analysis, data segmentation)**

    - 데이터 포인트들을 클러스터들로 나누는 것
    - 즉, 데이터셋에서 하위그룹(subgroup)을 찾는 것이다.

  * **분류 vs 클러스터링**

    - 클러스터링 : 비지도 학습 (unsupervised learning) - **레이블이 없음**
    - 분류 : 지도 학습 (supervised learning)

  * **클러스터링은 언제 사용하나요**

    - 데이터에서 인사이트를 얻기 위해
      - 데이터의 요약
    - 다른 데이터 마이닝 작업의 중간 과정으로
      - feature extraction
      - outlier detection. 어떤 클러스터에서도 멀리 떨어져 있는 데이터들
    - 데이터 압축
      - 이미지 압축 등
    - 추천 시스템
      - 비슷한 유저나 비슷한 제품을 찾기
    - 비즈니스
      - market segmentation

  * **클러스터링에서 어려운 점**

    - Quality

      - numerical, categorical, text, multimedia, networks 등 다양한 형식의 속성들 처리
      - 불규칙한 모양의 클러스터 발견하기
      - 노이즈가 있는 데이터 다루기

    - Scalability

      - 샘플이 아닌 모든 데이터 클러스터링 하기
      - 높은 차원 다루기
      - 점진적(incremental or stream) 입력 다루기. 입력 순서에 민감하지 않기

    - Constraint-based clustering

      - User-given preferences or constraints

    - 해석가능성과 사용가능성(interpretability and usability)

    - **재현성(reproducability)**

      

  ### 1-2. 클러스터링 기법들 (Clustering methodologies)

  * K-means clustering

    - 미리 정해진 숫자의 클러스터로 클러스터링을 한다.

  * Hierarchical clustering *<- 이게 조금 더 낫다*

    - 몇 개의 클러스터를 원하는지 모를 때
    - 데이터가 dendrogram이라는 트리 모양으로 나타내진다.
    - 그 뒤 몇 개의 클러스터를 원하는지 선택한다.

    #### 1-2-1. K-means clustering

    * **알고리즘**

      * 먼저 원하는 클러스터의 개수 K를 고른다.
      * 각각의 데이터 포인트마다 무작위로 1부터 K까지의 클러스터 번호를 지정한다. (초기화)
      * 클러스터 할당(assignment)이 바뀌지 않을 때까지 다음을 반복한다.
        - 각각의 클러스터에 속한 데이터 포인트들을 평균내어, 클러스터의 중심(centroid)을 계산한다.
        - 각각의 데이터 포인트마다 가장 가까운 중심(centroid)를 가진 클러스터에 할당한다.

      ![](D:\soyeon.kim\2. 개인\images\36.PNG)

      ![](D:\soyeon.kim\2. 개인\images\37.PNG)

    * **초기화 방법**

      * 무작위로 공간 상에서 중심(centroid)를 지정하기
      * 무작위로 K개의 데이터 포인트를 골라서 각각의 클러스터 중심으로 지정하기

    * **특징**

      * 모든 데이터 포인트는 K개 클러스터 중에 하나에 속한다.
      * 어떤 데이터 포인트도 두 개 이상의 클러스터에 속할 수 없다.
      * 랜덤성이 있다.
      * 클러스터 사이의 순서는 없다.

    * **수학적 인사이트**

      * 좋은 클러스터링 : 클러스터 내의 분산(within-cluster variation)이 작은 클러스터링

        𝑚𝑖𝑛𝑖𝑚𝑖𝑧𝑒𝐶1,...,𝐶𝐾∑𝑘=1𝐾𝑊(𝐶𝑘)minimizeC1,...,CK∑k=1KW(Ck)

        𝑊(𝐶𝑘)=1|𝐶𝑘|∑𝑖,𝑖′∈𝐶𝑘∑𝑗=1𝑝(𝑥𝑖𝑗−𝑥𝑖′𝑗)2W(Ck)=1|Ck|∑i,i′∈Ck∑j=1p(xij−xi′j)2

        - 𝑊(𝐶𝑘)W(Ck)는 k번째 클러스터의 클러스터 내 분산(within-cluster variation)
        - |𝐶𝑘||Ck|는 k번째 클러스터에 속하는 데이터 포인트의 개수
        - k번째 클러스터 내 분산은 k번째 클러스터에 속하는 모든 데이터 포인트들의 쌍 사이의 거리를 더하고 데이터의 개수로 나눈 것이다.

    * **클러스터 내 분산을 최소화하는 클러스터링 찾기**

      - 매우 어려운 문제이다.
      - n개의 데이터포인트를 K개의 클러스터로 나누는 데는 K의 n승 개의 방법이 있기 때문
      - K-means 알고리즘은 local optimum을 찾아주는 근사 알고리즘이다.
      - 알고리즘 특징 : 매번 iteration을 할 때마다, 𝑊(𝐶𝑘)W(Ck)는 줄어들기만 할 뿐 증가하지 않는다.

    * **k-means 알고리즘을 여러번 해서 서로 다른 결과를 얻었다면**

      - 클러스터 내 분산이 작은 쪽을 선택한다.

        ![](D:\soyeon.kim\2. 개인\images\38.PNG)

    

    #### 1-2-2. 계층형 클러스터링 (Hierarchical Clustering)

    * **알고리즘**

      * 데이터 포인트를 각각 데이터 1개를 갖고 있는 클러스터라고 본다.
      * 더 이상 할 게 없을 때까지 다음을 반복한다.
        - 지금 있는 모든 클러스터들 간의 거리(dissimmilarities)를 계산한다.
        - 가장 거리가 작은 두 클러스터를 합친다. (이 때 두 클러스터 간의 거리가 dendrogram에서 세로축의 값이 된다.)

      ![](D:\soyeon.kim\2. 개인\images\39.PNG)

      ![](D:\soyeon.kim\2. 개인\images\40.PNG)

    * **특징**

      * 계층형 클러스터링 (Hierarchical clustering)은 클러스터 개수를 미리 지정하지 않아도 된다.
      * 데이터를 트리 모양으로 나타내는 dendrogram을 만들어낸다.

    * **Dendrogram**

      ![](D:\soyeon.kim\2. 개인\images\41.PNG)

      ![](D:\soyeon.kim\2. 개인\images\42.PNG)

    * **Dendogram 해석**

      * Dendrogram의 '잎'은 각각의 데이터 포인트들이다.
      * 트리 위로 올라가며 가장 가까운 데이터 포인트들끼리 합쳐져서 가지(branch)를 만든다.
      * 더 위로 올라가면 가지(branch)들이 다른 잎(leave)이나 가지(branch)와 합쳐진다.
      * 가까운 가지일 수록 합쳐지는 게 더 빨리 (tree 아래 부분에서) 일어난다.
      * 즉, 나중에 (tree의 윗부분에서) 합쳐질 수록 두 가지가 다르다는 것 뜻한다.

    * **Dendrogram 기반으로 클러스터링하기**

      - 수평선 방향으로 어디에서 자를지 결정하면 클러스터링이 결정된다.
      - 즉, 어느 거리(distance) 이하의 가지까지만 합쳐질 수 있게 하면 클러스터링이 결정된다.
      - 하나의 dendrogram에서 어떤 개수의 클러스터도 구할 수 있다.
      - dendrogram을 눈으로 보고 그럴 듯한 클러스터 개수를 찾아낼 수 있는 것이 장점이다.

    * **왜 Hierarchical 이라고 하나요?**

      - 클러스터 개수를 줄여갈 수록, 항상 기존 클러스터가 합쳐져서 더 큰 클러스터를 형성한다.

      - 클러스터 개수를 늘여갈 수록, 항상 기존 클러스터가 나눠져서 더 작은 클러스터가 된다.

        ![](D:\soyeon.kim\2. 개인\images\43.PNG)

    * **Dendrogram을 해석할 때 흔히 하는 실수**

      - 위 그림에서, 5와 7은 상당히 비슷하다. (맞음)
      - 1과 6도 상당히 비슷하다. (맞음)
      - 9와 2는 상당히 비슷하다. (틀림)
      - 9는 8, 5, 7보다 2에 더 가깝지 않다.
      - dendrogram을 그릴 때 leave의 순서는 중요하지 않기 때문에, 가로축 방향으로 얼마나 가까운지는 의미가 없다.
      - 오직 두 가지(branches)가 합쳐지는 순간의 세로축의 값(distance)이 두 그룹이 얼마나 비슷한지를 알려주는 척도이다.

    * **거리를 어떻게 측정할 것인가**

      두 가지 거리를 정의해야 한다.

      1. **두 데이터 포인트 사이의 거리(dissimilarity)**

         - 보통 유클리드 거리(Euclidean distance)가 쓰인다.
         - 다른 거리도 쓸 수 있다.(Correlation-based distance, Manhattan distance, Minkowski distance)

         ![](D:\soyeon.kim\2. 개인\images\44.PNG)

      2. **두 클러스터(cluster) 사이의 거리(dissimilarity)**

         - 거리(dissimilarity)의 일반화된 개념이다.

         - linkage라고 부른다.

           ![](D:\soyeon.kim\2. 개인\images\45.PNG)

         - **Linkage의 종류**

           - Complete

             - 최대 클러스터간 거리 (Maximal intercluster dissimilarity). A 클러스터에 속한 점들과 B 클러스터에 속한 점들 사이의 거리를 모두 계산해서, 가장 큰 거리가 두 클러스터 사이의 거리가 된다.

               ![](D:\soyeon.kim\2. 개인\images\46.PNG)

           - Single

             - 최소 클러스터간 거리 (Minimal intercluster dissimilarity). 가장 작은 거리가 두 클러스터 사이의 거리가 된다. 이 방식으로 하면 보통 한 클러스터가 계속해서 커지는 결과가 나온다.

               ![](D:\soyeon.kim\2. 개인\images\47.PNG)

           - Average

             - 평균 클러스터간 거리 (Average intercluster dissimilarity). 두 클러스터에 속한 점들 사이 거리를 구해서 평균 낸다.

               ![](D:\soyeon.kim\2. 개인\images\48.PNG)

           - Centroid

             - 중심(centroid) 사이의 거리. 중심은 그 클러스터에 속한 점들의 평균으로 결정된다.

               ![](D:\soyeon.kim\2. 개인\images\49.PNG)

           - Median

             - 두 클러스터간 거리를 두 클러스터의 모든 멤버간 거리의 중앙값(median)으로 봅니다.

           - Ward

             - 두 클러스터간 거리를 두 클러스터의 모든 멤버간 거리의 평균(median)으로 보는데, 단, 공분산을 고려한 보정을 해줍니다.
             - 이것은 원형으로 데이터가 분산되어 있다면 euclidean distance가 적절하지만 타원형이라면 variable간 공분산을 고려해야 하기 때문입니다.

           - **Average나 complete가 가장 자주 쓰인다.**

             - 더 균형잡힌 클러스터가 만들어진다.
             - single은 보통 한 클러스터가 계속 커지는 문제가 있다.
             - centroid는 inversion이라는 문제가 있다. 즉, dendrogram 상에서 두 클러스터가 각각의 클러스터보다 낮은 높이에서 합쳐지는 것이다. dendrogram을 그릴 때 문제가 생긴다.

    #### 1-2-3. K-means vs. Hierarchical clustering

    * 계층형 클러스터링의 장점

      * 클러스터 개수를 미리 결정하지 않아도 된다.
      * dendrogram으로 데이터를 시각화할 수 있다.
      * 랜덤성이 없다.

    * K means의 장점

      * 상대적으로 빠르다.

      * 어떤 데이터셋에서는 큰 클러스터링이 작은 클러스터링을 포함하는 것이 부적절할 수 있다.

        - 예를 들어, 남녀 비율이 50 대 50 이며 미국인, 일본인, 프랑스인 세 인종으로 삼등분 되어 있는 사람들의 경우 2개로 나눌 때와 3개로 나눌 때 가장 적절한 클러스터링이 다를 것이다.

      * K-means는 보통 주어진 클러스터 개수에 대해서 더 나은 결과를 낳는다고 알려져 있다.

        

  ### 1-3. 클러스터링 평가하기 (Clustering Evaluation)

  * 적절한 클러스터 개수 구하기

    #### 1-3-1. **Elbow method**

    * cost의 감소 속도가 줄어드는 지점에서 끊기

      ![](D:\soyeon.kim\2. 개인\images\50.PNG)

    * **알고리즘**

      * 그래프를 보면 처음에는 급격히 cost가 감소하다가 어느 순간 정체된다.
      * 감소세가 정체되는 지점에서 클러스터 개수를 정하자

    * **한계**

      * 감소세가 정체되는 부분이 언제인지 결정하는 것은 주관적이다

      * 감소분이 최소인 지점이 항상 가장 좋은 클러스터링은 아니다.

        

    ### 1-3-2. Silhouette

    실루엣 값은 한 클러스터 안의 데이터들이 다른 클러스터와 비교해서 얼마나 비슷한가를 나타낸다.

    - 클러스터 안의 거리가 짧을 수록 좋고(cohesion) 다른 클러스터와의 거리는 멀수록 좋다.(separation)

    - 데이터 포인트 별로 값을 구할 수 있다.

    - 실루엣은 -1부터 1 사이의 값을 가진다.

    - 높을 수록 좋다.

    - 어떤 클러스터링 알고리즘이든지 구할 수 있다.

    - **계산 방법**

      - i번째 데이터 포인트에 대해서
      - a(i)는 같은 클러스터 안에 있는 다른 데이터 포인트와의 평균 거리(dissimilarity)
        - a(i)는 i번째 데이터 포인트가 클러스터에 얼마나 잘 맞는지 측정한다.
        - 낮을 수록 더 잘 속해있는 것
      - b(i)는 i가 속하지 않은 다른 클러스터와의 평균 거리 중 가장 작은 거리이다.
        - 내가 속하지 않은 가장 가까운 클러스터, 즉 이웃 클러스터(neighbouring cluster)와의 거리

    - **실루엣 정의**

      ![](D:\soyeon.kim\2. 개인\images\51.png)

      - b(i)가 a(i)보다 많이 클수록 1에 가까워지고, 좋다.

      - 0이면 지금 클러스터나 이웃 클러스터나 어디 있든 상관 없다는 얘기

        

  ### 1-4. Clustering 실습

  ```python
  import numpy as np
  # generate two clusters: a with 100 points, b with 50:
  np.random.seed(4711)  # for repeatability of this tutorial
  a = np.random.multivariate_normal([10, 0], [[3, 1], [1, 4]], size=[100,])
  b = np.random.multivariate_normal([0, 20], [[3, 1], [1, 4]], size=[50,])
  X = np.concatenate((a, b),)
  
  from matplotlib import pyplot as plt
  %matplotlib inline
  
  plt.scatter(X[:,0], X[:,1])
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\52.PNG)
  * **Hierarchical**

    ```python
    from sklearn.cluster.hierarchical import AgglomerativeClustering
    AgglomerativeClustering??
    ```

    * **Dendrogram**

      ```python
      from scipy.cluster.hierarchy import dendrogram, linkage
      Z = linkage(X, 'ward')
      ```

      *`ward`는 클러스터 내 총 분산(total within-cluster variance)을 최소화 시키는 linkage 방법*

  * **Z 들여다보기**

    ```python
    Z[:10].round(0)
    ```

    ![](D:\soyeon.kim\2. 개인\images\53.PNG)

    ```python
    plt.figure(figsize=(25, 10))
    plt.title('Hierarchical Clustering Dendrogram')
    plt.xlabel('sample index')
    plt.ylabel('distance')
    dendrogram(
        Z,
        leaf_rotation=90.,  # rotates the x axis labels
        leaf_font_size=8.,  # font size for the x axis labels
        color_threshold=5
    )
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\54.PNG)

  * **클러스터별 색깔 다르게 하기**

    `color_threshold`

    ```python
    plt.title('Hierarchical Clustering Dendrogram (truncated)')
    plt.xlabel('sample index')
    plt.ylabel('distance')
    dendrogram(
        Z,
        truncate_mode='lastp',  # show only the last p merged clusters
        p=12,  # show only the last pa merged clusters
        show_leaf_counts=False,  # otherwise numbers in brackets are counts
        leaf_rotation=90.,
        leaf_font_size=12.,
        show_contracted=True,  # to get a distribution impression in truncated branches
        color_threshold=50
    )
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\55.PNG)

  * **압축하기**

    ```python
    plt.title('Hierarchical Clustering Dendrogram (truncated)')
    plt.xlabel('sample index or (cluster size)')
    plt.ylabel('distance')
    dendrogram(
        Z,
        truncate_mode='lastp',  # show only the last p merged clusters
        p=12,  # show only the last p merged clusters
        leaf_rotation=90.,
        leaf_font_size=12.,
        show_contracted=True,  # to get a distribution impression in truncated branches
    )
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\56.PNG)

  * **merge 될 때 distance를 표시하기**

    ```python
    def fancy_dendrogram(*args, **kwargs):
        max_d = kwargs.pop('max_d', None)
        if max_d and 'color_threshold' not in kwargs:
            kwargs['color_threshold'] = max_d
        annotate_above = kwargs.pop('annotate_above', 0)
    
        ddata = dendrogram(*args, **kwargs)
    
        if not kwargs.get('no_plot', False):
            plt.title('Hierarchical Clustering Dendrogram (truncated)')
            plt.xlabel('sample index or (cluster size)')
            plt.ylabel('distance')
            for i, d, c in zip(ddata['icoord'], ddata['dcoord'], ddata['color_list']):
                x = 0.5 * sum(i[1:3])
                y = d[1]
                if y > annotate_above:
                    plt.plot(x, y, 'o', c=c)
                    plt.annotate("%.3g" % y, (x, y), xytext=(0, -5),
                                 textcoords='offset points',
                                 va='top', ha='center')
            if max_d:
                plt.axhline(y=max_d, c='k')
        return ddata
    
    fancy_dendrogram(
        Z,
        truncate_mode='lastp',
        p=12,
        leaf_rotation=90.,
        leaf_font_size=12.,
        show_contracted=True,
        annotate_above=10,  # useful in small plots so annotations don't overlap
    )
    plt.show()
    ```

  * **자르는 기준을 표시하기**

    ```python
    max_d = 20
    
    fancy_dendrogram(
        Z,
        truncate_mode='lastp',
        p=12,
        leaf_rotation=90.,
        leaf_font_size=12.,
        show_contracted=True,
        annotate_above=3,
        max_d=max_d,  # plot a horizontal cut-off line
    )
    plt.show()
    ```

  * **label 뽑아내기 - max distance로**

    ```python
    from scipy.cluster.hierarchy import fcluster
    max_d = 5
    clusters = fcluster(Z, max_d, criterion='distance')
    clusters
    ```

  * **label 뽑아내기 - 클러스터 개수로**

    ```python
    k=2
    fcluster(Z, k, criterion='maxclust')
    ```

  * **Evaluation**

    ```python
    import pandas as pd
    
    from sklearn.datasets import load_iris
    
    iris = load_iris()
    
    X = pd.DataFrame(iris.data, columns=iris.feature_names)
    X.head()
    ```

    ![](D:\soyeon.kim\2. 개인\images\57.PNG)

    ```python
    from sklearn.cluster import KMeans
    
    km = KMeans(n_clusters=8)
    km.fit(X)
    ```

    ![](D:\soyeon.kim\2. 개인\images\58.PNG)

    ```python
    from tqdm import tqdm_notebook
    
    scores = []
    iterations = range(1, 30)
    for i in tqdm_notebook(iterations):
        km = KMeans(n_clusters=i)
        km.fit(X)
        s = km.score(X)
        scores.append(s)
        
    from matplotlib import pyplot as plt
    %matplotlib inline
    
    plt.figure(figsize=(17,5))
    plt.plot(iterations, scores, 'b*-')
    ```

    ![](D:\soyeon.kim\2. 개인\images\59.PNG)

    ```python
    from sklearn.metrics import silhouette_score, silhouette_samples
    
    K = range(2,30)
    
    scores = []
    for k in tqdm_notebook(K):
        kmeans = KMeans(n_clusters=k)
        kmeans.fit(X)
    
        labels = kmeans.labels_
    #     centroids = kmeans.cluster_centers_
        score = silhouette_score(X, labels, metric='euclidean')
        scores.append(score)
    
    plt.plot(K, scores)
    plt.ylabel("Silouette")
    plt.xlabel("k")
    plt.title("Silouette for K-means cell's behaviour")
    ```

    ![](D:\soyeon.kim\2. 개인\images\60.PNG)



## 2. EDA

* dd

  ### 2-1. 문제 정의하기

  * Mall Customer Segmentation Data 데이터가 주어졌을 때, 고객을 분류한다.

  ### 2-2. 데이터 확인하기

  ```python
  import numpy as np # linear algebra
  import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
  import matplotlib.pyplot as plt 
  plt.style.use('fivethirtyeight')
  import seaborn as sns 
  import plotly as py
  import plotly.graph_objs as go
  from sklearn.cluster import KMeans
  import warnings
  import os
  warnings.filterwarnings("ignore")
  py.offline.init_notebook_mode(connected = True)
  
  df = pd.read_csv('./data/mall_customer_seg_data/Mall_Customers.csv')
  df.head()
  ```

  ![](D:\soyeon.kim\2. 개인\images\61.PNG)
  * **Histograms**

    ```python
    plt.figure(1 , figsize = (15 , 6))
    n = 0 
    for x in ['Age' , 'Annual Income (k$)' , 'Spending Score (1-100)']:
        n += 1
        plt.subplot(1 , 3 , n)
        plt.subplots_adjust(hspace =0.5 , wspace = 0.5)
        sns.distplot(df[x] , bins = 20)
        plt.title('Distplot of {}'.format(x))
    plt.show()
    ```

    ![](D:\soyeon.kim\2. 개인\images\62.PNG)



## 3. Preprocessing

*No need to preprocess*



## 4. Customer Segmentation

* Clustering using K-means

  ### 4.1 Segmentation using Age and Spending Score

  ```python
  '''Age and spending Score'''
  X1 = df[['Age' , 'Spending Score (1-100)']].iloc[: , :].values
  inertia = []
  for n in range(1 , 11):
      algorithm = (KMeans(n_clusters = n ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
      algorithm.fit(X1)
      inertia.append(algorithm.inertia_)
  ```

  *Selecting N Clusters based in Inertia (관성, Squared Distance between Centroids and data points, should be less)*

  ```python
  plt.figure(1 , figsize = (15 ,6))
  plt.plot(np.arange(1 , 11) , inertia , 'o')
  plt.plot(np.arange(1 , 11) , inertia , '-' , alpha = 0.5)
  plt.xlabel('Number of Clusters') , plt.ylabel('Inertia')
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\63.PNG)

  ```python
  algorithm = (KMeans(n_clusters = 4 ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
  algorithm.fit(X1)
  labels1 = algorithm.labels_
  centroids1 = algorithm.cluster_centers_
  ```

  ```python
  h = 0.02
  x_min, x_max = X1[:, 0].min() - 1, X1[:, 0].max() + 1
  y_min, y_max = X1[:, 1].min() - 1, X1[:, 1].max() + 1
  xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))
  Z = algorithm.predict(np.c_[xx.ravel(), yy.ravel()]) 
  ```

  ```python
  plt.figure(1 , figsize = (15 , 7) )
  plt.clf()
  Z = Z.reshape(xx.shape)
  plt.imshow(Z , interpolation='nearest', 
             extent=(xx.min(), xx.max(), yy.min(), yy.max()),
             cmap = plt.cm.Pastel2, aspect = 'auto', origin='lower')
  
  plt.scatter( x = 'Age' ,y = 'Spending Score (1-100)' , data = df , c = labels1 , 
              s = 200 )
  plt.scatter(x = centroids1[: , 0] , y =  centroids1[: , 1] , s = 300 , c = 'red' , alpha = 0.5)
  plt.ylabel('Spending Score (1-100)') , plt.xlabel('Age')
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\64.PNG)

  

  ### 4-2. Segmentation using Annual Income and Spending Score

  ```python
  '''Annual Income and spending Score'''
  X2 = df[['Annual Income (k$)' , 'Spending Score (1-100)']].iloc[: , :].values
  inertia = []
  for n in range(1 , 11):
      algorithm = (KMeans(n_clusters = n ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
      algorithm.fit(X2)
      inertia.append(algorithm.inertia_)
  ```

  ```python
  plt.figure(1 , figsize = (15 ,6))
  plt.plot(np.arange(1 , 11) , inertia , 'o')
  plt.plot(np.arange(1 , 11) , inertia , '-' , alpha = 0.5)
  plt.xlabel('Number of Clusters') , plt.ylabel('Inertia')
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\65.PNG)

  ```python
  algorithm = (KMeans(n_clusters = 5 ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
  algorithm.fit(X2)
  labels2 = algorithm.labels_
  centroids2 = algorithm.cluster_centers_
  ```

  ```python
  h = 0.02
  x_min, x_max = X2[:, 0].min() - 1, X2[:, 0].max() + 1
  y_min, y_max = X2[:, 1].min() - 1, X2[:, 1].max() + 1
  xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))
  Z2 = algorithm.predict(np.c_[xx.ravel(), yy.ravel()]) 
  ```

  ```python
  plt.figure(1 , figsize = (15 , 7) )
  plt.clf()
  Z2 = Z2.reshape(xx.shape)
  plt.imshow(Z2 , interpolation='nearest', 
             extent=(xx.min(), xx.max(), yy.min(), yy.max()),
             cmap = plt.cm.Pastel2, aspect = 'auto', origin='lower')
  
  plt.scatter( x = 'Annual Income (k$)' ,y = 'Spending Score (1-100)' , data = df , c = labels2 , 
              s = 200 )
  plt.scatter(x = centroids2[: , 0] , y =  centroids2[: , 1] , s = 300 , c = 'red' , alpha = 0.5)
  plt.ylabel('Spending Score (1-100)') , plt.xlabel('Annual Income (k$)')
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\66.PNG)

  

  ### 4-3. Segmentation using Age , Annual Income and Spending Score

  ```python
  X3 = df[['Age' , 'Annual Income (k$)' ,'Spending Score (1-100)']].iloc[: , :].values
  inertia = []
  for n in range(1 , 11):
      algorithm = (KMeans(n_clusters = n ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
      algorithm.fit(X3)
      inertia.append(algorithm.inertia_)
  ```

  ```python
  plt.figure(1 , figsize = (15 ,6))
  plt.plot(np.arange(1 , 11) , inertia , 'o')
  plt.plot(np.arange(1 , 11) , inertia , '-' , alpha = 0.5)
  plt.xlabel('Number of Clusters') , plt.ylabel('Inertia')
  plt.show()
  ```

  ![](D:\soyeon.kim\2. 개인\images\67.png)

  ```python
  algorithm = (KMeans(n_clusters = 6 ,init='k-means++', n_init = 10 ,max_iter=300, 
                          tol=0.0001,  random_state= 111  , algorithm='elkan') )
  algorithm.fit(X3)
  labels3 = algorithm.labels_
  centroids3 = algorithm.cluster_centers_
  ```

  ```python
  df['label3'] =  labels3
  trace1 = go.Scatter3d(
      x= df['Age'],
      y= df['Spending Score (1-100)'],
      z= df['Annual Income (k$)'],
      mode='markers',
       marker=dict(
          color = df['label3'], 
          size= 20,
          line=dict(
              color= df['label3'],
              width= 12
          ),
          opacity=0.8
       )
  )
  data = [trace1]
  layout = go.Layout(
  #     margin=dict(
  #         l=0,
  #         r=0,
  #         b=0,
  #         t=0
  #     )
      title= 'Clusters',
      scene = dict(
              xaxis = dict(title  = 'Age'),
              yaxis = dict(title  = 'Spending Score'),
              zaxis = dict(title  = 'Annual Income')
          )
  )
  fig = go.Figure(data=data, layout=layout)
  py.offline.iplot(fig)
  ```

  ![](D:\soyeon.kim\2. 개인\images\68.PNG)
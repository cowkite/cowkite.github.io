----

title: "딥러닝을 위한 TPU 사용하기"

date: 2019-09-24 08:46

categories: development deep-learning

----



## TPU

Tensor Processing Unit의 줄임말로 구글에서 2016년 5월에 발표한 데이터 분석 및 딥러닝용 하드웨어이다.  관련하여 Google 엔지니어인 *Norman P. Jouppi*가 저술한 논문 [(In-Datacenter Performance Analysis of a Tensor Processing Unit)](https://arxiv.org/ftp/arxiv/papers/1704/1704.04760.pdf)도 있다.

3개월치 데이터로 보름간의 수요를 예측하는 간단한 DL도, CPU에서는 600sku 기준으로 4시간씩 소요가 되었다. 
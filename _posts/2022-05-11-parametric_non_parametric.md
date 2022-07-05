---
layout: post
title:  "Parametric vs Non-Parametric"
categories: [ datascience ]
tags: [ science ]
fullview: false
comments: true
use_math: true
---

*모수적 방법과 비모수적 방법의 차이가 무엇일까?*

---

# Density Estimation

데이터는 어떤 변수가 가질 수 있는 다양한 가능성 중에서 하나가 현실 세계에 구체화된 값이다[1]. 관측된 데이터만을 가지고 그 변수가 가질 가능성에 대한 특성을 파악하고자 하는 것이 밀도 추정(Densitiy Estimation)이다. 

가능성이라는 것은 그 변수가 어떤 값을 가질 확률이다. 에를 들어, 톨게이트를 지나는 일일 차량 대수를 파악하고자 한다고 해보자. 이 때 변수는 '일일 차량 대수'이다. 실제로 매일 매일 차량 대수를 관찰해보면 500대, 200대, 300대, ... 서로 다른 데이터가 나올 것이다. 그렇기 때문에 하루 이틀 관찰한 결과로는 결론을 내리기가 힘들다. 하지만 1년이 지나고, 몇 넌이 지나면 '일일 차량 대수'라는 변수가 어떤 분포를 가지는지 파악할 수 있을 것이다.

<br/>
<br/>

# Parametric vs Non-Parametric

이러한 분포를 추정하는 데에는 두 가지 방법이 있다. Parametric과 Non-Parametric이다. 각각 모수적 방법론/비모수적 방법론이라고 한다. 

모수적 밀도 추정은 미리 pdf에 대한 모델을 정해놓고 데이터로부터 모델의 파라미터만 추정하는 방식이다. 이는 매우 빠르다. 비모수적 방법에 비해서 훨씬 적은 데이터가 필요하다. 예를 들어, '일일 차량 대수'가 정규 분포를 따른다고 가정하면, 데이터로부터 평균과 분산만 구하면 확률 분포를 만들어 낼 수 있다. 

하지만 이는 가정이 맞다는 전제 하에서만 가능한 일이다. 분포의 모델을 미리 안다는 것은 현실 문제에서 맞지 않을 수 있다. 회귀 분석을 아무 생각 없이 많이 해왔지만 3대 가정(독립성, 등분산성, 정규성)은 '퉁' 치고 넘어간다. 애초에 가정이 안 맞으면 뒤의 분석이 의미가 없음에도 많은 사람들이 '그렇다 치고' 하는 이유는 3대 가정에 맞는 데이터가 별로 없기 때문이다.

그래서 비모수적(Non-parametric) 방법론이 필요하다. 사전 정보나 지식 없이 순수하게 관측된 데이터만을 가지고 확률 분포를 추정해야 하는 것이다. 그렇기 때문에 모수적 방법에 비해서 매우 매우 많은 수의 데이터가 필요하다. 이에 대한 대표적인 예시가 히스토그램이다. 

<br/>

![img](https://gaussian37.github.io/assets/img/vision/opencv/pointprocessing/histogram/histgray.png)

<br/>

# Kernel Density Estimation

히스토그램은 단점이 있다. x축의 bin을 어떻게 잡냐에 따라서 분포가 달라진다. 적정한 bin 크기를 알기 어렵기도 하지만 bin마다 불연속하기 때문에 확률 분포로 그대로 쓰기 어렵다는 점(미분이 불가능하기 때문에)이 있다. 그리고 위 그림은 1차원 데이터이지만, 고차원 데이터에는 메모리 문제 등으로 인해 사용하기도 어렵다.

Kernel Density Estimation (커널 밀도 추정)은 비모수적 방법론 중에 하나이다. 커널 함수를 이용해서 히스토그램의 단점을 극복하고자 한 방법이다. 

커널 함수는 '원점을 중심으로 대칭이고 면적이 1인 non-negative 함수'이다. 대표적으로는 Gaussian, Uniform 커널이 있다. 

<br/>


[위키피디아]
A Kernel is a **non-nagative real-valued integrable** function K. For most applications, it is desirable to define the function to satisfy two additional requirements[3]:

- Normalization: ![img](https://wikimedia.org/api/rest_v1/media/math/render/svg/58133348a2172dc741a7208339292866dac2d9a2)

- Symmetry: ![img](https://wikimedia.org/api/rest_v1/media/math/render/svg/e6c8121d4bacfdd92a526adcbf6d123727656677)


![img](https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/Kernels.svg/1000px-Kernels.svg.png)

X를 확률변수(Random variable), $\lbrace x_1, x_2, \cdots, x_n\rbrace$ 를 관측된 샘플 데이터, K를 커널 함수라고 하자. 이때 KDE(Kernel Density Estimation)에서는 확률 변수 X에 대한 pdf를 다음과 같이 추정한다.

![img](https://t1.daumcdn.net/cfile/tistory/261A943654F0325532)

위 식에서 h는 커널 함수의 bandwidth 파라미터이다. 커널 함수가 뾰족한 형태(h가 작은 값)인지 완만한 형태(h가 큰 값)인지를 조절하는 역할이다. 이를 직관적으로 해석하면 다음과 같다.

1. 각각의 관측된 데이터마다 해당 값을 중심으로 하는 커널 함수를 생성한다: $K(x-x_i)$ 
2. n개의 커널 함수들의 평균


![img](https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/Comparison_of_1D_histogram_and_KDE.png/1000px-Comparison_of_1D_histogram_and_KDE.png)

히스토그램을 이용한 밀도 추정 방법과 KDE 방법을 비교하면, 히스토그램은 이산적(discrete)으로 bin마다 불연속성이 생기지만, KDE는 각 데이터를 커널 함수로 대치하여 더함으로써 연속적(continuous)한 pdf를 얻을 수 있다.

즉 KDE는 히스토그램을 smoothing한 것이며, 그 정도는 어떤 커널함수를 쓰냐에 따라 달라질 수 있다.

![img](https://t1.daumcdn.net/cfile/tistory/2175743554F037C925)

실제 KDE를 사용할 때, 어떤 커널 함수를 사용할지, 커널 함수의 bandwidth 파라미터인 h 값을 어떻게 잡을지가 중요한 이슈라고 한다.

만약 가우시안 커널 함수를 쓴다고 하면 MSE를 최소화하는 최적의 h는 **A rule-of-thumb bandwidth estimator**에 의해 다음과 같다.

![img](https://wikimedia.org/api/rest_v1/media/math/render/svg/b1538fbae068efc2dc9ca34513760df51e492c28)



# 참고
- [1] [Kernel Density Estimation(커널밀도추정)에 대한 이해](https://darkpgmr.tistory.com/147)
- [2] [Parametric vs Non-Parametric Methods in Machine Learning](https://towardsdatascience.com/parametric-vs-non-parametric-methods-2cea475da1a)
- [3] [위키피디아 - Kernel (statistics)](https://en.wikipedia.org/wiki/Kernel_(statistics)#Kernel_functions_in_common_use)
- [4] [위키피디아 - Kernel density estimation](https://en.wikipedia.org/wiki/Kernel_density_estimation)
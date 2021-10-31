---
layout: post
title:  "로지스틱 회귀를 차분히 정리해보자"
author: qqplot
categories: [ MachineLearning ]
image: assets/images/17.jpg
beforetoc: "로지스틱 회귀 분석에 대한 최대한 자세한 정리"
toc: true
---





## 로지스틱 회귀란?

회귀란 평균으로 다시 돌아온다는 뜻이다. Y=f(X) 라는 관계가 있을 때, 평균 함수와 오차 항으로 이루어진 것을 회귀식이라고 한다. 여기서 오차항은 평균이 0인 분포를 가진다. 즉, 회귀식에서 Y의 기대값인 E(Y)는 언젠가는 회귀하는 고정된 식으로 이루어져 있다고 가정한다. 여기서 Y값이 예, 아니오와 같이 2가지 경우만 있을 때, 로지스틱 회귀를 사용한다. 왜 그럴까?



### Y 값이 두 가지라는 것의 의미

Y값이 0과 1인 2가지 경우의 수 밖에 없다는 건 무엇을 의미할까? 이러한 Y 변수는 이진(Binary) 변수 혹은 지시(Indicator) 변수라고 한다.



우리가 흔히 아는 회귀식은 다음과 같다. 
$$
Y_i = \beta_0 + \beta_1 X_i + \epsilon_i, \;\;\;\;\;\;\;\;\; Y_i = 0, 1
$$
위와 같은 회귀식을 세웠을 때 Y 값에 대한 기대값은 다음과 같이 표현할 수 있다.  
$$
E\{Y_i\} = \beta_0 + \beta_1 X_i
$$
Epsilon은 오차 항(잔차)으로서 기대값이 0이기 때문에 사라진다. 그리고 평균 반응 함수는 X변수와 계수로서 표현된다. 여기서 Y의 값이 2가지 경우 뿐이니까 각각의 경우의 확률을 pi와 (1-Pi)로 두고 기대값을 다시 계산해보자.
$$
Y = \begin{cases}
1, \;\;\;if\; P(Y_i=1) = \pi_i\\
0, \;\;\;if\; P(Y_i=0) = 1-\pi_i\\ 
\end{cases}
$$

$$
E(Y_i) = 1\pi_i \,+\,0(1-\pi_i) \,=\,\pi_i =P(Y_i=1)
$$

Y의 기대값, 즉 평균 반응은, Y가 지시 변수일 경우에는, 언제나 Y=1 일 때의 확률을 의미한다.   
$$
E(Y_i) = \beta_0 + \beta_1X_i = \pi_i
$$




### Y가 이진 변수일 경우 생기는 문제

회귀분석은 4가지 가정을 만족할 때 성립한다.

1. 잔차의 정규성
2. 잔차의 등분산성
3. 잔차의 독립성
4. Y와 X의 선형성



하지만 Y가 이진 변수라면, X와의 단순 회귀식으로는 위 가정을 만족하지 않는다.



#### (1) 오차 항의 비-정규성

$$
\epsilon_i = Y_i - (\beta_0 + \beta_1X_i)
$$

반응변수가 이진일 잔차는 아래와 같이 오직 두 가지 값만 을 가진다.


$$
\begin{cases}
Y_i = 1 : \; \epsilon_i = 1 -\beta_0-\beta_1X_i\\
Y_i = 0 : \; \epsilon_i = -\beta_0 - \beta_1X_i
\end{cases}
$$
이와 같은 변수는 정규 분포를 이루고 있다고 할 수 없다.





#### (2) 오차 항의 비-등분산성

$$
\sigma^2\{Y_i\} = E\{(Y_i - E(Y_i)\} = (1-\pi_i)^2 + (0-\pi_i)^2(1-\pi_i) = \pi_i(1-\pi_i)\\
\sigma^2\{Y_i\}=E\{Y_i\}(1-E\{Y_i\})\\
\sigma^2\{Y_i\}=(\beta_0+\beta_1X_i)(1-\beta_0-\beta_1X_i)
$$

오차 항의 분산은 Y가 1인 경우의 확률과 0인 경우의 확률의 곱으로 표현된다. 이는 Y의 기대값으로 바꿀 수 있고, 이를 우리가 세웠던 회귀식으로 바꿀 수 있다. 또한 Y의 분산은 오차 항의 분산과 일치하므로 결국, Y의 분산은 X로 표현된다. X로 표현된다는 것은 X값이 어떤지에 따라서 Y의 분산이 바뀐다는 것이다. 그렇기 때문에 각 Y에 대해서 똑같은 분산을 가진다고 할 수 없으므로 등분산성 가정은 깨지게 된다.







#### (3) 반응 함수의 제약

위에서 표현했듯이 Y값의 기대값은 1일 경우의 확률이다. 즉 0과 1사이의 값이여야 한다는 것이다. 이는 Y=f(X)일 경우 f의 출력에 대한 제약으로 작용한다. 
$$
0 \leq E\{Y\} \leq 1
$$




### 확률(Probit) 평균 반응 함수

위와 같은 문제를 피할 수 있는 방법은 무엇이 있을까? 우선 Y^c에 대한 회귀식이 있다고 하자. 이는 앞서 Y와는 다른 식이다. 
$$
{Y_i}^c = {\beta_0}^c +{\beta_i}^cX_i + \epsilon^c
$$
예를 들어 Y^c가 15 이하면 Y는 1이고, 15 초과이면 0이라고 해보자.
$$
Y_i=\begin{cases}
1\;\;\;\;\; if\; {Y_i}^c \leq 15\\
0\;\;\;\;\; if\; {Y_i}^c > 15
\end{cases}
$$
이 경우, 확률을 표현해보면 다음과 같다.
$$
P(Y_i = 1) = \pi_i \\
= P({Y_i}^c \leq 15) \\
= P({\beta_0}^c + {\beta_1}^cX_i + {\epsilon_i}^c \leq 15)\\
= P({\epsilon_i}^c \leq 15 - {\beta_0}^c - {\beta_1}^cX_i)\\
= P(\frac{{\epsilon_i}^c}{\sigma^2} \leq {\frac{15 - {\beta_0}^c}{\sigma^2}} - \frac{{\beta_1}^c}{\sigma^2}X_i)\\
= P(Z \leq {\beta_0}^* + {\beta_1}^*X_i)
$$
Y=1 일 때의 확률은 똑같이 pi이지만 이 확률을 Y^c로 바꾸어도 동일하다. 그리고 임의로 세운 c over 회귀식으로 대체해보자. 그리고 표준편차를 양변에 나누어 정규분포의 Probit 식으로 만든다. 이는 다음과 같이 표현한다.
$$
P(Z \leq z) = \Phi(z)
$$
이렇게 되면 probit의 역함수가 또 다른 회귀식으로 표현된다.
$$
P(Y_i =1) = \Phi({\beta_0}^* + {\beta_1}^*X_i) \\
E(Y_i) = \pi_i = \Phi({\beta_0}^* + {\beta_1}^*X_i)\\
{\Phi}^{-1}(\pi_i) = {\pi_i}^\prime = {\beta_0}^* + {\beta_1}^*X_i
$$




### 로지스틱 평균 반응 함수

문제는 profit의 역함수가 굉장히 계산하기 까다롭다는 것이다. 정규분포 식의 역함수를 생각해보라. 떠오르지 않는다. 그렇기 때문에 여기서 정규분포와 비슷한 로지스틱 분포로 대체해보자. 이는 정규분포식 보다 훨씬 계산하기 쉽지만 비슷한 모양의 분포를 가지기 때문에 생긴 아이디어이다.



![image-20211031180418304](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/image-20211031180418304.png)

평균과 표준편자가 0과 1인 정규분포와 로지스틱 분포는 위와 같은 그림을 가진다. 로지스틱 분포가 꼬리가 약간 더 두껍다. 

또한 더 간단하게 계산하기 위해 로지스틱 분포의 표준편차를 pi/sqrt(3)으로 하자. 그러면 F_L과 같은 cdf를 얻을 수 있다. 



자, 이를 아까 정규분포 변환했듯이, 로지스틱 변환을 해보자. 



![image-20211031180648526](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/image-20211031180648526.png)



양변에 pi/sqrt(3)을 나누어 주면 로지스틱 cdf로 표현할 수 있다. 이를 식으로 표현하면 [1+exp(-beta0 - beta1)]^-1 이다. 여기서 계산의 편리를 위하여 X의 선형식을 놔두고 Y를 변형하면 ln(pi/(1-pi))로 표현할 수 있다. pi/(1-pi)는 Y의 odds이며 이에 대해 로그를 취한 것이 선형식을 가지는 것이 로지스틱 회귀이다. 이와 같은 Y의 형태를 logit이라고 표현한다. 즉 선형식을 쓰기 위한 링크 함수가 logit이라는 말이다.







### Maximum Likelihood Estimation

회귀식에서는 흔히 RMSE나 MSE를 쓴다. Y 값과 예측 Y 값의 차이, 즉 잔차의 제곱을 비용 함수로 쓴다는 것이다. 그러나 로지스틱 회귀식에서는 로짓변환으로 인해 잔차가 무한대이다. Y가 1인 관측치의 logit은 ln(1/0) 으로 양의 무한대이고, Y가 0인 관측치의 logit은 ln(0/1)으로 음의 무한대이다. 따라서 선형식의 계수를 추정하기 위해 MLE를 사용한다.



Likelihood 함수는 모든 관측치의 확률(pdf)을 곱한 것이다.



![MLE](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/MLE.jpeg)



이를 계산을 쉽게 하기 위해서 로그를 취하면 로그 Likelihood 함수가 된다. 이 함수를 최대화하는 계수가 주어진 데이터에 제일 잘 맞는 계수일 것이다. 비용함수로 표현하려면 마이너스(-)를 취하면 되고 최소값 문제로 바뀐다. 그러나 이를 만족하는 최적의 해를 구하기는 어렵다. 그래서 경사하강법을 이용해서 최선의 해를 구한다.



우선 경사(Gradient)를 구하는 방법을 살펴보자. 위에선 1차식으로만 표현했지만 아래는 다차식이다. 먼저 시그모이드를 미분하면 아래와 같은 모양이 된다는 것을 이해하자. Sigma(z)는 시그모이드 함수 1/(1+exp(-z))를 의미한다.



![로지스틱 경사하강법](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/로지스틱 미분.png)



시그모이드 함수는 아래와 같이 생겼다.

![로지스틱 함수 모양](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/로지스틱 함수 모양.png)





즉 하기와 같이 정의한다면, MLE의 경사는 미분을 통해 얻을 수 있다.

![로지시틱 표현방법](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/로지시틱 표현방법.png)



Log-Likilhood 함수를 LL(theta)로 표현하고, 각 theta에 대해서 편미분을 하면 깔끔하게 정리할 수 있다.



![로지스틱 MLE 미분](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/로지스틱 MLE 미분.png)



자 이렇게 세워진 식을 다음과 같은 순서로 경사하강 해나간다.

우선, 모든 theta를 0으로 초기화한다. 그리고 주어진 데이터로 theta_0 부터 gradient 계산식의 값을 구한다. 그 다음 각 theta에 old theta + 학습률 * gradient로 업데이트를 한다. 그리고 이를 수없이 반복하면 최적의 해와 근사한 값을 얻을 수 있다.



![로지스틱 경사하강법](/Users/kyubyungchae/Documents/qqplot.github.io/assets/images/로지스틱 경사하강법.png)





### 출처

1. MIT note 

   [보기]: https://web.stanford.edu/class/archive/cs/cs109/cs109.1178/lectureHandouts/220-logistic-regression.pdf

2. Applied Linear Regression Models z

   [보기]: http://www.kyobobook.co.kr/product/detailViewEng.laf?ejkGb=ENG&amp;mallGb=ENG&amp;barcode=9780071122214

   

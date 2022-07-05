---
layout: post
title:  "Information Entropy"
categories: [ datascience ]
tags: [ science ]
fullview: false
comments: true
use_math: true
---

*정보이론에 대해서 정리해보자.*

---

# Information Theory

정보이론은 응용 수학 중 하나이다. 통계와 결이 비슷하면서도 미묘하게 다르다. 정리해보고자 한다. 정보 이론에서 얻을 수 있는 중요한 아이디어는 두 가지이다.

- 확률 분포에 대한 특징을 부여한다
- 확률 분포 사이의 비슷함(다름)을 계량할 수 있다.
       
정보 이론에서 정보란 무엇인가? "깜놀도"라고 쉽게 표현한 것이 참 와닿는다[2]. 예를 들어, 아침에 태양이 뜬다와 같은 당연한 정보는 유용하지 않다(uninformative). 그러나 개기일식이 일어난다와 같이 흔치 않은 일은 참 유용하다(very informative). 이를 정리하면 다음과 같다.
      
- 일어날 확률이 높은 사건(likely event)는 정보량이 적다. 극단적으로 말하면 무조건 일어난다고 보장할 수 있는 사건은 아예 정보가 없다(0).
- 일어날 확률이 낮은 사건(less likely event)가 더 정보량이 많다.
- 독립적인 사건(independent event)는 추가적인 정보를 준다. 예를 들어, 동전 던지기를 생각해보자. 두 번 동전을 던져서 앞면이 두 번 나왔다는 정보는 한 번 던져서 앞면이 한 번 나온 정보보다 두 배 많은 정보를 가진다. 동전 던지기가 독립적인 시행이기 때문이다.
      
결국 정보란 확률값에 반비례한다고 할 수 있다. 사건 X에 대한 정보량을 $I(X)$로 하고 일어날 확률은 $P(X)$로 하면 $I(X) \propto \frac{1}{P(X)}$ 이렇게 표현할 수 있다.

<br/><br/>
              

# 정보량

위에 이야기한 3가지 특징을 만족하기 위해서 $X = x$인 사건에 대한 자기 정보(self-information)를 정의할 수 있다.


\begin{align}
I(x) = \log{\frac{1}{P(x)}}\, = \, -\log{P(x)}
\end{align}

여기서는 e를 로그의 밑으로 주었다(nat). 하나의 nat는 1/e 확률의 사건을 관찰함으로써 얻을 수 있는 정보량이다. 로그의 밑을 어떻게 주느냐에 따라서 단위를 다르게 설정할 수 있다. 

<br/><br/>

# 엔트로피 - 평균 정보량

결국 엔트로피란 정보량의 평균을 말한다. 이는 열역학에서 흔히 쓰이는 엔트로피라는 용어와는 크게 상관없는 용어이다. 새로운 용어라고 받아들이는 편이 이해가 쉽다.

이산 랜덤변수 $X$의 샘플 공간이 $\lbrace x_1, x_2, \cdots, x_n\rbrace$이라고 할 때 정보 엔트로피는 아래와 같다.


\begin{align}
H(X) = E\left[I(X)\right] = -\sum_{i=1}^{n}P(x_i)\log_b(P(x_i))
\end{align}

예를 들어, 주사위를 던진다고 하자. 1부터 6까지 주사위 눈에 따라 각각 100원 ~ 600원을 받는다고 하자. 이 때의 기대값은 다음과 같다. 


\begin{align}
기대값 = \frac{1}{6}\times 100 + \frac{1}{6}\times 200 + \cdots \frac{1}{6}\times 600 =\sum_{i=1}^6 P(x_i) M(x_i)
\end{align}

여기서 $x_i$는 사건이고 그에 대해 받는 돈(100원~600원)은 $M(x_i)$라는 함수로 표현할 수 있다. 결국 기대값은 각 사건이 일어날 확률 $\times$ 사건의 합이다. 따라서 정보 엔트로피는 모든 일어날 수 있는 사건에 대한 확률 $\times$ 정보량의 합이다. 다음과 같이 계산할 수 있다.

\begin{align}
H(x) = \sum_{i=1}^{n}P(x_i)(-\log_b(P(x_i)))
\end{align}

# 참고
- [1] [Deep Learning(2017), Goodfellow](https://www.deeplearningbook.org/)
- [2] [공돌이의 수학정리노트](https://angeloyeo.github.io/2020/10/26/information_entropy.html)

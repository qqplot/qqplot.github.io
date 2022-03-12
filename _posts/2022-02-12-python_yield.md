---
layout: post
title:  "Python에서 yield란"
categories: [ python ]
tags: [ basic ]
fullview: false
comments: true
---

*yield와 return의 차이점을 이해하고 generator와 어떤 연관이 있는지 알아보자*

---

사이킷런 api를 살펴보다가 `yield`라는 예약어(reserved word)가 나와서 뭔가 했다. 어디선가 봤던 거 같은데 그 역할이 가늠이 안되었기 떄문이다. 게다가 메소드에 `return`이 없다니! 내가 사용한 반환값은 뭐지?

<br/>

### yield 키워드와 제너레이터

대부분의 함수는 `return` 예약어를 이용하여 반환을 한다. 하지만 파이썬에서는 `yield`를 이용하여 다른 방식으로 결과값을 제공할 수 있다. 둘의 차이는 간단하다. `return`은 호출되는 즉시 종료이지만, yield는 함수를 다 읽을 때까지 계속 간다.

<br/>

```python
def return_abc():
    return "A"
    return "B"
    return "C"
```
<br/>

`return_abc()`를 호출하면 당연히 결과는 "A"일 것이다. 

<br/>

```python
def yield_abc():
    yield "A"
    yield "B"
    yield "C"
```

<br/>

그렇다면 `yield_abc()`를 호출하면 어떻게 될까? 결론은 `generator`가 반환된다. 이는 한 개만 반환해도 마찬가지이다.

![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/yield_return.png?raw=true)

<br/><br/>

### 제너레이터(generator)란?

파이썬에서 제너레이터는 데이터를 미리 불러오지 않고 필요할 때마다 생성하는 객체이다. `return`의 경우 함수의 사용을 종료하고 결과값을 호출부로 반환 후 메모리 상에서 클리어한다.
반면 `yield`는 반환 값을 `next()`를 호출한 쪽으로 전달하며, 함수의 상태는 메모리에 유지되어 있다. 즉 함수에서 사용된 local 변수나 pointer 등이 있는 namespace가 사라지지 않고 있는 것이다.

<br/>

그렇다면 왜 제너레이터를 쓰는가? 그 이유는 memory를 효율적으로 사용하기 위해서이다. 이해를 위해 `list`와 비교해보자. `list`는 사이즈가 커질수록 메모리 사용량이 늘어난다. 반면 `generator`는 메모리 사이즈는 크기에 상관없이 동일하다. 다시 말해, `list`는 모든 데이터를 메모리에 적재하는 반면, `generator`는 `next()` 메소드를 통해 차례로 값을 호출할 때에만 메모리에 올리는 방식이다.

<br/>

아래 간단한 실험을 보자.

![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/generator.png?raw=true)
<br/>

`[]`는 리스트를 반환하고 `()`는 제너레이터를 반환한다. 리스트는 100개의 값을 가질 때보다 1000개의 값을 가질 때에 크기가 520 byte에서 4264 byte로 늘어난다. 하지만 제너레이터는 생성하는 값의 크기와 상관없이 일정한 메모리(112 byte)만 차지한다.

<br/>

제너레이터는 값을 가지고 있는 것이 아니라 값을 가지고 올 수 있는 로직과 어디까지 가져왔는지(포인터)만을 저장하고 있기 때문이다.

![img](https://dojang.io/pluginfile.php/13960/mod_page/content/4/040002.png)

<br/>
<br/>

### 참고

- [1] [python generator(제너레이터) 란 무엇인가](https://bluese05.tistory.com/56)
- [2] [40.1 제너레이터와 yield 알아보기](https://dojang.io/mod/page/view.php?id=2412) 
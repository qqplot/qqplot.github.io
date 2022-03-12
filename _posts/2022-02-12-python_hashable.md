---
layout: post
title:  "Python에서 Hashable이란"
categories: [ python ]
tags: [ basic ]
fullview: false
comments: true
---

*파이썬에서 해쉬 가능하다는 것은 어떤 뜻일까?*

---

### Hashable (해시 가능한)

파이썬 용어집[1]에 따르면 어떤 객체(object)가 해시 가능하다는 것은 변경되지 않는 해시값이 있고 다른 객체와 비교할 수 있는 경우이다. 좀 더 상세히 말하자면 객체의 수명동안 `__hash__()` 메서드가 반환한 해시값이 변하지 않아야 하고, `__eq__()`, `__cmp__()` 메서드 등 다른 객체와 비교할 수 있는 메서드가 구현되어 있는 객체를 말한다. 여기서 같음을 비교하는 해시 가능한 객체는 동일한 해시 값을 가져야 한다.

해시 가능성(Hashable)은 데이터 구조가 내부적으로 해시 값을 사용하기 때문에 객체는 dictionary의 키 및 set 멤버로 사용할 수 있도록 한다. python에서 immutable 내장 객체는 해시 가능하지만 mutable한 컨테이너 (예: list 또는 dict)는 해시 가능하지 않다. 

<br/>

### 해시(Hash)란

임의의 크기를 가진 데이터(Key)를 고정된 크기의 데이터(Value)로 변화시켜 저장하는 방식이다. 키에 대한 해시값을 구하는 과정을 해싱(Hashing)이라고 한다.

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbx45DR%2FbtqDHrT3rQx%2Fw2EvrEnAwG3MqbJGINmRLk%2Fimg.png)


### 참고

- [1] [What does "hashable" mean in Python?](https://stackoverflow.com/questions/14535730/what-does-hashable-mean-in-python)
- [2] [파이썬 용어집](https://docs.python.org/2/glossary.html)
- [3] [[자료구조/알고리즘] 해시(Hash) 란?](https://power-overwhelming.tistory.com/42)
- [4] [해시함수란?](https://s1m0hya.tistory.com/27)

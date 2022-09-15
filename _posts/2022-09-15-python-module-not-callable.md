---
layout: post
title:  "TypeError: 'module' object is not callable 해결"
categories: [ python ]
tags: [ class, module, error ]
fullview: false
comments: true
---


*파이썬에서는 자바와 달리 파일명과 클래스명을 일치시켜야 한다는 제약이 없다. 편하긴 하지만 불러오는 입장에서 파일명만으로 클래스 명을 유추할 수 없다. 즉 모듈(파일)과 클래스를 명확히 구분해서 호출해야 한다.*

---


<br/>


# tqdm 호출

tqdm은 반복문의 진행 상태(progress status)를 bar 형태로 보여주는 편리한 패키지이다. 이 패키지를 호출할 때에 사용하다가 갑자기 `TypeError: 'module' object is not callable` 에러를 만나게 되었다. api 호출 방법이 틀리지 않았는데 뭐가 문제일까 한참 헤메다가 너무나도 치명적인 실수를 했다.


```python
import tqdm
```


# module과 class

위와 같이 module을 호출할 때에 모듈 객체는 호출이 불가능하다. 쉽게 이야기 하면 `tqdm.py` 파일에 tqdm 이라는 클래스가 정의가 되어 있는 것이다. 내가 필요한 것은 tqdm 클래스이기 때문에 `import tqdm`만 해서는 당연히 호출이 되지 않는다. 모듈명과 클래스 명이 동일하게 생성되어 있기 때문에 착각한 것이다.

파이썬에서는 자바와 달리 파일명과 클래스명을 일치시켜야 한다는 제약이 없다. 편하긴 하지만 불러오는 입장에서 파일명만으로 클래스 명을 유추할 수 없다. 즉 모듈(파일)과 클래스를 명확히 구분해서 호출해야 한다.


```python
from tqdm import tqdm
```


<br/>
<br/>

# 참고
- https://iksflow.tistory.com/85
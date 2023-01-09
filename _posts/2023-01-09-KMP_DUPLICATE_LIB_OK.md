---
layout: post
title:  "[ARM] KMP_DUPLICATE_LIB_OK란"
categories: [ ARM ]
tags: [ numpy ]
fullview: false
comments: true
---
** 논문 읽기: Adaptive Risk Minimization: Learning to Adapt to Domain Shift **

---

openMP를 사용하는 라이브러리를 쓰다보면 발생할 수 있는 에러는 중복 실행이다.

openMP는 cpu에서 여러 스레드를 사용할 수 있도록 해주는데 여러 라이브러리가 스레드를 생성하는 것을 os가 기본적으로 방지하고 있는 듯하다. 그래서 중복 실행을 허용하도록 바꾸어야 `matplotlib` 라이브러리 등 실행할 때에 에러가 나지 않을 수 있다. 

<br/>

```python
os.environ['KMP_DUPLICATE_LIB_OK'] = "TRUE"
```




<br/>
<br/>

# 참고
- https://normal-engineer.tistory.com/236
- https://stackoverflow.com/questions/74217717/what-does-os-environkmp-duplicate-lib-ok-actually-do
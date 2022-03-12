---
layout: post
title:  "컴퓨터 구조"
categories: [ computer ]
tags: [ basic ]
fullview: false
comments: true
---


컴퓨터 구조에 대한 간단한 메모

---

#### 컴퓨터 구조

컴퓨터 구조는 CPU, Memory, I/O(Disk, 키보드 등), System Bus로 나눌 수 있다. [2]

![img](https://t1.daumcdn.net/cfile/tistory/263A504B560486D324)

CPU는 Central Processing Unit의 약자로 번역하면 중앙처리장치이다. 데이터 처리(연산)을 담당한다. Memory는 데이터를 저장하는 기능을 수행한다. I/O는 컴퓨터와 외부 장치 사이에서 데이터를 교환하는 기능을 수행한다. System Bus는 위 구성 요소들을 연결하는 데이터 전송을 수행한다.

<br/><br/>

#### CPU 구성

CPU는 기억, 해석, 연산, 제어라는 4대 계산을 한다.

![img](https://media.vlpt.us/images/ckstn0777/post/53eccfb7-a8cb-432f-9a76-f40e0a00f6b5/image.png)

계산을 효율적으로 하기 위해 좀 더 일련의 과정(구성)을 하나의 칩으로 만들어 **프로세서**라는 것이 생겼다.
(프로세서 >= CPU)

- CPU: 계산을 수행하는 메인
- Co-processor: CPU를 도와주는 보조 프로세서
- Micro-processor: 보조 프로세서 중 하나로 소형 디바이스에 장착된 프로세서

그렇다면 **코어**란 무엇일까?

코어란 CPU 내부에서도 각종 연산을 담당하는 핵심 요소를 지칭한다. (CPU >= 코어)
CPU 내부에는 코어 말고도 컨트롤러, 캐시메모리 등이 들어 있다.

![img](https://media.vlpt.us/images/ckstn0777/post/6d33329e-298d-433d-bff5-75ff54fa1c87/image.png)

<br/><br/>

**캐시 메모리**란 CPU에서만 지칭하는 용어는 아니다. 속도가 다른 장치 사이에서 병목 현상을 줄이기 위한 역할을 수행한다. Memory Wall이라고도 하는데 프로세서의 처리속도(클럭)가 GHz인 반면에 메모리가 MHz여서 그 속도를 따라가지 못 하고 계속 정체 구간이 생긴다. CPU가 아무리 빨리 계산을 해도 데이터를 가져오는 속도가 따라가지 못한다는 말이다. 이를 막기 위해 캐시메모리에 미리미리 데이터를 가져다가 놓는다. 빠른 메모리는 비싸기 때문에 속도에 따라 L1, L2, L3 계층을 나누어 순서대로 탐색한다.

![img](https://media.vlpt.us/images/ckstn0777/post/c73b0e07-428a-4f3a-a4ed-0f77f4ce0a63/image.png)

<br/><br/>

#### 참고

- [1] [기초) 컴퓨터구조](https://rogiry.tistory.com/24)
- [2] [컴퓨터구조-컴퓨터-구조와-기능-CPU](https://velog.io/@ckstn0777/%EC%BB%B4%ED%93%A8%ED%84%B0%EA%B5%AC%EC%A1%B0-%EC%BB%B4%ED%93%A8%ED%84%B0-%EA%B5%AC%EC%A1%B0%EC%99%80-%EA%B8%B0%EB%8A%A5-CPU) 
- [3] [What-is-the-difference-between-4-core-and-4-CPU](https://www.quora.com/What-is-the-difference-between-4-core-and-4-CPU)
- [4] [How-is-L2-cache-shared-between-different-cores-in-a-CPU](https://www.quora.com/How-is-L2-cache-shared-between-different-cores-in-a-CPU)

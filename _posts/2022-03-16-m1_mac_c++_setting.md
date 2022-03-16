---
layout: post
title:  "M1 맥북에서 VSCode로 c++ 개발환경 세팅하기"
categories: [ c++ ]
tags: [ basic ]
fullview: false
comments: true
---

*VSCode로 c++ 개발 환경을 잡아보자.*

---

[junekkk님의 블로그](https://junekkk.tistory.com/)에서 많은 도움을 받았음을 먼저 밝힌다[2].

<br/>

### Mac에서 C++을 돌려야 한다.

MacOS에서 대표적인 IDE는 XCode가 있다. 하지만 i/o도 불편하고 굉장히 무겁다. Window에서는 보통 Visual Studio를 쓰는데 (VSCode랑은 다르다!) mac 버전은 라이트 버전이라 기능이 많이 없다. 그래서 평소 쓰던 VSCode에 c++ Extention을 설치하여 사용해보려고 한다.

<br/><br/>

#### 1. VSCode for Mac Donwload & Install Extentions

여기 [링크](https://code.visualstudio.com/)에서 vscode를 다운받는다.

<br/>

그 후에는 C/C++ Extention을 설치한다.

![img](https://k.kakaocdn.net/dn/clK4VS/btqBJ7XNey6/jzvyi828KkKSPRyjotykuk/img.png) 

<br/>

지금부터 c++ 사용하는 방법이 여러 가지가 갈린다. VSCode 자체에서 Run 하는 방법이 있다. Workspace를 설정하는 방법인데 생각보다 불편했다. 그래서 `Code Runner`라는 무료 익스텐션을 설치해서 쓸 것이다.

<br/>

![img](https://k.kakaocdn.net/dn/IWMoc/btqBQ1hKGPC/tCV1kYeSPKCTmPKJzpELbk/img.png)

<br/>

c++만을 위한 익스텐션은 아니고 실행 편의성을 위해서 여러 언어를 지원한다. 결국은 터미널에 원하는 명령어를 자동으로 실행시켜 주는 기능이라고 이해하면 된다. python을 할 때에는 `.py` 확장자를 가진 파일 하나로 실행이 가능했지만 c++은 컴파일을 한 뒤에 실행파일을 또 호출해야 한다. `.h` 와 `.cpp` 확장자를 가진 파일과 main.cpp 파일도 필요하고 좀 더 복잡하다.

아무튼 Code Runner도 더 편하게 쓰기 위해서는 몇 가지 설정을 바꾸어주여야 한다. 톱니바퀴 모양을 눌러 Extention Setting을 켠다. (그림은 약간 옛날 버전인 듯하다.)

<br/>

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbHRGEW%2FbtqBNjvHx6U%2FfzrCLfkkWKr1yiKNeU1O2K%2Fimg.png)


<br/>

그 후에는 중간 쯤 스크롤을 내리다보면 터미널에서 실행을 위한 Run In Terminal이라는 옵션이 있다. 이것을 체크해준다.


![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcy1WEx%2FbtqBO8aRbrZ%2FjtiVIkPMHi1FTNPZOtCujK%2Fimg.png)

<br/>

그 후에는 Executor Map를 찾아서 Edit in setting.json을 눌러서 설정을 바꾸자.

![img](https://k.kakaocdn.net/dn/d4qmhg/btqBL7W0Yb0/IVlPyvTuCkf7drkZlN4Ilk/img.png)

처음 열면 아래 처럼 설정이 되어 있을 것이다.

```json
{
    "code-runner.runInTerminal": true,
}
```

여기에서 `executorMap` 항목을 만들어 준다. cpp로 만들면 된다.

<br/>


```json
{
    "code-runner.runInTerminal": true,
    "code-runner.executorMap": {
        "cpp": "cd $dir && g++ -std=c++14 $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt"
    },
}
```

<br/>
이렇게까지 설정하면 터미널에서 코드가 잘 돌아갈 것이다.




<br/><br/>

### 참고
- [1] [[C++] 여러 파일을 컴파일하자.](http://csmylov.blogspot.com/2018/08/c.html)
- [2] [[VSCode] macOS에서 Visual Studio Code로 C/C++ 코딩하기(1) - Extension 설치](https://junekkk.tistory.com/20)
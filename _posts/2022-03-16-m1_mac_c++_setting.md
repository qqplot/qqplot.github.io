---
layout: post
title:  "M1 맥북에서 VSCode로 c++ 개발환경 세팅하기"
categories: [ cpp ]
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


<br/>

```cpp
#include "bear.h"
#include "mommabear.h"

int main(void) {
    Mommabear::TestBears(); // Invoke the static class fuction
    return 0;
}
```

그런데 위 코드처럼 직접 만든 헤더 파일을 include 하자 linker 에러가 발생했다.

`clang: error: linker command failed with exit code 1 (use -v to see invocation)`

위의 마지막 줄이 핵심인데, 아래 그림처럼 arm64라는 메시지가 나와서 m1 문제인가 싶어서 한참을 헤맸다. (세팅할 때마다 arm64에 하도 데이는 중이다...)

![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/cpp_error_linker.png?raw=true)

<br/>

결과적으로 문제는 컴파일과 빌드를 이해못했다는 것에 있었다. 우리가 Code Runner에게 실행하라고 한 명령어는 현재 파일만 컴파일하고 그 후 나온 실행파일을 실행하는 것이다.

`cd $dir && g++ -std=c++14 $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt`

그런데 같은 디렉토리에 있는 .cpp가 컴파일된 오브젝트 파일이 없으니 불러올 파일을 찾지 못했다는 linker 에러가 발생한 것이다. 그러니 같은 폴더 내의 cpp를 전부 포함해서 컴파일하면 정상적으로 실행이 된다.

`cd $dir && g++ -std=c++14 *.cpp -o $fileNameWithoutExt && $dir$fileNameWithoutExt`

지금과 같은 에러를 잡으면서 c++을 배우는 이유를 알게 되었다. 단순히 컴파일과 실행을 나누었을 뿐인데도 완전히 헤매고 있는 자신을 발견했기 때문이다. 컴퓨터가 어떻게 동작하는지 좀 더 이해하게 된 느낌이다. 앞으로 c++에 대해 정리해서 더 올리도록 하겠다. 그리고 Code Runner가 편하긴 하지만, 컴파일 명령어를 직접 치는 핸즈온이 필요할 듯하다. 한동안은 사용하지 말고 직접 쳐보면서 공부하겠다.


<br/><br/>

### 참고
- [1] [[C++] 여러 파일을 컴파일하자.](http://csmylov.blogspot.com/2018/08/c.html)
- [2] [[VSCode] macOS에서 Visual Studio Code로 C/C++ 코딩하기(1) - Extension 설치](https://junekkk.tistory.com/20)
- [3] [stackoverflow - Unable to run C++ files using code runner on VS Code](https://stackoverflow.com/questions/62288990/unable-to-run-c-files-using-code-runner-on-vs-code)
- [4] [VSCode 설정 - (3) C/C++ 빌드 및 실행 설정하기](https://huilife.tistory.com/entry/VSCode-%EC%84%A4%EC%A0%95-3-CC-%EB%B9%8C%EB%93%9C-%EB%B0%8F-%EC%8B%A4%ED%96%89-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)
- [5] [맥에서 VS code로 C/C++ 빌드, 실행 개발 환경설정](https://sean-ma.tistory.com/10)
- [6] [c++ tutorial - Compilers](http://cplusplus.com/doc/tutorial/introduction/)
---
layout: post
title:  "M1 맥북에 React Native 프로젝트 세팅하기"
categories: [ reactnative ]
tags: [ reactnative, m1 ]
fullview: false
comments: true
---

*M1 맥북에 프로젝트 세팅하기 - 당연한 건 당연한게 아니다*

[taese0_0ng님의 블로그](https://velog.io/@taese0ng/M1-%EB%A7%A5%EC%97%90%EC%84%9C-React-Native-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0)에 잘 정리가 되어 있어 큰 도움을 얻었음을 먼저 밝힌다. 그러나 지금 Mac OS인 몬트레이에서 완전하게 잘 작동하는 세팅은 아니다. 그래서 다시 한 번 정리해본다.

---



### 프로젝트 세팅하기

#### 1. Xcode 설치

AppStore 에서 검색 후 설치

<br/>

##### 2. Iterm2 설치하기

[링크](https://iterm2.com/)에서 Iterm2를 설치한다.

설치 이후 오른쪽 클릭 - 정보 가져오기 - Rosetta를 사용하여 열기 클릭 후 실행

<br/>

#### 3. oh-my-zsh 설치하기

zsh를 기본으로 사용하는데 더 편하게 사용하기 위해 설치

```sh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

<br/>

##### 4. homebrew 설치

mac에서 유용하게 쓰이는 프로그램들을 편리하게 설치할 수 있게 해주는 툴이다.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew --version
```
<br/>

#### 5. node, cocoapods, watchman, ffi 설치

`node`는 javasript 엔진을 기반으로 하는 서버 측 플랫폼이다[1]. `cocoapods`는 ios에서 사용하는 패키지 매니저이다[2]. `npm`이 node package manager인 것처럼 이것도 ios 구동을 위한 디펜던시를 관리해준다. `watchman`은 페이스북에서 개발한 오픈소스 프로그램으로 파일과 기록의 변화를 감지한다. 리액트 네이티브는 앱 구동 중에도 소스 코드 변화가 있으면 자동으로 업로드 후 리프레시를 해주는데 이는 소스 변화가 있으면 트리거 형태로 작동할 수 있기 때문이다. `ffi`는 ruby에서 다른 프로그래밍 언어로 정의된 함수를 사용하게 해주는 인터페이스이다[3].
     

```sh
# install node
brew install node
node -v
npm -v

# install watchman
brew install watchman
watchman --version

# install cocoapods
sudo gem install cocoapods
pod --version

# install ffi
sudo gem install ffi
```
<br/>

여기서 수정하고 싶은 것은 cocoapods 설치이다. gem을 통해서 설치하면 빌드가 제대로 되지 않는다. 아무래도 os 버전에 따른 이슈로 보인다. gem이 아닌 brew로 설치를 진행하면 제대로 빌드가 된다.

[stack overflow - How to running CocoaPods on Apple Silicon (M1)](https://stackoverflow.com/questions/64901180/how-to-running-cocoapods-on-apple-silicon-m1)


이후 zshrc 마지막줄에 환경변수를 설정하는 아래 문구 추가하고 source를 수행한다.

```sh
export GEM_HOME=$HOME/.gem
export PATH=$GEM_HOME/bin:$PATH
```

```sh
open ~/.zshrc
source ~/.zshrc
```

<br/>

#### 6. xcode 설정 

설치 이후 오른쪽 클릭 - 정보 가져오기 - Rosetta를 사용하여 열기 클릭 후 실행
상단 탭 Xcode - Preferences - Accounts 본인 iCloud 계정 추가
Locations에 Command Line Tools 눌러서 나오는 Xcode 13.2.1 (13C100) 를 선택
Components 탭에서 iOS 14.5 Simulator 선택

<br/>

#### 7. React-native 설치

```sh
npm install -g react-native-cli
react-native --version
```


자 여기까지 되었다면 ios는 실행이 가능하다.


![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/ios%20%E1%84%89%E1%85%B5%E1%84%86%E1%85%B2%E1%86%AF%E1%84%85%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%90%E1%85%A5%20%E1%84%80%E1%85%AE%E1%84%83%E1%85%A9%E1%86%BC%20%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB.png?raw=true)


<br/>



#### 8. vscode 설치
https://code.visualstudio.com

<br/>

#### 9. cask 설치하기

brew는 cli 프로그램만 대상이라면 cask는 gui 프로그램 대상으로 설치를 도와주는 툴이다. mac 환경에서 응용 프로그램을 명령어로 설치할 수 있게 한다.


```sh 
brew install cask
cask --version
```

<br/>

#### 10. java 설치하기

```sh
brew install --cask adoptopenjdk11
```

[java 버전이 기 설치되어있다면 변경하는 방법](https://llighter.github.io/install-java-on-mac/)

<br/>

#### 11. android studio 설치

[블로그](https://paullab.tistory.com/26) 참조

<br/>

#### 12. android emulator 설치

[블로그](https://iagreebut.tistory.com/86) 참조

<br/>

#### 13. vscode에서 react-native 프로젝트 생성 및 실행

12번의 에뮬레이터를 먼저 실행 시켜준 후
원하는 경로로 이동 react-native 프로젝트 생성

```sh
npx react-native init 프로젝트명
```

프로젝트 폴더 이동 후 react-native 실행

```sh
npx react-native run-android
```

초기화면이 에뮬레이터에 셋팅되면 완료

<br/>

#### 14. git clone

이제 깃랩에서 우리 프로젝트 소스를 클론해온 뒤에 테스트해본다.

```sh
git clone
npm install
npx react-native start
npx react-native run-android
```

<br/>

#### 15. 폴더 구조 및 navigation 잡기

[프로젝트 구조](https://www.reactnative.express/app/project_structure)는 해당 글을 참조했다. 그리고 네비게이션 구조를 잡았다.

하기의 요소들을 활용하여 개발할 예정이다.

- React Navigation [링크](https://reactnavigation.org/docs/getting-started)
- React element [링크](https://reactnativeelements.com/docs/3.4.2/getting_started)
- React Firebase [링크](https://rnfirebase.io/)

![img](https://github.com/qqplot/qqplot.github.io/blob/main/assets/images/%E1%84%8B%E1%85%A1%E1%86%AB%E1%84%83%E1%85%B3%E1%84%85%E1%85%A9%E1%84%8B%E1%85%B5%E1%84%83%E1%85%B3%20%E1%84%80%E1%85%AE%E1%84%83%E1%85%A9%E1%86%BC%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB.png?raw=true)

<br/>

### 앞으로

jira 등을 활용하여 회의록도 체계적으로 관히할 필요성이 있다.
모든 것은 시작이 있다. 앞으로 나아 가자.




### 참고

- [1] [What is Node.js?](https://www.tutorialspoint.com/nodejs/nodejs_introduction.htm)
- [2] [What is cocoapods](https://cocoapods.org/) 
- [3] [What is ruby ffi](https://www.rubyguides.com/2019/05/ruby-ffi/)
- [4] [Setting up the development environment](https://reactnative.dev/docs/environment-setup) 

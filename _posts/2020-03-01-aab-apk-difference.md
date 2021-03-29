---
layout: post
title:  "aab와 apk 차이는 무엇인가"
author: qqplot
categories: [ android ]
tags: [ android ]
image: assets/images/2.jpg
rating: 4.5
---



#### APP 크기에 대한 고민

APK는 Android Application Package의 약자로서 안드로이드 환경에서 돌아가는 프로그램의 확장자 이름이다. AAB는 Android Application Bundle의 약자다. 둘 다 Android 운영체제에서 도는 프로그램을 의미한다. 

apk < aab



20억이 넘는 기기를 확인한 결과 28%는 1GB 미만의 여유공간

APK 사이즈가 클수록 설치 완료에 이르기까지 더 많은 시간 소요 -> 다운로드 취소 버튼을 누를 가능성이 커진다.

와이파이, 무제한 데이터 요금제가 당연하지 않다. 대다수의 사용자들에게 데이터 요금은 중요한 문제다.



APK 크기는 설치 전환율 뿐만 아니라 앱이 삭제되는 주요한 요인이다. 삭제된 앱 다섯 개중 하나는 저장 공간을 확보하기 위함이다.

하루 안에 앱을 삭제하는 주된 이유는 앱의 퀄리티이고, 한 달 뒤에 삭제하는 이유는 저장공간 때문이다.



다중 APK

안드로이드는 팻 바이너리가 아닌 기기에 특화된 APK를 생성할 수 있는 기능을 오래전부터 지원해왔다.  다중 APK라 불리는 이 기술은 몇몇 상황에서 꽤 유용하게 사용할 수 있다. 하지만 모든 APK를 버전관리를 하는 것은 굉장히 힘든 일이다



앱 번들

2017년에 구글 플레이 앱 서명 기능이 출시되면서 배포와 관련된 몇 가지 변화가 생기기 시작했다. AAB는 구글 플레이 앱 게시(업로드)를 위한 파일 포맷이고, 이 파일을 기반으로 아키텍쳐, 화면 밀도, 언어에 최적화된 분할 APK를 생성한다. 최종적으로 앱 실행에 필수 요소인 baseAPK와 아키텍처, 화면 밀도, 언어별로 구분된 configuration APKs, 모듈식의 분리된 기능을 위한 APK인 dynamic feature APKs가 생성된다. 



앱번들은 여러 APK들의 조합으로 하나의 앱이 구성된다.                                                             





![img](https://lh3.googleusercontent.com/p5VYiFZbJKtVON148QdegKUOAJW1r46jVHBRUImOaR_5HkWMwdxIOmaCbZdjeLKA-7Z-XV6Dguq8wNdGnnKP51Bqt0AFCjx1LspBd1GnlR7zz7qIkSwc_WnCR1DWzwpAHjVHAALc)



https://developers-kr.googleblog.com/2018/05/google-io-2018-whats-new-in-android.html


















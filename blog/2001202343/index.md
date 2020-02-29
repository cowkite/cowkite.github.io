---
layout: post
title: Mac에서 ReactNative 프로젝트 환경 구성하기
subtitle: Xcode와 Android Studio를 통한 개발환경 구성
type: Mobile/ReactNative
blog: true
text: true
post-header: true
header-img: "img/about.jpg"
date: 2020-02-20 23:43
comments: true
---

## Mac(OSX) 셋팅 - iOS 개발 환경 구성

Mac(OSX) 환경에서 Xcode를 사용하여 iOS 앱을 빌드하는 방법을 설명한다.



### 패키지 설치

1. Homebrew 설치

   ```sh
   # 설치 여부 확인
   $ brew --version
   - bash: brew: command not found
   
   # 설치 명령어 실행
   $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   
   # 설치 완료 확인
   $ brew --version
   Homebrew 2.2.6
   Homebrew/homebrew-core (git revision 68e2e; last commit 2020-02-20)
   Homebrew/homebrew-cask (git revision e881d1; last commit 2020-02-20)
   ```

2. Node.js 설치

   ```sh
   # 설치 여부 확인
   $ node --version
   -bash: node: command not found
   
   # 설치 명령어 실행
   $ brew install node
   
   # 설치 완료 확인
   $ node --version
   v13.8.0
   $ npm --version
   6.13.7
   ```

3. Watchman 설치

   ```sh
   # 설치 여부 확인
   $ watchman --version
   -bash: watchman: command not found
   
   # 설치 명령어 실행
   $ brew install watchman
   
   # 설치 완료 확인
   $ watchman --version
   4.9.0
   ```

4. ReactNative CLI 설치

   ```sh
   # 설치 여부 확인
   $ react-native --version
   -bash: react-native: command not found
   
   # 설치 명령어 실행
   $ npm install -g react-native-cli
   
   # 설치 완료 확인
   $ react-native --version
   react-native-cli: 2.0.1
   react-native: 0.61.5
   ```

5. Cocoapods 설치

   ```sh
   # 설치 여부 확인
   $ pod --version
   -bash: pod: command not found
   
   # 설치 명령어 실행
   $ sudo gem install cocoapods
   
   # 설치 완료 확인
   $ pod --verison
   1.8.4
   ```

6. Yarn 설치

   ```sh
   # 설치 여부 확인
   $ yarn --version
   -bash: yarn: command not found
   
   # 설치 명령어 실행
   $ brew install yarn
   
   # 설치 여부 확인
   $ yarn --version
   1.22.0
   ```



### Xcode 설치

앱스토어 접속 후 Xcode 설치 (11버전 이상의 최신버전 설치)

오래걸리므로 설치 버튼 클릭 후 커피타임☕️을 갖는다.



### 프로젝트 import

1. Clone project

   ```sh
   $ git clone {PROJECT_PATH}
   ```

2. 패키지 설치

   ```sh
   # 프로젝트 path로 이동
   $ cd {PROJECT_PATH}
   
   # 패키지 설치
   $ yarn install
   ```

3. pod 설치

   ```sh
   # ios 프로젝트 path로 이동
   $ cd ios
   
   # pod 설치
   $ pod install
   ```

   

### 프로젝트 설정

1. *{project_path}* > `ios` > `InMyBag.xcworkspace` 파일 실행하여 Xcode 실행
2. 좌측 트리에서 최상단의 프로젝트 클릭하여 설정 화면 진입
3. 설정
   * Sigining > Audomatically manage sigining 체크 해제



<br/>

## Mac(OSX) 셋팅 - Android 개발 환경 구성

Mac(OSX) 환경에서 AndroidStudio를 사용하여 Android 앱을 빌드하는 방법을 설명한다.



### 패키지 설치

1. node, watchman 설치 (Mac-iOS 환경 구성 단계에서 이미 설치한경우 skip)

   ```sh
   $ brew install node
   $ brew install watchman
   ```

2. JDK 설치

   ```sh
   $ brew tap AdoptOpenJDK/openjdk
   $ brew cask install adoptopenjdk8
   ```

   

### AndroidStudio 설치

[Android 스튜디오 다운로드 사이트](https://developer.android.com/studio?hl=ko)에 접속하여 설치



### Android file transper 설치

[Android File Transfer 다운로드 사이트](https://www.android.com/filetransfer/)에 접속하여 설치



### ADB 설정 및 디바이스 연결

`.bah_profile`을 열어서 ADB 환경변수 작성

```sh
# 환경변수 설정 여부 확인
$ adb --version
-bash: adb: command not found

# 설정파일 open
$ open -e.bash_profile
```

```sh
# Android ADB
export ANDROID_HOME= #Android sdk path 입력 (ex: "/Users/oyis/Library/Android/sdk")
PATH="${PATH}:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools"
export PATH
```

```shell
# 환경변수 설정 완료 여부 확인
$ adb --version
Android Debug Bridge version 1.0.41
Version 29.0.5-5949299
Installed as /Users/oyis/Library/Android/sdk/platform-tools/adb
```



### 앱 빌드

1. 기기 연결
   * 실물 기기: 디바이스의 *개발자모드* 및 *USB디버깅* 활성화 된 상태에서 케이블 연결
   * 애뮬레이터(AVD): Android Studio에서 AVD image 생성

2. 앱 빌드

   * react-native cli로 빌드하는 방법 (react 코드를 수정중일 때 사용)

     ```sh
     # 프로젝트 path로 이동
     $ cd {PROJECT_PATH}
     
     # 앱 빌드
     $ react-native run-android
     ```

   * AndroidStudio에서 앱 빌드 후 react-native에 연결하는 방법 (native 코드를 수정중일 때 사용)

     ```sh
     # Android 프로젝트 path로 이동
     $ cd android
     
     # react-native 실행 및 연결
     $ react-native start
     $ adb reverse tcp:8081 tcp:8081
     ```

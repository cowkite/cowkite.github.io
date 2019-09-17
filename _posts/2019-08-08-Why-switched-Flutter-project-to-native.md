---
title: "Flutter 프로젝트를 네이티브로 전환한 이유"
date: 2019-08-08 18:28:57
categories: development flutter
---

## Flutter
[Flutter](https://flutter.dev/)는 확실히 각광받고 있는 플랫폼이다. 코드로 UI를 작성한다는 점이나 compat library들이 필요하지 않다는 점, 그리고 이젠 아니라고하면 섭섭하기까지한 리액티브뷰까지.

사실 제작년에 [ReactNative](https://facebook.github.io/react-native/)를 사용하면서는 잦은 업데이트와 업데이트시마다 생겨나는 매번 새로운(^^) 이슈 덕분에 안정화되지 않은 플랫폼을 도입하는 것에 공포증(?)이 생겼는데,
Flutter는 작년 12월을 기준으로 버전이 v1.0.0대로 올라왔고, 이 글을 쓰는 지금은 어느 새 v1.7.8이므로 (개발 당시 0.3 버전대였던 ReactNative에 비해서는) 꽤나 안정적으로 보였다.

마침 회사에서 새로 시작하게 된 프로젝트에서 Flutter를 사용해보자는 의견이 있었고,
어차피 UI 자체는 TypeScript로 개발해서 앱에는 웹뷰 형태로만 붙이는 간단한 앱이었기 때문에 '한번 해볼까?'하는 느낌으로 사내 첫 Flutter앱이 탄생 되었다.

그러나... 빌드가 잘만 되던 프로젝트가 코드 한줄, 설정 하나 건드리지 않았는데 어느 순간부터 본인 컴퓨터에서 빌드가 안 된다며.. ~~(복선)~~
팀원 한분이 프로젝트에서 탈락아닌 탈락을 하게되고..!

정말 똑같은 코드인데도 내 컴퓨터에서는 쌩쌩하게 빌드되는 덕분에 ~~Flutter에게 선택받은~~ 내가 프로젝트를 이어가게 되었다.

그리고 순탄하게 기존에 존재하던 생명주기 이슈와 각 플러그인별 버전 충돌 이슈도 해결하며! 이슈 없이 프로젝트가 진행되는 듯 싶었으나..!

**나는 일주일만에 Flutter를 버리고 네이티브 프로젝트로 전환하게 된다.**

<br>

## Flutter의 문제점

난 v1.7.8+hootfix3 버전을 사용했고, 일주일동안 겪은 Flutter의 이슈는 이렇다.

### 1. 갑자기 코드 반영이 안된다.
코드를 수정한 뒤 hot reloading, hot start 혹은 다시 run을 해도 수정된 코드가 반영이 안 된다.
이렇게 한번 코드 반영이 안 되기 시작하면 AndroidStudio를 재시작하거나 `flutter clean` 등 별 짓을 다해도 코드 반영이 안된다.

관련 이슈: [Flutter doesn't update the code changes on Android device](https://github.com/flutter/flutter/issues/13968)

딱히 에러를 내거나 그러지 않았는데도 왠지 모르게  어느 순간 코드가 갱신되지 않고..
난 그것도 모르고 '어라 이게 왜 안 되지;;'라며 삽질을 해댔다.. ㅠㅠ

### 2. clean 하고나면 잘 되던 코드도 갑자기 빌드가 안된다.
1번 이슈 등으로 인해 'flutter clean' 명령어를 쓰고나면, 갑자기 잘 되던 코드도 빌드가 안된다.
당연하지만 에러로그도 안 뜬다. (진짜 에러면 고치기라도 하지ㅠㅠ)
이렇게 되면 1번처럼 별짓을 해도 안되고..

더 웃긴건 localHistory 보면서 하나하나 롤백하다보면 어느 시점에 빌드가 된다.. ^^
빌드 성공 후 다시 최신 버전 코드로 빌드하면 어이없게도 아깐 안 되던게 빌드가 잘됨.
시간은 시간내로 날리고 이게 뭐하는 짓인가 싶다 ㅠ

### 3. hotStart ('R' command) 시 기기와의 연결이 끊긴다.
단순 UI 작업이 아니라 로직에 대한 작업을 해야해서 메인화면 재호출을 위해서는 `hotStart` 기능을 사용한다.
`hotReload`, `hotStart`는 flutter의 자랑이지만, 'R'키를 누르는 순간 빨간색으로 `Lost connection to device` 로그가 뜨면서 앱이 종료된다.

관련 이슈: [Lost connection to device](https://github.com/flutter/flutter/issues/22299)

당황하며 앱을 재실행해봐야 hotReload하며 변경한 수정사항은 앱을 다시 빌드해서 밀어넣어야만 반영되기 때문에 다시 앱을 빌드해야하고..
결국 hotStart는 무용지물이며 사용할 수 조차 없다. hotStart 때문에 hotReload까지 불편해져버리는 상황..
심지어 다른 개발자들은 hotReload 중에도 기기 연결이 끊긴단다.

### 4. 네이티브 코드 작업이 어렵다.
아무리 하이브리드 앱이여도 네이티브 코드 작업할 일이 생긴다.
그러나 flutter project에서 네이티브 코드 작업이란, 그냥 MS Notepad 손코딩과 다를바가 없다.
한마디로 **자동완성이 안된다.**
오버라이딩 한줄한줄 직접 입력하고, 메소드명 다 직접 입력하고.. 심지어 import 구문까지 코드 상단에 직접 적어주고..^^

### 5. 빌드 속도가 느리고 앱 용량이 크다.
그냥 WebView만 붙인 앱인데도 빌드가 2분 정도 소요된다. 네이티브 앱이면 30초도 안걸릴 것을..
앱 아이콘 외에는 이미지 파일 하나 없는 프로젝트인데도 기본 4MB.. (debug build 기준)
심지어 우리 앱은 안드로이드(그것도 특정 디바이스)만 지원할 앱이라 굳이 이 이슈를 안고가면서 flutter를 유지할 필요가 없었다.

<br>

## Flutter의 잘못은 아니다

개발 속도를 줄여준다는 Flutter의 호언장담과는 달리, 위 이슈들로 인해 체감상 두배 이상의 개발 시간이 소요됐다. ㅠㅠ
솔직히 모든 신기술에 있어 아무리 러닝커브를 이슈로 이야기해봐야, 사실상 러닝커브보다는 플랫폼의 비안정화가 가장 큰 장벽이라고 생각한다.

어쨌든 flutter는 여전히 잘 나가고있고, 수준급의 개발자들이 지속적으로 업데이트하고 있는만큼 버전이 올라갈 수록 더욱 안정적이게 될 것이라고 생각한다.
그 때쯤이면 좀 써볼만하겠지.. ㅠㅠ
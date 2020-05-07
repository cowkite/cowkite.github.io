---
layout: post
title: Android와 iOS에서 ReactNative NativeBridge 구현하기
subtitle: Kotlin과 Swift 환경에서의 ReactNative 브릿지 구성
type: Develop
blog: true
text: true
header-img: "img/about.png"
post-header: true
date: 2020-04-27 11:23
---



Firebase Analaytics 연동을 해야하는데, react 단에서 발생되는 이벤트를 각 OS의 Analytics 대시보드로 나타내야했다. [React Native Firebase](https://rnfirebase.io/) 라이브러리가 있긴했지만 나중에 커스텀하게 이벤트를 정의하다보면 제약사항이 많을 것 같아 직접 NativeBirdge를 구현해보기로 했다.



## Android (Kotlin) 브릿지 생성

네이티브 브릿지를 만들기 위해서는 구현부인 Module 클래스와 모듈을 패키징하기 위한 Package 클래스를 생성해야한다.



### Module 클래스 구현

`ReactContextBaseJavaModule`를 상속받은 클래스를 생성한다.

```kotlin
class AnalyticsModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {
  override fun getName(): String {
        return "SomeMoudule"
    }
}
```

이 때 `getName(): String` 메소드를 상속받아서 모듈 이름을 반환해주면 된다.

그리고 나면 구현하고자 하는 코드들을 메소드 단위로 작성하고, 메소드 레벨에 `@ReactMethod` 어노테이션을 태그해주면 react에서 해당 메소드들을 사용할 수 있다.

대충 아래와 같은 모양이다.

```kotlin
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.bridge.ReactContextBaseJavaModule
import com.facebook.react.bridge.ReactMethod

class SomeModule(reactContext: ReactApplicationContext) : ReactContextBaseJavaModule(reactContext) {
    override fun getName(): String {
        return "SomeModule"
    }

    @ReactMethod
    public fun someMethod(someParameter: String) {
        // TODO What you want to do
    }
}
```



### Package 클래스 구현

위에서 만든 모듈을 react에서 사용하기 위해 Package 클래스를 생성하여 Application 단과 연결해주어야 한다. 패키지는 여러개의 모듈을 한번에 패키징해서 반환하기때문에, 앱 당 1개만 있어도 되며 혹은 라이브러리화하여 큰 기능 단위로 하나로 묶어도 된다.

```kotlin
import android.view.View
import com.facebook.react.ReactPackage
import com.facebook.react.bridge.NativeModule
import com.facebook.react.bridge.ReactApplicationContext
import com.facebook.react.uimanager.ReactShadowNode
import com.facebook.react.uimanager.ViewManager
import java.util.*

class SomePackage : ReactPackage {

    override fun createNativeModules(reactContext: ReactApplicationContext): MutableList<SomeModule> {
        return mutableListOf(SomeModule(reactContext))
    }

    override fun createViewManagers(reactContext: ReactApplicationContext): MutableList<ViewManager<View, ReactShadowNode<*>>> {
        return Collections.emptyList()
    }

}
```

만약 여러개의 모듈을 하나로 패키징하고 싶다면, 해당 모듈들의 공통 부모가 되는 BaseModule 클래스를 상속받게 한 뒤 `createNativeModules()`의 반환값을 `MutableList<BaseModule>`로 반환해주면 된다.



### Application 연동

아마 ReactNative 최초 작업 시 GettingStart를 진행하면서 앱의 Application 클래스에 `ReactApplication`을 상속받아 `getReactNativeHost(): ReactNativeHost` 메소드를 구현했을 것이다. 이 때 반환하는 ReactNativeHost 인스턴스 중 `getPackages(): List<RyeactPackage>` 메소드에, 위에서 구현한 패키지를 리스트에 추가하여 반환하면 된다.

```kotlin
override fun getPackages(): List<ReactPackage> {
    val packageList = PackageList(this).packages
    packageList.add(AnalyticsPackage())
    return packageList
}
```



## iOS (Swift) 브릿지 생성

swift 개발환경인 경우 .swift 클래스에 코드 구현부 작성 후 .m 클래스를 통해 Objective-C인 ReactNative 환경에서 사용할 수 있도록 연결해주어야 한다.

### Module 클래스 구현

iOS에서는 따로 상속받아야할 클래스가 없어서 굳이 모듈..은 아니고 그냥 구현체 클래스라고 불러도되지만, react에서 동일한 코드로 각 OS를 호출하기 위해서는 Android에서 구현한 모듈명과 같은 클래스명을 사용해주어야한다. 따라서 똑같이 모듈이라고 칭했다.

```swift
@objc(SomeModule)
class SomeModule: NSObject {
  
}
```

자유롭게 클래스를 생성하되 클래스 레벨에 `@objc` 어노테이션을 태그하여 사용하고자하는 클래스명을 명시 해주어야 한다.

그 다음은 구현하고자 하는 코드들을 메소드 단위로 작성하고, 메소드 레벨에 똑같이 `@objc` 어노테이션을 사용하여 react에서 접근할 수 있도록 한다.

대충 아래와 같은 모양이다.

```swift
@objc(SomeModule)
class SomeModule: NSObject {
  @objc func someMethod(_ someParameter: String) {
    // TODO What you want to do
  }
}
```

이때 주의할 점은 첫번째 파라미터 앞에 '_'(underscope)를 넣어주어야 한다. 그렇지 않으면 `@objc` 어노테이션에 파라미터명을 모두 명시를 해주어야하는데, .m에서 Objective-C를 연결할 때 첫번째 파라미터로 함수명을 넘기기 때문에 실제 메소드 파라미터갯수와 일치하지 않는 오류가 발생한다. 



### Objective-C 브릿지 구현

위에서 말한대로 Objective-C와 연동하기위한 .m 클래스를 생성해주어야 한다. (ReactNative iOS 표준이 swift였으면 이런 번잡한짓 안 해도 될텐데..)

우선 `<React/RCTBridgeModule.h>` 헤더파일을 import 받은 뒤 interface를 작성해주어야한다. interface 작성을 위해서는 `RCT_EXTERN_MODULE`와 `RCT_EXTERN_METHOD` 키워드를 사용하여 모듈명과 메소드명을 적어주면 된다.

```objective-c
#import <React/RCTBridgeModule.h>

@interface RCT_EXTERN_MODULE(SomeModule, NSObject)
RCT_EXTERN_METHOD(someMethod:(NSString *)someParameter)
@end
```

메소드 인터페이스를 작성할때, 첫번째 파라미터에 메소드명을 명시해주어야한다. 이게 파라미터가 없는 상황과 1개인 상황, 그리고 다수개인 상황에서 작성이 조금 헷갈리는데, 대충 아래처럼 사용하면 된다.

```objective-c
RCT_EXTERN_METHOD(someMethod)
RCT_EXTERN_METHOD(someMethod:(NSString *)param)
RCT_EXTERN_METHOD(someMethod:(NSString *)param1 param2:(NSString *)param2)
RCT_EXTERN_METHOD(someMethod:(NSString *)param1 param2:(NSString *)param2 param3:(NSString *)param3)
```

(정말 objective-C는 아무리봐도 정감이 안 간다.. 핵극혐..)



## React에서의 브릿지 사용

react에서 사용할 때는 별거 없이 `NativeModules` 상속받은 뒤, 구현한 모듈의 메소드를 호출해주면 된다.

```react
import { NativeModules } from "react-native";

const someConst = () => {
  NativeModules.SomeModule.someMethod("someParameter")
}
```



브릿지 생성이나 연동이나 iOS가 훨씬 손도 덜 가는 것 같긴한데, 정작 swift 연동 때문에 iOS에서 삽질을 오래했다ㅠㅠ 대체 왜 아직도 legacy Java와 Objective-C를 표준 가이드로 제공하는지 모를일이다..

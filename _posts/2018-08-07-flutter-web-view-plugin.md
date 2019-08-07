---
title: "Flutter WebView Plugin"
date: 2019-08-07 08:57:03
categories: development flutter
---

# Flutter 앱에 웹뷰 적용하기

1. 상단바 영역까지 Full 화면 WebView로 잡히는 문제
initState() 에서 flutterWebViewPlugin.launch() 호출
MediaQuery.of(context).size.width
-> inheritFromWidgetOfExactType(MediaQuery) or inheritFromElement() was called before MainPageState.initState() completed.

2. ^0.2.1+2 -> ^0.3.5로 업데이트
D8: Program type already present: android.support.v4.os.ResultReceiver$MyResultReceiver

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:transformDexArchiveWithExternalLibsDexMergerForDebug'.
> com.android.builder.dexing.DexArchiveMergerException: Error while merging dex archives: ...jar...

3. Create Application for multidex (https://stackoverflow.com/a/54680454/4427613)

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:preDebugBuild'.
> Android dependency 'androidx.vectordrawable:vectordrawable-animated' has different version for the compile (1.0.0-rc01) and runtime (1.0.0) classpath. You should manually set the same version via DependencyResolution

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 25s

4. Remove dependency (https://github.com/flutter/flutter/issues/14020#issuecomment-380178542)
subprojects {
    project.configurations.all {
        resolutionStrategy.eachDependency { details ->
            if (details.requested.group == 'androidx.appcompat'
                    && !details.requested.name.contains('multidex')) {
                details.useVersion "1.0.0"
            }
        }
    }
}

e: D:\soyeon.kim\Developer tool\flutter_windows_v1.7.8+hotfix.3-stable\flutter\.pub-cache\hosted\pub.dartlang.org\barcode_scan-0.0.8\android\src\main\kotlin\com\apptreesoftware\barcodescan\BarcodeScannerActivity.kt: (8, 31): Unresolved reference: ActivityCompat
e: D:\soyeon.kim\Developer tool\flutter_windows_v1.7.8+hotfix.3-stable\flutter\.pub-cache\hosted\pub.dartlang.org\barcode_scan-0.0.8\android\src\main\kotlin\com\apptreesoftware\barcodescan\BarcodeScannerActivity.kt: (9, 27): Unresolved reference: content
e: D:\soyeon.kim\Developer tool\flutter_windows_v1.7.8+hotfix.3-stable\flutter\.pub-cache\hosted\pub.dartlang.org\barcode_scan-0.0.8\android\src\main\kotlin\com\apptreesoftware\barcodescan\BarcodeScannerActivity.kt: (86, 13): Unresolved reference: ContextCompat
e: D:\soyeon.kim\Developer tool\flutter_windows_v1.7.8+hotfix.3-stable\flutter\.pub-cache\hosted\pub.dartlang.org\barcode_scan-0.0.8\android\src\main\kotlin\com\apptreesoftware\barcodescan\BarcodeScannerActivity.kt: (89, 13): Unresolved reference: ActivityCompat

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':barcode_scan:compileDebugKotlin'.
> Compilation error. See log for more details

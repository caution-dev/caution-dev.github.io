---
layout: post
title:  "iOS Application state"
date:   2019-03-14 15:06:06 +0900
categories: iOS
tags: iOS 면접준비
author: Juhee Kim
mathjax: true

---

## Applicsation state

iOS 에서 앱은 5 가지의 상태로 구분이 가능하며 항상 하나의 상태를 가지고 있습니다.

* Not Running :  앱이 실행되지 않았거나, 시스템에서 종료되었습니다.
* In-Active : 앱이 Foreground에서 실행중이지만, 이벤트를 받을 수 없습니다. 대개 이 상태에 잠시 머물렀다가 다른 상태로 전이됩니다.
* Active : 일반적으로 앱이 화면에 떠 있을 때의 상태입니다. 이벤트를 받을 수 있습니다.
* Background : 앱이 백그라운드에서 코드를 실행하고 있습니다. 대부분의 앱은 일시 중지 상태로 잠시 이 상태가됩니다. 그러나 추가 실행 시간을 요청하는 앱은 일정 기간 동안 이 상태로 남아있을 수 있습니다. 또한 백그라운드로 직접 실행되는 앱은 비활성 상태 대신 이 상태로 전환됩니다. 
* Suspended : 앱이 백그라운드에 있지만 코드를 실행하지 않습니다. 시스템은 앱을 자동으로 이 상태로 이동시키고 그렇게하기 전에 앱에 알리지 않습니다. 일시 중지 된 앱은 메모리에 남아 있지만 코드는 실행하지 않습니다. 메모리 부족 상태가 발생하면 시스템은 예고없이 일시 중단 된 앱을 제거하여 메모리를 확보합니다.

![image-20190313203353909](https://docs-assets.developer.apple.com/published/f5ae1a0785/00b28327-17dc-4f0c-866f-29f854edfce3.png))

## 상태전이

 사용자의 동작에 따라 앱의 상태가 변경됩니다.

* 시나리오 1. 사용자가 앱을 실행합니다.
  * Not Running >> In-Active >> Active
* 시나리오 2. 앱 실행 도중 홈 버튼을 누릅니다.
  * Active >> In-Active >> Background
* 시나리오 3. 앱을 다시 켭니다.
  * Background >> Active
* 시나리오 4. 앱이 백그라운드에 있다가 Suspended 상태로 전이됩니다.
  * Active >> In-Active >> Background >> Suspended

## 상태 변화에 따라 다른 동작을 처리하기 위한 Delegate 메서드

*  application:willFinishLaunchingWithOptions: 앱 런칭이 끝나기 전 불리는 메소드로, 앱 런칭 타임에서 코드를 실행할 수 있는 첫 번째 시점입니다. 
* application:didFinishLaunchingWithOptions : 앱 런칭 시점에서 Application 단의 초기화 작업등을 수행할 수 있습니다.
* applicationDidBecomeActive : Active 상태로 전이 될 때 호출됩니다. Background 상태의 앱이 다시 호출되어 Active 상태가 되도 호출됩니다.
* applicationWillResignActive : 앱이 Active 상태를 벗어나기 전에 호출됩니다. (ex. 홈 버튼을 누르면 호출됨) 단, 앱이 Background 상태로 전이된다는 보장은 없습니다. In-Active에 잠깐 머물다가 다시 바로 Active 상태로 바뀔 수도 있습니다.
* applicationDidEnterBackground : Background로 진입 후 호출됩니다. 언제든 System에 의해 Suspended 상태로 전이될 수 있으므로 나중에 재생성할 수 있는 모든 리소스들을 제거하고 사용자 데이터를 저장한는 등의 작업을 수행하는 용도로 사용할 수 있다. 이 메소드 호출 이전에 applicationWillResignActive가 호출됩니다.
* applicationWillEnterForeground : Background를 벗어나 Foreground로 진입될 것을 알려준다. (아직 Active 상태는 아님) applicationDidEnterBackground 에서 해제한 리소드들을 재생성하는 용도로 사용할 수 있습니다. 이 메소드 호출 뒤에 applicationDidBecomeActive 가 호출됩니다.
* applicationWillTerminate : 앱이 종료될 것임을 알려줍니다. 앱이 Suspended  상태에서 종료되는 것이라면 호출되지 않는다.

### 앞 선 시나리오 적용

- 시나리오 1. 사용자가 앱을 실행합니다.

  1. Not Running 
  2. In-Active 
     * application:willFinishLaunchingWithOptions
     * application:didFinishLaunchingWithOptions

  3. Active
     * applicationDidBecomeActive 

- 시나리오 2. 앱 실행 도중 홈 버튼을 누릅니다.

  1. Active
     * applicationWillResignActive
  2. In-Active >> Background
     - applicationDidEnterBackground 

- 시나리오 3. 앱을 다시 켭니다.

  1. Background
     - applicationWillEnterForeground
  2. Active
     - applicationDidBecomeActive 

- 시나리오 4. 앱이 백그라운드에 있다가 Suspended 상태로 전이됩니다. 

  1. Active
     * applicationWillResignActive
  2. In-Active >> Background
     * applicationDidEnterBackground
  3. Suspended 

## Backgound 상태에서 할 수 있는 작업

![image-20190314162850542](/Users/juhee/Library/Application Support/typora-user-images/image-20190314162850542.png)

| Xcode background Mode            | UIBackgroundModes 값 | 설명                                                         |
| -------------------------------- | -------------------- | ------------------------------------------------------------ |
| Audio and AirPlay                | audio                | 백그라운드에서 들을 수 있는 컨텐츠를 재생하거나 오디오를 녹음합니다. (이 콘텐츠에는 AirPlay를 사용하여 스트리밍 오디오 또는 비디오 콘텐츠가 포함됩니다.) 사용자는 앱을 처음 사용하기 전에 마이크를 사용할 수있는 권한을 부여해야합니다. |
| Location updates                 | location             | 백그라운드에서 실행 중에도 사용자의 위치 정보를 제공받습니다. |
| Voice ove IP                     | voip                 | 인터넷 연결을 사용해서 전화할 수 있도록 합니다.              |
| Newsstand downloads              | newsstand-content    | 백그라운드에서 잡지나 뉴스들을 다운로드할 수 있는 앱입니다.  |
| External accessory communication | external-accessory   | 외부 엑서서리 프레임워크를 통해 정기적인 일정으로 업데이트를 제공해야 하는 하드웨어 액서서리와 함께 동작합니다. |
| Uses Bluetooth LE accessories    | bluetooth-central    | Core Blutooth 프레임워크를 통해 정기적 업데이트를 제공해야 하는 블루투스 액서서리와 함께 동작합니다. |
| Acts as a Bluetooth LE accessory | bluetooth-peripheral | 이 앱은 Core Blutooth 프레임워크를 통해 주변 장치 모드에서 Blutooth 통신을 지원합니다. 사용자 궈한이 필요합니다. |
| Background fetch                 | fetch                | 앱은 정지적으로 네트워크에서 소량의 데이터를 다운로드하고 처리합니다. |
| Remote notifications             | remote-notification  | 푸쉬 알림이 도착하면 앱에서 콘텐츠 다운로드를 시작하려고 합니다. 이 알림을 사용하여 푸쉬 알림과 관련된 콘텐츠 표시의 지연을 최소화하십니오. |



## 참고

* [App Programming Guide for iOS](https://developer.apple.com/library/archive/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html)
* [Managing your app's life cycle](https://developer.apple.com/documentation/uikit/core_app/managing_your_app_s_life_cycle)
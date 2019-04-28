---
layout: post
title:  "[공식문서]AVAudioSession"
date:   2019-04-28 14:26:06 +0900
categories: Docs
tags: iOS Apple-Docs Audio 번역 AVFoundation
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

앱에서 오디오를 어떻게 사용할 것인지 시스템과 통신하는 매개 객체입니다.

## Declaration
```swift
class AVAudioSession : NSObject
```

## Overview
오디오 세션은 앱과 운영 체제, 기본 오디오 하드웨어 사이의 중계자 역할을 합니다. 오디오 세션을 사용하면 필요한 오디오 하드웨어와의 상호작용이나 특정 동작을 설명할 필요 없이 운영 체제에 앱의 오디오 특성을 알릴 수 있습니다. 이 동작은 이러한 세부 사항의 관리를 오디오 세션에 위임함으로써 운영 체제가 사용자의 오디오 환경을 가장 잘 관리할 수 있습니다.

모든 iOS와 tvOS 앱은 다음과 같이 사전 구성된 기본 오디오 세션이 있습니다.

* 오디오 재생을 지원되지만 오디오 녹음은 허용되지 않습니다. ( 오디오 녹음은 tvOS에서 지원되지 않습니다. )
* iOS에서 링/무음 스위치를 무음 모드로 설정하면 앱에서 재생되는 모든 오디오가 음소거됩니다.
* iOS에서 기기가 잠기면 앱의 오디오는 음소거됩니다.
* 앱이 오디오를 재생하면, 다른 백그라운드 오디오는 음소거됩니다.

기본 오디오 세션은 유용한 동작들을 제공하지만, 미디어 재생 앱을 만들 때에 필요한 일반적인 오디오 동작을 제공하지는 않습니다. 기본 동작을 변경하려면 앱의 오디오 세션 카테고리를 구성해야합니다.

사용할 수 있는 카테고리는 7가지가 있지만 대부분의 플레이어 앱들은 [playback]()이 필요합니다. 이 카테고리는 오디오 재생이 이 앱의 주요 특징임을 알려줍니다. 이 카테고리를 설정하게 되면 앱의 오디오는 벨소리/무음 스위치가 무음 모드에서 설정된 상태에서도 앱의 오디오가 계속 재생됩니다. 이 카테고리를 사용하면 오디오나 AirPlay, PIP 백그라운드 모드를 사용할 때 앱에서 백그라운드로 오디오를 재생할 수 있습니다. 더 많은 정보를 확인하려면, [Background Audio 사용하기](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/enabling_background_audio)를 확인하세요.

AVAudioSession 객체를 사용하여 앱의 오디오 세션을 구성합니다. AVAudioSession은 오디오 세션 카테고리를 설정하고 다른 요소들을 구성하는 데 사용되는 싱글턴 객체입니다. 앱 수명주기 전체에서 오디오 세션과 상호 작용할 수 있지만, 다음 예와 같이 앱 실행시에 이러한 구성(configuration)을 수행하는 것이 유용합니다.

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey : Any]?) -> Bool {
    let audioSession = AVAudioSession.sharedInstance()
    do {
        try audioSession.setCategory(AVAudioSessionCategoryPlayback)
    } catch {
        print("Setting category to AVAudioSessionCategoryPlayback failed.")
    }
    // Other project setup
    return true
}
```
이 카테고리는 ```setActive(_:)``` 혹은  ```setActive(_:options:)``` 메소드를 사용하여 오디오 세션을 활성화할 때 사용됩니다.

> Note
> 카테고리를 설정한 후 어디서든 오디오 세션을 활성화할 수 있습니다. 하지만 일반적으로 앱이 오디오 재생을 시작할 때까지 이 호출을 연기하는 것이 바람직합니다. 이 호출을 연기하면 진행중인 다른 백그라운드 오디오가 중간에 중단되지 않습니다.

## Topics
### First Steps
앱의 Audio Session 싱글턴 객체에 접근하여 카테고리, 모드, 선호되는 기기 설정을 구성하세요.
```swift
// 공유된 audio session 인스턴스를 반환합니다.
class func sharedInstance() -> AVAudioSession
```

### Audio Session 구성하기
Audio session의 카테고리, 옵션, 모드를 설정하세요.
```swift
// 현재 오디오 세션의 카테고리
var category: AVAudioSession.Category

// 현재 오디오 세션의 카테고리를 설정합니다.
func setCategory(AVAudioSession.Category)

// 특정 옵션과 함께 오디오 세션의 카테고리를 설정합니다.
func setCategory(AVAudioSession.Category, options: AVAudioSession.CategoryOptions)

// 오디오 세션의 카테고리, 모드, 원하는 옵션을 설정합니다.
func setCategory(AVAudioSession.Category, mode: AVAudioSession.Mode, options: AVAudioSession.CategoryOptions)

// 현재 기기에 설정 가능한 오디오 세션 카테고리들입니다.
var availableCategories: [AVAudioSession.Category]

// 현재 오디오 세션 카테고리와 관련된 옵션들입니다.
var categoryOptions: AVAudioSession.CategoryOptions

// 현재 오디오 세션 모드입니다.
var mode: AVAudioSession.Mode

// 오디오 세션 모드를 설정합니다.
func setMode(AVAudioSession.Mode)

// 이 기기에서 사용가능한 오디오 세션 모드들입니다.
var availableModes: [AVAudioSession.Mode]
```

### Audio Session 활성화하기
카테고리와 모드 구성과 함께 오디오 세션을 활성화합니다.
```swift
// 특정 옵션을 사용하여 앱의 오디오 세션을 활성화하거나 비활성화 합니다.
func setActive(Bool, options: AVAudioSession.SetActiveOptions)

// watchOS에서 오디소 세션을 비동기로 활성화합니다.
func activate(options: AVAudioSessionActivationOptions, completionHandler: (Bool, Error?) -> Void)
```

### 녹음 허용 요청하기
사용자에게 오디오 녹음 허용을 요청합니다.

오디오를 녹음하려면 사용자의 명백한 허용이 필요합니다. 녹음이 가능한 카테고리를 사용하는 오디오 세션이 처음으로 오디오 입력 라우터를 사용하려고 시도한다면, 시스템에서 자동적으로 사용자에게 권한을 요청합니다. ```requestRecordPermission(_:)```메소드를 호출함으로써 먼저 권한을 요청할 수 있습니다. 사용자가 이 앱에서 녹음을 허용하지 않으면 무음만 녹음됩니다.

```swift
// 현재 녹음 허용 상태를 반환합니다.
var recordPermission: AVAudioSession.RecordPermission

// 사용자에게 오디오 녹음 허용을 요청합니다.
func requestRecordPermission(PermissionBlock)
```

### 다른 오디오에 응답하기
다른 앱이 백그라운드 오디오를 재생하려고 할 때 이 앱에서 오디오를 어떻게 재생할 것인지, 혹은 재생을 할 것인지 아닐지를 결정합니다.

```swift
// 다른 앱이 현재 오디오를 재생하고 있는지를 가리키는 Bool 값입니다.
var isOtherAudioPlaying: Bool

// 다른 앱이 무음으로 오디오를 재생해야하는지
var secondaryAudioShouldBeSilencedHint: Bool
```

### 오디오 세션 알림에 응답하기
오디오 세션을 활성화하기 전에 상태 변경 알림을 수신하려면 등록해야합니다.

AVAudioSession은 간섭, 라우터 변경 및 미디어 서비스 재설정과 같은 중요한 상태 변경을 나타내는 알림을 발신합니다. 오디오 세션을 활성화하기 전에 이러한 알림을 수신하려면 등록하세요!

#### 관련 아티클
* [Responding to Audio Session Interruptions](https://developer.apple.com/documentation/avfoundation/avaudiosession/responding_to_audio_session_interruptions)
* [Responding to Audio Session Route Changes](https://developer.apple.com/documentation/avfoundation/avaudiosession/responding_to_audio_session_route_changes)

```swift
// 오디오 간섭이 발생됐을 때 알려줍니다.
class let interruptionNotification: NSNotification.Name

// 시스템 오디오 라우터가 변경되었을 때 알려줍니다.
class let routeChangeNotification: NSNotification.Name

// 다른 애플리케이션에서 주요 오디오가 시작되거나 중단되었을 때 발신됩니다.
class let silenceSecondaryAudioHintNotification: NSNotification.Name

// 미디어 서버가 중단되었을 때 발신됩니다.
class let mediaServicesWereLostNotification: NSNotification.Name

// 미디어 서버가 재시작되었을 때 발신됩니다.
class let mediaServicesWereResetNotification: NSNotification.Name
```

### 오디오 라우터 제어하기
장치의 오디오 라우터 상태를 검색하고 원하는 입력/출력 라우터 설정을 구성하세요.

오디오 라우터는 오디오 신호의 전자 경로입니다. 장치 오디오 경로의 상태를 검사하고 원하는 입력 및 출력 경로 설정을 구성하세요.

```swift
// 현재 오디오 입력과 출력 라우터를 설명하는 객체입니다.
var currentRoute: AVAudioSessionRouteDescription

// 오디오 입력 경로가 가능한지 가리키는 Bool 값입니다.
var isInputAvailable: Bool

// 라우팅 가능한 입력 port들의 배열입니다.
var availableInputs: [AVAudioSessionPortDescription]?

// 선호하는 오디오 라우팅 입력 포트입니다.
var preferredInput: AVAudioSessionPortDescription?

// 선호하는 오디오 라우팅 입력 포트를 선정합니다.
func setPreferredInput(AVAudioSessionPortDescription?)

// 오디오 세션의 현재 입력 포트에 사용할 수 있는 데이터 소스들의 배열입니다.
var inputDataSources: [AVAudioSessionDataSourceDescription]?

// 현재 선택된 입력 데이터 소스입니다.
var inputDataSource: AVAudioSessionDataSourceDescription?

// 오디오 세션의 최근 입력 포트의 데이터 소스를 선택합니다.
func setInputDataSource(AVAudioSessionDataSourceDescription?)

// 현재 오디오 라우터의 사용가능한 출력 데이터 소스들의 배열입니다.
var outputDataSources: [AVAudioSessionDataSourceDescription]?

// 현재 선택된 출력 데이터 소스입니다.
var outputDataSource: AVAudioSessionDataSourceDescription?

// 오디오 세션의 출력 데이터 소스를 설정합니다.
func setOutputDataSource(AVAudioSessionDataSourceDescription?)

// 현재 오디오 라우터를 일시적으로 변경합니다.
func overrideOutputAudioPort(AVAudioSession.PortOverride)

// 현재 라우팅 정책입니다.
var routeSharingPolicy: AVAudioSession.RouteSharingPolicy

// 세션 카테고리, 모드, 라우트 공유 정책, 그리고 옵션들을 설정합니다.
func setCategory(AVAudioSession.Category, mode: AVAudioSession.Mode, policy: AVAudioSession.RouteSharingPolicy, options: AVAudioSession.CategoryOptions)
```
### 오디오 채널 제어하기
현재 오디오 장치에서 사용 가능한 오디오 입력 및 출력 채널 수를 검사하고 구성합니다.

```swift
// 현재 라우터의 오디오 입력 채널의 수입니다.
var inputNumberOfChannels: Int

// 현재 오디오 라우터에서 사용가능한 입력 채널의 최대값입니다.
var maximumInputNumberOfChannels: Int

// 현재 라우터에서 선호되는 입력 채널 수 입니다.
var preferredInputNumberOfChannels: Int

// 현재 라우터에서 선호되는 입력 채널 수를 설정합니다.
func setPreferredInputNumberOfChannels(Int)

// 오디오 출력 채널 수 입니다.
var outputNumberOfChannels: Int

// 현재 오디오 라우터에서 사용가능한 출력 채널의 최대값입니다.
var maximumOutputNumberOfChannels: Int

// 현재 라우터에서 선호되는 출력 채널의 수 입니다.
var preferredOutputNumberOfChannels: Int

// 현재 라우터에서 선호되는 출력 채널 수를 설정합니다.
func setPreferredOutputNumberOfChannels(Int)
```

### Audio 기기 설정 제어하기
입력 증폭(gain), 샘플 속도 및 I/O 버퍼 지속 시간과 같은 오디오 장치 설정을 검사하고 구성합니다. 입력 증폭, 샘플 속도 및 I / O 버퍼 지속 시간과 같은 오디오 장치 설정을 검사하고 구성합니다.

```swift
// 세션과 연관된 입력에 적용되는 증폭값 입니다.
var inputGain: Float

// 입력 증폭이 설정될 수 있는지 가리키는 Bool 값입니다.
var isInputGainSettable: Bool

// 입력 증폭을 특정 값으로 변경합니다.
func setInputGain(Float)

// 사용자에 의해 설정된 시스템 전체 출력 볼륨값입니다.
var outputVolume: Float

// 초단위 오디오 입력 대기 시간
var inputLatency: TimeInterval

// 초단위 오디오 출력 대기 시간
var outputLatency: TimeInterval

// 현재 오디오 샘플 속도(Hz)입니다.
var sampleRate: Double

// 선호되는 샘플 속도(Hz)입니다.
var preferredSampleRate: Double

// 입출력 선호 샘플 속도를 설정합니다.
func setPreferredSampleRate(Double)

// 현재 I/O 버퍼 지속 시간(초)입니다.
var ioBufferDuration: TimeInterval

// 선호되는 I/O 버퍼 지속 시간(초)입니다.
var preferredIOBufferDuration: TimeInterval

// 선호되는 I/O 버퍼 지속 시간(초)을 설정합니다.
func setPreferredIOBufferDuration(TimeInterval)
```

### 집약된 I/O 환경 설정하기
집약된 I/O 환경설정을 설정하여 선호하는 오디오 입력 동작을 구성하세요.

iOS 10부터 라이브 사진 기능을 지원하는 iPhone 및 iPad에서 AVCaptureSession의 기본 오디오 입력 구성이 변경되었습니다. 이 변화는 백그라운드 오디오 재생을 간섭하지 않고 라이브 사진을 찍을 수 있도록 합니다. 집약된 I/O 환경설정을 설정하여 선호하는 오디오 입력 동작을 구성하세요.

```swift
// 오디오 세션의 I/O 집약 환경설정을 설정합니다.
func setAggregatedIOPreference(AVAudioSession.IOType)
```

### 참조
 * [AVAudioSession](https://developer.apple.com/documentation/avfoundation/avaudiosession)

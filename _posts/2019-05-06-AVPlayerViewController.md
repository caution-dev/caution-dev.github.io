---
layout: post
title:  "[AVKit] AVPlayer"
date:   2019-05-06 17:16:06 +0900
categories: Apple-Docs
tags: iOS Audio 번역 AVKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

#### Class
## AVPlayerViewController
시스템에서 제공되는 재생 컨트롤러와 함께 플레이어 객체의 비디오 컨텐츠를 보여주는 객체입니다.

## Declaration
```swift
class AVPlayerViewController: UIViewController
```

## Overview
AVPlayerViewController를 사용하면 고유 시스템 플레이어의 스타일 및 기능에 맞게 미디어 플레이 기능을 앱에 손쉽게 추가할 수 있습니다. AVPlayerViewController는 시스템 프레임워크 클래스이므로 플레이어 앱은 추가적인 작업 없이 향후의 운영 시스템의 새로운 미학과 기능을 자동으로 적용할 수 있습니다.

> 중요
>  AVPlayerViewController의 하위 클래스를 생성하지 마세요. 이 클래스의 메서드를 재정의하는 것은 지원되지 않으며 정의되지 않은 동작이 발생할 수 있습니다.

### AirPlay 지원하기
AirPlay를 사용하면 사용자가 Apple TV에서 비디오 컨텐츠를 재생하고 오디오 컨텐츠를 타사 AirPlay 스피커 및 수신기로 스트리밍 할 수 있습니다. AVPlayerViewController는 자동으로 AirPlay를 지원하지만 앱에서 활성화되기 위해서는 일부 프로젝트 및 오디오 세션 구성 작업이 필요합니다. 이 작업을 수행하는 방법은 [Configuring Audio Settings for iOS and tvOS](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/ConfiguringAudioSettings/ConfiguringAudioSettings.html#//apple_ref/doc/uid/TP40016757-CH9)의 [Media Playback Programming Guide](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016757)를 참고하세요.


AirPlay 기능에 대한 자세한 내용은 [AirPlay Overview](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/AirPlayGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011045)를 참고하세요.

### PIP 재생 지원하기
iOS 9부터 AVPlayerViewController를 사용하여 지원되는 iPad 모델들에 PIP 재생을 제공할 수 있습니다. PIP 재생은 사용자로 하여금 피디오 플레이어를 작은 떠 있는 창으로 최소화하여 다른 앱에서나 혹은 해당 앱에서 다른 동작들을 수행할 수 있도록 하는 것입니다. 이는 새로운 수준의 멀티태스킹 기능을 제공하여 사용자는 자신의 장치에서 다른 활동을 하면서 계속 컨텐츠를 재생할 수 있습니다. iPad 앱에서 PIP를 지원하는 방법에 대한 자세한 내용은 [AVKit for iOS](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/UsingAVKitPlatformFeatures/UsingAVKitPlatformFeatures.html#//apple_ref/doc/uid/TP40016757-CH5-SW3)의 [Media Playback Programming Guide](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016757)를 참고하세요.

### tvOS에서 AVPlayerViewController 사용하기
tvOS의 ```AVPlayerViewController```는 앱에 고급 Siri 원격 제어 기능을 제공합니다. 이를 통해 사용자는 컨텐츠를 쉽게 재생하고 탐색하고 자막 및 대체 오디오 트랙과 같은 지원되는 기능에 액세스할 수 있습니다. ```AVPlayerViewController```는 또한 "15초 건너 뛰기" 및 "뭐라고 말했나요?"와 같은 Siri 원격 음성 명령을 사용하여 콘텐츠 재생을 제어하도록 지원합니다.


tvOS 용 AVKit은 ```AVPlayerViewController``` 및 ```AVPlayerItem```의 기능을 확장하여 추가적인 컨텐츠 탐색 및 보여주기 방법을 제공합니다.

* **외부 메타 데이터.** 만일 [externalMetadata](https://developer.apple.com/documentation/avfoundation/avplayeritem/1627623-externalmetadata) 프로퍼티를 사용하여 플레이어 뷰 컨트롤러에 표시된 [AVPlayerItem](https://caution-dev.github.io/apple-docs/2019/04/28/AVPlayer.html) 객체와 추가 메타 데이터를 연결하면 AVPlayerViewController UI내에서 해당 정보를 표시할 수 있습니다.
* **탐색 마커 그룹.** 네비게이션 마커 그룹은 사용자가 미디어 프리젠테이션의 중요한 이벤트 사이를 이동할 수 있게 합니다. 탐색 마커 그룹의 가장 일반적인 형태는 장(Chapter) 목록이지만, 탐색을 위한 추가 혹은 대체 수단을 만들 수도 있습니다. (예 : 사용자가 기록된 스포츠 이벤트의 주요 순간 사이를 빠르게 이동할 수 있도록 허용합니다.) 플레이어 뷰 컨트롤러를 통해 사용자는 여러 마커 그룹 중에서 미디어 컨텐츠를 탐색 할 수 있습니다.

  [AVNavigationMarkersGroup](https://developer.apple.com/documentation/avkit/avnavigationmarkersgroup) 클래스를 사용해서 네비게이션 마커를 생성하고 설명을 추가할 수 있으며, [navigationMarkerGroup](https://developer.apple.com/documentation/avfoundation/avplayeritem/1627626-navigationmarkergroups) 프로퍼티를 통해 마커 그룹을 플레이어 뷰 컨트롤러에 표시되고 있는 [AVPlayerItem](https://developer.apple.com/documentation/avfoundation/avplayeritem) 객체와 연결합니다.

* **삽입된 컨텐츠** 일부 컨텐츠는 플레이어 보기에 보여지는 주요 컨텐츠와 연관이 없거나, 다른 프레젠테이션 요구사항을 가질 수 있습니다. 예를 들어, 사용자가 광고를 건너뛰도록 하거나 사용자가 필수 법적 고지를 건너 뛰지 않도록 할 수 있습니다.

  [AVInterstitialTimeRange](https://developer.apple.com/documentation/avkit/avinterstitialtimerange) 클래스를 사용하여 삽입된 컨텐츠를 설명하고, [interstitialTimeRanges](https://developer.apple.com/documentation/avfoundation/avplayeritem/1627628-interstitialtimeranges) 프로퍼티를 사용하여 해당 시간 범위를 현재 플레이어 뷰 컨트롤러에 표시되는 [AVPlayerItem](https://developer.apple.com/documentation/avfoundation/avplayeritem) 와 연결합니다. 그런 다음 삽입된 보기 컨텐츠가 재생될 때 플레이어 뷰 컨트롤러의 [delegate](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/1615840-delegate) 객체에 알림을 보내고 이에 따라 [requiresLinearPlayback](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/1627633-requireslinearplayback) 속성과 같은 재생 옵션을 변경할 수 있습니다.
* **컨텐츠 제안.** TV쇼 시리즈 컨텐츠를 보여줄 때, 현재 에피소드가 끝나면 사용자가 볼 수 있는 추가적인 컨텐츠를 제안하고자 합니다. tvOS 10부터 AVKit 컨텐츠 제안을 사용하여 이 기능을 앱에 쉽게 추가할 수 있습니다.

  [AVContentProposal](https://developer.apple.com/documentation/avkit/avcontentproposal) 클래스를 사용하여 제안된 컨텐츠를 설명하고, 플레이어 뷰 컨트롤러의 [AVPlayerItem](https://developer.apple.com/documentation/avfoundation/avplayeritem))의 [nextContentProposal](https://developer.apple.com/documentation/avfoundation/avplayeritem/1650942-nextcontentproposal) 프로퍼티로 설정하세요. 플레이어 뷰 컨트롤러의 [delegate](https://developer.apple.com/documentation/avkit/avplayerviewcontroller/1615840-delegate) 객체의 메서드를 구현하여 컨텐츠 제안을 보여주거나 제안에 대한 사용자의 수락, 거절, 지연에 응답하는 액션을 수행할 준비를 할 수 있습니다.

 자세한 내용은 [AVKit for tvOS](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/UsingAVKitPlatformFeatures/UsingAVKitPlatformFeatures.html#//apple_ref/doc/uid/TP40016757-CH5-SW4)의 [Media Playback Programming Guide](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/MediaPlaybackGuide/Contents/Resources/en.lproj/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016757)를 참고하세요.

## Topics
### Player 객체에 접근하기
```swift
// player 뷰 컨트롤러에 미디어 컨텐츠를 제공하는 player 입니다.
var player: AVPlayer?
```

### Delegate 객체에 접근하기
```swift
// player 뷰 컨트롤러를 위한 위임자 객체입니다.
var delegate: AVPlayerViewControllerDelegate?

// player 뷰 컨트롤러와 연관해서 미디어 재생 이벤트에 응답하기 위해 메소드들입니다.
protocol AVPlayerViewControllerDelegate
```

### Player View Controller 속성들에 접근하기
```swift
// player view controller가 재생 컨트롤러를 보여줘야 하는지에 대한 Bool 값입니다.
var showsPlaybackConntrols: Bool

// 사용자에 의해 요청이 왔을 때 리시버가 정보 뷰를 보여줘야 하는지에 대한 Bool 값입니다.
var playbackControlsIncludeInfoViews: Bool

// 리시버가 재생 바와 관련된 컨트롤러를 보여주어야 하는지에 대한 Bool 값입니다.
var playbackControlsIncludeTransportBar: Bool

// 플레이어 뷰 컨트롤러가 iPad 에서 PIP를 허용하는지에 대한 Bool 값입니다.
var allowsPictureInPicturePlayback: Bool

// 비디오 컨텐츠와 재생 컨트롤 사이에 보여지는 뷰입니다.
var contentOverlayView: UIView?

// 재생 버튼이 눌렸을 때 player가 자동적으로 풀 스크린으로 보여져야 하는지에 대한 Bool 값입니다.
var entersFullScreenWhenPlaybackBegins: Bool

// 재생이 끝났을 때 player가 풀스크린을 종료해야 하는지에 대한 Bool 값입니다.
var exitsFullScreenWhenPlaybackEnds: Bool

// AVPlayer 객체와 연결된 현재 아이템의 첫번째 비디오 프레임이 보여질 준비가 되었는지에 대한 Bool 값입니다.
var isReadyForDisplay: Bool

// 플레이어 뷰 컨트롤러 뷰의 바운더리 내에서 현재 보여지는 비디오 이미지의 사이즈와 위치.
var videoBounds: CGRect

// 플레이어 뷰 컨트롤러의 뷰의 바운더리 내에서 어떻게 비디오가 보여져야 하는지 정하는 string 값입니다.
var videoGravity: AVLayerVideoGravity

// 사용자가 미디어 컨텐츠를 건너뛸 수 있는지를 결정하는 Bool 값입니다.
var requiresLinearPlayback: Bool

// 재생 컨트롤이 보여질 때에도 영구적으로 가려지지 않을 영역을 나타내는 레이아웃 가이드입니다.
var unobscuredContentGuide: UILayoutGuide

// 플레이어 뷰 컨트롤러가 현재 재생중인 정보 센터를 업데이트하는지 여부를 나타내는 Bool 값입니다.
var updatesNowPlayingInfoCenter: Bool

// 리시버가 화면의 기본 표시 기준을 재생중인 에셋의 기준으로 자동 변경하는지 여부를 나타내는 부울 값입니다.
var appliesPreferredDisplayCriteriaAutomatically: Bool

// 시스템 제공 정보 및 설정 패널과 함께 클라이언트 특정 컨텐츠 및 제어 기능을 제공하는보기 컨트롤러.
var customInfoViewController: UIViewController?
```

### 컨텐츠 제안을 관리하기
```swift
// 컨텐츠 제안의 표시를 담당하는 뷰 컨트롤러
var contentProposalViewController: AVContentProposalViewController!
```

### 건너뛰기 동작 관리
```swift
// 건너뛰기 제스처에 의해 발생된 동작입니다.
var skippingBehavior: AVPlayerViewControllerSkippingBehavior

// 플레이어 뷰 컨트롤러의 건너뛰기 동작을 표시하는 상수
enum AVPlayerViewControllerSkippingBehavior

// 뒤로 건너 뛰기를 사용할 수 있는지 여부를 나타내는 Bool 값입니다.
var isSkipBackwardEnabled: Bool

// 앞으로 건너 뛰기를 사용할 수 있는지 여부를 나타내는 Bool 값입니다.
var isSkipForwardEnabled: Bool
```

### 부제 관리
```swift
// 사용자가 사용할 수 있는 자막 언어 집합을 제한하는 언어 코드의 옵셔널 타입 배열입니다.
var allowedSubtitleOptionLanguages: [String]?

// 사용자가 자막 표시를 비활성화 할 수 있는지 여부를 결정하는 부울 값입니다.
var requiresFullSubtitles: Bool
```

### 프리롤 동영상 광고 재생
```swift
// iAd에 이 앱이 프리롤 광고를 재생할 예정임을 알립니다.
class func preparePrerollAds()

// 프리 롤 동영상 iAd 재생을 요청합니다.
func playPrerollAd(completionHandler: (Error?) -> Void)

// 프리롤 동영상 광고의 재생을 취소합니다.
func cancelPreroll()
```

### Relationships
##### 상속
* UIViewController
##### 프로토콜 채택
* CVarArg,Equatable
* Hashable
* NSExtensionRequestHandling
* UIPasteConfigurationSupporting
* UIStateRestoring
* UIUserActivityRestoring

### 함께보기
 * [Adopting Picture in Picture in a Standard Player](https://developer.apple.com/documentation/avkit/adopting_picture_in_picture_in_a_standard_player) : 플레이어 뷰 컨트롤러를 사용하여 PIP 컨트롤을 플레이어에 추가하세요.
 * [Adopting Picture in Picture in a Custom Player](https://developer.apple.com/documentation/avkit/adopting_picture_in_picture_in_a_custom_player) : PIP (picture-in-picture) 컨트롤러를 사용하여 사용자 지정 플레이어에 PIP (Picture-in-Picture) 컨트롤을 추가하십시오.
 * [Working with Interstitial Content (tvOS)](https://developer.apple.com/documentation/avkit/working_with_interstitial_content_tvos) : 법적 텍스트, 콘텐츠 경고 또는 광고와 같은 추가 콘텐츠를 주요 미디어 콘텐츠와 함께 제시하십시오.
 * [Adding Information to the Info Panel (tvOS)](https://developer.apple.com/documentation/avkit/adding_information_to_the_info_panel_tvos) : Apple TV의 정보 패널에 메타 데이터 정보와 탐색 표시자를 추가하십시오.
 * [class AVPlayerView](https://developer.apple.com/documentation/avkit/avplayerview) : 시스템 개체 재생 컨트롤과 함께 플레이어 개체의 비디오 내용을 표시하는 macOS 전용 객체입니다.
 * [class AVPictureInPictureController](https://developer.apple.com/documentation/avkit/avpictureinpicturecontroller) : iPad에서 크기가 조정 된 부동 창에서 사용자가 시작한 비디오 재생에 응답하는 컨트롤러입니다.
 * [class AVRoutePickerView](https://developer.apple.com/documentation/avkit/avroutepickerview) : 재생 경로 선택 컨트롤을 표시하는 객체입니다.
 * [class AVInterstitialTimeRange](https://developer.apple.com/documentation/avkit/avinterstitialtimerange) : 시청각 자료의 광고 또는 법적 고지와 같은 삽입 광고 콘텐츠의 시간 범위.
 * [class AVNavigationMarkersGroup](https://developer.apple.com/documentation/avkit/avnavigationmarkersgroup) : 시청각 프리젠테이션의 재생을 탐색하기 위한 마커 세트.


### 원문
 * [AVPlayerViewController](https://developer.apple.com/documentation/avkit/avplayerviewcontroller)

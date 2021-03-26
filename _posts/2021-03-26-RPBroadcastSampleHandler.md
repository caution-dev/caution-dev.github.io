---
layout: post
title:  "RPBroadcastSampleHandler"
date:   2021-03-26 14:26:06 +0900
categories: Swift
tags: iOS ReplayKit Swift Apple WWDC
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}


#### Class
## RPBroadcastSampleHandler
ReplayKit에서 수신된 버퍼 개체를 처리하는 개체입니다.

### Declaration
```swift
class RPBroadcastSampleHandler: RPBroadcastHandler
```

### Overview
ReplayKit에 의해 녹화되는 [CMSampleBuffer](https://developer.apple.com/documentation/coremedia/cmsamplebuffer)를 처리하기 위해서는 `RPBroadcastSampleHandler`를 서브클래싱하여 사용해야 합니다. 확장자의 Info.plist 파일에서 `RPBroadcastProcessModeSampleBuffer`를 `RPBroadcastProcessModeSampleBuffer`로 설정하여 이 처리 모드를 활성화합니다.

하위 클래스에서 비디오 및 오디오 버퍼를 처리하는 `processSampleBuffer(_:with:)` 메서드와 `broadcastStarted(SetupInfo: )`, `broadcastFinished()`, `broadcastPaused()`, 그리고 `broadcastResumed()` 메서드를 구현하여 브로드캐스트 시작 및 중지를 처리합니다.

ReplayKit는 `RPBroadcastSampleHandler` 하위 클래스에서 메소드를 직렬 방식으로 호출합니다. 한 메서드를 호출한 후 ReplayKit은 첫 번째 메서드가 반환될 때까지 다른 메서드를 호출하지 않습니다. 즉, 스레드의 안전을 위해 잠금이나 동기화를 사용하지 않고도 저장된 상태를 업데이트할 수 있습니다.

#### Sample Buffer Clips 처리하기
- [func broadcastStarted(withSetupInfo: [String: NSObject]?](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2143170-broadcaststarted)
  - 라이브 브로드캐스트를 시작한 후 필요한 작업을 수행합니다.
- [func broadcastPaused()](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2143174-broadcastpaused)
  - 라이브 브로드캐스트가 중단된 후 필요한 작업을 수행합니다.
- [func broadcastResumed()](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2143168-broadcastresumed)
  - 라이브 브로드캐스트가 재시작된 후 필요한 작업을 수행합니다.
- [func broadcastFinished()](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2143169-broadcastfinished)
  - 라이브 브로드캐스트가 종료된 후 필요한 작업을 수행합니다.
- [func broadcastAnnotated(withApplicationInfo: [AnyHashable: Any])](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2935571-broadcastannotated)
  - 라이브 브로드캐스트를 시작한 후 필요한 작업을 수행합니다.
- [let RPApplicationInfoBundleIdentifierKey: String](https://developer.apple.com/documentation/replaykit/rpapplicationinfobundleidentifierkey)
  - 사용자 정보 dictionary에서 앱의 번들 식별자를 검색하기 위한 키입니다.
- [func processSampleBuffer(CMSampleBuffer, with: RPSampleBufferType)](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2123045-processsamplebuffer)
  - 라이브 방송 중에 비디오 및 오디오 데이터를 사용할 수 있게 되면 처리합니다.
- [let RPVideoSampleOrientationKey: String](https://developer.apple.com/documentation/replaykit/rpvideosampleorientationkey)
  - 비디오 방향을 설명하는 샘플에 첨부된 키입니다.
- [func finishBroadcastWithError(Error)](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler/2721526-finishbroadcastwitherror)
  - 브로드캐스트를 중지하고 오류를 다시 브로드캐스트 앱으로 전달합니다.

### RelationShips
#### Inherits From
- [RPBroadcastHandler](https://developer.apple.com/documentation/replaykit/rpbroadcasthandler)
### See Also
#### Media Clip Processing
- [class RPBroadcastController](https://developer.apple.com/documentation/replaykit/rpbroadcastcontroller)
  - 브로드캐스트를 시작하고 제어하는 메서드를 포함하는 개체입니다.
- [class RPBroadcastHandler](https://developer.apple.com/documentation/replaykit/rpbroadcasthandler)
  - 브로드캐스트 앱으로 메시지를 보내는 개체입니다.

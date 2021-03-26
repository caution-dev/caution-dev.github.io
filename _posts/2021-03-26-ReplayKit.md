---
layout: post
title:  "ReplayKit"
date:   2021-03-26 13:26:06 +0900
categories: Swift
tags: iOS ReplayKit Swift Apple
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다. 회사에서 웨일온 만들면서 ReplayKit을 쓰게 되어가지고....
간만의 문서 번역이네용 :)


#### Framework
## ReplayKit
화면의 비디오를, 앱과 마이크의 오디오를 가지고 비디오를 녹화하거나 스트리밍 합니다.

### Overview
ReplayKit을 사용하면, 사용자는 화면의 영상과 앱과 마이크의 오디오를 녹화할 수 있습니다. 그리고 이는 메일, 메세지, 소셜미디어를 통해 다른 사용자들에게 고유할 수 있습니다. 콘텐츠를 공유 서비스로 실시간 브로드캐스트하기 위한 앱 확장을 구축할 수 있습니다. ReplayKit은 AVPlayer 컨텐츠와 호환되지 않습니다.

### 주제
#### Replay 공유
- [macOS App 녹화하고 스트리밍하기](https://developer.apple.com/documentation/replaykit/recording_and_streaming_your_macos_app)
  - macOS 앱과 게임에 ReplayKit을 더함으로써 화면 녹화를 공유하거나 앱에서 실시간 음성 혹은 비디오를 브로드캐스트할 수 있습니다.

- [RPScreenRecoder](https://developer.apple.com/documentation/replaykit/rpscreenrecorder)
  - 앱의 오디오 및 비디오를 녹화할 수 있는 공유 레코더 개체입니다.
- [RPPreviewViewController](https://developer.apple.com/documentation/replaykit/rppreviewviewcontroller)
  - 사용자가 ReplayKit로 만든 화면 기록을 미리 보고 편집할 수 있는 사용자 인터페이스를 표시하는 개체입니다.

#### Media Clip 처리
- [RPBroadcastController](https://developer.apple.com/documentation/replaykit/rpbroadcastcontroller)
  - 브로드캐스트 시작 및 제어 방법을 포함하는 개체
- [RPBroadcastHandler](https://developer.apple.com/documentation/replaykit/rpbroadcasthandler)
  - 브로드캐스트 앱으로 메시지를 보내는 개체입니다.
- [RPBroadcastSampleHandler](https://developer.apple.com/documentation/replaykit/rpbroadcastsamplehandler)
  - ReplayKit에서 수신된 버퍼 개체를 처리하는 개체입니다.

#### Live Broadcast 구현
- [RPBroadcastActivityViewController](https://developer.apple.com/documentation/replaykit/rpbroadcastactivityviewcontroller)
  - 사용자가 브로드캐스트 서비스를 선택하는 사용자 인터페이스를 표시하는 뷰 컨트롤러입니다.
- [RPSystemBroadcastPickerView](https://developer.apple.com/documentation/replaykit/rpsystembroadcastpickerview)
  - 누르면 브로드캐스트 선택기가 표시되는 브로드캐스트 단추를 표시하는 뷰입니다.
- [RPBroadcastActivityController](https://developer.apple.com/documentation/replaykit/rpbroadcastactivitycontroller)
  - macOS 브로드캐스트 선택기를 표시하는 컨트롤러 객체입니다.
- [RPBroadcastActivityControllerDelegate](https://developer.apple.com/documentation/replaykit/rpbroadcastactivitycontrollerdelegate)
  - 브로드캐스트 작업 제어기의 선택 이벤트에 응답하기 위해 구현할 방법을 정의하는 프로토콜입니다.

#### Error
- [RPRecordingErrorCode](https://developer.apple.com/documentation/replaykit/rprecordingerrorcode)
  - ReplayKit 에러 도메인 코드들입니다.
- [RPRecodingErrorDomain](https://developer.apple.com/documentation/replaykit/rprecordingerrordomain-5gi)
   - ReplayKit 프레임워크 오류 도메인.

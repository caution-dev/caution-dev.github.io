---
layout: post
title:  "Go Live with ReplayKit"
date:   2021-03-26 17:26:16 +0900
categories: Swift
tags: iOS ReplayKit Swift Apple WWDC
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

[영상 링크](https://developer.apple.com/videos/play/wwdc2016/601/)

## ReplayKit
ReplayKit을 사용하면, 사용자는 화면의 영상과 앱과 마이크의 오디오를 녹화할 수 있다.
이를 통해 게임에 대한 음성 해설을 제공할 수 있고, 시스템 공유 시트를 사용하여 공유할 수 있다.

ReplayKit은 HD급 비디오를 제공합니다.
  - 게임 성능에 거의 영향을 주지 않고 장치의 배터리에서 최소의 전력을 소비한다.
사생활 보호
 - 녹화가 시작되기 전 사용자 프롬프트를 표시하여 사용자들이 게임 플레이을 녹화하기 전 인지하고 이를 허용한다.
 - 시스템 UI는 제외하여 녹화한다. (메세지, 애플리케이션 알림 등)

### 새로운 기능
- Apple TV OS에서 ReplayKit 지원
- 실시간으로 브로드캐스팅 가능 ( 타사 방송 서비스까지 지원 )
- Face Time 카메라를 녹화할 수 있으며, 마이크 녹음 API를 향상시킴

### ReplayKit Architecture
![images](/images/replayKit/replaykit_wwdc_00.png)
애플리케이션에서 운영체제를 통해 레코딩을 시작하려면 RPScreenRecorder을 통해 녹화를 시작할 수 있다.
녹화가 시작되면 Replay Daemon 으로 메세지가 전달되고 앱의 데이터를 Movie file에 쓰기 시작한다.

![images](/images/replayKit/replaykit_wwdc_01.png)
애플리케이션이 녹화를 중지하면 Replay Daemon이 쓰기를 종료하고 완성된 Movie file을 Application 단으로 돌려주기 위해 `RPPreviewViewController`를 제공한다.
이 뷰 컨트롤러를 통해 사용자들은 영상을 미리 보면서 편집하고, 공유할 수 있다.

### Class and Protocols
#### RPScreenRecorder
녹화를 시작 / 중단 / 취소
현재 녹화할 수 있는지 체크할 수 있음

#### RPScreenRecorderDelegate
녹화 가능 여부가 변경되는 시점을 알 수 있음
오류가 발생하여 녹화가 중지되었을 때를 알 수 있음

#### RPPreviewViewControler
사용자에게 녹화영상을 미리볼 수 있는 뷰 컨트롤러를 제공
편집과 잘라내기, 공유

#### RPPreviewControllerDelegate
프리뷰가 종료되었을 때의 시점을 알 수 있다.

#### ReplayKit on Apple TV
##### StartRecording
```swift
private lazy var sharedRecorder = RPScreenRecorder.shared()
func didPressRecordButton() {
    sharedRecorder.startRecording { error in              // 녹화 시작
       if error == nil {
           self.showIndicatorView(text: "Recording")
      }
   }
}

// 녹화중이라는 인디케이터를 보여줍시다.
func showIndicatorView(text: String) {
   recordingIndicatorWindow = UIWindoe(frame: UIScreen.main().bounds) // 별도 윈도우에 만들어서 영상에 포함되지 않음 ㅇㅁㅇ?
   recordingIndicatorWindow?.isHidden = false
   recordingIndicatorWindow?.backgroundColor = UIColor.clear()
   recordingIndicatorWindow?.isUserInteractionEnabled = false

   let indicatorView = IndicatorView(text: text)
   recordingIndicatorWindow?.addSubview(indicatorView)
}

// 중지 버튼을 눌렀습니다!
func didPressStopButton() {
    sharedRecorder.stopRecording { previewViewController, error in  // 녹화가 종료되면 previewViewController 객체가 함께 넘어옴
        self.hideIndicatorView()

       if error == nil {    // error 없이 종료되었다면 프리뷰 화면을 보여줄 것.
          self.previewController = previewViewController
          self.previewViewController?.previewControllerDelegate = self
      }
}

// 메뉴가 뜬 상태에서, 공유를 눌렀을 경우
func didPressShareButton() {
   if let preview = previewViewController {
       preview.mode = .share /// 미리보기는 preview
       self.present(preview, animated: true)
   }
}

// 사용자가 프리뷰 화면에서 되돌아 오려고 할떄
// RPPreviewViewControllerDelegate 내부 함수입니다.
func previewControllerDidFinish(_ previewController: RPPreviewViewController) {
 previewController.dismiss(animated: true) // 옹 이쪽에서 닫아줍니다
}
```
### Discarding the Recording
다음 녹화가 시작되면 이전 녹화영상은 제거된다.
- 하나의 앱에서는 하나의 녹화만 허용됨
PreviewController에 날라가면 같이 제거됨.
- 명시적으로 제거하고 싶다면 `RPScreenRecorder.discardRecording()`을 사용할 것

## ReplayKit on Apple TV
비디오와 오디오 컨텐츠를 녹화할 수 있다.
- 마이크는 시스템에 설정된 마이크를 사용합니다.
미리보기, 공유, 녹화, iOS와 같이 심플한 API, tvOS 10부터 사용가능쓰!

## Live Broadcast
플레이어는 직접 iOS 또는 TVOS 장치에서 타사 스트리밍 서비스에 게임 플레이를 생방송할 수 있다.
iOS에서는 마이크와 카메라를 제공하여 FaceTime 카메라를 사용해 해설을 제공할 수도 있다.
컨텐츠는 broadcast service를 통해서만 접근 가능하며 매우매우 안전하다.

![images](/images/replayKit/replaykit_wwdc_02.png)

### Initiating a Broadcast
```swift
func didPressBroadcastButton() {
   RPBroadcastActivityViewController.load { broadcastAVC, error in  // broadcast service 를 제공하는 앱을 선택할 수 있는 화면 표출해주기 (share extension이랑 비슷한 흐름)
      if let broadcastAVC = broadcastAVC {
      broadcastAVC.delegate = self
      self.present(broadcastAVC, animated: true)
      }
   }
}

// 서비스를 선택하고 / 혹은 선택하지 않고 닫았을 때
// RPBroadcastActivityViewControllerDelegate
func broadcastActivityViewController(
    _ broadcastAVC: RPBroadcastActivityViewController,
    didFinishWith broadcastController: RPBroadcastController?,
    error: NSError?) {

   broadcastAVC.dismiss(animated: true) {
      self.startCountDownTimer {     // 아니 broadcastController 이게 있는지 먼저 체크해야하는 거 아닌가
           broadcastController?.startBroadcast { error in
            // broadcast started!
        }
 }
```

### Indicating a Broadcast
사용자에게 현재 broadcasting 중이라는 것을 알려주는 것이 중요함 ;)
- `broadcastController?.isBroadcasting` 플래그를 사용할 것

### Finish Broadcast
```swift
func didPressBroadcastButton() {
      self.broadcastController?.finishBroadcast { error in
          if error == nil {
             // broadcast finished!
             self.updateBroadcastUI()
         }
     }
}

// RPBroadcastActivityViewControllerDelegate
func broadcastActivityViewController(
   _ broadcastActivityViewController: RPBroadcastActivityViewController,
   didFinishWith broadcastController: RPBroadcastController?,
   error: NSError?) {

     self.broadcastController = broadcastController

     // set a delegate to be notified of errors
     self.broadcastController?.delegate = self
 }

//RPBroadcastControllerDelegate
func broadcastController(
   _ broadcastController: RPBroadcastController,
   didFinishWithError error: NSError?) {
     if error != nil {
       // error occurred during broadcast
       self.showErrorMessage(message: error!.localizedDescription)

      // update UI to indicate the broadcast is stopped
     self.updateBroadcastUI()
   }
}
```

#### 사용자가 앱을 백그라운드로 내린다거나, 전화가 온다거나 하는 상황에선?
앱이 백그라운드에서 실행되면 브로드캐스트를 자동으로 일시 중단한다.
따라서 다시 활성화되는 시점에 사용자에게 브로드캐스트를 다시 재개할 것인지 물어봐야 한다.

```swift
func applicationWillResignActive() {
    // ReplayKit will automatically pause the broadcast
 }

 func applicationDidBecomeActive() {
    if self.broadcastController?.isBroadcasting == true {
       self.promptUserToResumeBroadcast { userWantsToResume in
           if (userWantsToResume == true) {
                  // user wants to resume
                  self.broadcastController?.resumeBroadcast()
                  self.updateBroadcastUI()
         } else {
                 // user does not want to resume
                 self.broadcastController?.finishBroadcast { error in
                 self.updateBroadcastUI()
        }
```

### Class와 Protocol
- [RPBroadcastActivityViewController]()
  - 설치되어 있는 브로드캐스트 서비스들을 보여주는 칭구
- [RPBroadcastActivityViewControllerDelegate]()
  - 브로드캐스트 셋업이 완료되었을 때 알려줄 수 있는 delegate
- [RPBroadcastController]()
  - 브로드캐스트를 시작 / 종료 하는 주체
  - 브로드캐스트가 현재 진행중인지 체크할 수 있음
- [RPBroadcastControllerDelegate]()
  - 브로드캐스트 중 나타나는 오류 핸들링

### Broadcast Services
* Broadcast UI Extension
  - 브로드캐스트 시 나타나는 화면 설정
* Broadcast Upload Extension
  - 브로드캐스트 영상 / 음성 데이터 업로드 및 처리

### Broadcast Extensions
앱 내부에 포함되어 있지만, 다른 애플리케이션 process 에서 실행됨.
상위 애플리케이션과 데이터를 공유할 수 있음
애플리케이션에 비해서는 주어진 리소스가 제한적이다. (효율적이게 구성합시다)

#### Xcode Templates
Add Target -> iOS/tvOS -> Application Extension -> Broadcast UI Extension / Broadcast Upload Extension

#### Broadcast UI Extension
로그인이나 정보 등록 등 방송에 필요한 모든 전처리 단계가 이 단계에서 해야함.
이용약관을 보여주거나 이 영상을 외부 SNS 에 공유할 수 있는 등 !
셋업이 완료되면 브로드캐스트 설정이 완료되었음을 알려주어야 한다.

#### Broadcast Upload Extension
영상 / 음성 데이터를 받아서 처리
서버에 업로드
브로드 캐스트 서비스와 연관된 다른 필요한 요소들을 구현할 것.

![images](/images/replayKit/replaykit_wwdc_03.png)

#### 추가 옵션
FaceTime camera 지원
- 카메라 프리뷰를 제공
```swift
if let cameraPreview = RPScreenRecorder.shared().cameraPreviewView {
    cameraPreview.frame = CGRect(…)
    self.view.addSubview(cameraPreview)
}
```

유연한 마이크 녹음 지원
- 켜고 끄는 방식! 초 심플!
```swift
func enableMic {
   RPScreenRecorder.shared().isMicrophoneEnabled = true
 }
func disableMic {
   RPScreenRecorder.shared().isMicrophoneEnabled = false
 }
```

iOS 10 이상에서 사용 가능~

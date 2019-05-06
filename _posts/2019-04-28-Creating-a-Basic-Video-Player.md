---
layout: post
title:  "[공식문서] Creating a Basic Video Player (iOS and tvOS)"
date:   2019-04-28 22:36:06 +0900
categories: Apple-Docs
tags: iOS Audio 번역 AVKit Articles
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}
내장된 컨트롤이 있는 간단한 비디오 재생 앱을 만듭니다.

## Overview
AVKit과 AVFoundation을 사용하여, 간단한 비디오 플레이어 앱을 만들 수 있습니다.

## Audio Session 설정
Audio Session을 사용하여 앱에서 오디오를 어떤 목적으로 사용할 건지에 대해 시스템에 알려줄 수 있습니다. Audio Session은 앱과 운영체제, 오디오 하드웨어간의 중개자 역학을 합니다. 앱이 미디어 재생 앱에서 예상하는 오디오 동작을 제공하도록 오디오 세션을 구성합니다.

 1. ```import AVFoundation```을 사용하여 AVFoundation 프레임워크를 ```AppDelegate.swift``` 클래스에 추가합니다.
 2. ```application(_:didFinishLaunchingWithOptions:)``` 메소드에서, 오디오 세션 공유 인스턴스에 접근하여 앱의 오디오 세션 카테고리를 [playback](https://caution-dev.github.io/apple-docs/2019/04/28/playback.html)으로, 모드를 [moviePlayback]()으로 설정합니다.

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {
    let audioSession = AVAudioSession.sharedInstance()
    do {
        try audioSession.setCategory(.playback, mode: .moviePlayback)
    }
    catch {
        print("Setting category to AVAudioSessionCategoryPlayback failed.")
    }
    return true
}
```

## 사용자 인터페이스 구성하기
앱의 오디오 세션을 구성한 후, 플레이어를 위한 사용자 인터페이스를 만들어야 합니다.
  1. ```Main.storyboard```파일을 열고 Library의 검색 필드에 button을 입력하고 Button 객체를 찾습니다.
  2. Button 객체를 View Controller 화면의 view에 드래그하고 Play Video라는 타이틀을 설정해줍니다.
  3. 버튼이 중앙에 위치하도록 수직 중앙 수평 중앙을 제약조건을 추가해줍니다.

![](https://docs-assets.developer.apple.com/published/1fda0a0d60/54f16e3d-7fdf-48f7-83ff-de37c7ce6503.png)

## 재생 행위를 구현하기
이제 사용자 인터페이스를 만들었으니 비디로 재생을 위한 코드를 작성해야 할 때입니다.
  1. Project Navigator에서 ```Main.storyboard``` 파일을 선택하고 assistant editor를 엽니다.
  2. Control을 누른 채로 Play Video button을 ```ViewController.swift``` 클래스에 드래그하여 새로운 ```@IBAction``` 메소드를 추가하고 이름을 ```playVideo```로 지정합니다.
  3. assistant editor를 닫고 Project Navigator에서 ```ViewController.swift``` 클래스를 선택합니다. 클래스 정의 위에, AVKit과 AVFoundation 프레임워크를 import 합니다.
  4. ```playVideo```메소드에서, 다음 구문을 추가합니다.

```swift
@IBAction func playVideo(_ sender: UIButton) {
    guard let url = URL(string: "https://devstreaming-cdn.apple.com/videos/streaming/examples/bipbop_adv_example_hevc/master.m3u8") else {
        return
    }
    // Create an AVPlayer, passing it the HTTP Live Streaming URL.
    let player = AVPlayer(url: url)

    // Create a new AVPlayerViewController and pass it a reference to the player.
    let controller = AVPlayerViewController()
    controller.player = player

    // Modally present the player and call the player's play() method when complete.
    present(controller, animated: true) {
        player.play()
    }
}
```
이 앱은 이제 iOS와 tvOS 기기 시뮬레이터에서 동작합니다.

### 원문
 * [Creating a Basic Video Player (iOS and tvOS)](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos)

### 함께보기
* [Enabling Background Audio](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/enabling_background_audio)
* [Playing Audio from a Video Asset in the Background](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/playing_audio_from_a_video_asset_in_the_background)
* [Controlling Background Audio](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos)

---
layout: post
title:  "[공식문서] Playing Audio from a Video Asset in the Background"
date:   2019-04-28 17:36:06 +0900
categories: Apple-Docs
tags: iOS Audio 번역 AVKit Articles
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}
비디오가 백그라운드로 이동할 때 비디오 자산에서 오디오를 계속 재생합니다.

## Overview
많은 미디어 재생 응용 프로그램의 일반적인 기능은 응용 프로그램이 백그라운드로 보내질 때 오디오를 계속 재생하는 것입니다. 이는 사용자가 앱을 전환하거나 기기를 잠근 결과 발생할 수 있습니다. 앱에서 background 오디오를 재생할 수 있게 하려면 먼저 [AVAudioSession](https://caution-dev.github.io/docs/2019/04/28/AVAudioSession.html) 및 [Enabling Background Audio](https://caution-dev.github.io/docs/2019/04/28/Enabling-Background-Audio.html)에 설명 된대로 앱의 기능을 구성해야합니다.

## Video Player에 연결 혹은 연결 해제하기
MP3 또는 M4A 파일과 같은 오디오 전용 애셋을 재생하는 경우 설정이 완료되면 앱에서 배경 오디오를 재생할 수 있습니다. 동영상 애셋의 오디오 부분을 재생해야 하는 경우 추가 단계가 필요합니다. 만약 플레이어의 현재 항목이 장치에 비디오를 표시하는 경우, 앱이 백그라운드로 보내지면 [AVPlayer](https://developer.apple.com/documentation/avfoundation/avplayer) 인스턴스 재생이 자동으로 일시 중지됩니다. 오디오를 계속 재생하려면 백그라운드로 전화될 때 프레젠테이션에서 AVPlayer 인스턴스 연결을 끊고 포그라운드로 돌아갈 때 다시 연결하십시오.

```swift
func applicationDidEnterBackground(_ application: UIApplication) {

    // 백그라운드로 진입할 때 프레젠터에서 AVPlayer 연결을 해제합니다.

    // AVPlayerViewController로 비디오를 표시하는 경우
    playerViewController.player = nil

    // AVPlayerLayer로 비디오를 표시하는 경우
    playerLayer.player = nil
}

func applicationWillEnterForeground(_ application: UIApplication) {
    // 포그라운드로 돌아올 때 AVPlayer를 다시 프레젠터에 연결해줍니다.

    // AVPlayerViewController로 비디오를 표시하는 경우
    playerViewController.player = player

    // AVPlayerLayer로 비디오를 표시하는 경우
    playerLayer.player = player
}

```

### 원문
 * [Playing Audio from a Video Asset in the Background](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/playing_audio_from_a_video_asset_in_the_background)

### 함께보기
 * [Playing Audio from a Video Asset in the Background](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/playing_audio_from_a_video_asset_in_the_background)
 * [Controlling Background Audio](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/controlling_background_audio)

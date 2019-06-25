---
layout: post
title:  "[AVKit] Enabling Background Audio"
date:   2019-04-28 17:26:06 +0900
categories: Apple-Docs
tags: iOS Audio 번역 AVKit Articles
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

앱이 백그라운드로 진입해도 지속적으로 오디오를 재생하도록 구성합니다.

## Overview
iOS 및 tvOS 앱에서 일부 백그라운드 작업을 사용하려면 특정 기능을 사용하도록 설정해야합니다. 미디어 플레이어 앱에 필요한 일반적인 기능은 백그라운드 오디오를 재생하는 것입니다. 이 기능을 사용하면 사용자가 다른 앱으로 전환하거나 iOS 기기를 잠글 때 앱의 오디오를 계속 사용할 수 있습니다. 이 기능은 iOS에서 AirPlay 스트리밍과 PIP 재생과 같은 고급 재생 기능을 활성화하는 데에도 필요합니다.

이 기능을 구성하는 가장 간단한 방법은 Xcode를 사용하는 것입니다. Xcode에서 앱의 타겟을 선택하고 Capabilities 탭을 선택하세요. Capabilities 탭 하단에서 **Background Modes** 스위치를 ON으로 변경하고 하위 가능 모드 목록에서 **"Audio, AirPlay, and Picture in Picture"** 옵션을 선택하세요.

![](https://docs-assets.developer.apple.com/published/547e36d6fd/d30003e5-09aa-46db-9cac-181511bb2113.png)

이 모드를 사용하고 오디오 세션을 구성하면 앱이 백그라운드에서 오디오를 재생할 준비가 되었습니다.

### 원문
 * [playback](https://developer.apple.com/documentation/avfoundation/avaudiosession/category/1616509-playback)

### 함께보기
 * [Playing Audio from a Video Asset in the Background](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/playing_audio_from_a_video_asset_in_the_background)
 * [Controlling Background Audio](https://developer.apple.com/documentation/avfoundation/media_assets_playback_and_editing/creating_a_basic_video_player_ios_and_tvos/controlling_background_audio)

---
layout: post
title:  "[SiriKit] INIntentHandlerProviding"
date:   2021-05-13 21:42:06 +0900
categories: Apple-Docs
tags: iOS Swift Intent Extension SiriKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

#### Protocol
## INIntentHandlerProviding
해당 intent들을 처리하는 커스텀 객체에 intent를 전달하기 위한 인터페이스

### Declaration
```swift
protocol INIntentHandlerProviding
```

### Overview
`INIntentHandlerProviding` 프로토콜은 SiriKit 요청을 Intents Extension의 적절한 객체로 라우팅하는 방법을 정의합니다. 이 프로토콜의 메소드는 특정 `Intent` 객체를 해결/확인/처리가 가능한 객체를 반환하는 디스패처 역할을 합니다. 이 프로토콜은 [INExtension](https://developer.apple.com/documentation/sirikit/inextension) 클래스가 채택하고 있으며 사용자에 맞게 커스텀하여 제공해야 합니다.

프로젝트에 `Intent` extension을 추가하면, Xcode는 자동으로 이 프로토콜을 채택한 기본 `INExtension` 하위클래스를 만듭니다. 이 하위클래스의 [`hadler(for:)`](https://developer.apple.com/documentation/sirikit/inintenthandlerproviding/1638898-handler) 메서드를 수정하여 처리 객체를 생서하도록 사용하세요. 여러 `Intent`에 대해 같은 클래스를 사용하거나, 각 `Intent`에 따라 다른 클래스를 사용해도 됩니다.



## Topics
----
#### Providing Handlers for Intents
 * func [handler(for: INIntent) -> Any? ](https://developer.apple.com/documentation/sirikit/inintenthandlerproviding/1638898-handler)
   * 특정 `Intent` 객체를 해결/확인/처리가 가능한 객체를 반환합니다.

## Relationships
#### Inherits From `NSObjectProtocol`
#### Conforming Types `INExtension`

## 함께 보기
------
#### Siri Intents
* [Creating an Intents App Extension](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
Xcode project에 Intent app extension을 추가하고 설정해봅시다.
* [Structuring Your Code to Support App Extensions](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
백엔드 서비스를 프라이빗 프레임워크로 옮겨서 앱과 앱 익스텐션이 함께 사용할 수 있도록 해봅니다.
* [Dispatching Intents to Handler Objects](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
SiriKit에게 주어진 intent를 처리할 수 있는 객체를 제공합니다.
* [Resolving and Handling intents](https://caution-dev.github.io/swift/2021/05/13/Resolving_and_Handling_Intents.html)
해당 intent들을 처리하는 커스텀 객체에 intent를 전달하기 위한 인터페이스
* [Intents and Related Classes](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
Inetent 익스텐션을 구성하기 위한 클래스를 배워봅시다.
* [Providing Hands-Free App Control with Intents](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
익스텐션 없이 intent를 해결하고 / 확정하고 / 처리해봅시다.


이거 다 읽으면... 튜토리얼 플젝 하나 만들어보려구요
맨날 문서만 읽고 정리는 안 하는 듯 하여 ㅎ ㅡㅎ

저 링크를 다 읽으면 저의 블로그 링크로 교체됩니다. 이전까지는 원본 문서 링크로 연결됩니다.

그럼 이만! 뿅!

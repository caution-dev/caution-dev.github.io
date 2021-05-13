---
layout: post
title:  "[SiriKit] Resolving and Handling Intents"
date:   2021-05-13 19:42:06 +0900
categories: Apple-Docs
tags: iOS Swift Intent Extension SiriKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.

SiriKit을 써보고 싶었던 건 아니고, 단축어를 앱에 적용해보고 싶었는데 그게 SiriKit 내부에 포함되나 보더라고요 (맞나) 그래서 급작스런 SiriKit 입니다.

#### Article
## Intent 해결 및 처리
앱 서비를 위해 사용자의 요구사항을 resolve(해결하고), confirm(확정해주고), handle(다루기)

### Overview
SiriKit은 `Intents app extension`과 함께 제공된 정보를 기반으로 사용자의 요청을 수행할 수 있는지 확인합니다. 해결 단계에서 `intent` 객체의 개별 매개 변수(parameter)를 확인하고 필요에 따라 SiriKit에 설명을 요청합니다. 확인 단계에서 모든 `intent` 파라미터에 대한 마지막 점검을 한 번 수행하고, 서비스가 `intent`의 요구를 이행할 준비가 되었는지 확인합니다. `intent`가 유효하다면, SiriKit에서 해당 의도를 당신이(우리 앱이) 처리하도록 요청합니다.

> ### 중요
> watchOS는 모든 의도를 지원하지 않습니다. 예를 들어, watchOS의 `Intents App Extension`은 비디오 통화를 시작하거나 메시지 속성을 설정하거나 CarPlay 도메인 관련 `intent`를 처리할 수 없습니다. watchOS에서 이들을 처리하기 전에 가능 여부를 먼저 확인하세요.

## Topics
----
#### Resolving and Handling intents
 * [Intent 해결 및 관리](https://developer.apple.com/documentation/sirikit/resolving_and_handling_intents/resolving_the_parameters_of_an_intent)
   * Intent의 매개변수를 확인하고, 처리하기 위한 모든 정보가 있는지 확인합니다.
 * [Confirming the Details of an Intent](https://developer.apple.com/documentation/sirikit/resolving_and_handling_intents/confirming_the_details_of_an_intent)
   * Intent의 매개변수를 최종 확인하고, 서비스가 이 Intent를 처리할 준비가 되었는지 확인합니다.
 * [Handling an Intent](https://developer.apple.com/documentation/sirikit/resolving_and_handling_intents/handling_an_intent)
   * Intent를 이행하고 SiriKit에게 당신이 한 일에 대한 피드백을 제공합니다.
------
#### Responses
* [class INIntentResolutionResult](https://developer.apple.com/documentation/sirikit/inintentresolutionresult)
  * Intent 객체의 파라미터에 대한 해결 결과
* [class INIntentResponse](https://developer.apple.com/documentation/sirikit/inintentresponse)
  * Intent 객체에 대한 당신의 응답


## 함께 보기
#### Siri Intents
* [Creating an Intents App Extension](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension)
Xcode project에 Intent app extension을 추가하고 설정해봅시다.
* [Structuring Your Code to Support App Extensions](https://developer.apple.com/documentation/sirikit/structuring_your_code_to_support_app_extensions)
백엔드 서비스를 프라이빗 프레임워크로 옮겨서 앱과 앱 익스텐션이 함께 사용할 수 있도록 해봅니다.
* [Dispatching Intents to Handler Objects](https://developer.apple.com/documentation/sirikit/dispatching_intents_to_handler_objects)
SiriKit에게 주어진 intent를 처리할 수 있는 객체를 제공합니다.
* [protocol INIntentHandlerProviding](https://caution-dev.github.io/swift/2021/05/13/INIntentHandlerProviding.html)
해당 intent들을 처리하는 커스텀 객체에 intent를 전달하기 위한 인터페이스
* [Intents and Related Classes](https://developer.apple.com/documentation/sirikit/intents_and_related_classes)
Inetent 익스텐션을 구성하기 위한 클래스를 배워봅시다.
* [Providing Hands-Free App Control with Intents](https://developer.apple.com/documentation/sirikit/providing_hands-free_app_control_with_intents)
익스텐션 없이 intent를 해결하고 / 확정하고 / 처리해봅시다.

그럼 이만! 뿅!

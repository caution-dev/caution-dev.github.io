---
layout: post
title:  "[WebKit] WKUserScript"
date:   2019-05-06 16:36:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

#### Class
WKUserScript 개체는 웹 페이지에 삽입 할 수 있는 스크립트를 나타냅니다.

### Declaration
```swift
class WKUserScript : NSObject
```

### Topics
#### Script 초기화
```swift
// WKUserContentController에 추가될 수 있는 초기화된 사용자 스크립트를 반환합니다.
init(source: String, injectionTime: WKUserScriptInjectionTime, forMainFrameOnly: Bool)
```

### Script 정보 초기화
```swift
// 스크립트의 소스 코드입니다.
var source: String

// 스크립트가 웹페이지에 삽입되어야 하는 타이밍입니다.
var injectionTime: WKUserScriptInjectionTime

// 스크립트가 메인 프레임에만 삽입되어야 하는지(true)
// 혹은 모든 프레임에 삽입되어야 하는 지(false)에 대한 Bool 값입니다.
var isForMainFrameOnly: Bool
```

### 상수
```swift
// 스크립트가 웹페이지에 삽입되어야 하는 타이밍입니다.
// .atDocumentStart : DOM을 생성한 다음 다른 컨텐츠들을 읽어오기 전에 삽입됩니다.
// .atDocumentEnd : DOM을 읽어온 다음, 다른 하위 리소스들을 불러오기 전에 삽입됩니다.
enum WKUserScriptInjectionTime
```

### Relationships
NSObject의 서브 클래스이며 CVarArg, Equatable, Hashable, NSSecureCoding 프로토콜을 채택합니다.

### 함께보기
* [WKUserContentController](https://caution-dev.github.io/apple-docs/2019/05/04/WKUserContentController.html) : WKUserContentController 객체는 JavaScript가 메시지를 게시하고 웹 스크립트에 사용자 스크립트를 삽입하는 방법을 제공합니다.
* [WKScriptMessage](https://caution-dev.github.io/apple-docs/2019/05/04/WKScriptMessage.html) : WKScriptMessage 객체에는 웹 페이지에서 보낸 메시지에 대한 정보가 들어 있습니다.
* [WKScriptMessageHandler](https://caution-dev.github.io/apple-docs/2019/05/06/WKScriptMessageHandler.html) : WKScriptMessageHandler 프로토콜을 준수하는 클래스는 웹 페이지에서 실행중인 JavaScript에서 메시지를 수신하는 메서드를 제공합니다.

### 원문
 * [WKUserScript](https://developer.apple.com/documentation/webkit/wkuserscript)

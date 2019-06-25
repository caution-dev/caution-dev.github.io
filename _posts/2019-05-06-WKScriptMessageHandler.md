---
layout: post
title:  "[WebKit] WKScriptMessageHandler"
date:   2019-05-06 16:36:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}
#### Protocol
## WKScriptMessageHandler
WKScriptMessageHandler 프로토콜을 채택하는 클래스는 웹 페이지에서 실행중인 JavaScript에서 메시지를 수신하는 메서드를 제공합니다.

### Declaration
```swift
protocol WKScriptMessageHandler
```

### Topics
#### 메시지 수신하기
```swift
// 웹페이지로부터 스크립트 메시지를 수신했을 때 호출됩니다.
func userContentController(WKUserContentController, didReceive: WKScriptMessage)
```

### Relationships
NSObjectProtocol 프로토콜을 채택합니다.

### 함께보기
* [WKUserContentController](https://caution-dev.github.io/apple-docs/2019/05/04/WKUserContentController.html) : WKUserContentController 객체는 JavaScript가 메시지를 게시하고 웹 스크립트에 사용자 스크립트를 삽입하는 방법을 제공합니다.
* [WKScriptMessage](https://caution-dev.github.io/apple-docs/2019/05/04/WKScriptMessage.html) : WKScriptMessage 객체에는 웹 페이지에서 보낸 메시지에 대한 정보가 들어 있습니다.
* [WKUserScript](https://caution-dev.github.io/apple-docs/2019/05/06/WKUserScript.html) : WKUserScript 객체는 웹 페이지에 삽입 할 수있는 스크립트를 나타냅니다.

### 원문
 * [WKScriptMessageHandler](https://developer.apple.com/documentation/webkit/wkscriptmessagehandler)

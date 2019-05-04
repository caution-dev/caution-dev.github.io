---
layout: post
title:  "[공식문서] WKScriptMessage"
date:   2019-04-28 18:36:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit Framework
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKScriptMessage 객체는 웹 페이지에서 보낸 메세지에 대한 정보를 포함합니다.

### Declaration
```swift
class WKScriptMessage : NSObject
```

### Topics
#### 메세지 정보 검사
```swift
// 메세지의 바디입니다.
var body: Any

// 메세지를 보내는 프레임
var frameInfo: WKFrameInfo

// 메시지를 보낼 메시지 핸들러의 이름.
var name: String

// 메세지를 보낸 웹뷰
var webView: WKWebView?
```

### Relationships
NSObject가 부모 클래스이고, CVarArg, Equatable, Hashable 프로토콜을 채택함

### 함께보기
* [WKUserContentController](https://developer.apple.com/documentation/webkit/wkusercontentcontroller) : WKUserContentController 객체는 JavaScript가 메시지를 게시하고 웹 스크립트에 사용자 스크립트를 삽입하는 방법을 제공합니다.
* [WKUserScript](https://developer.apple.com/documentation/webkit/wkuserscript) : WKUserScript 개체는 웹 페이지에 삽입 할 수있는 스크립트를 나타냅니다.
* [WKScriptMessageHandler](https://developer.apple.com/documentation/webkit/wkscriptmessagehandler) : WKScriptMessageHandler 프로토콜을 준수하는 클래스는 웹 페이지에서 실행중인 JavaScript에서 메시지를 수신하는 메서드를 제공합니다.

### 원문
 * [WebKit](https://developer.apple.com/documentation/webkit)

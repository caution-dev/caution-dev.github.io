---
layout: post
title:  "[공식문서] WKUserContentController"
date:   2019-04-28 18:36:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKUserContentController 객체는 JavaScript가 메시지를 게시하고 웹 스크립트에 사용자 스크립트를 삽입하는 방법을 제공합니다.

### Declaration
```swift
class WKUserContentController : NSObject
```

### Topics
#### 메세지 핸들러 추가하기
```swift
// 메세지 핸들러를 추가합니다.
func add(WKScriptMessageHandler, name: String)
```

### 유저 스크립트를 추가하고 제거하기
```swift
// 유저 스크립트를 추가합니다.
func addUserScript(WKUserScript)

// 유저 스크립트와 연관된 모든 것을 제거합니다.
func removeAllUserScripts()

// 스크립트 메시지 핸들러를 제거합니다.
func removeScriptMessageHandler(forName: String)

// 유저 컨텐츠 컨트롤러와 관련된 유저 스크립트들입니다.
var userScripts: [WKUserScript]
The user scripts associated with the user content controller.
````

### 컨텐츠 룰을 추가하고 제거하기
```swift
// 컨텐츠 룰의 리스트를 추가합니다.
func add(WKContentRuleList)

// 룰 리스트를 제거합니다.
func remove(WKContentRuleList)

// 모든 룰 리스트를 제거합니다.
func removeAllContentRuleLists()

// 웹 컨텐츠에 적용할 수 있는 컴파일된 룰 리스트입니다.
class WKContentRuleList
A compiled list of rules to apply to web content.

class WKContentRuleListStore
```

### Relationships
NSObject의 서브 클래스이며 CVarArg, Equatable, Hashable, NSSecureCoding 프로토콜을 채택합니다.

### 함께 보기
* [WKScriptMessage](https://developer.apple.com/documentation/webkit/wkscriptmessage) : WKScriptMessage 개체에는 웹 페이지에서 보낸 메시지에 대한 정보가 들어 있습니다.
* [WKUserScript](https://developer.apple.com/documentation/webkit/wkuserscript) : WKUserScript 개체는 웹 페이지에 삽입 할 수있는 스크립트를 나타냅니다.
* [WKScriptMessageHandler](https://developer.apple.com/documentation/webkit/wkscriptmessagehandler) : WKScriptMessageHandler 프로토콜을 준수하는 클래스는 웹 페이지에서 실행중인 JavaScript에서 메시지를 수신하는 메서드를 제공합니다.

### 원문
 * [WKUserContentController](https://developer.apple.com/documentation/webkit/wkusercontentcontroller)

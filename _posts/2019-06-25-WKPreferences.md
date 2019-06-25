---
layout: post
title:  "[WebKit] WKPreferences"
date:   2019-06-25 17:12:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

웹 뷰에 대한 기본 속성 설정을 캡슐화한 객체입니다.

### Declaration
```swift
class WKPreferences : NSObject
```
### Overview
이 preferences 객체는 웹 뷰가 초기화될 때 사용되는 configuration에 의해 지정됩니다.

### Topics
#### 렌더링 설정
```swift
// 최소 font size
var minimumFontSize: CGFloat

var tabFocusesLinks: Bool
```

#### Java와 JavaScript 설정
```swift
// Java가 가능한지
var javaEnabled: Bool

// 사용자가 동작하지 않아도 JavaScript를 통해 새 창 열기가 가능한지
var javaScriptCanOpenWindowsAutomatically: Bool

// Javascript가 가능한지
var javaScriptEnabled: Bool

// PlugIn 사용이 가능한지
var plugInsEnabled: Bool
```
### 함께보기
* [WKNavigationDelegate](https://caution-dev.github.io/apple-docs/2019/06/25/WKNavigationDelegate.html)
  * 웹뷰에서 탐색 요청을 수락하고, 불러오고 및 완료하는 과정에서 트리거되는 사용자 지정 동작을 구현하는 데 도움이됩니다.
* [WKProcessPool](https://caution-dev.github.io/apple-docs/2019/06/25/WKProcessPool.html)
  * WKProcessPool 객체는 웹 컨텐츠 프로세스를 나타내는 풀입니다.
* [WKWindowFeatures](https://caution-dev.github.io/apple-docs/2019/06/25/WKWindowFeatures.html)
  * 새 웹뷰가 요청될 때 포함 창에 대한 선택적 속성들을 지정합니다.

* [WKWebView](https://caution-dev.github.io/apple-docs/2019/06/25/WKWebView.html)
  * 인앱브라우저와 같이 상호작용할 수 있는 웹 컨텐츠 객체입니다.
* [WKWebViewConfiguration](https://caution-dev.github.io/apple-docs/2019/06/25/WKWebViewConfiguration.html)
  * 웹 뷰를 초기화할 때 사용되는 속성 컬렉션입니다.
* [WKUIDelegate](https://caution-dev.github.io/apple-docs/2019/06/25/WKUIDelegate.html)
  * 웹 페이지 대신 고유 사용자 인터페이스 요소를 표시하는 메서드를 제공합니다.

---
layout: post
title:  "[WebKit] WKProcessPool"
date:   2019-06-25 17:12:56 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKProcessPool 객체는 웹 컨텐츠 프로세스를 나타내는 풀입니다.

### Declaration
```swift
class WKProcessPool : NSObject
```

### Overview
웹 뷰와 연관된 프로세스 풀은 웹 뷰 configuration에 의해 지정됩니다. 각 웹뷰는 구현 정의 프로세스 제한(implementation-defined process limit)에 도달 할 때까지 자체 웹 컨텐츠 프로세스를 제공받습니다. 그 후에 동일한 프로세스 풀이 있는 웹 뷰가 결국 웹 컨텐츠 프로세스를 공유하게됩니다.

> **Note**
> WKProcessPool 객체는 그 자체의 속성이나 메소드가 없는 불투명 토큰입니다.


### 함께보기
* [WKNavigationDelegate](https://caution-dev.github.io/apple-docs/2019/06/25/WKNavigationDelegate.html) : 웹뷰에서 탐색 요청을 수락하고, 불러오고 및 완료하는 과정에서 트리거되는 사용자 지정 동작을 구현하는 데 도움이 됩니다.
* [WKWindowFeatures](https://caution-dev.github.io/apple-docs/2019/06/25/WKWindowFeatures.html) : 새 웹뷰가 요청될 때 포함 창에 대한 선택적 속성들을 지정합니다.
* [WKWebView](https://caution-dev.github.io/apple-docs/2019/06/25/WKWebView) : 인앱브라우저와 같이 상호작용할 수 있는 웹 컨텐츠 객체입니다.
* [WKWebViewConfiguration](https://caution-dev.github.io/apple-docs/06/25/WKWebViewConfiguration.html) : 웹 뷰를 초기화할 때 사용되는 속성 컬렉션입니다.
* [WKPreferences](https://caution-dev.github.io/apple-docs/2019/06/25/WKPreferences.html) : 웹 뷰에 대한 환경 설정을 캡슐화합니다.
* [WKUIDelegate](https://caution-dev.github.io/apple-docs/2019/06/25/WKUIDelegate.html) : 웹페이지를 대신해서 고유 사용자 인터페이스 요소들을 보여주는 메소드들을 제공합니다.

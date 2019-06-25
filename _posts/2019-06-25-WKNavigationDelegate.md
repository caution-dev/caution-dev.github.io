---
layout: post
title:  "[WebKit] WKNavigationDelegate"
date:   2019-06-25 17:36:06 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKNavigationDelegate 프로토콜의 메서드는 웹 뷰에서 탐색 요청을 수락하고, 불러오고 및 완료하는 과정에서 트리거되는 사용자 지정 동작을 구현하는 데 도움이됩니다.

### Declaration
```swift
protocol WKNavigationDelegate
```
### Topics
#### Navigation 시작하기
웹뷰가 웹 컨텐츠를 수신하기 시작했을 때 호출됩니다.
```swift
func webView(WKWebView, didCommit: WKNavigation!)
```
웹 뷰에 웹 컨텐츠가 불러와지기 시작할 때 호출됩니다.
```swift
func webView(WKWebView, didStartProvisionalNavigation: WKNavigation!)
```
#### 서버 동작에 응답하기
웹뷰가 웹 컨텐츠를 수신하기 시작했을 때 호출됩니다.
```swift
func webView(WKWebView, didCommit: WKNavigation!)
```
웹 뷰가 서버 redirect를 수신했을 때 호출됩니다.
```swift
func webView(WKWebView, didReceiveServerRedirectForProvisionalNavigation: WKNavigation!)
```
#### 인증 문제
웹 뷰가 인증 문제에 응답해야 할 때 호출됩니다.
```swift
func webView(WKWebView, didReceive: URLAuthenticationChallenge, completionHandler: (URLSession.AuthChallengeDisposition, URLCredential?) -> Void)
```

#### 에러에 대응하기
탐색도중 오류가 발생했을 때 호출됩니다.
```swift
func webView(WKWebView, didFail: WKNavigation!, withError: Error)
```
웹뷰가 컨텐츠를 불러오는 동안 오류가 발생했을 때 호출됩니다.
func webView(WKWebView, didFailProvisionalNavigation: WKNavigation!, withError: Error)

#### 불러오는 진행률 추적
탐색이 완료되었을 때 호출됩니다.
```swift
func webView(WKWebView, didFinish: WKNavigation!)
```
웹 뷰의 웹 컨텐츠가 종료될 때 호출됩니다.
```swift
func webViewWebContentProcessDidTerminate(WKWebView)
```
#### 네비게이션 허용
탐색 취소를 허용할 것인지에 대해 결정합니다.
```swift
func webView(WKWebView, decidePolicyFor: WKNavigationAction, decisionHandler: (WKNavigationActionPolicy) -> Void)
```
응답이 수신된 후 탐색을 허용할지 아니면 취소할지 결정합니다.
```swift
func webView(WKWebView, decidePolicyFor: WKNavigationResponse, decisionHandler: (WKNavigationResponsePolicy) -> Void)
```

#### 네비게이션 정책
```swift
// webView(_:decidePolicyFor:decisionHandler:)에서 사용되는 정책 열거형
enum WKNavigationActionPolicy

// webView(_:decidePolicyFor:decisionHandler:)에서 사용되는 정책 열거형
enum WKNavigationResponsePolicy
```
> cancel / allow

### 함께보기
* [WKProcessPool](https://caution-dev.github.io/apple-docs/2019/06/25/WKProcessPool.html) : WKProcessPool 객체는 웹 컨텐츠 프로세스를 나타내는 풀입니다.
* [WKWindowFeatures](https://developer.apple.com/documentation/webkit/wkwindowfeatures.html) : 새 웹뷰가 요청될 때 포함 창에 대한 선택적 속성들을 지정합니다.
* [WKWebView](https://caution-dev.github.io/apple-docs/2019/06/25/WKWebView.html) : 인앱브라우저와 같이 상호작용할 수 있는 웹 컨텐츠 객체입니다.
* [WKWebViewConfiguration](https://caution-dev.github.io/apple-docs/2019/06/25/WKWebViewConfiguration.html) : 웹 뷰를 초기화할 때 사용되는 속성 컬렉션입니다.
* [WKPreferences](https://caution-dev.github.io/apple-docs/2019/06/25/WKPreferences.html) : 웹 뷰에 대한 환경 설정을 캡슐화합니다.
* [WKUIDelegate](https://caution-dev.github.io/apple-docs/2019/06/25/WKUIDelegate.html) : 웹페이지를 대신해서 고유 사용자 인터페이스 요소들을 보여주는 메소드들을 제공합니다.

* [WKScriptMessage](https://caution-dev.github.io/apple-docs/2019/05/04/WKScriptMessage.html) : WKScriptMessage 객체에는 웹 페이지에서 보낸 메시지에 대한 정보가 들어 있습니다.
* [WKScriptMessageHandler](https://caution-dev.github.io/apple-docs/2019/05/06/WKScriptMessageHandler.html) : WKScriptMessageHandler 프로토콜을 준수하는 클래스는 웹 페이지에서 실행중인 JavaScript에서 메시지를 수신하는 메서드를 제공합니다.

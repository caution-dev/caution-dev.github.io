---
layout: post
title:  "WKWebView & Service Worker API"
date:   2021-08-04 16:59:06 +0900
categories: WebKit
tags: WebKit WKWebView
author: Caution
mathjax: true
comments: true
---

* content
{:toc}

2018/2/7의 [WebKit](https://webkit.org/blog/8090/workers-at-your-service/)의 포스팅에 의하면 Safari와 `SFSafariViewController`에서 [Service Worker API]()를 지원한다고 합니다.
11.3 부터 지원이 된 것 같은데 이 시점에는 `WKWebView` 에서 지원이 된 것은 아니었던 것으로..

#### Service Worker?
- [w3c의 ServiceWorker](https://w3c.github.io/ServiceWorker/)
- [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)

> `Service Worker`는 (가능한 경우) 기본적으로 웹 애플리케이션, 브라우저 및 네트워크 사이에 있는 프록시 서버 역할을 한다. 이는 그 무엇보다도 효과적인 오프라인 경험의 생성을 가능하게 하고, 네트워크 요청을 가로채서 네트워크가 이용 가능 여부에 따라 적절한 조치를 취하고, 서버에 있는 Asset을 업데이트하기 위한 것이다. 또한 푸시 알림과 백그라운드 동기화 API에 대한 액세스를 허용한다.

개인적으로 이해하기로는, JS 베이스로 브라우저와는 별도로 추가로 동작하는 일꾼을 하나 둡니다. 이 일꾼을 통해 초기에 로딩했던 컨텐츠들을 관리하고, 새로운 업데이트가 필요하다면 서버에 요청하고, 그렇지 않을 경우 기존 리소스들을 사용하여 오프라인 상황에서도 정상적으로 동작하도록 효율적인 웹앱을 만들 수 있습니다.
Service Worker를 이야기 하다 보면 [PWA](https://developer.mozilla.org/ko/docs/Web/Progressive_web_apps/Introduction) 개념이 나오는데, 연결된 링크를 확인해주세요.

##### WKWebView
아무튼 이 Service Worker API를 iOS 13 이후에서는 `WKWebView`에서 지원한다고 하는데, 별도의 API가 지원되는 것은 아닌 것 같고, `navigation.serviceWorker`에 접근해서 사용하는 듯 합니다.

결론은, `.entitlements` 파일에 `com.apple.developer.WebKit.ServiceWorkers`를 `true`로 지정하니까 되었습니다.

테스트 방법은 다음과 같습니다.
- 콘솔에서 `navigation.serviceWorker`가 실존하는지 체크하기
- https://googlechrome.github.io/samples/service-worker/basic/ 를 접속하여 개발자 콘솔을 열고, live test 를 동작시켜 network 탭에서 servie worker가 생성되는지 확인하기.


#### Default Browser
사실 저 entitlements key가, [공식 문서](https://developer.apple.com/documentation/bundleresources/entitlements) 상으로는 없는 것 같은데 다들 어떻게 알아오시는 걸까요 대단.. 연관된 가장 [최신 문서](https://developer.apple.com/documentation/xcode/preparing-your-app-to-be-the-default-browser-or-email-client)를 따르자면 `com.apple.developer.web-browser`를 사용하여 기본 브라우저 허용을 받으면 Service Worker 를 WKWebView에서 사용할 수 있다고 합니다.
기본 브라우저로 설정되면서 자연스럽게 serviceWorker 항목이 true 로 지정되는 것일까용.

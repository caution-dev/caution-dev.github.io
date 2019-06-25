---
layout: post
title:  "[WebKit] WKWebViewConfiguration"
date:   2019-06-25 17:14:00 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKWebView를 초기화하는 데 필요한 속성들을 담는 객체입니다.

WKWebViewConfiguration 클래스를 사용하면 웹 페이지의 랜더링 속도, 미디어 재생 처리 방법, 사용자가 선택할 수 있는 항목의 세분화 및 기타 여러 옵션을 결정할 수 있습니다.

WKWebViewConfiguration은 WKWebView가 처음 초기화될 때에만 사용됩니다. WKWebView가 생성된 이후에는 이 클래스를 통해 속성들을 변경할 수 없습니다.

### Topics
#### 새로운 Web View의 속성 구성하기
```swift
// user-agent 문자열에 사용된 애플리케이션의 이름
var applicationNameForUserAgent: String?

// 웹뷰가 사용하는 WKPreferences 객체
var preferences: WKPreferences

// 뷰의 웹 컨텐츠 프로세스를 포함하는 process pool
var processPool: WKProcessPool

// 웹 컨텍스트와 연결할 사용자 Content 컨트롤러
var userContentController: WKUserContentController

// 웹 뷰에서 사용한 웹 사이트 데이터 저장소
var websiteDataStore: WKWebsiteDataStore
```

#### 웹페이지의 크기를 바꿀 수 있는지 결정
```swift
// WKWebView 객체가 웹페이지의 크기 변경을 항상 허용해야 하는지 결정하는 Bool 값
// 기본값 : false
var ignoresViewportScaleLimits: Bool
```

#### 렌더링 속성을 설정
```swift
// 웹 뷰가 컨텐츠 렌더링을 메모리에 완전히 로드될 때까지 기다릴지(억제할 지)에 대한 Bool 값
// 기본값 : false
var suppressesIncrementalRendering: Bool
```

#### Media 재생환경 설정
```swift
// HTML5 동영상이 인라인으로 재생되는지 혹은 기본 전체 화면 컨트롤러를 사용하는지 여부를 나타내는 Bool 값
// 기본값 : false
var allowsInlineMediaPlayback: Bool

// AirPlay가 허용되는지 여부를 나타내는 Bool 값
// 기본값 : true
var allowsAirPlayForMediaPlayback: Bool

// HTML5 비디오가 picture-in-picture를 재생할 수 있는지 여부를 나타내는 Bool 값
// 기본값 : true
var allowsPictureInPictureMediaPlayback: Bool

// 사용자 제스처로 재생이 실행되어야 하는 미디어들
var mediaTypesRequiringUserActionForPlayback: WKAudiovisualMediaTypes

// 사용자 제스처로 재생이 실행되어야 하는 미디어 타입들
struct WKAudiovisualMediaTypes
The media types that require a user gesture to begin playing
```

#### 선택 세분화
```swift
// 사용자가 웹 뷰에서 컨텐츠를 선택할 수 있는 세분화 레벨
var selectionGranularity: WKSelectionGranularity

// 사용자가 웹 뷰에서 컨텐츠를 선택할 수 있는 세분화 레벨
enum WKSelectionGranularity
The granularity with which a selection can be created and modified interactively.
```
> WKSelectionGranularity
> * character : 문자 영역을 선택할 수 있습니다.
> * dynamic : 선택 가능한 레벨이 동적으로 달라집니다.

#### 사용자 인터페이스 방향 선택
```swift
// 사용자 인터페이스 요소들의 방향
var userInterfaceDirectionPolicy: WKUserInterfaceDirectionPolicy

// 웹 뷰에서 사용자 인터페이스 요소들의 방향을 결정하는 데 사용되는 정책
enum WKUserInterfaceDirectionPolicy
```
> WKUserInterfaceDirectionPolicy
> * content: CSS/HTML/XHTML 규격을 따른다. 기본값
> * system: 뷰의 userInterfaceLayoutDirection 속석을 따른다.

#### 데이터 타입 구별
```swift
// 예상되는 데이터 검색 유형
var dataDetectorTypes: WKDataDetectorTypes
The type of data detection desired.

// 발견된 데이터 유형을 나타내는 구조체
// 전화번호, URL, 주소, 캘린더 이벤트, 운송번호, 항공번호 등
struct WKDataDetectorTypes
```

#### 새로운 URL 스키마 핸들러를 추가하기
```swift
// 주어진 URL 스키마에 사용될 위한 URL 스키마 핸들러를 추가합니다.
func setURLSchemeHandler(WKURLSchemeHandler?, forURLScheme: String)

// 주어진 URL 스키마에 현재 등록되어있는 스키마 핸들러를 반환합니다.
func urlSchemeHandler(forURLScheme: String) -> WKURLSchemeHandler?
```

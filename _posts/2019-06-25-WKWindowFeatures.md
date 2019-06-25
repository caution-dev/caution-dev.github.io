---
layout: post
title:  "[WebKit] WKWindowFeatures"
date:   2019-06-25 17:15:00 +0900
categories: Apple-Docs
tags: iOS 번역 WebKit
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

WKWindowFeatures 객체는 새 웹뷰가 요청될 때 포함 창에 대한 선택적 속성들을 지정합니다.

### Topics
#### 창 크기와 방향 검사
```swift
// 포함하는 윈도우의 재조정 할 크기. 혹은 resizability가 지정되어 있지 않으면 nil
var allowsResizing: NSNumber?

// 포함하는 창의 높이를 지정하는 CGFloat 값. 높이가 지정되지 않은 경우 nil.
var height: NSNumber?

// 포함하는 창의 너비를 지정하는 CGFloat 값. 너비를 지정하지 않으면 nil.
var width: NSNumber?

// 포함하는 창의 x 좌표를 지정하는 CGFloat 값이거나 x 좌표가 지정되지 않은 경우 nil입니다.
var x: NSNumber?

// 포함하는 창의 y 좌표를 지정하는 CGFloat 값이거나, y 좌표가 지정되지 않은 경우 nil입니다.
var y: NSNumber?
```

#### 가시적인 속성 검사
```swift
// 메뉴 바가 보여져야 하는지의 여부. 정해지지 않았으면 nil
var menuBarVisibility: NSNumber?
// 상태 바가 보여져야 하는지의 여부. 정해지지 않았으면 nil
var statusBarVisibility: NSNumber?
// tool 바가 보여져야 하는지의 여부. 정해지지 않았으면 nil
var toolbarsVisibility: NSNumber?
```

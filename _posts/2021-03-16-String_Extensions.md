---
layout: post
title:  ""
date:   2019-03-16 00:26:06 +0900
categories: Swift
tags: iOS String
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.

오늘은, String을 사용하면서 자주 사용되는 Extension과 헷갈리는 녀석들을 다 모아보았습니다.

#### Q. 문자열 교체(치환) 하기
URL에서 '/'를 '_'로 교체하고 싶다면?
```swift
let url = URL(string: "https://caution-dev.github.io/swift/2021/03/26/ReplayKit.html")

var urlString = url?.absoluteString
// "https://caution-dev.github.io/swift/2021/03/26/ReplayKit.html"

urlString.replacingOccurrences(of: "/", with: "_")
// "https:__caution-dev.github.io_swift_2021_03_26_ReplayKit.html"

```

#### Q. 문자열에서 앞에서 N개만 가져오기
`prefix(Int)` 사용하기
```Swift
let fullName: String = "caution"
let prefix3: Substring = fullName.prefix(3)
// "cau"
let prefixString: String = String(prefix3)
```

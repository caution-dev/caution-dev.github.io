---
layout: post
title:  "Subject"
date:   2019-07-14 09:47:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 Subject
author: Juhee Kim
mathjax: true
---

* content
{:toc}

Subject는 관찰자와 Observable 모두에서 사용할 수 있는 ReactiveX의 일종의 브리지 또는 프록시입니다. 관찰자이기 때문에 하나 이상의 Observable을 구독 할 수 있으며 Observable이기 때문에 관찰하는 항목을 다시 내보낼 수 있고 새로운 항목을 내보낼 수도 있습니다.

Subject가 Observable을 구독하면 Observable이 항목을 내보내기 시작합니다.(Observable이 "차가운"경우 - 즉 항목을 보내기 전에 구독을 기다리는 경우). 이것은 결과 Subject가 원래의 "cold" Observable를 "hot" Observable로 만드는 효과를 가질 수 있습니다.

## Varieties of Subject
각각의 use case에 맞게 설계된 4가지 Subject가 있습니다. 이들 중 일부는 모든 구현에서 사용할 수 있는 것은 아니며 일부 구현은 다른 명명 규칙을 사용합니다.

### AyncSubject
`AsyncSubject`는 complete 이벤트가 발생한 다음 마지막 한 개의 항목만을 배출합니다. 만약 항목이 들어오지 않았다면 항목을 배출하지 않고 종료됩니다.

만약 오류때문에 Observable이 종료된다면 어떠한 항목도 배출하지 않고 error 이벤트를 전달합니다.

#### 예제
```swift
    func testForAsyncSubject() {
        let disposeBag = DisposeBag()
        let asynSubject = AsyncSubject<String>()
        asynSubject.onNext("Is Anyone Else?")
        asynSubject.subscribe(onNext: { str in
            print("observer 1 : \(str)")
        }, onError: { error in
            print("observer 1 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        asynSubject.onNext("Hi")
        asynSubject.onNext("Observers")

        asynSubject.subscribe(onNext: { str in
            print("observer 2 : \(str)")
        }, onError: { error in
            print("observer 2 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        asynSubject.onNext("nice to")
        asynSubject.onNext("meet you guys.")
        asynSubject.onError(TestError.test)
        asynSubject.onCompleted()
    }
```
#### 결과
```
observer 1 error : test
observer 2 error : test
```

### BehaviorSubject
`BehaviorSubject`를 구독하면, 가장 최근 항목을 배출하며 계속해서 항목이 발생할 때마다 배출합니다.

마찬가지로 오류로 인해 Observable이 종료되면 오류를 전달합니다. 오류가 발생된 이후 이 Subject를 구독하면 기본값이 아니라 오류가 전달됩니다.
```swift

    func testForBehaviorSubject() {
        let disposeBag = DisposeBag()
        let behaviorSubject = BehaviorSubject<String>(value: "Is Anyone Else?")
        behaviorSubject.subscribe(onNext: { str in
            print("observer 1 : \(str)")
        }, onError: { error in
            print("observer 1 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        behaviorSubject.onNext("Hi")
        behaviorSubject.onNext("Observers")

        behaviorSubject.subscribe(onNext: { str in
            print("observer 2 : \(str)")
        }, onError: { error in
            print("observer 2 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        behaviorSubject.onNext("nice to")
        behaviorSubject.onNext("meet you guys.")
        behaviorSubject.onError(TestError.test)
        behaviorSubject.onCompleted()
    }
```
```
observer 1 : Is Anyone Else?
observer 1 : Hi
observer 1 : Observers
observer 2 : Observers
observer 1 : nice to
observer 2 : nice to
observer 1 : meet you guys.
observer 2 : meet you guys.
observer 1 error : test
observer 2 error : test
```
### PublishSubject
`PublishSubject`는 subscribe를 시작한 시점을 기준부터 발생한 항목들을 전달합니다. `PublicSubject`는 hot Observable로 생성 즉시 아이템을 방출하기 시작할 수 있으며, Subject가 생성되고 관찰자가 publish를 시작하는 사이에 하나 이상의 항목이 손실될 수도 있습니다. Observable에서 모든 항목을 전달하는 것이 보장되어야 할경우 "cold" Observable 동작을 수동으로 다시 시작할 수 있도록 `Create`를 통해서 Observable을 구성하거나, `ReplqySubject`를 사용하세요.

Observable이 에러로 종료되었으면, `PublishSubject` 또한 오류 이벤트를 전달합니다.
#### 예제
```swift
    func testForPublishSubject() {
        let disposeBag = DisposeBag()
        let publishSubject = PublishSubject<String>()
        publishSubject.onNext("Is Anyone Else?")
        publishSubject.subscribe(onNext: { str in
            print("observer 1 : \(str)")
        }, onError: { error in
            print("observer 1 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        publishSubject.onNext("Hi")
        publishSubject.onNext("Observers")

        publishSubject.subscribe(onNext: { str in
            print("observer 2 : \(str)")
        }, onError: { error in
            print("observer 2 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        publishSubject.onNext("nice to")
        publishSubject.onNext("meet you guys.")
        publishSubject.onError(TestError.test)
        publishSubject.onCompleted()
    }
```
#### 결과
```
observer 1 : Hi
observer 1 : Observers
observer 1 : nice to
observer 2 : nice to
observer 1 : meet you guys.
observer 2 : meet you guys.
observer 1 error : test
observer 2 error : test
```
observer가 붙기 전에 발행된 "Is Anyone Else?"는 관찰자가 없기 때문에 아무도 처리하지 않았습니다. 이후에 observer 1과 observer 2 가 구독을 시작했고, 시작한 시점으로부터 발행된 이벤트들만 처리하게 됩니다.

### ReplaySubject
`ReplaySubject`는 관찰자가 구독하는 시기와 관계없이 Observable이 생성한 모든 항목을 관찰자에게 내보냅니다. 또한 ReplaySubject 버전은 재생 버퍼가 일정 크기 이상으로 커질 위험이 있거나 이전에 항목이 배출된 이후 지정된 시간이 경과하면 오래된 항목을 폐기합니다.

`ReplaySubject`를 사용할경우, `onNext(:)`를 여러 메서드에서 호출하지 않도록 주의하세요. 비순차적인 호출로 인해 `Observable`이 손상되고 결과적으로 어떤 항목이나 notification이 먼저 재생되어야 하는지에 대해 모호함이 발생할 수 있습니다.
#### 예제
```swift
 func testForReplaySubject() {
        let disposeBag = DisposeBag()
        let replaySubject = ReplaySubject<String>.create(bufferSize: 2)
        replaySubject.onNext("Is Anyone Else?")
        replaySubject.subscribe(onNext: { str in
            print("observer 1 : \(str)")
        }, onError: { error in
            print("observer 1 error : \(error)")
        }, onCompleted: {
            print("observer 1 complete ")
        }).disposed(by: disposeBag)

        replaySubject.onNext("Hi")
        replaySubject.onNext("Observers")

        replaySubject.subscribe(onNext: { str in
            print("observer 2 : \(str)")
        }, onError: { error in
            print("observer 2 error : \(error)")
        }, onCompleted: {
            print("observer 2 complete ")
        }).disposed(by: disposeBag)

        replaySubject.onNext("nice to")
        replaySubject.onNext("meet you guys.")
        replaySubject.onError(TestError.test)
        replaySubject.subscribe(onNext: { str in
            print("observer 3 : \(str)")
        }, onError: { error in
            print("observer 3 error : \(error)")
        }, onCompleted: {
            print("observer 3 complete ")
        }).disposed(by: disposeBag)
        replaySubject.dispose()
        replaySubject.subscribe(onNext: { str in
            print("observer 4 : \(str)")
        }, onError: { error in
            print("observer 4 error : \(error)")
        }, onCompleted: {
            print("observer 4 complete ")
        }).disposed(by: disposeBag)
}
```
#### 결과
```
observer 1 : Is Anyone Else?
observer 1 : Hi
observer 1 : Observers
observer 2 : Hi
observer 2 : Observers
observer 1 : nice to
observer 2 : nice to
observer 1 : meet you guys.
observer 2 : meet you guys.
observer 1 error : test
observer 2 error : test
observer 3 : nice to
observer 3 : meet you guys.
observer 3 error : test
observer 4 error : Object `RxSwift.(unknown context at $10e4f9250).ReplayMany<Swift.String>` was already disposed.
```
버퍼가 있기 때문에 observer 1가 구독을 시작하기 전에 발생한 "Is Anyone Else" 또한 함께 전달됩니다.
마찬가지로 observer 2가 구독되었을 때 이전에 발생한 2개의 이벤트 또한 전달받습니다.
observer 3는 에러가 발생한 다음 구독을 시작하는데요, 버퍼에 error는 포함되지 않은 채로 이전 이벤트 2개와 error 이벤트를 전달받습니다.
observer 4는 replay subject가 dispose 된 다음 구독을 시작합니다. 이 때는 어떠한 이벤트도 전달받지 못하고 이미 disponse된 객체라고 알려줍니다.

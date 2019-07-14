---
layout: post
title:  "Observable Create"
date:   2019-07-14 09:44:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 Observable operator
author: Juhee Kim
mathjax: true
---

* content
{:toc}

### Create
사용자 정의 `Observable` 흐름을 만듭니다.
#### 예제
`neverCompleteObservable`는 `onNext`를 호출하고 프린트합니다. `onCompleted`가 호출되지 않습니다 ㅋㅋ
```swift
    func testCreate() {
        let disposeBag = DisposeBag()

        let neverCompleteObservable = { (element: String) -> Observable<String> in
            return Observable.create { observer in
                observer.on(.next(element))
                print("observer!!!!")
                //  observer.on(.completed)
                return Disposables.create()
            }
        }

        neverCompleteObservable("🔴")
            .subscribe(onNext: {print($0)}, onError: nil, onCompleted: {print("complete!")}, onDisposed: {print("disposed!")})
            .disposed(by: disposeBag)
    }
```
#### 결과
```
🔴
observer!!!!
disposed!
```

### Generate
scheduler: ImmediateSchedulerType

조건에 맞을 때까지 계속 item을 발생시킵니다. (while 구문같네요)
* initialState : 시작값
* condition : 조건
* iterate : 다음 값!
#### 예제
뭐, 1부터 100까지 짝수만 출력하고 싶다면?
```swift
    func testGenerate() {
        let disposeBag = DisposeBag()

        Observable.generate(
            initialState: 2,
            condition: { $0 <= 100 },
            iterate: { $0 + 2 }
            )
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
#### 결과
```
2
4
...
98
100
```

### deferred
관찰자가 구독을 시작하기 전까지 `Observable`을 생성하지 않습니다. **Cold** 그리고 관찰자가 구독을 시작할 때 매번 **새로운 `Observable`을 만들어 냅니다!**
#### 예제
```swift
    func testDeferred() {
        let disposeBag = DisposeBag()
        var count = 1

        let deferredSequence = Observable<String>.deferred {
            print("Creating \(count)")
            count += 1

            return Observable.create { observer in
                print("Emitting...")
                observer.onNext("🐶")
                observer.onNext("🐱")
                observer.onNext("🐵")
                return Disposables.create()
            }
        }

        deferredSequence
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)

        deferredSequence
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
구독 후 dispose를 했지만, 다시 subscribe를 시작하면 다시 observable을 만들어 냅니다!

### never | empty
* never : 아무 이벤트도 배출시키지 않는 시퀀스를 만듭니다.
* empty : complete 이벤트만 발생시키는 시퀀스를 만듭니다.
* error : error 이벤트만 발생시키는 시퀀스를 만듭니다.
```swift
    func testNeverEmptyError() {
        let disposeBag = DisposeBag()
        let neverSequence = Observable<String>.never()

        let neverSequenceSubscription = neverSequence
            .subscribe { _ in
                print("This will never be printed")
        }

        neverSequenceSubscription.disposed(by: disposeBag)

        Observable<Int>.empty()
            .subscribe { event in
                print(event)
            }
            .disposed(by: disposeBag)

        Observable<Int>.error(TestError.test)
            .subscribe { event in
                print(event)
            }
            .disposed(by: disposeBag)
    }
```
#### 결과
```
// neverSequenceSubscription의 onNext는 불리지 않는다.
completed    // empty는 onCompelete Event만 발생
error(test)    // error는 onError Event만 발생
```
### From & Just
* from : 주어진 Array, Dictionary, Set을 기준으로 Observable Sequence를 만듭니다.
* just : 주어진 요소로 하나의 항목을 배출하는 Observable Sequence를 만듭니다.
같은 Collection을 파라미터로 제공하면 from은 컬렉션의 항목 개수만큼 항목을 배출하고 just는 컬렉션이든, 단일 항목이든 하나의 항목만을 배출합니다.
#### 예제
```swift
    func testFromJust() {
        let disposeBag = DisposeBag()
        let array = ["🐶", "🐱", "🐭", "🐹"]
        print("from")
        Observable.from(array)
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
        print("just")
        Observable.just(array)
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
#### 결과
```
from
🐶
🐱
🐭
🐹
just
["🐶", "🐱", "🐭", "🐹"]
```
### of
여러 항목을 파라미터로 받아서 각 항목에 1개씩 배출하는 Observable Sequence를 만듭니다.
#### 예제
```swift
    func testOf() {
        let disposeBag = DisposeBag()
        Observable.of("🐶", "🐱", "🐭", "🐹")
            .subscribe(onNext: { element in
                print(element)
            })
            .disposed(by: disposeBag)
    }
```
#### 결과
```
🐶
🐱
🐭
🐹
```
> from은 1개의 파라미터를 받아서 각 요소별로 항목을 만들고,
> of는 여러개의 파라미터를 받아서 항목을 만들며,
> Just는 1개의 파라미터를 받아서 1개의 항목을 만듭니다!

### Range
1씩 증가하는 `Int` 값을 주어진 개수만큼 배출하는 Observable Sequence를 만듭니다.
#### 예제
```swift
    func testRange() {
        let disposeBag = DisposeBag()
        Observable.range(start: 1, count: 10)
            .subscribe { print($0) }
            .disposed(by: disposeBag)
    }
```
### 결과
```
next(1)
next(2)
...
next(9)
next(10)
```
### Repeat Element & take
항목을 무한대로 뽑아내는 Observable Sequence를 만듭니다.
무한으로 뽑아내니까 take를 함께 써보았습니다! `take(count:)`는 Observable Sequence에서 특정 개수만큼만 수신하고 구독을 해제합니다.

> take의 자매품으로
> `take(duration:, scheduler:)`
> `takeLast(count:)`, `takeUntil(other:)`, `takeUntil(behavior:, predicate)', `takeWhile(predicate:)` 가 있습니다.

#### 예제
```swift
    func testRepeat() {
        let disposeBag = DisposeBag()
        let observable = Observable.repeatElement("🐶")
        observable
            .take(3)
            .subscribe(onNext: { print("1 : \($0)")})
            .disposed(by: disposeBag)
    }
```

#### 결과

```
1 : 🐶
1 : 🐶
1 : 🐶
2 : 🐶
```
### Interval & Timer
* Interval : 주어진 시간 단위마다 Int 를 배출하는 Observable Sequence를 만듭니다.
* Timer : 주어진 시간 뒤에 특정 item을 배출합니다.
#### Interval
```swift
    func testInterval() {
        let interval = Observable<Int>.interval(.milliseconds(500), scheduler: SerialDispatchQueueScheduler.init(qos: .background))
            .subscribe(onNext: { item in
                print(item)
            }, onCompleted: {
                print("completed")
            }, onDisposed: {
                print("disposed")
            })

        Thread.sleep(forTimeInterval: 4.0)
        interval.dispose()
    }
```
```
0
1
2
3
4
5
6
7
disposed
```
#### Timer
타이머를 실행하기 위해 지정된 스케줄러를 사용하여 지정된 초기 상대 시간 경과 후에 값을 주기적으로 생성하는 관찰 가능 시퀀스를 반환합니다.
* `dueTime: RxTimeInterval` : 첫 번째 값을 내보낼때까지 기다릴 시간
* `period: RxTimeInterval` : 후속 값을 산출하는 시간
* `scheduler: SchedulerType` : Timer를 생성할 Scheduler

```swift
    func testTimer() {
       let timer = Observable<Int>.timer(.seconds(1), period: .seconds(1), scheduler: SerialDispatchQueueScheduler.init(qos: .background))
            .subscribe(onNext: { item in
                print(item)
            }, onCompleted: {
                print("completed")
            }, onDisposed: {
                print("disposed")
            })

        Thread.sleep(forTimeInterval: 4.0)
        timer.dispose()
    }
```
```
0
1
2
3
disposed
```

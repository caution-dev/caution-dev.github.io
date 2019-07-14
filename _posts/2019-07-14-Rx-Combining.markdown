---
layout: post
title:  "Combining Observables"
date:   2019-07-14 09:47:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 operator Combining
author: Juhee Kim
mathjax: true
---

* content
{:toc}

### Merge & Concat & MergeDelay
* Merge : 여러가지 Observable을 하나의 Observable로 결합합니다.
* Concat : 여러가지 Observable을 하나의 Observable로 이어붙입니다.
* MergeDelayError : Merge 한 Observable에서 `onError`이벤트가 발생하면, 모든 Observable의 항목이 배출된 다음 마지막에 `onError`이벤트를 배출합니다. **RxSwift에는 없습니다!**
* merge(maxConcurrent:) : Merge 할 수 있는 Observable의 maximum 개수를 제한합니다.

`concat`은 여러개의 Observable을 이어 붙입니다. 여러 개의 Observable이 동시에 항목을 배출하더라도, 앞선 Observable의 항목이 모두 배출된 다음에 다음 Observable을 `subscribe`하여 항목을 배출합니다. 그렇기 때문에 concat에 "Hot Observable"을 붙이게 되면 앞 선 Observable이 끝나는 시점에 따라 배출받는 항목이 달라질 수 있습니다.

#### 예제보기
앞서 사용했던 `Player` 구조체를 사용했습니다. 플레이어의 `score`는 `BehaviorSubject`이며 두 플레이어를 만들어서 `score`를 `merge`해보죠.
```swift
    func testMerge() {
        let disposeBag = DisposeBag()
        let 👦🏻 = Player(score: 80)
        let 👧🏼 = Player(score: 90)

        Observable.of(👦🏻.score, 👧🏼.score)
        .merge()
        .subscribe(onNext: {
            print("merge  : \($0)")
        }, onError: { error in
            print("merge error")
        })
        .disposed(by: disposeBag)

        👦🏻.score.onNext(85)
        👦🏻.score.onNext(95)
        👧🏼.score.onNext(100)
        👦🏻.score.onError(TestError.test)
        👧🏼.score.onNext(86)
        👦🏻.score.onNext(88)
    }
```
```
merge  : 80    // 👦🏻 = Player(score: 80)
merge  : 90    // 👧🏼 = Player(score: 90)
merge  : 85    // 👦🏻.score.onNext(85)
merge  : 95    // 👦🏻.score.onNext(95)
merge  : 100   // 👧🏼.score.onNext(100)
merge error    // 👦🏻.score.onError(TestError.test)
```
`BehaviorSubject`이기 때문에 `merge`되는 순간 기본값인 80 과 90 이 내보내졌고, 이후 어느 플레이어든 상관없이 배출 순서에 맞게 그대로 배출됩니다. `onError` 이벤트가 발생했을 경우 모든 Observable이 dispose 됩니다.

#### concat은 어떨까요?
같은 코드를 사용해서, `merge`를 `concat`으로 변경해보겠습니다.
```
concat  : 80    // 👦🏻 = Player(score: 80)
concat  : 85    // 👦🏻.score.onNext(85)
concat  : 95    // 👦🏻.score.onNext(95)
concat error    // 👦🏻.score.onError(TestError.test)
```
`concat`은 Observable이 끝나는 시점에 다음 Observable을 구독합니다. 하지만 첫 번째 Observable에서 `onError` 발생하고 dispose 되버려서, 두 번째 플레이어에 대한 스코어정보는 알수가 없군요. 에러 이벤트를 발생시키지 않는다면, 다음과 같은 결과가 나옵니다.

```
concat  : 80    // 👦🏻 = Player(score: 80)
concat  : 85    // 👦🏻.score.onNext(85)
concat  : 95    // 👦🏻.score.onNext(95)
concat  : 88    // 👦🏻.score.onNext(88)
concat  : 90    // 👧🏼 = Player(score: 90)
concat  : 100   // 👧🏼.score.onNext(100)
concat  : 86    // 👧🏼.score.onNext(86)
```
#### merge(maxConcurrent:)
한 번에 merge 할 수 있는 Observable의 개수를 제한합니다.
세 번째 player인 👦🏻👧🏼를 만들고, maxConcurrent를 2개로 제한한다면 어떻게 될까요?
```swift
    func testMergeMaxConcurrent() {
        let disposeBag = DisposeBag()
        let 👦🏻 = Player(score: 80)
        let 👧🏼 = Player(score: 90)
        let 👦🏻👧🏼 = Player(score: 40)

        Observable.of(👦🏻.score, 👧🏼.score, 👦🏻👧🏼.score)
        .merge(maxConcurrent: 2)
        .subscribe(onNext: {
            print("merge  : \($0)")
        }, onError: { error in
            print("merge error")
        })
        .disposed(by: disposeBag)

        👦🏻.score.onNext(85)
        👦🏻👧🏼.score.onNext(55)
        👧🏼.score.onNext(100)
        👦🏻.score.onCompleted()
        👦🏻👧🏼.score.onNext(60)
        👧🏼.score.onNext(86)
    }
```
결과는 다음과 같습니다. 2개의 Observable만 merge하도록 허용했기 때문에, `👦🏻👧🏼.score`의 event는 `👦🏻.score.onCompleted()`가 호출되어서 첫 번째 Observable이 종료된 다음에 배출됩니다.
```
merge  : 80    // 👦🏻 = Player(score: 80)
merge  : 90    // 👧🏼 = Player(score: 90)
merge  : 85    // 👦🏻.score.onNext(85)
merge  : 100   // 👧🏼.score.onNext(100)
merge  : 40    // 👦🏻👧🏼 = Player(score: 40)
merge  : 55    // 👦🏻👧🏼.score.onNext(55)
merge  : 86    // 👧🏼.score.onNext(86)
```

### Zip
여러가지 Observable의 항목들을 특정 함수로 묶어서 처리하고 그 결과를 내보냅니다.

`Zip` 메서드는 두 개의 혹은 그 이상의 다른 observable에 의해 차례대로 방출된 항목의 조합을 특정 함수를 거쳐 만들어진 Observable을 반환합니다. 이 함수는 Observable에서 방출된 순서를 정확하게 지켜서 처리됩니다.

각 Observables 마다 항목이 모두 방출되었을 때 그 항목들을 조합해서 새로운 항목을 만들어 내기 때문에, 일부 Observable들만이 항목을 방출했다고 해서 새 항목을 방출하지는 않습니다.

#### 예제
`interval`을 사용해서 1초마다 하나씩 항목을 emit 하게 하는 Observable을 생성하고, `PublishSubject`로 새로운 참가자를 발행하는 Subject를 함께 `zip`해봅시다.

```swift
    func testZip() {
        let disposeBag = DisposeBag()
        let scheduler = SerialDispatchQueueScheduler(qos: .background)
        let interval = Observable<Int>.interval(.seconds(1), scheduler: scheduler)
        let playerSubject = PublishSubject<String>()

        interval.subscribe(onNext: { print("interval emit : \($0)")})
            .disposed(by: disposeBag)

        Thread.sleep(forTimeInterval: 1)
        Observable.zip(interval, playerSubject) { index, player in
            "\(index) 번째 참가자 \(player)"
            }.subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)

        playerSubject.onNext("김주희")
        playerSubject.onNext("이하늘")
        playerSubject.onNext("박민지")

        Thread.sleep(forTimeInterval: 5)
    }
```
#### 결과
```
interval emit : 0
interval emit : 1
0 번째 참가자 김주희
interval emit : 2
1 번째 참가자 이하늘
interval emit : 3
2 번째 참가자 박민지
interval emit : 4
```
이 함수의 쓰레드는 총 6초동안 살아있는데요, 6초가 되는 순간 종료되면서 disposeBag에 의해 dispose 되므로 `interval` 또한 종료됩니다.
그래서 `interval`이 배출하는 항목은 0-4 까지 5개의 항목을 내보내게 됩니다.

`zip`은 방출된 순서에 맞추어서 두 개의 Observable을 결합하는 것입니다.

그렇기 때문에 `interval`이 1까지 나온 상태에서 `playerSubject`와 `zip`을 시작해도 `interval`의 첫 번째 항목과 `playerSubject`의 첫 번째 항목이 결합되어 나옵니다.

이후로 `playerSubject`에서 새로운 항목이 배출되면 `interval`의 다음 항목과 결합되어 나오고, `interval`의 항목이 더 많이 배출되어도 `playerSubject`와 **매칭되는 항목이 없으면 새로운 항목을 배출하지 않습니다.**

### CombineLatest
두 Observable 중 하나가 항목을 방출 할 때 지정된 함수를 통해 Observable이 방출했던 최신 항목을 결합하고 이 함수의 결과에 따라 변형된 항목을 내 보냅니다.

`CombineLatest` 연산자는 `Zip`과 비슷한 방식으로 작동하지만 `Zip`은 각 압축 된 소스 Observable이 이전에 압축 해제 된 항목을 내보낼 때만 항목을 내보내는 반면 CombineLatest는 소스 Observable 중 하나가 항목을 내보낼 때마다 항목을 내보냅니다. Observables의 출처가 적어도 하나의 항목을 방출했다). Source Observables가 항목을 방출 할 때, CombineLatest는 제공 한 함수를 사용하여 다른 소스 Observables 각각에서 가장 최근에 방출 된 항목을 결합하고 해당 함수의 반환 값을 방출합니다.

#### 예제
위의 zip을 조금 변형했습니다. 0.4초 주기의 `interval`이 있고, 0.6초 마다 플레이어 정보를 내보내는 `player`를 가지고 `combineLatest`를 사용해봤습니다.
```swift
    func testCombineLatest() {
        let disposeBag = DisposeBag()
        let scheduler = SerialDispatchQueueScheduler(qos: .default)
        let interval = Observable<Int>.interval(.seconds(1), scheduler: scheduler)
        let playerInterval = Observable<Int>.interval(.seconds(2), scheduler: scheduler)
        let players = Observable<String>.from(["김주희","이하늘","박민지"])
        let player = Observable.zip(playerInterval, players) { $1 }
        Observable.combineLatest(interval, player) { index, player in
            "interval > \(index) / player > \(player)"
            }.subscribe(onNext: {
                print(Date())
                print($0) })
            .disposed(by: disposeBag)
        Thread.sleep(forTimeInterval: 6)
    }
```
결과는 다음과 같네요.
```
interval > 1 / player > 김주희  // 0
interval > 2 / player > 김주희  // 1
interval > 3 / player > 김주희  // 2  동시에 들어와도 앞의 Observable 항목(interval 3)을 먼저 배출합니다.
interval > 3 / player > 이하늘  // 2  
interval > 4 / player > 이하늘  // 3
interval > 5 / player > 이하늘  // 4
interval > 5 / player > 박민지  // 4
interval > 6 / player > 박민지  // 5
```
Observable에서 동시에 이벤트가 발생해도 Observable이 선언된 순서대로 이벤트를 발생시킵니다. 저는 **6초동안 6개의 이벤트가 발생할거라고 생각했는데, 8개의 이벤트가 발생했네요!** 왜 그럴까요?

2초에는 `interval`에서도 이벤트가 발생하고 `player`에서도 이벤트가 발생합니다. 하지만 `combineLatest`에서 `interval`이 `player`의 앞에 선언되었기 때문에 `interval`의 새로운 항목인 '3'과 기존 `player`의 마지막 항목인 '김주희'를 결합시키고, 그 다음 `interval`의 기존 항목인 '3'과 `player`의 새로운 항목 `이하늘`을 결합합니다.


### WithLatestFrom
`withLatestFrom` 은 `combineLatest`와는 조금 다릅니다. `combineLatest`는 다수의 Observable에서 이벤트가 발생될 때마다 새로운 이벤트를 만들어내지만, `withLatestFrom`은 주요 Observable이 있습니다. 주 Observavble에 새로운 이벤트가 발생했을 때, 다른 Observable의 마지막 항목을 가져다 결합하는 형태입니다.

그래서 `combineLatest`는 Observable의 `static func`으로 다음과 같이 결합할 source Observable을 받지만,
```swift
static func combineLatest<O1, O2>(_ source1: O1, _ source2: O2, resultSelector: @escaping (O1.Element, O2.Element) throws -> String) -> Observable<String> where O1 : ObservableType, O2 : ObservableType
```
`withLatestFrom`은 Observable의 instance `func`으로 이 Observable에 결합할 second Observable을 받습니다.
```swift
func withLatestFrom<Source, ResultType>(_ second: Source, resultSelector: @escaping (Int, Source.Element) throws -> ResultType) -> Observable<ResultType> where Source : ObservableConvertibleType
```

#### 예제
예를 들어서, 한 식당에 웨이팅하는 사람들이 있다고 합시다.
1.8초마다 사람이 들어갈 수 있지만 그 때 사람이 없을 경우에는 지나가며, 사람들은 1초만 기다립니다.
```swift
    func testWithLatestFrom() {
        let disposeBag = DisposeBag()
        let scheduler = SerialDispatchQueueScheduler(qos: .default)
        let enterInterval = Observable<Int>.interval(.milliseconds(1800), scheduler: scheduler)
        let waitingInterval = Observable<Int>
            .interval(.seconds(1), scheduler: scheduler)
        let waitingList = Observable<String>.from(["김주희","이하늘","박민지", "공주연", "이상민"])
        let waitings = Observable.zip(waitingInterval, waitingList) { $1 }

        enterInterval.withLatestFrom(waitings, resultSelector: { $1 })
            .subscribe(onNext: {
                print($0) })
            .disposed(by: disposeBag)
        Thread.sleep(forTimeInterval: 6)
    }
```

이걸 좀 더 쉽게 도식화 해봅시다. (X 식당에 자리가 났을 때 발생되는 이벤트입니다.)

                 X                  X                 X

0---------1---------2---------3---------4---------5--------6

        김주희      이하늘      박민지      공주연     이상민      

첫 번째로 X가 발생했을 때 기다리고 있던 사람은 김주희였고,
두 번째로 X가 발생했을 때 기다리고 있던 사람은 박민지였습니다.
이하늘은 1초만 기다리고 자리를 떠났기 때문에 다음 사람으로 넘어갔습니다.

그리고 마찬가지로 공주연 또한 지나가고, 마지막으로 이상민이 호출됩니다.

결과를 보시죠!
```
김주희
박민지
이상민
```

### StartWith
Observable이 새로운 항목을 내보내기 전에 미리 지정된 항목을 내보냅니다.

이전 operator 보다는 비교적 간단하네요. 새로운 항목을 내보낼 기 전 최초의 항목을 추가해주는 연산자입니다!
#### 예제
```swift
    func testStartWith() {
        let disposeBag = DisposeBag()
        Observable.from([1, 2])
        .startWith(0)
        .subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)
    }
```
```
0
1
2
```

### SwitchLatest
Observable Sequence를 항목으로 받으면 이 Sequence의 이벤트들을 내보내고, 다음 Sequence가 항목으로 진입될 경우 이전 Sequence의 마지막 항목을 생략합니다.
```swift
    func testSwitchLastest() {
        let disposeBag = DisposeBag()
        let subject1 = BehaviorSubject(value: "⚽️")
        let subject2 = BehaviorSubject(value: "🍎")
        let subjectsSubject = BehaviorSubject(value: subject1)

        subjectsSubject.asObservable()
            .switchLatest()
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)

        subject1.onNext("🏈")
        subject1.onNext("🏀")

        subjectsSubject.onNext(subject2)
        subject1.onNext("⚾️")
        subject2.onNext("🍐")
    }
```
```
⚽️
🏈
🏀
🍎
🍐
```

### And/Then/When
패턴과 Plan intermediaries를 통해 2개 이상의 Observable을 결합합니다.

And/Then/When 연산자는 `.zip`연산자와 매우 유사하지만 중간 데이터 구조를 사용합니다. 두 개 이상의 Observable을 받아들이고 한 번에 한 set 씩 방출된 각가의 배출물을 `Pattern` 개체로 결합합니다. 그 다음 `Pattern` 객체를 조작하여 `Plan` 단계에서 변형합니다. 이후 이 `Plan` 객체를 `Observable`로 변환합니다.

근데 RxSwift 에서 andThen 은 `Completable`에만 사용될 수 있나봐요. 하지만 그 예가 많지 않아서, 좀 더 찾아봐야 할 것 같습니다.
[참고하기](https://github.com/ReactiveX/RxSwift/blob/master/RxSwift/Traits/Completable%2BAndThen.swift)

---
layout: post
title:  "Transforming Observables"
date:   2019-07-14 09:47:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 operator Transforming flatMap map
author: Juhee Kim
mathjax: true
---

* content
{:toc}

### map
Observable Sequence에 변형 클로적를 적용하고 이를 기반으로 새로운 Observable Sequence를 생성합니다.
```swift
    func testMap() {
        let disposeBag = DisposeBag()
        Observable.of(1, 2, 3)
            .map { $0 * $0 }
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
1
4
9
```

### flatMap and flatMapLatest
Observable에 의해 방출된 항목들을 원하는 형태의 Observable로 변환합니다. Observable의 항목을 다시 Observable<Element>로 변환할 경우 flatMap은 Observable의 항목을 받은 것처럼 풀어냅니다.
`Observable<T>` => `T`
#### flatMap 예제
```swift
    func testFlatMap() {
        let disposeBag = DisposeBag()

        struct Player {
            init(score: Int) {
                self.score = BehaviorSubject(value: score)
            }
            let score: BehaviorSubject<Int>
        }

        let 👦🏻 = Player(score: 80)
        let 👧🏼 = Player(score: 90)

        let player = BehaviorSubject(value: 👦🏻)
        player.asObservable()                     // Observale<Player>
            .flatMap { $0.score.asObservable() }  // Observable<BehaviorSubject> -> Observable<Observable<Int>>
            .subscribe(onNext: { print("flatmap : \($0)") })
            .disposed(by: disposeBag)
        👦🏻.score.onNext(85)
        player.onNext(👧🏼)
        👦🏻.score.onNext(95)
        👧🏼.score.onNext(100)
    }
```
👦🏻를 초기값으로 가지는 `BehaviorSubject`를 구독하는 관찰자 `player`가 있습니다.
plyaer에 대한 onNext()는 한 번만 불립니다.

하지만 `BehaviorSubject`이기 때문에 기본값인 👦🏻와, `onNext(👧🏼)` 로 총 2개의 항목이 배출됩니다.

그리고 각 player의 score 또한 `BehaviorSubject`로, Observable Stream입니다.
굳이 표현하자면, BehaviorSubject로 넘어오는 항목의 형태는 `Observable<Player>`이며, score는 `Observable<Observable<Item>>`으로 표현될 수 있습니다.

`flatmap`을 통해서 `Observable<Player>`의 score를 `.asObservable()`로 `Observable<Observable<Item>>` 형태로 변환했습니다.
그 결과는 다음과 같습니다.
```
flatmap : 80
flatmap : 85
flatmap : 90
flatmap : 95
flatmap : 100
```
`onNext(👧🏼)`를 통해 배출되는 항목이 달라졌지만, player의 `score : BehaviorSubject`에 subscribe를 했기 때문에 계속 관찰자로서 변화를 받아들일 수 있습니다.

위에서 설명했던 `.map`으로 변경하면 결과가 어떨까요? 연산자를 `.flapMap`에서 `.map`으로 변경하면 다음과 같은 결과를 가집니다.
```
flatmap : RxSwift.BehaviorSubject<Swift.Int>
flatmap : RxSwift.BehaviorSubject<Swift.Int>
```
`.map`을 통해 Player의 score로 변환했기 때문에 넘어오는 항목은 `BehaviorSubject<Int>` 타입이 넘어옵니다.
flatMap은 Observable Sequence를 Observable처럼 평평하게 풀어내주지만, Map은 변환만 해줍니다.

그럼 flatMapLatest는 뭘까요? 마찬가지로 연산자를 `.flatMapLatest`로 변경한 결과는 다음과 같습니다.
```
flatmap : 80
flatmap : 85
flatmap : 90
flatmap : 100
```

마지막 Observable Sequence만을 유지합니다. 이전과는 달리 👦🏻의 스코어가 95점으로 변경된 이벤트를 받지 못합니다.

자매품으로 `.flatMapFirst`가 있습니다. 이 연산자는 처음으로 들어온 Observable Sequence만을 유지합니다.
```
flatmap : 80
flatmap : 85
flatmap : 95
```
`onNext(👧🏼)` 에 대한 이벤트를 전혀 받아오지 못합니다.

### scan
초기 시드값(Int)를 가지고 들어오는 Observable 항목에 대한 변경을 하고, 변경된 값을 다시 시드값으로 삼아서 다음 들어오는 항목에 사용합니다.
```swift
    func testScan() {
        let disposeBag = DisposeBag()
        Observable.of("Hello", "Every", "body")
            .scan("start") { aggregateValue, newValue -> String in
                "\(aggregateValue) : \(newValue)"
            }
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
start : Hello
start : Hello : Every
start : Hello : Every : body
```

이를 기반으로 Int의 1승부터 10승까지를 출력해주는 기능을 만들어봅시다~
```swift
   func power(element: Int) {
        Observable.repeatElement(element)
            .scan(1) { aggregateValue, newValue -> Int in
                aggregateValue * newValue
            }
            .take(10)
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
  }
  power(2)
```
```
2
4
8
16
32
64
128
256
512
1024
```
앞의 값을 시드 삼아 만들기 때문에 제곱한 결과가 나오게 됩니다 XD

### Buffer
Buffer는 omit 되는 observable를 지정된 크기만큼 담고 있다가 그 버퍼 크기가 꽉 차면 그때 observable을 배열로 내보내는 연산자입니다.
```swift
    func testBuffer() {
        let disposeBag = DisposeBag()
        Observable<Int>.range(start: 1, count: 10)
            .buffer(timeSpan: .seconds(2), count: 3, scheduler: SerialDispatchQueueScheduler(qos: .background))
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
[1, 2, 3]
[4, 5, 6]
[7, 8, 9]
[10]
```

### GroupBy
GroupBy는 주어진 조건에 따라 Observable을 Grouping 해서 `Observable<GroupedObservable<Key, Int>>` 형태로 변경합니다.
저는 1부터 10까지 Observable을 발생시키고 짝수냐 홀수냐에 따라 2개의 그룹으로 나누었습니다.
```swift
    func testGroupBy() {
        let disposeBag = DisposeBag()
        Observable<Int>.range(start: 1, count: 10)
            .groupBy { $0 % 2 == 0 ? "even" : "odd" }
            .subscribe(onNext: { print("\($0.key) : \($0.asObservable())")})
            .disposed(by: disposeBag)
    }
```
```
odd : RxSwift.(unknown context at $10d81fa88).GroupedObservableImpl<Swift.Int>
even : RxSwift.(unknown context at $10d81fa88).GroupedObservableImpl<Swift.Int>
```
key와 value를 출력하니, key로 함께 묶인 GroupedObservable 이 나오는 군요! 그리고 총 10개의 항목이 배출되었지만, 그룹화되어 2개의 항목으로 묶여서 나왔습니다.

이렇게 된 상태에서는 내부의 값을 알아보기가 어렵습니다. 그럼 어떻게 해야할까요?

key와 함께 해당하는 값을 함께 보고 싶다면~ 10번 발생했다면 10번 모두 대응하고 싶다면~~~~ 앞에서 썼던 `.flatMap`을 활용합시다.

`.flatMap`은 `Observable<Observable<Int>>`로 되어있다면 얘를 `Observable<Int>`로 평평~하게 한번 감싸진 것을 풀어내줍니다.

```swift
    func testGroupBy() {
        let disposeBag = DisposeBag()
        Observable<Int>.range(start: 1, count: 10)
            .groupBy { $0 % 2 == 0 ? "even" : "odd" }
            .flatMap({ group -> Observable<String> in
                return group.asObservable()
                    .map { "\(group.key) : \($0)" }
            })
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
odd : 1
even : 2
odd : 3
even : 4
odd : 5
even : 6
odd : 7
even : 8
odd : 9
even : 10
```
우왕! ㅋ_ㅋ 원하는 결과가 나왔네요.

### Window
Observable에서 Observable window으로 항목을 주기적으로 세분하고 한 번에 하나씩 항목을 내보내는 대신 window를 내보냅니다.
Window는 Buffer와 유사하지만 Observable 소스에서 항목의 패킷을 내보내는 대신 Observable Sequence로 변경하여 내보냅니다.
```swift
    func testWindow() {
        let disposeBag = DisposeBag()
        Observable<Int>.range(start: 1, count: 10)
            .window(timeSpan: .seconds(2), count: 3, scheduler: SerialDispatchQueueScheduler(qos: .background))
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
RxSwift.AddRef<Swift.Int>  // 1, 2, 3
RxSwift.AddRef<Swift.Int>  // 4, 5, 6
RxSwift.AddRef<Swift.Int>  // 7, 8, 9
RxSwift.AddRef<Swift.Int>  // 10, 11, 12
```
얘를 다시 풀어내고 싶다면? `.flatMap`을 사용하면 되겠죠!
```swift
    func testWindow() {
        let disposeBag = DisposeBag()
        Observable<Int>.range(start: 1, count: 10)
            .window(timeSpan: .seconds(2), count: 3, scheduler: SerialDispatchQueueScheduler(qos: .background))
            .flatMap { $0 }
            .subscribe(onNext: { print($0) })
            .disposed(by: disposeBag)
    }
```
```
1
2
3
4
5
6
7
8
9
10
```
어떤 결과를 원하느냐에 따라 `buffer`와 `window`를 선택해서 사용해볼 수 있겠네요.

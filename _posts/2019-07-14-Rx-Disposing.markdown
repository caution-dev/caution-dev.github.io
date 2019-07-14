---
layout: post
title:  "Disposing"
date:   2019-07-14 09:45:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 Disposing
author: Juhee Kim
mathjax: true
---

* content
{:toc}

## Dispose?
구독 후 Observable Sequence 항목 생성을 취소하고 resource를 반환하려면, `dispose`를 호출하세요.

Sequence가 유한 시간 내에 종료되면, dispose를 호출하지 않거나 disposeBag으로 disposed를 사용하지 않아도 resource leak이 발생하지 않습니다. 하지만 이 resource들은 요소의 생성이 완료되거나 오류를 반환하여  시퀀스가 완료될 때까지 사용됩니다.

만약 Sequence가 스스로 종료되지 않는다면 `disposeBag`, `takeUntil` 연산자를 사용하거나 다른 방식으로 `dispose`가 수동으로 호출되지 않는 한 **리소스가 영구적으로 할당됩니다.**

**dispose bag이나 `takeUntil` 연산자를 사용하면 자원을 정리할 수 있습니다. Sequence가 유한시간 안에 종료되더라도 사용하는 게 좋습니다.**

#### 왜 Swift.Error가 Generic이 아닌가요? (갑자기?) [원본](https://github.com/ReactiveX/RxSwift/blob/master/Documentation/DesignRationale.md#why-error-type-isnt-generic)
```swift
enum Event<Element>  {
    case next(Element)      // next element of a sequence
    case error(Error)       // sequence failed with error
    case completed          // sequence terminated successfully
}
```
만약 error가 generic type이라면 어떨까요?

`Observable<String, E1>` and `Observable<String, E2>`가 있다면, 각각의 Observable이 만들어내는 에러 타입이 무엇인지 알아내야 합니다. 이는 composition 속성을 해치며, Rx는 왜 Sequence가 실패했는지에는 관심이 없으며(ㅋㅋ), 실패한 결과를 observable chain 하위로 전달하게 됩니다.

어음 느낌적인 느낌으로 굳이 Generic 타입의 Error를 사용할 필요가 없다-는 건 알겠는데, (이미 Swift에서 정의한 Error protocol을 사용하는 것으로 충분하기 때문에) 글의 의미를 이해를 못하겠음.

## Disposing
observable sequence를 종료시키는 방법입니다. `dispose`를 구독에 호출하면 기존에 할당된 모든 항목과 앞으로의 항목들에 대한 resource가 모두 해제되며 sequence가 종료됩니다.

`interval` 연산자와 관련된 예가 있습니다. 300 milliseconds마다 항목이 발생되며, 2초간 이 쓰레드를 유지해봅시다.
```swift
let scheduler = SerialDispatchQueueScheduler(qos: .default)
let subscription = Observable<Int>.interval(.milliseconds(300), scheduler: scheduler)
    .subscribe { event in
        print(event)
    }

Thread.sleep(forTimeInterval: 2.0)

subscription.dispose()
```
> SerialDispatchQueueScheduler를 사용하여 현제 쓰레드와 별도의 쓰레드를 사용하는 Observable을 만들었습니다.
> 그렇기 때문에 현재 쓰레드가 종료되어 버리면 Observable Sequece의 쓰레드가 아무리 항목을 만들어도 받을 수 없습니다.(구독중인 쓰레드가 종료되었으니까)
> 그래서 `Thread.sleep(forTimeInterval: 2.0)`를 통해 현재 쓰레드를 2초간 유지해줍니다.
> 그럼 300 milliseconds마다 항목이 배출되니까, 다음과 같은 결과가 나오겠죠!
```
0
1
2
3
4
5
```
`dispose`를 호출하고 싶지 않다면, 다른 방법이 있습니다! `DisposeBag`을 사용하거나 `takeUntil` 연산자를 사용하세요!

그래서 위의 코드에서는 `dispose`가 호출된 다음 뭔가가 print 될까요? 정답은 : 때때로 다릅니다. XD
* 만약 `scheduler`가 **serial scheduler**라면 (`MainScheduler`라던가) `dispose`가 **같은 serial scheduler**에서 호출됩니다. 그래서 더 이상 print 되지 않죠.
* 다른 경우에는 네, 그럴 수 있습니다.
#### 예시
```swift
        let serial = SerialDispatchQueueScheduler(qos: .default)
        let subscription = Observable<Int>.interval(.milliseconds(100), scheduler: serial)
            .subscribe { event in
                print("serial: \(event)")
        }

        let concurrent: SchedulerType = ConcurrentDispatchQueueScheduler(qos: .default)
        let subscription2 = Observable<Int>.interval(.milliseconds(100), scheduler: concurrent)
            .subscribe { event in
                print("concurrent: \(event)")
        }

        Thread.sleep(forTimeInterval: 3.0)
        print("ended")
        subscription.dispose()
        subscription2.dispose()
```
#### 결과
```
serial: next(0)
concurrent: next(0)
serial: next(1)
concurrent: next(1)
...
serial: next(28)
concurrent: next(28)
serial: next(29)
ended
concurrent: next(29)
```
### Dispose Bags
Dispose bag은 Rx에서 ARC와 같은 동작을 합니다. `DisposeBag`이 dealloc되는 시점에, DisposeBag에 포함된 모든 `disposable` 들에 대해 `dispose`가 호출됩니다.

그렇기 때문에 Dispose bag에는 `dispose` 메서드가 없습니다. 만약 Dispose bag의 모든 항목을 정리해야한다면, 새로운 DisposeBag을 만들면 됩니다.
```
  self.disposeBag = DisposeBag()
```
그러면 이전 DisposeBag이 dealloc되면서 담겨있던 항목들이 `dispose()`가 호출됩니다.

만약 명시적으로 dispose를 처리하고 싶다면 `CompositeDisposable`을 사용하세요.
### Take until
다른 방법으로는 dealloc될 때 `takeUntil`을 사용해서 자동으로 dispose 되도록 하는 방법이 있습니다.
```swift
sequence
    .takeUntil(self.rx.deallocated)
    .subscribe {
        print($0)
    }
```
### 찾아보기
[Disposable의 종류](https://brunch.co.kr/@tilltue/49)

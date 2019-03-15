---
layout: post
title:  "GCD vs Operation Queue"
date:   2019-03-15 17:26:06 +0900
categories: iOS
tags: iOS 면접준비 GCD Operation-Queue 비동기 동시성
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

## 동시성 프로그래밍과 비동기 프로그래밍

#### 프로세서

컴퓨터 내에서 프로그램을 수행하는 하드웨어 유닛으로 CPU (Central Processing Unit)이 여기에 속합니다. 한 컴퓨터가 여러 개의 프로세서를 갖는다면 멀티 프로세서라고 합니다. (듀얼 프로세서 등)

#### 코어

프로세서 내부의 주요 연산회로를 말합니다. 싱글코어는 하나의 연산회로가 내장되어 있는 것이고 듀얼코어는 두 개의 연산회로가 내장된 것을 말합니다.

#### 프로그램과 프로세스

프로그램은 보조기억 장치에 저장된 실행코드를 말합니다. 프로세스는 이 프로그램을 구동하여 실행코드와 그 상태가 실제 메모리상에서 실행되는 작업 단위를 말합니다. 동시에 여러 개의 프로세스를 운용하는 **시분할 방식** 을 멀티태스킹이라고 합니다. 이러한 프로세스 관리는 운영체제에서 담당합니다.

#### 스레드

스레드는 하나의 프로세스 내에서 실행되는 작업흐름의 단위를 말합니다. 보통 한 프로세스는 하나의 스레드를 가지고 있지만 환경에 따라 둘 이상의 스레드를 동시에 실행할 수 있습니다. 이러한 방식을 멀티스레딩이라고 합니다. 프로그램 실행이 시작될 때부터 동작하는 스레드를 메인 스레드라고 하고 나중에 생성된 스레드를 서브 스레드 또는 세컨더리 스레드라고 합니다.

#### 비동기 프로그래밍

프로그램의 주 실행 흐름을 멈추어서 기다리지 않고 다음 작업을 실행할 수 있게 하는 방식입니다. 코드의 실행 및 결과 처리를 별도의 공간에 맡겨둔 뒤 그 실행결과를 기다리지 않고 다음 코드를 실행하는 병렬처리 방식입니다. 비동기 프로그래밍은 언어 및 프레임워크에서 지원하는 여러 방법으로 구현할 수 있습니다.

#### 동시성 프로그래밍

논리적인 용어로 동시에 실행되는 것처럼 보이는 방식입니다. 싱글 코어에서 멀티스레드를 동작시키기 위한 방식으로 멀티 태스킹을 위해 여러 개의 스래드가 번갈아 가면서 실행되는 방식입니다.

#### 병렬성 프로그래밍

물리적으로 정확히 동시에 실행되는 것을 말합니다. 멀티 코어에서 멀티 스레드를 동작시키는 방식으로 **데이터 병렬성(Data Parallelism)**과 **작업 병렬성(Task Parallelism)**으로 구분됩니다.

* 데이터 병렬성 : 전체 데이터를 나누어 서브 데이터들로 만든 뒤, 서브 데이터들을 병렬 처리해서 작업을 빠르게 수행하는 방법입니다.
* 작업 병렬성 : 서로 다른 작업을 병렬 처리하는 것을 말합니다.

#### 동시성과 병렬성의 차이

 동시성 프로그래밍과 병렬성 프로그래밍 모두 비동기 동작을 구현할 수 있지만 동작 원리가 다릅니다.

 예를 들어 꼬치 가게에서 꼬치를 사려고 기다리고 있다고 생각해봅시다. 꼬치 가게 판매 직원이 2명이어서 사람들은 줄을 2줄로 섰습니다. 그래서 판매직원 한명이 한 줄을 담당해서 N:N으로 업무처리를 진행하는 게 됩니다. 이게 **병렬성** 입니다. 병렬성은 물리적으로 동시에 여러 작업을 처리할 수 있습니다. 판매직원이 하나의 **코어** 가 되고, 줄이 처리해야하는 데이터나 작업인 것이죠. 이 병렬성을 구현하기 위해서는 **멀티 코어** 환경이 필요합니다.

 그렇다면 이 상황에서 판매 직원 하나가 급히 자리를 비운다면 어떻게 될까요? 한 줄만 계속해서 판매된다면 다른 줄의 사람들의 원성을 사게 되겠죠. 그래서 한 명의 판매직원이 두 줄의 손님을 번갈아가면서 판매합니다. **논리적으로는 하나의 코어가 여러 줄의 작업을 동시에 처리하는 것처럼 보이지만 물리적으로 동시에 처리하는 것은 아닙니다.**

#### iOS 환경에서의 동시성 프로그래밍 지원 종류

* GCD (Grand Central Dispatch) : 멀티 코어와 멀티 프로세싱 환경에서 최적화된 프로그래밍을 할 수 있도록 애플이 개발한 기술입니다.
* Operation Queue : 비동기적으로 실행되어야 하는 작업을 객체 지향적인 방법으로 사용합니다.



## Operation Queue

#### Operation

 작업과 관련된 코드와 데이터를 나타내는 추상 클래스

#### Operation Object

 애플리케이션에서 수행하려는 Operation을 캡슐화하는 데 사용하는 Foundation 프레임워크의 Operation 클래스 인스턴스입니다.

#### Operation Queue

* Operation의 실행을 관리하며, Operation을 담는 대기열(Queue)입니다.
* 대기열에 담긴 Operation을 취소하려면 Operation Object의 ```cancel()```을 호출하거나 Operation Queue의 ```cancelAllOperations()```를 호출하여 대기열의 모든 연산을 취소하는 방법이 있습니다.
* KVO를 사용해 작업 진행 상황을 감시할 수 있습니다.



## Grand Central Dispatch

 기본적으로 스레드 풀의 관리를 프로그래머가 아닌 운영체제에서 관리하기 때문에 프로그래머가 작업을 비동기적으로 **간편하게** 사용할 수 있습니다. 프로그래머가 작업을 생성하고 Dispatch Queue에 추가하면 GCD는 작업에 맞는 스레드를 자동으로 생성해서 실행하고 작업이 종료되면 스레드를 제거합니다.

#### Dispatch Queue

 작업을 담고 있는 대기열로, 항상 선입선출(FIFO : First in first out) 방식으로 실행됩니다. 대기열에 추가된 항목은 시스템이 관리하는 스레드 풀에서 처리하고 작업을 완료하면 스레드를 알아서 해제합니다.

* Serial Disptach Queue : 한 번에 하나의 작업만을 실행하며 이 작업이 끝나서 대기열에서 제외도고 새로운 작업이 시작되기 전까지 기다립니다. 별도로 명시하지 않으면 DispatchQueue의 기본은 Serial 입니다.
* Concurrent Disptach Queue : 이미 시작된 작업이 완료될 때까지 기다리지 않고 가능한 많은 작업을 진행합니다. 즉, 병렬처리 방식입니다.

#### Quality of Service (QoS)

 Concurrent하게 작업을 처리하면서 작업의 우선 순위를 지정하기 위해 사용합니다.

* userInteractive : 중요도가 높고 즉각적인 반응이 요구되는 작업으로 즉각적으로 작업이 처리됩니다. UI업데이트나 이벤트 핸들링 등에 사용됩니다.
* userInitiated : 빠른 결과를 기대할 때 사용하는 QoS
* utility : 계산, I/O, 네트워킹 등 시간이 다소 오래 걸리는 작업입니다.
* background : 유저가 인지하지도 못하는 뒷단에서 실행되는 작업입니다.

#### Dispatch Source

 특정 유형의 시스템 이벤트를 비동기적으로 처리하기 위한 C 기반 매커니즘입니다. 특정 유형의 시스템 이벤트에 대해 정보를 캡슐화하고, 해당 이벤트가 발생할 때마다 특정 클로저 객체 혹은 기능을 Dispatch Queue에 전달합니다.

### GCD API

#### Main Queue 생성

``` swift
DispatchQueue.main.async {}
```

#### Global Queue 생성

``` swift
DispatchQueue.global().sync {}
DispatchQueue.global(qos: .background).async {}
```

#### Custom Dispatch Queue 생성

``` swift
Dispatch Queue(label: "com.serialQueue").async {}
Dispatch Queue(label: "com.concurrentQueue",
              qos: .default,
              attributes: .concurrent,
              autoreleaseFrequency: .inherit,
              target: nil).async {}
```

#### asyncAfter : 지정된 시간이 지나면 작업을 실행합니다.

``` swift
DispatchQueue.main.asyncAfter(deadline: .now() + 0.1) {}
DispatchQueue.main.asyncAfter(deadline: .now() + .miliseconds(100)) {}
```

### Serial, Concurrent, sync, async
Serial과 Concurrent의 차이는 직렬 Queue와 병렬 Queue의 차이를 가집니다.
* sync : 큐에 작업을 추가한 후, 추가된 작업이 종료될 때 까지 기다립니다.
sync의 경우 하나의 작업이 Queue에서 빠져나갈 때까지 기다리기 때문에, Serial 이냐 Concurrent냐의 차이는 없습니다. sync로 모든 작업을 Queue에 넣을 경우 그 **순서가 보장됩니다**.

* aync : 큐에 작업을 추가하고, 이 작업의 완료 여부와 관계없이 다음 명령을 실행합니다.
두 개의 Serial Queue 가 있고 여기에 작업을 각각 따로 넣어본다면 어떨까요?
```swift
DispatchQueue(label: "first").async { print("1") }
DispatchQueue(label: "second").async { print("2") }
DispatchQueue(label: "first").async { print("3") }
DispatchQueue(label: "second").async { print("4") }
DispatchQueue(label: "first").async { print("5") }
DispatchQueue(label: "second").async { print("6") }
```
그 결과는 실행할 때마다 달라집니다. 하지만 Serial, 직렬 큐이기 때문에 1 > 3 > 5, 2 > 4 > 6 의 순서는 보장됩니다.
중간에 sleep을 넣으면 어떻게 될까요?
```swift
DispatchQueue(label: "first").async { print("1") }
DispatchQueue(label: "second").async {
    print("2 start")
    sleep(5)
    print("2 end")
}
DispatchQueue(label: "first").async { print("3") }
DispatchQueue(label: "second").async { print("4") }
DispatchQueue(label: "first").async {
    print("5 start")
    sleep(5)
    print("5 end")
}
DispatchQueue(label: "second").async { print("6") }
==> 결과
1
2 start
3
5 start
4
6
2 end
5 end
```
마찬가지로 1 > 3 > 5와 2 > 4 > 6 의 순서로 시작됩니다. 하지만 2번과 5번은 최소 5초 이상 걸리는 작업으로 다른 작업들보다 늦게 끝났습니다.
직렬 큐이기 때문에 들어오는 작업 순서대로 처리를 시작하지만, 앞 선 작업이 완료되지 않아도 다음 작업을 처리합니다.

Concurrent * Async 방식에서는 어떨까요?
``` swift
let fisrtConcurrentQueue = DispatchQueue(label: "first",
                                   qos: .default,
                                   attributes: .concurrent,
                                   autoreleaseFrequency: .inherit,
                                   target: nil)
let secondConcurrentQueue = DispatchQueue(label: "second",
                                   qos: .default,
                                   attributes: .concurrent,
                                   autoreleaseFrequency: .inherit,
                                   target: nil)
fisrtConcurrentQueue.async { print("1") }
secondConcurrentQueue.async { print("2") }
fisrtConcurrentQueue.async { print("3") }
secondConcurrentQueue.async { print("4") }
fisrtConcurrentQueue.async { print("5") }
secondConcurrentQueue.async { print("6") }
==> 결과
1
4
5
6
3
2
```
병렬 큐에 비동기로 동작되기 때문에 작업 처리의 순서가 보장되지 않습니다.


## GCD vs Operation Queue

* Operation Queue에서는 동시에 실행할 수 있는 Operation의 최대 수를 지정할 수 있습니다.

* Operation Queue에서는 Operation을 일시 중지, 다시 시작 및 취소를 할 수 있습니다.

* Operation Queue에서는 KVO를 사용할 수 있는 많은 [프로퍼티](https://developer.apple.com/documentation/foundation/operation)들이 있습니다.

  > isCancelled, isAsynchronous, isExecuting, isFinished, isReady, dependencies, queuePriority, completionBlock

* GCD는 앞선 기능을 하지 못합니다. Operation Queue를 좀 더 쉽게 쓸 수 있도록 개발한 것으로 좀 더 오버헤드가 있지만 사용하기에 매우 간편합니다.


### 참고

* [부스트코스 - 동시성 프로그래밍과 비동기 프로그래밍](https://www.edwith.org/boostcourse-ios/lecture/16866/)
* [부스트코스 - DispatchQueue](https://www.edwith.org/boostcourse-ios/lecture/16917/)
* [Apple - DispatchQoS](https://developer.apple.com/documentation/dispatch/dispatchqos)
* [네이버블로그 - iOS 멀티쓰레딩](http://blog.naver.com/PostView.nhn?blogId=jdub7138&logNo=220949191761&parentCategoryNo=&categoryNo=112&viewDate=&isShowPopularPosts=true&from=search)

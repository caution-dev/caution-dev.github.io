---
layout: post
title:  "Observable"
date:   2019-07-14 09:43:19 +0900
categories: RxSwift
tags: Rx RxSwift 번역 Observable
author: Juhee Kim
mathjax: true
---

* content
{:toc}

## Rx는 뭔가요?
- An API for asynchronous programming with observable streams
- 관찰 가능한 stream을 이용한 비동기 프로그래밍을 위한 API
- ReactiveX is a library for composing asynchronous and event-based programs by using observable sequences.
- ReactiveX는 관찰 가능한 시퀀스를 사용하여 비동기 및 이벤트 기반 프로그램을 작성하기위한 라이브러리입니다.

Observer 패턴을 확장해서 데이터 / 이벤트 에 대한 시퀀스를 지원하고, 저레벨 쓰레딩, 동기화, 스레드 안정성, 동시성 데이터 구조, 그리고 I/O를 막지 않는 것들에 대한 추상화된 개념과 함께 선언적으로 사용할 수 있는 연산자들을 추가했습니다.

## Observable
ReactiveX에서 관찰자는 `Observable`을 구독합니다. 관찰자는 `Observable`이 배출해내는 item이나 item 흐름에 반응합니다. 이 패턴을 사용하면 Observable이 item을 배출해내기까지 어떤 흐름을 차단할 필요가 없으며, item을 배출해낼 때까지 기다립니다.
결국 Observable 이란, 관찰자가 반응할 만한 item/혹은 새로운 흐름을 배출해낼 수 있는 것을 의미합니다.

Rx를 쓰지 않고도 기존 `DispatchQueue` 를 사용해서도 비동기 작업을 쓸 수 있습니다. 하지만 Rx를 쓰면 비동기 작업을 좀 더 간결하게 작성할 수 있으며, Rx가 제공하는 여러 연산자를 이용해서 간편하게 비동기 작업을 할 수 있습니다.

## Background
많은 소프트웨어 프로그래밍 작업에서 작성한 지침은 작성한 순서대로 점진적으로 실행되고 완료됩니다. 그러나 ReactiveX에서 많은 명령어가 병렬로 실행될 수 있으며 나중에 결과가 "관찰자"에 의해 임의의 순서로 캡처됩니다. 메소드를 호출하는 대신 데이터를 검색하고 변환하는 메커니즘을 `Observable` 관찰자를 구독하고 그 시점에서 이전에 정의 된 메커니즘이 관찰자가 보초를 세우고 준비가 될 때마다 배출량을 포착하고 대응할 수 있도록 조치를 취합니다.

이 방법의 장점은 서로 의존하지 않는 여러 작업을 할 때 다음 작업을 시작하기 전에 각 작업이 완료되기를 기다리는 대신 동시에 작업을 시작할 수 있다는 것입니다. 작업 묶음은 번들 중 가장 긴 작업으로 완료하는 데 오랜 시간이 걸립니다.

비동기 프로그래밍 및 디자인의이 모델을 설명하는 데 사용되는 용어가 많이 있습니다. 이 문서는 다음 용어를 사용합니다 : 관찰자가 Observable을 구독합니다. Observable은 관찰자의 method를 호출하여 관찰자에게 항목을 내보내거나 통지를 보냅니다.

## Observer 만들기
1. 비동기 호출의 반환 값과 함께 유용한 메소드를 정의하십시오. 이 메소드가 관찰자의 일부입니다.
2. 비동기 호출 자체를 Observable로 정의하십시오.
3. 관찰자가 Observable을 구독함으로써 Observable에 연결됩니다. (이는 Observable을 초기화합니다.)
4. 다른 작업을 계속 하면 됩니다! 관찰자는 Observable에서 item이 방출되면 미리 정의된 메소드를 호출합니다.

### onNext, onCompelted, onError
Subscribe 메소드는 **어떻게 Observable에 관찰자를 연결할 것인가?**에 대한 것입니다.
관찰자는 기본적으로 다음 메소드들을 가집니다.
#### `onNext`
Observable이 새로운 항목을 배출할 때마다 호출됩니다. 이 메서드는 호출된 아이템을 parameter로 받습니다.
#### 'onError'
Observable이 예상했던 데이터를 생성하지 못했거나, 혹은 다른 오류가 발생했을 때 이 메서드를 호출합니다. 이 메서드가 호출되면 `onNext`나 `onCompleted`가 더 이상 호출되지 않습니다. `onError` 메서드는 오류를 나타내는 객체를 parameter로 받습니다.
#### `onCompleted`
`onNext`가 호출된 이후에, 어떠한 오류도 발생되지 않았다면 마-지막에 이 메서드가 호출됩니다.

> `onNext`는 매번 불리지만, `onError`와 `onCompleted`는 item이 배출되고 마지막에 불립니다. (둘 다 부를 수는 없습니다.)
> 그래서 `onNext`는 item을 '배출'한다고 하고, `onError`와 `onCompleted`는 item을 '소비'한다고들 합니다.

### 구독중단하기
일부 ReactiveX 구현에는 `unsubscribe` 메서드를 구현하는 인터페이스인 `Subscriber`라는 관찰자가 있습니다. 이 메서드를 호출하여 구독자가 현재 구독중인 `Observable`에 더 이상 관심이 없다는 것을 나타낼 수 있습니다. 이 `Observable`을 구독하는 다른 관찰자가 없다면 새로운 항목을 배출하는 것을 그만두도록 선택할 수 있습니다.

이 구독 취소의 결과는 관찰자가 구독 한 Observable에 적용되는 연산자 체인을 통해 다시 연결되어 체인의 각 링크가 항목을 내보내는 것을 중지시킵니다. 이것은 즉각적으로 수행되는 것을 보장하지 않기때문에, Observable은 더 이상 관찰자가 없음에도 잠시 동안 항목을 생성하고 방출하려고 시도 할 수 있습니다.

### Naming Convention
ReactiveX는 각 언어별로 고유한 이름을 지정하고 있습니다. 정식으로 지정된 naming 표준은 없지만, 구현상 많은 공통점이 있습니다.

 또한 이 이름 중 일부는 다른 문맥에서 다른 의미를 가지거나 특정 구현 언어의 관용구에 어색한 것으로 보입니다.

예를 들어 `onEvent` 이름 패턴을 봅시다. (예 : onNext, onCompleted, onError). 어떤 상황에서는 이러한 이름이 event handler를 등록하는 방법처럼 보이지만, ReactiveX에서는 이벤트 핸들러 자체의 이름을 지정합니다.

## Hot / Cold Observable
Observable이 언제 항목을 발생하는지에 따라 Hot/Cold로 나눌 수 있습니다.
- Hot Observable은 생성되는 즉시 항목을 배출하기 시작하기 때문에, 관찰자가 구독하는 시점에 따라 수신하는 항목이 다를 수 있습니다.
- Cold Observable은 관찰자가 구독을 시작하면 그 때부터 아이템을 방출합니다. 그렇기 때문에 관찰자는 모든 항목이 포함된 전체 시퀀스를 볼 수 있습니다.

ReactiveX에는 "Connectable" Observable이 있습니다. 이것은 관찰자의 구독여부와 관계 없이 Connect 메서드가 호출될 때까지 항목 방출을 시작하지 않습니다.

### Composition via Observable Operators
Observables와 Observer는 ReactiveX의 시작일뿐입니다. 그 자체만으로도 표준 관찰자 패턴을 조금 확장한 것일 뿐이며 단일 콜백보다는 일련의 이벤트를 처리하는 데 더 적합합니다.

진정한 힘은 Observables가 방출하는 일련의 항목을 변형, 결합, 조작 및 작업 할 수 있게 해주는 연산자인 "Reactive Extensions"(ReactiveX)에서 나옵니다.

이러한 Rx 연산자를 사용하면 비동기 시퀀스를 선언적 방식으로 함께 구성하여 콜백의 모든 효율성 이점을 얻을 수 있지만 일반적인 비동기 시스템과 관련된 중첩 콜백 처리기의 단점은 없습니다.

#### 연산자들 집합
* Creating Observables
  * Create, Defer, Empty/Never/Throw, From, Interval, Just, Range, Repeat, Start, and Timer
* Transforming Observable Items
  * Buffer, FlatMap, GroupBy, Map, Scan, and Window
* Filtering Observables
  * Debounce, Distinct, ElementAt, Filter, First, IgnoreElements, Last, Sample, Skip, SkipLast, Take, and TakeLast
* Combining Observables
  * And/Then/When, CombineLatest, Join, Merge, StartWith, Switch, and Zip
* Error Handling Operators
  * Catch and Retry
* Utility Operators
  *. Delay, Do, Materialize/Dematerialize, ObserveOn, Serialize, Subscribe, SubscribeOn, TimeInterval, Timeout, Timestamp, and Using
* Conditional and Boolean Operators
  *. All, Amb, Contains, DefaultIfEmpty, SequenceEqual, SkipUntil, SkipWhile, TakeUntil, and TakeWhile
* Mathematical and Aggregate Operators
  * Average, Concat, Count, Max, Min, Reduce, and Sum
* Converting Observables
  * To
* Connectable Observable Operators
  * Connect, Publish, RefCount, and Replay
* Backpressure Operators
  * a variety of operators that enforce particular flow-control policies

### 연산자 체이닝
대부분의 연산자는 Observable에서 작동하고 Observable을 반환합니다. 이렇게 하면 체인에서 이러한 연산자를 차례로 적용 할 수 있습니다. 체인의 각 연산자는 이전 연산자의 연산결과 인 Observable을 수정합니다.

Builder 패턴와 같이, 이 패턴에서는 특정 클래스의 연산 메소드를 통해 동일한 클래스 항목에 대한 수정을 할 수 있습니다. 이러한 패턴을 사용하면 비슷한 방식으로 메서드를 연결할 수 있습니다. 그러나 빌더 패턴에서 메소드가 체인에 나타나는 순서는 일반적으로 중요하지 않습니다. Observable 연산자는 중요 사항을 순서대로 지정합니다.

Observable 연산자 체인은 체인을 생성 한 원래 Observable에서 독립적으로 작동하지 않으며, 체인 되기 직전의 연산자가 생성한 Observable에서 각각 차례로 작동합니다.

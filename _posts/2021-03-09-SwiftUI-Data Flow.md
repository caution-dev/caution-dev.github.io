---
layout: post
title:  "SwiftUI Data Flow"
date:   2021-03-09 00:26:06 +0900
categories: iOS
tags: iOS Swift SwiftUI Combine
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

# 9. Data Flow

### 뭘 배울까요?

---

- **Property Wrappers** : @State @Published @ObservedObject
- **Publishers**
- @Binding

# [Property Wrappers](https://github.com/apple/swift-evolution/blob/master/proposals/0258-property-wrappers.md)

- `@Something` 은 모두 Property Wrapper!
-  일반적으로 wrappedValue 와 projectedValue를 가지고 있는 `struct` 임. 더 많은 property를 가질 수 도 있고, projectedValue가 없을 수도 있음!
- 이미 "**정해져 있는 동작"** 들을 래핑하는 변수에 적용하여 캡슐화 함. 

### 예를 들면...

`@State` :: View 내부에서 수정할 수 있도록 heap에 사는 변수 만들기

`@Published` :: 변수의 변경사항을 공표(publish)하기

`@ObservedObject` :: publish된 변경사항이 감지되면 `View` 를 다시 그리도록 하기

## [Syntatic Sugar](https://en.wikipedia.org/wiki/Syntactic_sugar)

- @Published 는 사실 요렇게 생겼습니다.

```swift
struct Published<Value> {

  init(initialValue: Value)

  var wrappedValue: Value
  var projectedValue: Publisher<Value, Never>   // 우리가 $ 사용해서 연결할 때 여기 접근함

}
```

- 이렇게 선언하면

```swift
@Published var emojiArt: EmojiArt = EmojiArt()
```

- 사실 요렇게 쓰이는 거죠

```swift
var _emojiArt: Published<EmojiArt> = Published(wrappedValue: EmojiArt())
var emojiArt: EmojiArt {
  get { _emojiArt.wrappedValue }
  set { _emojiArt.wrappedValue = newValue }
}
```

### 왜 이런걸 만들어 둔 거야?

- `SwiftUI` 를 사용하면서 필요한 주요 동작들이 있는데, 이를 템플릿화하여 사용자들이 잘~ 편하게~ 가져다 쓸 수 있도록 하기 위해서
- 각 Property Wrapper들은, 미리 "정해져있는" 동작을 하도록 선언되어 있습니다.

## `@Published`

`wrappedValue` 가 `set` 되는 시점에 `Publish` ( `projectedValue` )를 통해 변경사항을 전달한다. 이 변경사항은 `$emojiArt` 로 연결되어 있는 곳으로 전파됩니다. 그리고 이는 `ObservableObject` 에서 `objectWillChange.send()` 를 호출합니다.

## `@State`

`wrappedValue` 가 값타입이건 참조타입이건(보통 스유에서는 값타입) heap에 저장합니다. 변경사항이 생기면, 연결된 `View` 를 다시 그립니다.

```swift
struct State<Value>: DynamicProperty {

  init(initialValue: Value)

  var wrappedValue: Value
  var projectedValue: Binding<Value>   

}
```

- **DynamicProperty** :: View의 외부 속성을 업데이트하는 stored variable에 대한 interface
- [.init(wrappedValue:) vs .init(initialValue:)](https://forums.swift.org/t/swiftui-state-init-wrappedvalue-vs-init-initialvalue-whats-the-difference/38904/19)

## `@ObservedObject`

`wrappedValue` 는 `ObservableObject` 를 채택한 타입이여야 합니다.

`wrappedValue` 가 `objectWillChange.send()` 를 호출했을 때 `View` 를 다시 그립니다.

```swift
struct ObservedObject<ObjectType>: DynamicProperty where ObjectType : ObservableObject{

  init(initialValue: Value)

  var wrappedValue: Value
  var projectedValue: ObservedObject<ObjectType>.Wrapper { get }

  public struct Wrapper {
    public subscript<Subject>(dynamicMember keyPath: ReferenceWritableKeyPath<ObjectType, Subject>) -> Binding<Subject> { get }
  }

}
```

## `@Binding`

`wrappedValue` :: 다른 무언가와 연결된 값 

다른 어떤 곳에서 `wrappedValue` 를 `get/set` 할 수 있습니다. 값이 변경되었을 때, `View` 를 다시 그립니다.

아주아주아주 많은 곳에서 쓸 수 있음.

**진실의** 단일 소스를 가지게 한다는 점에서, MVVM 패턴에서 매우 중요한 역할을 합니다.

- 데이터는 `ViewModel` 이 가지고 있지만, 이 데이터는 `View` 에서도 제어하기도 함. (둘 중 뭐가 진짜게?)
- `View` 에서 `stored property` 를 추가하는 것이 아닌, `ViewModel` 의 변수를 `@Biniding` 하여 사용 할 수 있다. 한 쪽에서 바뀌면, 둘 다 바뀌기 때문에 둘 다 진짜가 된당!

```swift
struct OtherView: View {
  @Binding var sharedText: String   // @State가 아닌 @Binding으로 선언
  var body: View {
    Text(sharedText)
  }
}
```

```swift
struct MyView: View {
	@State var myString = "Hello"
  var body: View {
    OtherView(sharedText: $myString)  // myString을 OtherView와 연결
  }
}
```

### 상수값을 Binding하기

```swift
OtherView(sharedText: .constant("Howdy"))
```

### Computed Binding

```swift
OtherView(sharedText: Binding(get:, set:)
```

## `@EnvironmentObject`

`@ObservedObject` 랑 유사한데, 넘겨주는 방식이 다릅니다. 

### ObservedObject

```swift
struct MyView: View {
	@ObservedObject var viewModel: ViewModelClass
  ...
}
```

```swift
let myView = MyView(viewModel: theViewModel)
```

### EnvironmentObject

```swift
struct MyView: View {
	@EnvironmentObject var viewModel: ViewModelClass
  ...
}
```

```swift
let myView = MyView().environmentObject(theViewModel)
```

가장 큰 차이점!

상위 뷰에서 `@EnvironmentObject` 를 선언하면, `body` 내부의 모든 뷰에서 (모달로 띄운 거 말고) 접근할 수 있다!!!!!!

`View` 하나에서 같은 `ObservableObject` 타입의 `@EnvironmentObject` 는 하나만 존재할 수 있다.

기본적으로 wrappedValue 와 동작방식은 ObservableObject 와 같다!

### Environment

EnvironmentObject 랑 연관없음!

View의 환경적인 요소값들에 대해 읽어오는 Property Wrapper.

`[EnvironmentValue](https://developer.apple.com/documentation/swiftui/environmentvalues)` 타입인 KeyPath를 받아서 해당 값에 접근한다. 

```swift
struct Environment<Value> : DynamicProperty {
  init(_ keyPath: KeyPath<EnvironmentValues, Value>)
  var wrappedValue: Value { get }
  // projected value는 없습니당.
}
```

```swift
// property
@Environment(\.lineLimit) var lineLimit

// 값을 변경하고 싶을 때는 이렇게
MyView()
  .environment(\M.lineLimit, 2)
// 혹은 이렇게 축약해
```

### Custom Environment

```swift
private struct MyEnvironmentKey: EnvironmentKey {
    static let defaultValue: String = "Default value"
}

extension EnvironmentValues {  // EnvironmentValues 에 커스텀 키 값 추가
    var myCustomValue: String {
        get { self[MyEnvironmentKey.self] }
        set { self[MyEnvironmentKey.self] = newValue }
    }
}

extension View {
    func myCustomValue(_ myCustomValue: String) -> some View {
        environment(\.myCustomValue, myCustomValue)
    }
}
```

# Publisher 맛보기

---

## Publisher?

일반적으로는 값을 내보내는데, 만약 실패한다면 실패 값을 내보내는 녀석. `Combine` 에 정의되어 있음.

```swift
public struct Publisher : Publisher {

        public typealias Output = Wrapped
        public typealias Failure = Never // Never Failure. Never ends.

        public let output: Optional<Wrapped>.Publisher.Output?
        public init(_ output: Optional<Wrapped>.Publisher.Output?)

        public func receive<S>(subscriber: S) where Wrapped == S.Input, S : Subscriber, S.Failure == Never
    }
```

비슷한 예를 찾자면, Rx의 Observable, Subject, Relay 와 유사함.

성공/실패/오류 에 대해 이전에는 오류를 던지는(`throw`) 하는 방식으로 구현되었다면, 이제는 그러한 실패/오류 까지도 하나의 타입으로 명시하여 처리하는 방식

## Listening Publisher

### Publisher.sink(receiveCompletion:, receiveValue:)

```swift
cancellable = myPublisher.sink(       // Sink returns that implements the Cancellabe protocol
  receiveCompletion: { result in ... }, 
  receiveValue: { thingThePubliserPublishes in ... }
)
```

- Cancellable :: publisher에 대한 listening을 중단할 수 있음!
    - 바꿔말하면 `cancel()` 을 호출하기 전까지 계속 연결되어 있을 것이라는점 🙂
    - release 될 때 stop 되면서 날라가버리므로 연결 라이프사이클과 함께 유지되는 instance property로 사용하는 것은 어떨까~?

### View.onReceive(publisher:, perform:)

```swift
View
 .onReceive(publisher) { thingThePublisherPublishes in
  // do something!
  // At this time your View will be invalidated automaticallly.
}
```

### 어디서 사용되나요?

- 오 아까 위에서 봣쥬? @Publisher 의 `projectedValue` 의 타입이 `Publisher` 입니당.
- `URLSession.dataTaskPublisher`
- `Timer.publish(every:)`
- `NotificationCenter.publisher(for:)`
- 등등등~!

### URLSession.dataTaskPublisher

```swift
class ViewModel {
	@Published var backgroundImage: UIImage?
	
	private var fetchImageCancellable: AnyCancellable?

  private func fetchImage(url: URL) {
    stopLoadImage()
		let session = URLSession.shared
		let publisher = session.dataTaskPublisher(for: url)
		               .map { data, urlResponse in UIImage(data: data) }  // 이미지로 변환
		               .receive(on: DispatchQueue.main) // main queue에서 받기
		               .replaceError(with: nil)         // Error가 떨어질 경우 nil을 넘겨줌.
		// let canceller = publisher.assign(to: `\ViewModel.backgroundImage, on: self)
		fetchImageCancellable = publisher.assign(to: `\.backgroundImage, on: self) // 어디에 있는지 명시해줬기 때문에 생략가능
	}

  private func stopLoadImage() {
     fetchImageCancellable?.cancel() 
  }

}
```

```swift
class ViewModel {
	@Published var backgroundImage: UIImage?
	
	private var fetchImageCancellable: AnyCancellable?

  private func fetchImage(url: URL) {
    stopLoadImage()
		fetchImageCancellable = URLSession.share.dataTaskPublisher(for: url)
		               .map { data, urlResponse in UIImage(data: data) }  // 이미지로 변환
		               .receive(on: DispatchQueue.main) // main queue에서 받기
		               .replaceError(with: nil)
                   .assign(to: `\.backgroundImage, on: self) // 어디에 있는지 명시해줬기 때문에 생략가능
	}

  private func stopLoadImage() {
     fetchImageCancellable?.cancel() 
  }

}
```

Chaining 가능!


### 참조
 * [원본영상](https://www.youtube.com/watch?v=mz-rNLWJ0bk&list=PLpGHT1n4-mAtTj9oywMWoBx0dCGd51_yG&index=9)

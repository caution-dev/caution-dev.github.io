---
layout: post
title:  "AutoClosure"
date:   2021-06-27 16:05:06 +0900
categories: Swift
tags: Swift Closure 번역
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.

우리가 무심코 사용했던 `true && false` 에서 사용되는 `&&`에 대해 알아보려고 합니다.
이 함수가 어떻게 생겼는지 아시나요? 요렇게 생겼숩니다.

```swift
/// - Parameters:
///   - lhs: The left-hand side of the operation.
///   - rhs: The right-hand side of the operation.
public static func && (lhs: Bool, rhs: @autoclosure () throws -> Bool) rethrows -> Bool
```

여기서 `autoclosure`라는 키워드가 나오는데 뭔지 아시나요? 저는 몰라서 >< 찾아보기로 하였습니다.

### AutoClosure
`autoclosure`는 함수에 인수로 전달되는 표현식을 자동적으로 감싸주는 `closure`를 말합니다. 이것은 어떤 인수도 필요하지 않고, 이 `closure` 불렸을 때, 그것은 그 안에 있는 표현식의 값을 반환합니다. 이러한 구문상의 편의성은 함수의 파라미터로 명시적 `closure`를 사용하는 대신 주변의 중괄호를 생략하면서 일반적인 표현식을 작성할 수 있도록 합니다.

`autoclosure`를 흔히 호출하지만 이러한 기능을 구현하는 것은 일반적이지 않습니다. 예를 들어, `assert(condition:message:file:line:)` 함수는 `condition` 및 `message` 매개 변수 `autoclosure`로 받습니다. `condition` 인수는 디버그 빌드에서만 수행되고 조건이 거짓인 경우에만 메시지 `message` 인수가 수행됩니다.

`autoclosure`를 사용하면 `closure`를 호출할 때까지 내부 코드가 실행되지 않도록 지연시킬 수 있습니다. 이러한 `Delaying evaluation`은 코드 수행 시기를 제어할 수 있기 때문에 `side-effect`가 있거나 계산 비용이 많이 드는 코드에 유용합니다. 아래 코드는 `closure`가 수행을 지연시키는 방식을 보여줍니다.

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"

let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"

print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"
```

`customersInLine`의 첫 번째 요소는 `closure` 내부의 코드에 의해 제거되며, 배열의 요소는 `closure`가 실제로 호출될 때까지 제거되지 않습니다. `closure`를 호출하지 않으면 `closure` 내부의 식이 평가되지 않으며, 이는 배열 요소가 제거되지 않음을 의미합니다. `customerProvider`의 type은 `String`이 아니라 `() -> String`으로, 문자열을 반환하는 매개 변수가 없는 함수입니다.

함수에 매개 변수로 `closure`를 전달할 때에도 동일한 방식으로 `closure` 수행을 지연시킬 수 있습니다.

```swift
// customersInLine is ["Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: { customersInLine.remove(at: 0) } )
// Prints "Now serving Alex!"
```

위의 `serve(고객:)` 함수는 고객의 이름을 반환하는 명시적 `closure`를 인수로 받습니다. 아래 버전의 `serve(고객:)` 동일한 작업을 수행하지만 해당 매개 변수의 유형을 명시적 `closure`가 아닌 `@autoclosure` 특성으로 표기하여 `autoclosure`를 받습니다. 이제 매개 변수로 `closure` 대신 문자열을 사용한 것처럼 함수를 호출할 수 있습니다. `customerProvider` 매개 변수의 유형이 `@autoclosure` 특성을 가지므로 인수가 자동으로 `closure`로 변환됩니다.

```swift
// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```

scope 탈출 허용되는 `autoclosure`를 원하는 경우 `@autoclosure` 및 `@escaping` 특성을 모두 사용하십시오. `@escaping` 특성은 위의 [Escaping Closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html#ID546)를 참고하세요.

```swift
// customersInLine is ["Barry", "Daniella"]
var customerProviders: [() -> String] = []
func collectCustomerProviders(_ customerProvider: @autoclosure @escaping () -> String) {
    customerProviders.append(customerProvider)
}
collectCustomerProviders(customersInLine.remove(at: 0))
collectCustomerProviders(customersInLine.remove(at: 0))

print("Collected \(customerProviders.count) closures.")
// Prints "Collected 2 closures."
for customerProvider in customerProviders {
    print("Now serving \(customerProvider())!")
}
// Prints "Now serving Barry!"
// Prints "Now serving Daniella!"
```

`closure` `autoclosure`

위의 코드에서 `collectCustomerProviders(_:)` 기능은 전달된 `customerProvider` 인수 `closure`를 호출하는 대신 `collectCustomerProviders`에 `closure`를 추가합니다. 배열은 함수의 범위 밖에 선언됩니다. 즉, 함수가 반환된 후 배열의 `closure`를 실행할 수 있습니다. 따라서 `customerProvider` 매개 변수의 값이 함수의 범위를 벗어날 수 있도록 허용되어야 합니다.


### 참조
 * [Closures](https://docs.swift.org/swift-book/LanguageGuide/Closures.html)

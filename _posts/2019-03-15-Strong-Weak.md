---
layout: post
title:  "Strong Weak 참조"
date:   2019-03-15 23:26:06 +0900
categories: Swift
tags: iOS 면접준비 strong weak Swift
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.
혹시 값 타입과 참조 타입의 차이를 아시나요? 아니면 swift의 class와 struct의 차이를 아시나요?(모르겠다면 [클릭]())
오늘 우리가 말하려고 하는 건 참조 타입 객체를 사용할 때 강한 참조, 약한 참조 중 어떤 걸 써야하는지에 대한 이야기입니다.

먼저 예를 들어보겠습니다. 부모(Parent)와 자식(Child) 클래스가 있다고 생각해보겠습니다.
``` swift
class Parent {
  let name: String
  init(name: String) { self.name = name }
  var child: Child?
}

class Child {
  let name: String
  var parent: Parent?
  init(name: String, parent: Parent) {
    self.name = name
    self.parent = parent
  }
}
```
부모는 자식을 가지고, 자식은 부모를 가질 수 있죠. 하지만 안타깝게도 부모와 자식 모두 서로를 상실(?)할 수 있기 때문에 optional type으로 선언했습니다.
그럼 이제 부모와 자식을 만들어고 연결해줍시다.
``` swift
var parent: Parent? = Parent(name: "가상부모")
var child: Child? = Child(name: "가상아이", parent: parent)
parent.child = child
```
 자 이 작업을 자세히 살펴봅시다. 첫 번째 줄에서 Parent 인스턴스를 만들었고, parent가 이 인스턴스를 **참조**하게 만들었습니다. 이때 Parent 인스턴스 대한 Reference Count 가 1로 올라갑니다. 두 번째 줄에서 Child 객체를 만들었고 child가 **참조**하고 있으니 마찬가지로 Child 객체의 Reference count는 1로 올라갑니다.

 하지만 주의해야할 것은 Child 인스턴스를 초기화하면서 parent 인스턴스 프로버티가 Parent 인스턴스를 참조하도록 설정해주었다는 것입니다. 이 작업을 통해서 Parent 인스턴스를 Reference Count는 2로 올라갑니다.
그리고 세 번째 줄에서 다시 한 번 Parent 인스턴스의 child 프로퍼티에 방금 생성한 Child 인스턴스를 참조하도록 합니다. 이를 통해 Child의 Reference count 또한 2로 올라갑니다.

우리가 참조할 때에 weak나 unowned와 같은 참조타입을 명시해주지 않았기 때문에 이 두 인스턴스는 기본적으로 서로를 strong 하게 참조하고 있습니다.

이 상태에서, 우리가 선언했던 parent 와 child를 모두 nil로 바꾸면 어떻게 될까요?
``` swift
parent = nil
child = nil
```
일단 변수 parent, child 에서 인스턴스에 대한 참조가 해제되었습니다. 그래서 Parent와 Child는 사실상 논리적으로는 아무도 참조하고 있지 않기 때문에 **메모리 해제**가 이루어져야하지만, Reference count가 2에서 1로 떨어졌기 때문에 여전히 1이 남아있어 ARC에 의한 해제가 발생하지 않습니다.
두 인스턴스가 서로를 **강하게 참조**하고 있기 때문이죠. 둘 중 어떤 것을 해제하려고 하더라도 다른 것에서 참조하고 있다고 인식되게 됩니다.

이 문제를 해결하려면 어떻게 해야할까요?

### 강한 참조, 약한 참조, 미소유 참조
참조 타입을 참조하는 방식에는 3가지가 있습니다. **strong, weak, unowned**
기본적으로 프로퍼티를 선언할 때 weak 나 unowned를 선언하지 않으면 기본 값은 strong입니다.
weak와 unowned는 약한 참조로 참조가 일어나더라도 Reference Count를 증가시키지 않습니다.

앞선 class들을 다시 변경해봅시다.
``` swift
class Parent {
  let name: String
  var child: Child?
  init(name: String) { self.name = name }
}

class Child {
  let name: String
  weak var parent: Parent?
  init(name: String, parent: Parent) {
    self.name = name
    self.parent = parent
  }
}
```
Child의 parent 프로퍼티를 weak 로 선언해주었습니다. 이렇게 weak 참조로 변하게 되면, 참조하고 있는 인스턴스가 메모리 해제를 시도하려고 할 때(nil 로 변환될 때), 이 값은 자동으로 **nil**로 변하게 됩니다. 즉 **메모리 해제를 방해하지 않습니다**.
그렇기 때문에 약한 참조방식은 상수(let)이 아닌 변수(var)에서만 가능하며, nil로 변할 수 있기에 optional 타입만 허용됩니다.

#### 그렇다면 왜 모든 변수를 weak로 선언하지 않나요?
class는 참조 타입입니다. 여러 변수가 하나의 인스턴스를 참조하고 있을 수 있습니다. 만약 모든 변수가 weak로 설정되어 있다면, 어느 한 변수에서 nil로 변환되거나 ARC에 의해서 메모리 해제가 발생했을 때, 의도치 않게 다른 참조 또한 nil로 변할 수 있습니다.

모든 참조를 약한 참조로 변경했습니다.
```swift
class Parent {
    let name: String
    weak var child: Child?
    init(name: String) { self.name = name }
}

class Child {
    let name: String
    weak var parent: Parent?
    init(name: String, parent: Parent?) {
        self.name = name
        self.parent = parent
    }
}
```

코드를 조금 변경해보죠. Child를 변수에 참조시키지 않고 바로 Parent의 프로퍼티에 담아보겠습니다.
```swift
let parent: Parent? = Parent(name: "가상부모")
parent?.child = Child(name: "가상아이", parent: parent)
print(parent?.child?.name)
```
결과는 어떨까요? Child 인스턴스가 잘 살아있다면 ```가상아이```가 나타나야 하겠지만 결과는 ```nil```이 나옵니다.
왜 그럴까요? Xcode에서 이 코드를 작성하게 되면 다음과 같은 오류를 발생시킵니다.
> Instance will be immediately deallocated because property 'child' is 'weak'

약한 참조는 Reference count 를 증가시키지 않습니다. 그렇기 때문에 Child 인스턴스가 만들어졌지만 RC가 0이기 때문에 언제든지 ARC에 의해서 dealloc될 수 있습니다.

위의 문제를 해결하려면 어떻게 해야할까요?
간단합니다. Parent의 child property를 강한 참조로 변경해주면 문제가 해결됩니다.
**강한 참조가 약한 참조보다 나쁘다는 것이 아니라, 순환참조를 해결하기 위해 약한 참조를 사용**합니다.

#### 그럼 unowned는 뭔가요?
마찬가지로 RC를 증가시키지 않는 참조법이지만, Optional 타입이 아닐 때 사용할 수 있습니다.
주의해야할 점은 메모리 해제가 이루어진 뒤 미소유 참조에 그대로 접근하게 되면 런타임 에러가 발생할 수 있습니다.

 따라서 unowned를 사용할 때에는 이 값이 nil인지 아닌지를 체크해서 사용해야 합니다.

###자동 참조 계수(Automatic Reference Counting)
Swift는 앱의 메모리 사용을 추적하고 관리하는 자동 참조 계수(ARC)를 사용. 대부분의 경우에 메모리 작업은 잘 작동하며, 메모리 관리를 생각할 필요 없다. ARC는 인스턴스가 더이상 필요가 없을 때 클래스 인스턴스에 사용된 메모리를 자동적으로 해제한다.

몇가지 경우에 ARC는 메모리 순서에서 코드 부분들의 사이 관계에 대한 더 많은 정보가 필요하다.

> 참조 계수는 클래스의 인스턴스에만 적용되며, 구조체와 열거형은 값 타입이지 참조 타입이 아니며 참조를 저장 못하고 넘기지 못한다.

#### ARC 작업 방법(How ARC Works)
매시간 클래스의 새로운 인스턴스를 만들며, ARC는 인스턴스에 대한 정보를 메모리 덩어리에 저장하기 위해 할당한다. 메모리는 인스턴스의 타입 정보와 저장 속성에 할당된 인스턴스 값을 쥔다.

게다가 인스턴스가 더이상 필요가 없으면 ARC는 인스턴스에 사용된 메모리를 해제하고 메모리는 다른 목적을 위해 사용되어진다. 더 이상 필요가 없을 때 메모리에는 클래스 인스턴스가 공간을 차지하지 않는다는 확신한다.

그러나 ARC는 사용중에 인스턴스를 할당 해제하면 인스턴스의 속성 접근이나 인스턴스 메소드 호출이 더이상 가능하지 않다. 대신에 인스턴스를 접근하려고 하면, 앱은 크래쉬가 날 수 있다.

인스턴스가 필요한 동안에는 사라지지 않게 하기 위해선, ARC는 많은 속성, 상수 그리고 변수가 현재 각 클래스 인스턴스를 참조하기 위해 추적한다. ARC는 적어도 하나의 활성화 참조가 있는 이상 인스턴스는 할당 해제되지 않고 계속 존재한다.

속성, 상수 또는 변수에 클래스 인스턴스를 할당할 때, 속성, 상수 또는 변수는 인스턴스에 강한 참조를 만든다. “강한” 참조는 인스턴스르 강하게 유지하며, 강한 참조가 남아있다면 해당 인스턴스를 할당 해제하지 못한다.
### 참조
 * [ARC Summary](http://minsone.github.io/mac/ios/swift-automatic-reference-counting-summary)

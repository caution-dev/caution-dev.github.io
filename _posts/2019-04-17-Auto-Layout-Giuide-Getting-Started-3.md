---
layout: post
title:  "Auto Layout Guide 따라잡기 - Anatomy of a Constraint"
date:   2019-04-17 15:26:06 +0900
categories: Tutorial
tags: iOS autolayout 오토레이아웃 강의 튜토리얼 따라하기
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다. 오늘은 이전 포스팅에 이어서 [Anatomy of a Constraint](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AnatomyofaConstraint.html#//apple_ref/doc/uid/TP40010853-CH9-SW1)을 번역합니다.

#### Auto Layout Guide 번역 시리즈
* Getting started
  * [Understanding Auto Layout](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-1.html)
  * [Auto Layout Without Constraints](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-2.html)
  * [Anatomy of a Constraint](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-3.html)

> 하기 컨텐츠는 Apple이 작성한 [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AnatomyofaConstraint.html#//apple_ref/doc/uid/TP40010853-CH9-SW1)를 번역, 재사용한 컨텐츠임을 밝힙니다.


### Auto Layout의 원리
뷰 계층 구조의 레이아웃은 일련의 선형 방정식으로 정의됩니다. 각 제약 조건은 단일 방정식을 나타냅니다. 당신의 목표는 원하는 레이아웃을 표현할 수 있는 일련의 방정식을 선언하는 것입니다. 샘플 방정식은 다음과 같습니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/view_formula_2x.png)

이 제약 조건은 빨간색 뷰의 leading 엣지가 파란색 뷰의 trailing 엣지 이후 8.0 포인트 여야 함을 나타냅니다. 방정식에는 다음과 같은 여러 부분이 있습니다.

* **Item 1.** 방정식의 첫 번째 아이템으로, 이 방적식에서는 빨간 뷰입니다. 이 아이템은 뷰나 레이아웃 가이드여야 합니다.
* **Attribute 1.** 이 요소는 첫 번째 항목 내부의 특성이어야 합니다. 이 방정식에서는 빨간 뷰의 leading 엣지를 가리킵니다.
* **Relationship.** 왼쪽과 오른쪽의 관계를 나타냅니다. 이 관계는 같거나, 크거나 같거나, 작거나 같은 관계 중 하나를 선택할 수 있습니다. 이 방정식에서는 왼쪽과 오른쪽이 같습니다.
* **Multiplier.** 두 번째 특성에 곱할 실수입니다. 이 방정식에서 Multiplier는 1.0입니다.
* **Item 2.** 방정식의 두 번째 아이템으로, 이 방정식에서는 파란 뷰입니다. 첫 번째 아이템과는 달리 이 값은 필수 요소가 아니며 비워둘 수 있습니다.
* **Attribute 2.** 두 번째 아이템에 속한 특성으로, 이 방정식에서는 파란 뷰의 trailing 엣지입니다. 만약 두 번째 아이템이 비어있다면, 이 값은 특성이 아니어야 합니다.
* **Constant.** 상수 값으로, 이 방정식에서는 8.0입니다. 이 값은 Attribute 2에 더해집니다.

대부분의 제약 조건은 사용자 인터페이스에서 두 항목 간의 관계를 정의합니다. 이러한 항목은 뷰 또는 레이아웃 가이드를 나타낼 수 있습니다. 제약 조건은 항목의 높이와 너비 간의 비율 설정과 같이 단일 항목의 두 가지 속성 간 관계를 정의 할 수도 있습니다. 항목의 높이 또는 너비에 상수 값을 지정할 수도 있습니다. 상수 값을 사용하여 작업 할 때 두 번째 아이템을 공백으로 두고 두 번째 속성은 Not An Attribute으로 설정되고 승수는 0.0으로 설정됩니다.

### Auto Layout 속성들
Auto Layout에서, 속성들은 제약될 수 있는 특성들을 정의합니다. 일반적으로, 4가지 모서리와 (leading, trailing, top, bottom) 높이, 너비, 그리고 수직 수평 정들을 포함합니다. 텍스트 아이템들은 하나 이상의 baseline 속성들을 가집니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/attributes_2x.png)

속성 리스트 전체를 확인하려면 [NSLayoutAttribute](https://developer.apple.com/documentation/uikit/nslayoutattribute) enum 을 확인하세요.
> Note
OS X과 iOS 둘 다 NSLayoutAttribute를 사용하긴 하지만 그 값들이 조금 다르게 정의되어 있습니다. 전체 속성들을 보려면 정확한 플랫폼의 문서를 보고 있는지 확인하세요.

### 예시 방정식들
이 방정식에 다양한 매개 변수와 속성을 사용하면 여러 가지 유형의 제약 조건을 만들 수 있습니다. 뷰 사이의 여백을 정의하거나 뷰 가장자리를 정렬하거나 두 뷰의 상대적인 크기를 정의하거나 뷰의 비율을 정의 할 수도 있습니다. 그러나 모든 속성이 호환되는 것은 아닙니다.

기본 유형의 속성은 두 가지가 있습니다. 크기 특성 (예 : height 및 width) 및 위치 특성 (예 : leading, left 및 top). 크기 속성은 항목의 위치를 가리키지 않고 항목의 크기를 지정하는 데 사용됩니다. 위치 속성은 다른 항목과 관련된 항목의 위치를 ​​지정하는 데 사용됩니다. 그러나 항목의 크기는 표시하지 않습니다.

이러한 차이점을 염두에 두고, 다음 규칙을 적용하세요.

* 위치 속성에 크기 속성의 제약을 줄 수 없습니다.
* 위치 속성에 상수값을 줄 수 없습니다.
* 위치 속성에는 1.0을 제외한 다른 값을 Multiplier 값으로 줄 수 없습니다.
* 지역 속성에서, 수직 속성에 대해 수평 속성에 대한 제약을 줄 수 없습니다.
* 지역 속성에서, Leading, Trailing 속성과 Left, Right 속성을 제약할 수 없습니다.
( Leading - Trailing을 함께 사용하고, Left - Right를 함께 사용해야 합니다.)

다음은 다양한 방정식들을 나타냅니다.
```swift
// 높이 제약조건 설정
View.height = 0.0 * NotAnAttribute + 40.0

// 두 버튼 사이에 상수 거리 설정
Button_2.leading = 1.0 * Button_1.trailing + 8.0

// 두 버튼의 leading 정렬
Button_1.leading = 1.0 * Button_2.leading + 0.0

// 두 버튼의 너비를 동일하게 설정
Button_1.width = 1.0 * Button_2.width + 0.0

// 뷰를 상위 뷰의 중앙에 위치
View.centerX = 1.0 * Superview.centerX + 0.0
View.centerY = 1.0 * Superview.centerY + 0.0

// 종횡비율 설정
View.height = 2.0 * View.width + 0.0
```

> Note
이 챕터의 모든 방정식들은 의사코드를 사용했습니다. 실제 코드로 작성된 제약조건을 보고자 한다면 [Programming Creating Constraints](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ProgrammaticallyCreatingConstraints.html#//apple_ref/doc/uid/TP40010853-CH16-SW1) 나 [Auto Layout Cookbook](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/LayoutUsingStackViews.html#//apple_ref/doc/uid/TP40010853-CH3-SW1)을 활용하세요.

### 대입이 아니라 같음
Note에 표기된 방정식들은 대입이 아니라 서로 같음을 나타내고 있다는 것이 중요합니다.

Auto Layout이 이러한 방정식을 풀어나갈 때 단순히 오른쪽의 값을 왼쪽에 대입하는 것이 아닙니다. 대신에 **이러한 관계를 만족할 수 있는 속성 1과 속성 2의 값을 계산합니다.** 이는 우리가 방정식을 재정렬할 필요가 없다는 것을 의미합니다. 예를 들어, 다음 방정식들은 위의 방정식들과 동일합니다.

```swift
// 두 버튼 사이에 상수 거리 설정
Button_1.trailing = 1.0 * Button_2.leading - 8.0

// 두 버튼의 leading 정렬
Button_2.leading = 1.0 * Button_1.leading + 0.0

// 두 버튼의 너비를 동일하게 설정
Button_2.width = 1.0 * Button.width + 0.0

// 뷰를 상위 뷰의 중앙에 위치
Superview.centerX = 1.0 * View.centerX + 0.0
Superview.centerY = 1.0 * View.centerY + 0.0

// 종횡비율 설정
View.width = 0.5 * View.height + 0.0
```

> Note
항목들을 재정렬할 때 승수(Multiplier)와 상수를 역으로 배치했는지 확인해야 합니다. 예를 들어, 상수 8.0은 -8.0이 되고, 승수 2.0은 0.5가 되어야 합니다. 상수 0.0과 승수 1.0은 바꿀 필요가 없습니다.

### 애매모호하지 않고, 만족스러운 레이아웃 만들기

Auto Layout을 사용할 때 목표는 만족하는 해결책이 단 하나만 존재하는 일련의 방정식을 제공하는 것입니다. 모호한 제한 조건에는 가능한 한 가지 이상의 솔루션이 있습니다. 불만족스러운 제약 조건은 유효한 해결책이 없습니다.

일반적으로 제약조건들은 각 뷰마다 크기와 위치를 모두 정의해야합니다. 상위뷰의 크기가 이미 정해져있다고 가정한다면,(예를 들어 iOS에서 화면의 root view), 애매모호하지 않고 만족스러운 레이아웃을 적용하려면 뷰 마다, 방면(수직,수평)마다 적어도 은 두 제약조건이 필요합니다.(상위 뷰는 세지 않았을 때) 하지만 어떤 제약조건을 사용할 것인지 선택할 수 있는 광범위한 옵션들이 있습니다. 예를 들어, 다음 세 가지 레이아웃 모두 모호하지 않고 만족스러운 레이아웃을 생성합니다.(수평 제약만 표시됨)

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/constraint_examples_2x.png)

* 첫 번째 레이아웃은 뷰의 leading 과 상위 뷰의 leading을 제약합니다. 또한 view에 고정된 너비를 제공합니다. 뷰의 trailing 위치은 상위 뷰의 크기와 다른 제약조건들에 기반해서 계산할 수 있습니다.
* 두 번째 레이아웃은 뷰의 leading 과 상위 뷰의 leading을 제약합니다. 또한 뷰의 trailing을 상위 뷰의 trailing과 관련있게 하였습니다. 뷰의 너비는 상위 뷰의 크기와 다른 제약조건들에 기반하여 계산할 수 있습니다.
* 세 번째 레이아웃은 뷰의 leading 과 상위 뷰의 leading을 제약합니다. 또한 뷰와 상위 뷰를 가운데 정렬합니다. 뷰의 너비와 trailing의 위치는 상위 뷰의 사이즈와 다른 제약조건들에 기반하여 계산할 수 있습니다.

각 레이아웃은 하나의 뷰와 두 개의 수평적인 제약조건이 있습니다. 이 경우에 제약조건들이 뷰의 너비와 수평적 위치를 완벽히 정의합니다. 이는 모든 레이아웃들이 수평방면에서 애매모호하지 않고 만족스러운 레이아웃을 만들어낸다는 것을 의미합니다. 하지만 이러한 레이아웃들이 완전히 유용하지만은 않습니다. 상위 뷰의 너비가 변경되었을 때를 고려해봅시다.

첫 번째 레이아웃에서 뷰의 너비는 변하지 않습니다. 대개의 경우 우리가 원하는 상황이 아니죠. 사실 일반적으로는 뷰에 상수 크기를 할당하는 것을 피해야합니다. Auto Layout은 환경에 적응할 수 있는 동적인 레이아웃을 만들기 위해 디자인되었습니다. 고정된 크기를 뷰에 줄 때마다 그 능력을 낭비하게 됩니다.

분명하지는 않지만, 두 번째와 세 번째 레이아웃은 동일한 동작을 만들어냅니다. 이들은 상위 뷰의 너비가 변하더라도 뷰와 상위 뷰 간에 고정된 여백을 유지합니다. 하지만 완전히 같지는 않습니다. 일반적으로, 두 번째 예제는 이해하기 쉽지만, 세 번째는 여러 항목을 가운데 정렬할 때에 더 유용할 수 있습니다. 항상 그렇듯이 특정 레이아웃에 가장 적합한 방법을 선택하세요.

이제 좀 더 복잡한 레이아웃을 고려해봅시다. iPhone에 두 개의 뷰를 나란히 표시하려고 한다고 가정해봅시다. 당신은 이 두 뷰들이 모든 방면에서 좋은 여백을 가지고 항상 같은 너비를 가지고 있기를 원합니다. 또한 장치가 회전 할 때 크기가 올바르게 조정되어야합니다.

다음 그림은 세로 및 가로 방향의보기를 보여줍니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/Blocks_Portrait_2x.png)
![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/Blocks_Landscape_2x.png)

그럼 어떠한 제약조건을 주어야 할까요? 다음 그림은 올바른 해결책 중 하나를 보여줍니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/two_view_example_1_2x.png)

이 해결책은 다음과 같은 제약조건들을 사용합니다.
```swift
// 수직 제약조건
Red.top = 1.0 * Superview.top + 20.0
Superview.bottom = 1.0 * Red.bottom + 20.0
Blue.top = 1.0 * Superview.top + 20.0
Superview.bottom = 1.0 * Blue.bottom + 20.0

// 수평 제약조건
Red.leading = 1.0 * Superview.leading + 20.0
Blue.leading = 1.0 * Red.trailing + 8.0
Superview.trailing = 1.0 * Blue.trailing + 20.0
Red.width = 1.0 * Blue.width + 0.0
```

앞서 말했던 법칙에 따라, 이 레이아웃에는 두 개의 뷰가 있고, 각 뷰 마다 두 개의 수평 제약조건, 두 개의 수직 제약조건이 존재합니다. 이 제약조건들이 정확한 가이드가 아니더라도 , 올바른 방향에 있다는 것을 알 수 있습니다. 더욱 중요한 것은 제약 조건이 두 뷰의 크기와 위치를 고유하게 지정하여 모호하지 않고 만족스런 레이아웃을 생성한다는 것입니다. 이러한 제약 조건 중 하나라도 제거하면 레이아웃이 모호해집니다. 추가 제약 조건을 추가하면 충돌이 발생할 위험이 있습니다.

하지만 이 방법이 유일한 해결책은 아닙니다. 다음은 똑같이 유효한 접근 방식입니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/two_view_example_2_2x.png)

파란색 상자의 위쪽과 아래쪽을 상위 뷰에 고정하는 대신 파란색 상자의 위쪽을 빨간색 상자의 위쪽에 맞춥니다. 마찬가지로 파란색 상자의 아래쪽을 빨간색 상자의 아래쪽에 맞춥니다. 제약 조건은 다음과 같습니다.

```swift
// 수직 제약조건
Red.top = 1.0 * Superview.top + 20.0
Superview.bottom = 1.0 * Red.bottom + 20.0
Red.top = 1.0 * Blue.top + 0.0
Red.bottom = 1.0 * Blue.bottom + 0.0

// 수평 제약조건
Red.leading = 1.0 * Superview.leading + 20.0
Blue.leading = 1.0 * Red.trailing + 8.0
Superview.trailing = 1.0 * Blue.trailing + 20.0
Red.width = 1.0 * Blue.width + 0.0
```

위와 동일하게 두 개의 뷰와 네 개의 수평 제약조건, 네 개의 수직 제약조건을 가집니다. 여전히 애매모호하지 않고 만족스러운 레이아웃을 제공하고요.

> Note
어떤 게 더 나을까요?
이 해결책들은 모두 유효한 레이아웃을 제공합니다. 그럼 어떤 게 더 나을까요?
불행히도, 객관적으로 한 접근법이 다른 접근법보다 완전히 우수하다는 것을 증명하는 것은 사실상 불가능합니다. 각 접근방식에는 강약점이 존재합니다.
첫 번째 솔루션은 뷰를 삭제해야 할 때 더 권장됩니다. 뷰 계층에서 뷰를 삭제하면 해당 뷰를 참조하는 모든 제약조건들도 제거해야합니다. 그렇기 때문에 만약 빨간 뷰를 삭제하면 파란 뷰는 세 개의 제약조건만을 가진 채 그 자리에 남게 됩니다. 다시금 유효한 레이아웃을 만들기 위해서는 하나의 제약조건을 추가해주기만 하면 됩니다. 두 번째 솔루션에서는 빨간 뷰를 삭제하면 파란 뷰는 하나의 제약조건만을 가지게됩니다.
다른 방면에서 첫 번째 솔루션은 뷰의 상단과 하단을 정렬하고자 한다면 두 개의 제약조건이 모두 같은 상수 값을 가지도록 해야합니다. 만약 하나의 제약조건을 변경한다면, 다른 쪽도 변경해야한다는 걸 잊지 말아야겠죠.

### 부등식 제약조건
이제까지의 모든 예제들은 서로 같은(=) 제약조건들만을 살펴보았지만 이는 이야기의 한 부분에 불과합니다. 제약조건은 서로 같지 않음 또한 잘 나타낼 수 있습니다. 특히, 제약 조건의 관계는 같거나 크거나 같거나 작을 수 있습니다. 예를 들어 제약 조건을 사용하여 뷰의 최소 또는 최대 크기를 정의 할 수 있습니다 (코드 목록 3-3).

**코드 목록 3-3** 최대 크기와 최소 크기 할당
```swift
// 최소 너비 설정
View.width >= 0.0 * NotAnAttribute + 40.0

// 최대 너비 설정
View.width <= 0.0 * NotAnAttribute + 280.0
```
서로 같지 않은 제약조건을 사용하면 '뷰에는 방면별로 두 개의 제약 조건이 있어야한다'는 규칙이 무너집니다. 하나의 '서로 같은' 관계를 두 개의 부등식으로 대체 할 수 있습니다. 목록 3-4에서 단일 등호 관계와 두 개의 부등식은 동일한 동작을 생성합니다.

**코드 목록 3-4** 하나의 등식을 두 개의 부등식으로 변경
```swift
// 단일 등식 관계
Blue.leading = 1.0 * Red.trailing + 8.0

// 두 개의 부등식 관계로 대체할 수 있다.
Blue.leading >= 1.0 * Red.trailing + 8.0
Blue.leading <= 1.0 * Red.trailing + 8.0
```

하지만 두 개의 부등식 관계가 항상 단일 등식 관계와 같은 것은 아니므로, 두 개의 부등식을 단일 등식으로 전환하는 것은 그 결과가 다를 수 있습니다. 예를 들어, 코드 3-3 에서는 뷰의 가능한 너비의 범위를 정의했지 뷰의 크기를 정의한 것은 아닙니다. 뷰의 위치와 크기를 알기 위해서는 추가적인 수평 제약조건이 필요합니다.

### 제약조건 우선순위
기본적으로 모든 제약조건은 충족되어야 합니다. Auto Layout은 이 모든 제약조건들을 만족시키는 해결책을 계산해야만합니다. 만일 그럴 수 없다면 오류가 발생합니다. Auto Layout은 만족되지 않는 제약조건에 대한 정보를 콘솔에 출력하며, 깨트릴 제약조건을 선택합니다. 그리고 깨뜨린 제약조건을 제외하고 다시 해결책을 계산합니다. 더 많은 정보를 알고 싶다면 [Unsatisfiable Layout](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ConflictingLayouts.html#//apple_ref/doc/uid/TP40010853-CH19-SW1)을 확인하세요.

선택적 제약 조건을 만들 수도 있습니다. 모든 제약 조건은 1과 1000 사이의 우선 순위를 갖습니다. 우선 순위가 1000인 제약 조건은 항상 충족되어야 합니다. 다른 모든 제약 조건은 선택 사항입니다.

솔루션을 계산할 때 Auto Layout은 우선 순위가 높은 순서에서 가장 낮은 순서까지 모든 제약 조건을 충족 시키려고 시도합니다. 옵션 제약 조건을 만족할 수 없다면, 그 제약 조건은 건너 뛰고 다음 제약 조건으로 넘어갑니다.

선택적 제약 조건을 충족시킬 수 없더라도 여전히 레이아웃에 영향을 미칠 수 있습니다. 제약 조건을 건너 뛰고 나서 레이아웃에 애매한 점이 있으면 시스템은 제약 조건에 가장 가까운 솔루션을 선택합니다. 이런 식으로 만족스럽지 않은 선택적인 제약 조건은 뷰가 어떤 방향으로 나타날 지에 대해 영향을 주게됩니다.

선택적 제약 조건과 불평등은 종종 서로 작용합니다. 예를 들어, 코드 목록 3-4에서 두 가지 부등식에 대해 다른 우선 순위를 제공 할 수 있습니다. 보다 크거나 같음 관계 (1000의 우선 순위)가 충족되어야 하며, 보다 작거나 같은 관계가 낮은 우선 순위(우선 순위 250)를  가지도록 해봅시다. 즉, 파란색 뷰는 빨간색에서 8.0 포인트보다 가까울 수 없습니다. 그러나 다른 제약 조건으로 인해 더 멀리 떨어질 수 있습니다. 그래도 선택적 구속 조건은 레이아웃에서 다른 제약 조건이 주어지면 파란색 뷰를 빨간색 뷰로 끌어 당겨 8.0 지점 간격에 최대한 가깝게 만듭니다.

> Note
1000 개의 우선 순위 값 모두를 사용해야 할 필요는 없습니다. 사실, 일반적으로 우선 순위는 시스템이 정의해 둔 낮은 (250), 중간 (500), 높음 (750) 및 필수 (1000) 우선 순위를 중심으로 묶여야합니다.
동일 우선 순위를 가지는 것을 막기 위해서 이 값들보다 1~2 정도 높고 낮은 값을 사용하려 할 수 있습니다. 그 이상의 우선순위를 가지는 관계를 만들어야한다면 레이아웃을 재점검해보는 것이 필요할 수 있습니다.
iOS에 미리 정의 된 제약 상수의 목록을 보려면 [UILayoutPriority](https://developer.apple.com/documentation/uikit/uilayoutpriority) 열거 형을 참조하세요. OS X의 경우 레이아웃 우선 순위 상수를 참조하십시오.

### 고유 컨텐츠 크기(Intrinsic Content Size)
이제까지 모든 예제는 뷰의 위치와 크기를 정의하기 위해 제약 조건을 사용했습니다. 그러나 일부 뷰는 이들의 최근 컨텐츠에서 자연스럽게 그 크기를 가집니다. 이것을 고유 컨텐츠 크기라고합니다. 예를 들어 버튼의 고유 콘텐츠 크기는 제목의 크기와 작은 여백을 더한 크기입니다.

모든 뷰에 고유 콘텐츠 크기가 있는 것은 아닙니다. 뷰의 경우, 고유 컨텐츠 크기는 뷰의 높이, 폭 또는 둘 다를 정의 할 수 있습니다. 표 3-1에 몇 가지 예가 나와 있습니다.

**표 3-1** 일반적인 control에서 고유 컨텐츠 크기
|View|고유 컨텐츠 크기|
|:---:|:---:|
|UIView, NSView|고유 컨텐츠 크기 없음|
|Sliders - iOS | 너비만 가짐|
|Sliders - macOS | slider 타입에 따라 너비, 높이 혹은 둘 다를 정의|
|Labels, buttons, switches, text fields|너비와 높이를 모두 정의|
|Text views, image views|고유 컨텐츠 크기가 달라질 수 있음|

고유 컨텐츠 사이즈는 뷰의 현재 컨텐츠에 기반합니다. 레이블이나 버튼의 고유 컨텐츠 사이즈는 보여지는 텍스트의 양과 사용된 폰트에 기반합니다. 다른 뷰들은 고유 컨텐츠 사이즈가 좀 더 복잡합니다. 예를 들어, 이미지가 없는 이미지 뷰는 고유 컨텐츠 사이즈를 가지지 않습니다. 하지만 이미지를 추가하면 그 이미지의 사이즈가 이미지 뷰의 고유 컨텐츠 사이즈로 설정됩니다.

텍스트 뷰의 고유 컨텐츠 사이즈는 컨텐츠에 따라, 스크롤이 가능한지 아닌지에 따라, 이 뷰에 적용된 다른 제약 조건들이 있느냐에 따라 달라질 수 있습니다. 예를 들어, 스크롤이 가능하다면, 뷰는 고유 컨텐츠 사이즈를 가지지 않습니다. 스크롤이 불가능하다면 뷰의 고유 컨텐츠 사이즈는 기본적으로 줄 바꿈을 제외한 텍스트의 사이즈에 기반하여 계산됩니다. 예를 들어 만약 text에 줄바꿈이 없다면, 컨텐츠를 한 줄의 텍스트로 레이아웃하여 높이와 너비를 계산합니다. 만일 이 뷰에 특정 너비에 대한 제약조건을 추가한다면 주어진 너비에서 텍스트를 보여줄 수 있도록 고유 컨텐츠 사이즈의 높이를 정의합니다.

Auto Layout은 각 방면의 제약조건들의 쌍을 사용하여 뷰의 고유 컨텐츠 사이즈를 나타냅니다. content hugging은 뷰를 안쪽으로 잡아 당겨 콘텐츠 주위에 꼭 맞게 합니다. compression resistance은 내용을 올바르게 다 보일 수 있도록 뷰를 바깥쪽으로 밀어냅니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/intrinsic_content_size_2x.png)

이 제약 조건들은 목록 3-5에 있는 부등식을 통해 정의됩니다. 여기에서 IntrinsicHeight 및 IntrinsicWidth 상수는 뷰의 고유 콘텐츠 크기에서 높이 및 너비 값을 나타냅니다.

**코드 목록 3-5**
```swift
// Compression Resistance
View.height >= 0.0 * NotAnAttribute + IntrinsicHeight
View.width >= 0.0 * NotAnAttribute + IntrinsicWidth

// Content Hugging
View.height <= 0.0 * NotAnAttribute + IntrinsicHeight
View.width <= 0.0 * NotAnAttribute + IntrinsicWidth
```

이러한 제약조건들도 각자의 우선순위를 가질 수 있습니다. 기본적으로 뷰는 content hugging에 대해서는 250의 우선순위를, compression resistance에 대해서는 750의 우선순위를 가집니다. 그러므로 뷰를 늘리는 것이 줄이는 것보다 쉽습니다. 대부분의 control에서 원하는 동작이기도 합니다. 예를 들어 버튼을 고유 컨텐츠 크기보다 크게 늘릴 수 있지만, 고유 컨텐츠 크기보다 줄여버린다면 내부 컨텐츠가 잘리게 됩니다. Interface Builder에서 이러한 우선 순위를 변경할 수 있습니다. 더 많은 정보가 필요하다면 [Setting Content-Hugging and Compression-Resistance Priorities](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/WorkingwithConstraintsinInterfaceBuidler.html#//apple_ref/doc/uid/TP40010853-CH10-SW2)를 확인하세요.

가능하다면 레이아웃에서 뷰의 고유 컨텐츠 사이즈를 사용하세요. 이는 뷰의 내용이 변경됨에 따라 레이아웃이 동적으로 적응하도록 할 수 있습니다. 이는 또한 애매모호하지 않고 충돌되지 않는 레이아웃을 만들기 위해 필요한 제약조건의 수를 줄여주지만, 뷰의 content-hugging과 compression-resistance (CHCR) 우선순위를 관리할 필요가 있을 겁니다. 다음은 고유 컨텐츠 사이즈를 다루기 위한 가이드라인입니다.

* 공간을 채우기 위해서 일련의 뷰들이 자동적으로 늘려져야 할 때, 모든 뷰가 동일한 content-hugging 우선순위를 가진다면 레이아웃이 모호할 수 있습니다. Auto Layout은 어떤 뷰가 늘려져야 하는지 알 수 없습니다.
일반적인 예로 레이블과 텍스트 필드 쌍이 있습니다. 일반적으로는 레이블이 고유 사이즈 그대로 남아있고 텍스트 필드가 남은 공간을 채우기를 바랄겁니다. 이를 명확히 하기 위해서는 텍스트 필드의 수평 content-hugging 우선순위를 레이블의 값보다 낮추어야 합니다.
사실 이 예는 너무 일반적이어서 Interface Builder가 자동으로 처리하여 모든 레이블의 content-hugging 우선 순위를 251로 설정합니다. 프로그램적으로 레이아웃을 만들어야 한다면 content-hugging 우선순위를 직접 수정해야합니다.

* 이상하고 예상치 못한 레이아웃은 보이지 않는 배경(예 : 단추 또는 레이블)이 포함된 뷰가 고유 콘텐츠 크기를 초과하여 우연히 늘어날 때 자주 발생합니다.
Odd and unexpected layouts often occur when views with invisible backgrounds (like buttons or labels) are accidentally stretched beyond their intrinsic content size. The actual problem may not be obvious, because the text simply appears in the wrong location. To prevent unwanted stretching, increase the content-hugging priority.

Baseline constraints work only with views that are at their intrinsic content height. If a view is vertically stretched or compressed, the baseline constraints no longer align properly.

Some views, like switches, should always be displayed at their intrinsic content size. Increase their CHCR priorities as needed to prevent stretching or compressing.

Avoid giving views required CHCR priorities. It’s usually better for a view to be the wrong size than for it to accidentally create a conflict. If a view should always be its intrinsic content size, consider using a very high priority (999) instead. This approach generally keeps the view from being stretched or compressed but still provides an emergency pressure valve, just in case your view is displayed in an environment that is bigger or smaller than you expected.

### 원문
 * [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AnatomyofaConstraint.html#//apple_ref/doc/uid/TP40010853-CH9-SW1)

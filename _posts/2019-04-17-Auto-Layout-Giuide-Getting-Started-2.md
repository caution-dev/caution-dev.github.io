---
layout: post
title:  "Auto Layout Guide 따라잡기 - Auto Layout Without Constraints"
date:   2019-04-17 15:26:06 +0900
categories: Tutorial
tags: iOS autolayout 오토레이아웃 강의 튜토리얼 따라하기
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다. 오늘은 이전 포스팅에 이어서 [Auto Layout Without Constraints](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AutoLayoutWithoutConstraints.html#//apple_ref/doc/uid/TP40010853-CH8-SW1)을 번역합니다.

#### Auto Layout Guide 번역 시리즈
* Getting started
  * [Understanding Auto Layout](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-1.html)
  * [Auto Layout Without Constraints](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-2.html)
  * [Anatomy of a Constraint](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-3.html)

> 하기 컨텐츠는 Apple이 작성한 [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AutoLayoutWithoutConstraints.html#//apple_ref/doc/uid/TP40010853-CH8-SW1)를 번역, 재사용한 컨텐츠임을 밝힙니다.


### 제약 조건 없는 Auto Layout
스택 뷰는 복잡한 레이아웃을 적용하지 않고도 Auto Layout 기능을 쉽게 활용할 수있는 방법을 제공합니다. 단일 스택 뷰는 사용자 인터페이스 요소의 행 또는 열을 정의합니다. 스택보기는 이러한 요소들을 다음 특성에 따라 정렬합니다.
* axis: [UIStackView](https://developer.apple.com/documentation/uikit/uistackview)의 요소들을 수직 혹은 수평으로 쌓을 건지 그 축을 정의합니다.
* orientation: [NSStackView](https://developer.apple.com/documentation/appkit/nsstackview)의 요소들을 수직 혹은 수평으로 쌓을 건지 그 축을 정의합니다.
* distribution: 축에 따라 뷰들을 어떻게 레이아웃할 것인지 정의합니다.
* alignment: 스택뷰 축의 수직방향에 대한 레이아웃을 정의합니다.
* spacing: 뷰들 간의 공간을 정의합니다.

스택 뷰를 사용하려면, 인터페이스 빌더에서 세로 또는 가로 스택 뷰를 캔버스 위로 드래그하십시오. 그런 다음 스택 뷰에 넣을 요소를 끌어서 스택 뷰에 놓습니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/IB_StackView_Simple_2x.png)

스택 뷰는 배치 된 뷰의 content-hugging 및 compression-resistance 우선 순위에 대한 레이아웃을 기반으로합니다. 이 속성은 Size Inspector를 사용하여 수정할 수 있습니다.

> Note
정렬 된 뷰에 직접 제약 조건을 추가하여 레이아웃을 추가로 수정할 수 있습니다. 하지만 가능한 충돌을 피하려면, 일반적으로 뷰의 크기가 주어진 방향에서 intrinsic content (내장 컨텐츠 크기)로 설정된 상태에서 해당 방향에 대한 제약 조건을 추가해야 안전합니다. 충돌하는 제약 조건에 대한 자세한 내용은 [Unsatisfiable Layouts](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/ConflictingLayouts.html#//apple_ref/doc/uid/TP40010853-CH19-SW1)을 참조하십시오.
> 덧붙이는 말
수평 스택 뷰를 사용하고 있고, 하위에 넣을 뷰가 스택뷰 공간에서 수평으로 10만큼의 공간을 떨어져 있었으면 좋겠다라고 생각될 경우 이 뷰의 너비가 픽스된 상태에서 수평방향에 대한 제약 조건을 주어야 안전합니다. 뷰의 크기는 스택뷰의 속성과 뷰의 content-hugging, compression-resistance에 따라 달라질 수 있기 때문에 원하는 방향(수직 혹은 수평)에 따른 제약조건을 추가로 주고 싶다면 그 방향에 대한 뷰의 크기(높이 혹은 너비)를 지정해주는 것이 안전합니다.

또한 스택 뷰 내에 다른 스택 뷰를 중첩시켜 좀 더 복잡한 레이아웃을 만들 수 있습니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/IB_StackView_NestedStacks_2x.png)

일반적으로 가능한 한 많은 뷰를 스택뷰로 관리하십시오. 스택뷰만으로 원하는 목표를 달성 할 수 없는 경우에만 제약 조건을 생성하세요. 스택 뷰 사용에 대한 자세한 내용은 [UIStackView Class Reference](https://developer.apple.com/documentation/uikit/uistackview) 또는 [NSStackView Class Reference](https://developer.apple.com/documentation/appkit/nsstackview)를 참조하십시오.

> Note
중첩된 스택 뷰를 창의적으로 사용하면 복잡한 사용자 인터페이스가 생성 될 수 있지만 제약 조건을 완전히 피할 수는 없습니다. 최소한 가장 바깥 쪽 스택의 위치(및 크기)를 정의하기 위해 항상 제약 조건이 필요합니다.

### 원문
 * [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)

---
layout: post
title:  "Auto Layout Guide 따라잡기 - Understanding Auto Layout"
date:   2019-04-10 15:26:06 +0900
categories: Apple-Docs
tags: iOS autolayout 오토레이아웃 강의 튜토리얼 따라하기
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다. 본격적으로 Auto Layout 공부를 하려고 하는데 어떻게 시작할까 하다가 Apple의 가이드 문서인 [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)를 번역해보려고 합니다!

#### Auto Layout Guide 번역 시리즈
* Getting started
  * [Understanding Auto Layout](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-1.html)
  * [Auto Layout Without Constraints](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-2.html)
  * [Anatomy of a Constraint](https://caution-dev.github.io/tutorial/2019/04/10/Auto-Layout-Giuide-Getting-Started-3.html)


> 하기 컨텐츠는 Apple이 작성한 [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)를 번역, 재사용한 컨텐츠임을 밝힙니다.

### Auto Layout 이해하기
오토 레이아웃은 뷰 계층 안의 모든 뷰들의 사이즈와 위치를 해당 뷰에 적용된 제약 조건에 기반하여 동적으로 계산합니다. 예를 들어, 이미지 뷰와 가로로 중앙 정렬하고, 이미지의 하단 8 point 아래에 위쪽 가장자리가 존재하도록 버튼에 제약 조건을 줄 수 있습니다. 만약 이미지 뷰의 사이즈나 위치가 변경된다면, 버튼의 위치는 자동적으로 제약 조건에 일치하도록 조정됩니다.

이러한 제약 조건 기반 접근을 사용하면 사용자 인터페이스를 내 외부 변화에 맞추어서 동적으로 반응하도록 만들 수 있습니다.

#### 외부 변화
외부 변화는 상위뷰의 사이즈나 모양이 변경되었을 때 발생합니다. 변경이 일어날 때마다 사용가능한 공간을 최대한 활용하기 위해 뷰 계층을 업데이트 해야합니다. 외부 변화에 대한 예:
* OS X : 사용자가 창의 크기를 변경(resize)합니다.
* iOS : iPad에서 Split View를 사용하거나 해제합니다.
* iOS : 기기를 회전시킵니다.
* iOS : 전화 중 혹은 음석 녹음 바가 나타나거나 사라집니다.
* 다른 사이즈ㅡ의 클래스들을 지원하고자 할 때
* 다른 화면 사이즈들을 지원하고자 할 때

이러한 변화들은 대개 런타임에서 발생하고 앱에서 동적인 응답이 필요합니다. 다른 사이즈의 화면을 지원하는 것과 같은 기능은 앱이 다양한 환경에 적응할 수 있음을 나타냅니다. 화면 크기가 일반적으로 런타임에 변경되지는 않지만, 적응형 인터페이스를 만들면 iPhone 4S, iPhone 6 Plus 혹은 iPad에서도 앱을 동일하게 실행할 수 있습니다. Auto Layout은 iPad에서 Slide Over 및 Split View를 지원하기 위한 핵심 구성 요소이기도 합니다.다른 환경에 앱을 적응시키는 대표적인 요소입니다.

#### 내부 변화
내부 변화는 사용자 인터페이스에서 뷰 또는 컨트롤의 크기가 변경될 때 발생합니다. 다음은 이러한 내부 변경의 일반적인 예입니다.:
* 앱에서 표시하는 컨텐츠가 변경됩니다.
* 앱이 국제화를 지원합니다.
* 앱이 동적 유형 (iOS)를 지원합니다.

앱의 컨텐츠가 변경되었을 때, 새로운 콘텐츠는 기존과 다른 레이아웃을 요구할 수 있습니다. 이는 흔히 앱에서 텍스트나 이미지를 표시할 때 발생합니다. 예를 들어, 뉴스 앱은 개별 뉴스 아티클의 사이즈에 기반해서 레이아웃을 조정할 필요가 있습니다. 유사하게, 사진 콜라주는 다양한 이미지 사이즈와 비율을 다루어야만 합니다.

국제화란 앱을 다른 언어, 지역, 문화에 적용될 수 있도록 만드는 과정입니다. 국제화된 앱에서 레이아웃은 이러한 차이점을 고려하여 앱이 지원하는 모든 언어 및 지역에서 올바르게 표시되어야 합니다.

국제화는 레이아웃에 주로 3가지 영향을 줍니다. 첫번재로, 사용자 인터페이스를 다른 언어로 번역할 때 레이블은 언어마다 다른 공간이 필요합니다. 예를 들어 독일어는 일반적으로 영어보다 더 많은 공간이 필요하며, 일본어는 보통 더 적은 공간이 필요합니다. 두번째로, 언어가 변경되지 않더라도 날짜와 숫자를 나타내는 데 사용되는 형식이 지역마다 다를 수 있습니다. 이러한 변경은 일반적으로 언어 변경보다 미묘하긴 하지만 사용자 인터페이스는 약간씩 변형되어야 합니다.

셋째로, 언어를 변경하는 것은 텍스트 사이즈에만 영향을 주는 것이 아니라 레이아웃 설계에도 영향을 줍니다. 다른 언어들은 다른 언어 방향을 사용합니다. 예를 들어 영어는 왼쪽에서 오른쪽으로 가는 레이아웃 방향을 사용하며, 아랍어와 히브리어는 오른쪽에서 왼쪽으로 가는 레이아웃을 사용합니다. 일반적으로 사용자 인터페이스 요소들을 정렬하는 것은 레이아웃 방향과 일치해야합니다. 만약 버튼이 영어에서는 뷰의 오른쪽 하단 모서리에 있어야 한다면 아랍어에서는 왼쪽 하단에 있어야 합니다.

결론적으로, 만약 iOS 앱이 동적 타입을 지원한다면, 사용자는 앱에서 사용할 글씨 크기를 조절할 수 있습니다. 이는 사용자 인터페이스에서 글자 요소들의 높이나 너비를 변경할 수 있습니다. 만일 사용자가 앱이 실행되고 있을 때 글자 크기를 변경하더라도 폰트와 레이아웃이 모두 적용되어야 합니다.

### Auto Layout vs Frame 기반 Layout
사용자 인터페이스를 레이아웃하는 데에는 주요 세가지 접근법이 있습니다. 사용자 인터페이스를 프로그래밍으로 짤 수도 있고, 외부 변화에 대한 자동적인 응담을 할 수 있도록 autoresizing mask를 사용할 수 있으며, Auto Layout을 사용할 수도 있습니다.

전통적으로, 앱은 뷰 계층의 각 뷰에 대한 프레임을 프로그래밍 방식으로 설정하여 사용자 인터페이스를 레이아웃합니다. 프레임은 상위 뷰의 좌표계에서 뷰의 원점, 높이 및 너비를 정의합니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/layout_views_2x.png)

사용자 인터페이스를 레이아웃하기 위해서는 뷰 계층 안의 모든 뷰에 대한 사이즈와 위치를 계산해야 합니다. 그리고 나서, 변경이 일어나면, 영향 받는 모든 뷰에 대한 프레임을 다시 계산해야합니다.

다양한 방법으로, 프로그램방식으로 정의된 뷰의 프레임은 매우 유연하고 강력합니다. 변화가 발생했을 때 그야말로 원하는 대로 변경할 수 있습니다. 아직까지 모든 변화를 스스로 관리해야하기 때문에 간단한 사용자 인터페이스를 레이아웃 하는 것 조차도 디자인, 디버그, 유지하는데 많은 양의 노력이 필요합니다. 완전한 적응형 사용자 인터페이스를 만드는 것은 난이도를 증가시킵니다.

이러한 노력을 줄일 수 있도록 autoresizing mask를 사용할 수 있습니다. autoresizing mask는 상위 뷰의 프레임이 변경되었을 때 어떻게 뷰 프레임을 변경할 것인가에 대해 정의합니다. 이는 외부 변화에 적응하는 레이아웃을 만드는 것을 단순화합니다.

그러나 autoresizing masks는 가능한 레이아웃의 비교적 작은 하위 세트를 지원합니다. 예를 들어 사용자 인터페이스에서, 일반적으로 프로그래밍을 통해 autoresizing mask를 보완해야 합니다. 추가적으로 autoresizing mask는 외부 변화에 대해서만 적용됩니다. 내부 변화에 대해서는 지원하지 않습니다.

비록 autoresizing mask가 프로그램적인 레이아웃의 반복적인 개선사항일 뿐이지만, auto layout은 완전히 새로운 패러다임을 나타냅니다. 뷰 프레임을 생각하지 않는 대신에 뷰들의 관계에 대해 생각합니다.

Auto Layout은 제약조건들을 사용해서 사용자 인터페이스를 정의합니다. 제약조건은 일반적으로 두 개의 뷰 간의 관계를 나타냅니다. Auto Layout은 이러한 제약조건에 기반하여 사이즈와 위치를 계산합니다. 이는 내 외부 변화에 대해 동적인 응답을 하는 레이아웃을 만들어냅니다.

![image](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/Art/layout_constraints_2x.png)


특정 동작을 하는 제약조건들을 설계하는 데 사용되는 논리는 절차적 혹은 객체 지향 코드를 작성하는 것과는 매우 다릅니다. 다행히도 Auto Layout을 마스터하는 것은 여느 다른 프로그래밍 과제를 달성하는 것과 다르지 않습니다. 2가지 기본적인 단계가 있습니다.: 첫 번째로 제약조건에 기반한 레이아웃 뒤의 로직을 이해할 필요가 있으며, API를 공부해야합니다. 당신은 이미 다른 프로그래밍 과제를 배울 때 이러한 단계들을 성공적으로 달성해왔을 겁니다. Auto Layout 또한 예외는 아닙니다.

이 가이드의 나머지 부분은 당신이 Auto Layout으로 쉽게 전환할 수 있도록 설계되었습니다. [Auto Layout Without Constraints] 챕터는 Auto Layout 기반 사용자 인터페이스 생성을 단순화 하는 상위 레벨의 추상화에 대해서 설명합니다. [Anatomy of a Constraint] 챕터는 Auto Layout과 원활히 상화작용하기 위해 이해해야 하는 배경 이론을 제공합니다. [Working with Constraints in Interface Builder]()는 Auto Layout을 설계하는 도구들에 대해 설명하고, [Programmatically Creating Constraints]() 와 [Auto Layout Cookbook]() 챕터는 API를 상세하게 설명합니다. 마지막으로 [Auto Layout Cookbook]()은 다양한 수준과 넓은 범위의 예제 레이아웃을 보여줌으로써, Auto Layout을 공부하고 이를 프로젝트에 가져다 쓸 수 있도록 합니다. 그리고 [Debugging Auto Layout]() 은 문제가 발생할 경우 이를 해결하기 위한 조언과 도구들을 제공합니다.


### 참조
 * [Auto Layout Guide](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html#//apple_ref/doc/uid/TP40010853-CH7-SW1)

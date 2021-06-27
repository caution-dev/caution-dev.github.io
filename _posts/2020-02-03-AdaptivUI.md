---
layout: post
title:  ""
date:   2020-02-03 00:26:06 +0900
categories: iOS
tags: iOS Swift iPadOS AdaptiveUI
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution입니다.

iOS 개발자는 OS의 주체인 Apple의 의도를 파악하는 것이 가장 중요하다고 생각합니다.  
그래서 Apple에서 가이드하는 Adaptive UI를 통해 Whale이 Universal로 가는 길을 제시하고자 합니다.

Adaptive UI의 `adaptive`는 `모든 iOS 디바이스에 잘 맞도록 콘텐츠를 조정할 수 있음`을 의미합니다.  
Adaptive UI는 모든 iOS 디바이스에서 Whale의 디자인과 기획에 담긴 의도를 사용자에게 온전히 전달하기 위한 최고의 방법입니다.

Whale이 Universal로 나아가기 위해서는 아래의 **두 가지 이슈**를 해결해야 합니다.
1. Apple에서 가이드 해주는 Adaptive Model을 통해 기존의 UI를 Adaptive UI로 변경
2. layout을 결정할 때 UIScreen을 기준으로 하던 기존 로직들에 대한 수정

해당 문서는 [iPad 버전 진행 이슈 #667](https://oss.navercorp.com/whale/Mobile-Spec/issues/667)의 iPad의 Split View 대응을 위한 Adaptive UI 및 관련 지식인 Trait과 Size Class에 관한 문서입니다.  
Adaptive UI의 세세한 내용은 [Building Adaptive User Interfaces](https://developer.apple.com/design/adaptivity/)에 리소스들이 모여있으니 참고 부탁드리겠습니다.

## Trait
[UITraitEnvironment](https://developer.apple.com/documentation/uikit/uitraitenvironment) protocol을 따르는 UIViewController, UIView 같은 객체들은 해당 객체와 관련된 현재 environment을 나타내는 trait(특성)들로 이루어진 [traitCollection](https://developer.apple.com/documentation/uikit/uitraitenvironment/1623514-traitcollection)이라는 property를 갖고 있습니다.

trait의 종류는 아래와 같습니다.
* [horizontalSizeClass](https://developer.apple.com/documentation/uikit/uitraitcollection/1623508-horizontalsizeclass)
* [verticalSizeClass](https://developer.apple.com/documentation/uikit/uitraitcollection/1623513-verticalsizeclass)
* [displayScale](https://developer.apple.com/documentation/uikit/uitraitcollection/1623519-displayscale)
* [userInterfaceIdiom](https://developer.apple.com/documentation/uikit/uitraitcollection/1623521-userinterfaceidiom)

여기에서는 trait이 **trait을 갖는 객체와 관련된 현재 environment를 나타낸다는 것**과 바로 다음에 설명할 **Size Class가 하나의 trait이라는 것**만 알아가시면 되겠습니다.

## Size Class
Size Class는 크기에 따라 콘텐츠 영역에 자동으로 할당되는 하나의 trait입니다.  
apple은 adaptive한 UI를 위하여 이 Size Class의 사용을 권장하고 있습니다.

시스템은 뷰의 넓이와 높이를 2가지 종류의 Size Class로 정의합니다.

* compact(denotes constrained space)
* regular(denotes expansive space)

뷰는 아래와 같이 4가지의 Size Class의 조합을 갖습니다.
* regular width, regular height
* compact width, compact height
* regular width, compact height
* compact width, regular height

traitCollection을 통해 view가 가진 [horizontalSizeClass](https://developer.apple.com/documentation/uikit/uitraitcollection/1623508-horizontalsizeclass), [verticalSizeClass](https://developer.apple.com/documentation/uikit/uitraitcollection/1623513-verticalsizeclass)를 알 수 있습니다.

기기별, 상황별 Size Class에 관련된 자세한 설명은 [Human Interface Guidelines의 Adaptivity and Layout](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/adaptivity-and-layout/)의 Size Classes에서 확인하실 수 있습니다.

## Adaptivity and Size Changes
### [The Adaptive Model](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/TheAdaptiveModel.html#//apple_ref/doc/uid/TP40007457-CH19-SW1) 문서 요약
* iOS의 Adaptive Model을 이용하면 어떤 디바이스에서도 잘 맞도록 콘텐츠를 조정할 수 있다.
* Adaptive Interface를 만들 때 가장 중요한 것은 Auto Layout이다.
* 고수준의 결정은 trait을 이용한다.

### [Building an Adaptive Interface](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/BuildinganAdaptiveInterface.html#//apple_ref/doc/uid/TP40007457-CH32-SW1) 문서 요약(Adaptive UI를 적용하는 프로세스)
* (`traitCollectionDidChange(_:)` 같은 메소드를 통해) trait이 변경될 때 interface의 굵직한 부분들을 결정
* Interface Builder 혹은 code로 Size Class별 Constraint를 걸어 **Size Class를 기준으로 layout을 결정**하도록 대응
* Auto Layout Constraint로도 안되는 부분은 `viewWillTransition(to:with:)`를 통해 추가 대응

## Example in Whale
Adaptive UI의 골자는 layout을 iPhone, iPad와 같은 idiom으로 결정하는 것이 아니라 Size Class를 기준으로 결정하는 것입니다.

왜 Size Class를 써야 할까요?  
Size Class를 사용하면 iPad의 Split View에서 다양하게 변하는 window 크기에 쉽게 대응할 수 있고,  
이는 브라우저인 Whale이 반드시 지원해야 하는 [multiple windows](https://developer.apple.com/design/human-interface-guidelines/ios/system-capabilities/multiple-windows/)를 적용하기 위한 초석이 되기 때문입니다.

또한 [The Adaptive Model](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/TheAdaptiveModel.html#//apple_ref/doc/uid/TP40007457-CH19-SW1)에서는 trait 중 하나인 [userInterfaceIdiom](https://developer.apple.com/documentation/uikit/uitraitcollection/1623521-userinterfaceidiom)의 역할을 아래와 같이 가이드하고 있습니다.
> This trait is provided for backward compatibility and conveys the type of device on which your app is running. Avoid using this trait as much as possible. For layout decisions, use the horizontal and vertical size classes instead.  
이 trait은 이전 버전과의 호환성을 위해 제공되며 앱이 실행되는 디바이스의 유형을 전달합니다. 이 특성을 가능한 한 많이 사용하지 마십시오. layout을 결정하기 위해서는 Size Class를 대신 사용하십시오.

아래 예제는 아래의 **두 가지 문제점**을 해결하여 탭 리스트를 Split View에 대응한 것입니다.
1. idiom(디바이스의 종류)을 기준으로 layout을 결정한다.
2. UIScreen(디바이스의 크기)을 기준으로 layout을 결정한다.

### 기존의 UI를 Adaptive UI로 변경 in TabListGridFlowLayout
**변경 전, idiom을 기준으로 layout을 결정하는 기존의 로직:**
```Swift
struct TabGridCellInfo {

    static var size: CGSize {
        if UI_USER_INTERFACE_IDIOM() == .pad {
            return CGSize(width: 166, height: 200)
        } else {
            if UIScreen.isSmallLayout {
                let defaultSize = self.defaultSize
                return CGSize(width: defaultSize.width * 0.8, height: defaultSize.height * 0.8)
            } else {
                return self.defaultSize
            }
        }
    }

    static var defaultSize: CGSize {
        return CGSize(width: 161, height: 195)
    }

}
```
```Swift
class TabListGridFlowLayout: UICollectionViewLayout {

    ...

    var numberOfTabColumns: Int {
        if UI_USER_INTERFACE_IDIOM() == .pad {
            if UIApplication.isStatusBarLandscape {
                return 4
            } else {
                return 3
            }
        } else {
            if UIApplication.isStatusBarLandscape {
                return UIDevice.isBigPhone ? 4 : 3
            } else {
                return 2
            }
        }
    }

    ...

}
```

**변경 후, Size Class을 기준으로 layout을 결정하는 로직:**
```Swift
internal struct AdaptiveGridCellInfo {

    // MARK: - internal

    internal static func verticalScrollItemSize(
        of collectionView: UICollectionView,
        defaultSize: CGSize,
        columnCount: Int,
        insets: UIEdgeInsets = .zero,
        interItemSpacing: CGFloat = 0.0
    ) -> CGSize {
        let totalInsets = insets.left + insets.right
        let totalSpacing = interItemSpacing * CGFloat(columnCount - 1)
        let ratio = defaultSize.height / defaultSize.width
        let width = (collectionView.bounds.width - totalInsets - totalSpacing) / CGFloat(columnCount)

        return CGSize(width: width, height: width * ratio)
    }

    ...

}
```
```Swift
extension CGSize {

    internal var reversed: CGSize {
        return CGSize(width: self.height, height: self.width)
    }

}
```
```Swift
class TabListGridFlowLayout: UICollectionViewLayout {

    ...

    private var itemSize: CGSize {
        guard let collectionView = collectionView else {
            return .zero
        }

        let isRegular = collectionView.traitCollection.horizontalSizeClass == .regular
        let isLandscape = collectionView.frame.width > collectionView.frame.height
        let defaultSize = CGSize(width: 161, height: 195)

        return AdaptiveGridCellInfo.verticalScrollItemSize(
            of: collectionView,
            defaultSize: isRegular && isLandscape ? defaultSize.reversed : defaultSize,
            columnCount: self.columnCount,
            insets: self.edgeInsets,
            interItemSpacing: self.interItemSpacing
        )
    }

    private var columnCount: Int {
        guard let collectionView = collectionView else {
            return 0
        }

        let isSmallScreen = max(UIScreen.mainScreenWidth, UIScreen.mainScreenHeight) < 736.0
        let isCompact = collectionView.traitCollection.horizontalSizeClass == .compact
        let isPortrait = collectionView.frame.width < collectionView.frame.height

        return isCompact && isPortrait ? 2 : (isSmallScreen ? 3 : 4)
    }

    ...

}
```

### UIScreen을 기준으로 layout을 결정하는 부분 변경 in TabPageControlView
**변경 전, UIScreen을 기준으로 layout을 결정하는 기존의 로직:**
```Swift
class TabPageControlView: UIView {

    ...

    override func sizeThatFits(_ size: CGSize) -> CGSize {
        let width = UIScreen.mainScreenWidth

        ...

        return CGSize(width: width, height: self.height)
    }

    private var height: CGFloat {
        return UIApplication.isStatusBarLandscape ? 53.0 : 43.0
    }

    ...

}
```
```Swift
class TabListViewController: UIViewController, ReplaceAttachable, CommandBroker {

    ...

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

        ...

        self.tabPageControl.sizeToFit()

        ...

    }

    ...

}
```

**변경 후, super view의 bounds를 기준으로 layout을 결정하는 로직:**
```Swift
class TabPageControlView: UIView {

    ...

    /*override func sizeThatFits(_ size: CGSize) -> CGSize {
        let width = UIScreen.mainScreenWidth

        ...

        return CGSize(width: width, height: self.height)
    }*/

    /*private var height: CGFloat {
        return UIApplication.isStatusBarLandscape ? 53.0 : 43.0
    }*/

    ...

}
```
```Swift
class TabListViewController: UIViewController, ReplaceAttachable, CommandBroker {

    ...

    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()

        ...

        self.tabPageControl.frame.size = CGSize(
            width: self.view.bounds.width,
            height: UIApplication.isStatusBarLandscape ? 53.0 : 43.0
        )

        ...

    }

    ...

}
```

### viewWillTransition(to:with:) in TabListGridViewController
**변경 후, transition이 일어나면 탭 리스트를 layout 정보를 업데이트하는 로직:**
```Swift
class TabListGridViewController: UICollectionViewController, TabListExpressible, TabListScrollPositionProvider {

    …

    override func viewWillTransition(to size: CGSize, with coordinator: UIViewControllerTransitionCoordinator) {
        super.viewWillTransition(to: size, with: coordinator)

        coordinator.animate(alongsideTransition: { _ in
            self.collectionView.collectionViewLayout.invalidateLayout()
        })
    }

    …

}
```

### 참조
 * [ARC Summary](http://minsone.github.io/mac/ios/swift-automatic-reference-counting-summary)

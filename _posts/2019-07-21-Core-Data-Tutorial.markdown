---
layout: post
title:  "Core Data - Tutorial"
date:   2019-07-21 15:18:19 +0900
categories: Core Data
tags: Coredata Apple-framework iOS tutorial
author: Juhee Kim
mathjax: true
comments: true
---

* content
{:toc}

안녕하세요! caution 입니다. 현재 Mashup이라는 동아리를 하고 있는데, iOS 팀 세션에서 간단히 Core Data를 익힐 수 있는 발표를 진행했습니다. 그 내용을 정리해서 공유하고자 합니다.

Core Data에 대해 더 세부적인 개념이 필요하다면 이전 포스팅을 찹고해주세요! 그럼, 시작합니다!


## CoreData?

### CoreData란 무엇인가요?

iOS 앱에서는 데이터를 저장하기 위한 다양한 방식을 제공합니다. `Document` 영역에 파일을 저장할 수도 있고, 간단한 설정이라면 `UserDefaults`를 사용할 수도 있습니다. 혹은 `Realm`이나 `SQLite`와 같은 Database를 사용할 수도 있죠.

CoreData도 마찬가지입니다. CoreData는 앱에서 **모델 레이어 객체의 관리**에 사용할 수 있는 프레임워크입니다.

CoreData는 이 **모델 레이어 객체**의 life cycle 관리나, *object graph management*, 영속성 관리와 관련된 일반적인 해결책들을 제공하고 있습니다.

> object graph management 란, 파일 형태로 저장되어 있는 데이터를 지정된 객체와 연결해주는 것을 의미합니다. CoreData를 사용하면 데이터를 읽어왔을 때 그 결과가 객체의 형태를 띄고, 객체의 속성을 수정함으로써 그대로 다시 데이터를 저장할 수 있습니다.

CoreData는 영속성 관리를 위해 `SQLite`를 사용하지만, `SQLite`의 wrapper 역할을 하는 것은 아닙니다.

* CoreData를 사용하면 `Library/Application Support`에 `.sqlite`파일이 생성됩니다. 이를 통해 내부에 저장된 데이터를 읽고/수정하는 것이 가능합니다.
* 하지만 데이터 영속성을 위해 `SQLite`를 쓴다고 해서 `SQLite`가 지원하는 모든 동작을 지원하는 것은 아니며, 같은 동작이더라도 내부적으로 다르게 동작할 수 있습니다.

### CoreData는 어떻게 구성되어 있나요?
CoreData를 구성하는 다양한 클래스들이 있습니다. 이를 모두 살펴보기는 어렵고, 직접 사용함에 있어서 꼭 필요한 녀석들을 설명하겠습니다.

잠깐 CoreData는 접어두고, 우리의 앱에서 객체 데이터를 파일로 저장한다고 생각해봅시다. 어떤 동작들이 필요할까요?

먼저 우리가 원하는 형태의 class를 생성해야 할 것입니다. 어떤 데이터를 저장하고 싶은 것인지 정해야겠죠.

하지만 우리는 하나가 아닌 여러 class의 데이터를 저장할 것입니다. 그러려면 이 객체들을 관리하고 있는 관리자가 필요하겠네요. 이제부터 이 관리자는 객체들의 생성, 수정을 관리하게 됩니다.

이제 원하는 데이터를 파일에 저장해야 합니다. 나중에 파일에서 다시 데이터를 불러올 수 있도록하는 기능도 구현해야 할 것입니다. 파일로 저장하는 동작은 실패할 수도 있고, 성공할 수도 있습니다.

크게 이렇게 세 가지 동작으로 나눌 수 있겠습니다. 이걸 CoreData와 연관지어서 생각해보죠. 차근차근히요.

#### .xcdatamodeld
프로젝트를 처음 생성할 때 `Use Core Data`를 선택하면 자동으로 이 확장자를 가진 파일이 생성됩니다. 이 파일에서는 Core Data로 저장할 `Entity`, `Fetched Request`들을 정의하고 그 관계를 구상할 수 있습니다.

다음과 같이 어떤 Entity에 어떤 Type의 Attribute가 있는지 정의할 수 있습니다.

![image](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/Art/Model_Editor_2x.png)

또한 Entity들간의 Relationship 을 정의할 수 있으며, 추상 Entity도 만들 수 있습니다.

![image](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/Art/Entity_Inheritence_2_2x.png)

> 이미지 출처 : [Apple-Core-Data](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CoreData/KeyConcepts.html#//apple_ref/doc/uid/TP40001075-CH30-SW1)

이제 어떤 데이터들을 저장할 지, 그리고 Entity간 관계가 정해졌다면, 실제로 이 데이터들이 어떤 객체로 연결될 것인지를 설정해주어야 합니다.

#### NSManagedObjectModel

 `NSPersistentStoreCoordinator`가 데이터를 읽어서 `NSManagedObjectContext`로 올릴 때 `.xcdatamodeld` 파일로부터 `NSManagedObjectModel` 객체를 만듭니다. 이 객체는 스키마의 Entity를 나타내는 하나 이상의 `NSEntityDescription` 객체가 포함되어 있습니다. 각 `NSEntityDescription` 객체에는 스키마의 Entity attribute (or field)을 나타내는 속성 설명 개체 (NSPropertyDescription의 하위 클래스 인스턴스)가 있습니다.

 즉 `NSPersistentStoreCoordinator`는 `NSManagedObjectModel`을 사용하여 `NSManagedObjectContext`에 올라가 있는 `NSManagedObject` 인스턴스들의 데이터를 File에 저장할 수 있는 형태로 변환하고, File에서 읽어들일 때 다시 `NSManagedObject` 로 변환해야 할 때 이 `NSManagedObjectModel`에 기반하여 변환합니다.

#### NSManagedObject
`NSManagedObject`는 `NSPersistentStoreCoordinator`에 의해서 인스턴스화 된 Entity의 데이터(Record)를 나타냅니다. 모든 `NSManagedObject`는 `NSManagedObjectContext`에 의해 관리됩니다.

`NSManagedObject` 인스턴스를 통해 앱의 데이터를 추가/수정/삭제할 것입니다. 그러므로 각 Entity에 대응되는 `NSManagedObject`가 필요합니다.

```swift
import UIKit
import CoreData
import Foundation

class EmployeeMO: NSManagedObject {

    @NSManaged var name: String?

}
```

이 `NSManagedObject` class는 직접 정의할 수도 있지만, 일반적으로는 `.xcdatamodeld`을 통해 Entity 구조를 만들고 이를 기반으로 `NSManagedObject` class들을 generate 합니다. 이 방법은 Xcode에서 기본적으로 지원하며, 자세한 사항은 Tutorial project를 진행하면서 설명하겠습니다.

> 여기까지가 Core Data에서 Entity 구조를 구성하는 파일과, 이에 연결될 수 있는 객체를 살펴보았습니다. 실제로는 `NSManagedObject` 파일을 우리가 다루는 일반적인 model struct/class 처럼 사용하게 될 것이며, `NSManagedObjectModel`을 직접 다루지는 않을 것입니다.

### NSManagedObjectContext

앞서 잠깐 설명드리면서 언급되었던 class 입니다. 이 클래스는 `NSManagedObject` 인스턴스들을 생성하고, 관리하는 역할을 합니다. 파일에서 불러와진 데이터들을 기반으로 `NSManagedObject` 인스턴스로 변환하면 `NSManagedObjectContext`가 관리합니다. 또한 새로운 `NSManagedObject` 를 만들려면 `NSManagedObjectContext`를 통해서 생성해야 합니다.

```swift
func object(with: NSManagedObjectID) -> NSManagedObject
// Returns an object for a specified ID even if the object needs to be fetched.
```

그 외에 `NSManagedObject`가 존재하는지, 그 수를 세거나, 특정 `NSManagedObject`들을 불러올 수 있는 `NSFetchRequest<NSFetchRequestResult>`를 생성하는 등 다양한 메소드들을 제공합니다.

`NSManagedObject`는 Core Data를 사용하면서 가장 자주 사용하게 될 class입니다. 이 클래스는 로드한 Entity의 데이터(Record)들을 관리하고 있으며, Entity에 데이터를 추가하거나, 삭제할 때에도 이 class 인스턴스를 통해야 합니다. 또한 변경된 데이터를 **저장**할 때에도 이 인스턴스를 사용합니다.

> 주의하세요! Core Data는 in-memory-DB의 역할을 수행합니다. 모든 로드된 데이터는 `NSManagedObject`가 살아있는 한 memory 위에 올라가 있습니다. 또한, 새로운 데이터가 추가되었든, 수정사항이 발생했든 모든 데이터는 `NSManagedObject`가 가지고 있으며, **명시적으로 `context.save()`를 호출해야 수정사항이 저장됩니다.**

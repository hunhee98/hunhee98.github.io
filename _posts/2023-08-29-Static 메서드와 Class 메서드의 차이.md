---
layout: post
title: "[Swift] Static 메서드와 Class 메서드의 차이"
date: 2023-08-29 09:10 +0900
tags:
- method
categories:
- Swift
---
메서드는 **타입 메서드(Type Method)**, **인스턴스 메서드(Instance Method)**로 나뉜다. 이 중에서 메서드의 구현 부 앞에 **`static`** , **`class`** 키워드가 붙는 경우는 타입 메서드에 속한다. 

타입 메서드란 간단하게 클래스나 구조체의 타입에 참조해서 사용할 수 있는 메서드를 말한다.

```swift
class Dog {

  static func bite() { }
	class func run() { }
}

// 위와 같이 클래스나 구조체 내부에서 메서드 앞에 static, class를 붙여 타입 메서드로 선언한다.

Dog.bite()
Dog.run()

// 타입 메서드는 인스턴스를 생성할 필요 없이 타입에 바로 참조하여 사용한다.
```

그렇다면 static 과 class는 어떤 차이가 있을까? 결론부터 말하자면 이것은 **`상속`** 과 관련이 있다.

static 키워드의 경우 상속 시 재정의가 불가능하고, class 키워드의 경우 상속 시 재정의가 가능하다. 여기서 "구조체는 상속 관계를 가질 수 없는데?" 라는 의문이 들었는데, 당연하게도 **구조체에서는 static 키워드로만 타입 메서드를 정의**할 수 있다.

```swift

struct Cat {

  static func ggookggook() { }
  //class func haak() { } -> X
}
```
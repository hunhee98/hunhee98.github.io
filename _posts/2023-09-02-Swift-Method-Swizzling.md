---
layout: post
title: "[Swift] Method Swizzling 이해하기"
date: 2023-11-17 11:31 +0900
tags:
  - methodSwizzling
categories:
  - Swift
  - Objective-C
---
`Method Swizzling` 의 개념에 대해 알아보자

(사실 Method Swizzling에 대해 정말 잘 설명한 여러 블로그들을 볼 수 있는데   
다른 블로그 글을 읽는 것보다 직접 학습하고 정리해보는게 낫다.   
즉, 다른 블로그 보시는게 이해에는 더 빠를 수 있음~)

Swizzle:  `뒤섞다`  라는 의미를 가지고 있음.   
메서드를 뒤섞다? 좀 처럼 와닿지 않는 표현인 듯
## Method Swizzling이란
메소드를 뒤섞는다는 것은 표현 그대로. **메소드가 실행될 때** 원하는 메소드로 바꿔 실행할 수 있다고 카더라..

이것도 잘 와닿지는 않지만, 그래 어떻게든 바꿔서 실행된다고 해보자.   
그런데 메소드가 실행될 때? 뭔가 의문이 드는 부분이다.

Swift에서 어떤 Method가 실행될지 결정하는 방식은   
컴파일에 결정 짓는 `Static Dispatch`   
그리고 런타임에 결정 짓는 `Dynamic Dispatch` 이 있는데   
둘 다 Swizzling이 가능한걸까?

런타임에 결정 짓는 **동적 디스패치**라면 모를까,   
컴파일에 결정 짓는 **정적 디스패치**에 이게 가능하다면   
정적이라는 말이 무색하게 굉장한 오버헤드가 발생할 것만 같다.

그래서 **공식 문서**를 한 번 살펴보았음.   
(이것도 무슨 키워드로 찾아봐야하나 한참 뒤졌다..)   

그 전에 메소드 스위즐링의 예제를 한 번 보자.   
내가 즐겨보는 김종권님 블로그의 예제를 인용했다.   

### Method Swizzling 구현 예제

[Method Swizzling, 우아하게 dealloc 로그 찍기](https://ios-development.tistory.com/911)

```swift
extension UIViewController {
  static func swizzleMethod() {
    guard let originalMethod = class_getInstanceMethod(Self.self, #selector(viewWillAppear)),
          let swizzledMethod = class_getInstanceMethod(Self.self, #selector(Self.hunyViewWillAppear))
    else { return }
    method_exchangeImplementations(originalMethod, swizzledMethod)
  }

  @objc func hunyViewWillAppear() {
    print("Huny View Will Appear")
  }
}
```

`class_getInstanceMethod` 로 대상 메서드와 바꾸려는 메소드의 인스턴스를 가져옴.   
`method_exchangeImplemetations` 메서드로 두 메서드를 바꾼다.

이때 맞바꾸는 메소드 2개 모두 @objc dynamic 메서드여야함!!   
viewWillAppear 말고도 직접 구현한 @objc dynamic 메서드여도 된다.   
@objc dynamic 메서드가 뭔지는 또 차차 알아보자..   
이름만 봐서는 우선 Objective-C의 런타임 기술을 활용하는 메서드가 아닐까? 추론해본다.

![](https://i.imgur.com/N8D6Dqo.png)
[method_exchangeImplementations(_:_:) | Apple Developer Documentation](https://developer.apple.com/documentation/objectivec/1418769-method_exchangeimplementations)

공식 문서에도 다음과 같이 나와있다. Objective-C 런타임에 2개의 메소드의 구현을 맞바꾼다.   
이제 메소드를 스위즐하는 메소드를 실행하면 된다.   

보통 여기서 다른 블로그 분들은`AppDelegate` 에서 실행을 하더라.   
근데 꼭 AppDelegate에서 실행할 필요는 없고, 메소드 스위즐을 쓰는 목적에 맞게 원하는 시점에서 쓰면 된다.   
결국 스위즐링도 런타임에 일을 더 벌이는거니까 남용하면 단연코 성능상 좋지 않다.

```swift
  func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      UIViewController.swizzleMethod()
    return true
  }
```

실행해보면 실제로 viewWillAppear에는 아무런 구현을 해놓지 않았음에도   
스위즐한 메소드의 구현이 실행되는 것을 볼 수 있음

![](https://i.imgur.com/wKAbMnp.gif)

단! 주의할 점은 스위즐링된 메소드가 실행되고 나서 **오리지널 메소드가 한 번 더 실행**된다는 것!   
예시로 든 위 코드에서는 viewWillAppear를 **오버라이드**하지 않으면 스위즐링된 메서드만 실행되더라.

### 그래서 메서드 스위즐링 어느 경우에 쓰냐?
이 부분은 나도 안써봤다.   
향간의 블로그에 쓰여있는 얘기로는

1. 메모리 누수를 체크하기 위해 객체의 deinit이 실행될 때 로깅
2. 사용자 이벤트 or 에러 로깅

등 앱 전반에 공통적으로 적용해야할 일이 있을 때 쓰인다 카더라.   

근데 휴먼 에러가 발생하기 딱 좋은 기능인 것 같다.   
내가 어디선가 이 메서드를 스위즐링하고 있는지 개발자가 반드시 알아야하며,   
Objective-C 런타임의 기능이기 때문에 언제 어떻게 업데이트로 인해 비정상적으로 작동하게 될지 모른다.



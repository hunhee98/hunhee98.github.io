---
layout: post
title: "[Swift] Class Singleton과 Struct Singleton의 차이"
date: 2023-08-06 16:09 +0900
tags:
- Class
- Struct
- Singleton
categories:
- Swift
---
**Class**와 **Struct**에 **Singleton pattern**을 구현하였을 때 어떤 차이점이 있는지 실험해보자. 
본문을 제대로 이해하려면 **`Reference Type`**, **`Value Type`**에 대한 이해가 동반되어야 한다.

실험 조건:
- 구조체와 클래스를 동일한 구조로 작성한다.
- 싱글톤 인스턴스를 변수1, 변수2에 담는다.
- 변수1의 프로퍼티 값을 변경한다.
- 변수1, 변수2의 프로퍼티 값과 메모리 주소를 출력한다.

Class와 Struct는 메모리에서 서로 다르게 동작한다. 클래스는 Reference Type으로 동작하는 반면 구조체는 Value Type으로 동작한다. 
따라서 아래와 같이 실험해보았다.


### Class, Struct Singleton

```swift
class LocationServiceClass {
  private init() { }
  
  static let shared = LocationServiceClass()

  var location: (Int, Int) = (0, 0)

  func getLocation() {
    print("현재 위치는 \(location)입니다")
  }
}

struct LocationServiceStruct {

  private init() { }

  static var shared = LocationServiceStruct()

  var location: (Int, Int) = (0, 0)

  func getLocation() {
    print("현재 위치는 \(location)입니다")
  }
}
```

대충 사용자의 위치를 구하는 기능을 싱글톤으로 구현했다고 가정하자. 각 프로퍼티, 메서드에 대한 설명은 아래와 같다.
- **`shared`** : LocationService 공유 인스턴스
- **`var location`** : 위치 좌표를 저장하는 프로퍼티
- **`getLocation()`** : 위치 좌표를 출력하는 메서드

우선 **`클래스`**의 경우 어떻게 동작하는지 살펴보자.

```swift
let classService1 = LocationServiceClass.shared
classService1.getLocation()
classService1.location = (1, 0)

let classService2 = LocationServiceClass.shared
classService2.getLocation()

Memory.address(classService1)
Memory.address(classService2)
```

싱글톤 클래스의 인스턴스를 각각의 변수에 저장하고, 값을 변경하고 출력값을 확인해보았다.

![](https://i.imgur.com/jx7wbSu.png)

classService1을 통해 바꾼 값이 classService2에서도 공유되는 것을 확인할 수 있다. 메모리 주소 역시 동일하다. 이 말은 즉슨 LocationServiceClass의 shared 변수를 복사한 classService1, classService2 모두 같은 원본 데이터의 메모리 주소를 참조하고 있음.

**`구조체`** 역시 동일하게 테스트해보았다.

```swift
var structService1 = LocationServiceStruct.shared
structService1.getLocation()
structService1.location = (1, 0)

var structService2 = LocationServiceStruct.shared
structService2.getLocation()

Memory.address(&structService1)
Memory.address(&structService2)
```

![](https://i.imgur.com/CVfl0q5.png)

구조체는 클래스와는 다르게 동작하는 것을 볼 수 있다. 싱글톤 패턴의 이점인 **`데이터 공유`** 가 이뤄지지 않고 있다. 메모리 주소 역시 서로 다른 것을 확인할 수 있는데 이 이유는 구조체는 값 타입이기 때문이다. 인스턴스를 복사할 경우 주솟값이 아닌 그 값 자체를 복사해서 새로운 메모리 공간에 저장한다. 따라서 싱글톤 패턴을 구현하더라도 전혀 의미가 없는 것이다.

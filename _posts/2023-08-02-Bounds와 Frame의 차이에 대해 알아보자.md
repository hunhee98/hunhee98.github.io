---
layout: post
title: "Bounds와 Frame의 차이에 대해 알아보자"
date: 2023-08-02 13:12 +0900
tags:
- UIKit
categories:
- iOS
---
UIKit의 여러 뷰들을 다루다 보면 Bounds와 Frame이란 속성에 대해 흔하게 접할 수 있다. 오늘은 Bounds와 Frame이 각각 어떤 역할을 하는 프로퍼티인지 정확히 알아보자.

Frame과 Bounds은 둘 다 UIView의 인스턴스 프로퍼티임.

![](https://i.imgur.com/UKCH0VK.png)

코드를 확인해보니 둘 다 타입이 **`CGRect`** 로 동일하다. (CGRect에 대한 개념은 [CGRect, CGPoint, CGSize 프로퍼티 이해하기](https://walkerhilla.github.io/posts/CGRect,-CGPoint,-CGSize-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0/) 글에서 다뤘다.) 둘 다 너비와 높이, 위치에 대한 정보를 저장하는 애들이구나? 라고 유추해볼 수 있겠다. 근데 분명 차이점이 있기 때문에 이렇게 프로퍼티 2개를 굳이 만들어뒀을 것이다. 공식문서를 확인해보자
###  [bounds](https://developer.apple.com/documentation/uikit/uiview/1622580-bounds)
>The bounds rectangle, which describes the view’s location and size in its own coordinate system.

자체 좌표계에서 뷰의 위치와 크기를 표현한다(?) 
무슨 말인지 잘 모르겠지만, 우선 넘어가자

### [frame](https://developer.apple.com/documentation/uikit/uiview/1622621-frame)
>The frame rectangle, which describes the view’s location and size in its superview’s coordinate system.

상위 뷰의 좌표에서 뷰의 위치와 크기를 표현한다.

음? 이쯤되니 어렴풋이 그려지는 듯하다. 

bounds는 **`자신만의 좌표`** 에서 뷰의 위치와 크기를 나타내고, Frame은 **`부모 뷰의 좌표`** 에서 뷰의 위치와 크기를 나타내는 것이다!!
근데 이게 대체 뭔 말인지... 백문이 불여일견이라고 코드로 직접 실험해보자.

### bounds와 frame의 차이

UIView를 2개를 각각 **`view1`**, **`view2`** 로 이름을 붙여 생성하였다. 그리고 view2는 view1의 자식 뷰로 만들었다.
또한, 두 뷰의 구분을 위해 서로 다른 배경색을 지정하고, view2의 크기를 view1보다 작게 설정했다.

view1과 view2에 각각 bounds와 frame 속성을 달리 줬을 때 화면에 어떻게 나타나는지 살펴보자.

```swift
view1.frame = CGRect(x: 80, y: 80, width: 300, height: 300)
view2.frame = CGRect(x: 80, y: 80, width: 150, height: 150)
```

![](https://i.imgur.com/PcSQ2Hw.png)

view1, view2 모두 frame 속성으로 같은 x, y, width, height의 CGRect를 설정해주었는데 화면에는 다르게 표시된다. 왜 이렇게 되는 것일까? 바로 위에서 봤던 Frame의 특성 때문이다. 

**상위 뷰의 좌표에서 뷰의 위치와 크기를 나타낸다.**

현재 코드 상으로 view1의 상위 뷰는 화면 전체 크기에 해당하는 view이고, view2의 상위 뷰는 view1이다. **`frame`** 은 뷰의 원점(0,0)으로부터 각각 x, y만큼 떨어진 지점부터 뷰를 그리는 것이다. UIKit에서 뷰의 원점(origin)은 뷰의 왼쪽 상단이다. 그림으로 그려보면 이해가 더 쉬울 것이다.

![](https://i.imgur.com/0dr6euB.png)

자, 그럼 Bounds는 어떻게 동작할까? 우선 현 상태에서 view들의 Bounds 값을 확인해보자,.

![](https://i.imgur.com/LL8x3Yt.png)

view1과 view2 모두 0으로 나온다. 값을 바꿨을 때 화면이 어떻게 달라지는지 보자

```swift
view1.bounds.origin = CGPoint(x: 50, y: 50)
```

![](https://i.imgur.com/OPpxDi4.png)

분명 view1의 bounds origin 값을 변경했는데 view2가 움직였다.. view2의 frame, bounds origin을 확인해보니 변하지 않았다. 도대체 내부에서 뭔 일이 일어나는걸까? 

bounds의 공식문서를 다시 살펴봤더니 이런 내용이 있었다.

>Changing the bounds rectangle automatically redisplays the view without calling its [`draw(_:)`](https://developer.apple.com/documentation/uikit/uiview/1622529-draw) method. If you want UIKit to call the [`draw(_:)`](https://developer.apple.com/documentation/uikit/uiview/1622529-draw) method, set the [`contentMode`](https://developer.apple.com/documentation/uikit/uiview/1622619-contentmode) property to [`UIView.ContentMode.redraw`](https://developer.apple.com/documentation/uikit/uiview/contentmode/redraw).

bounds 값을 변경할 경우 view는 draw 메소드를 호출하지 않고 자동적으로 화면을 다시 그린다. 

즉, view1의 bounds를 바꿨는데 다시 그려진 결과가 저 모양이라는거다.. 그렇다면 bounds는 **자신만의 좌표계**에서 뷰를 나타낸다고 했다. 자신만의 좌표에서 x가 50, y가 50만큼 이동했지만, frame과는 별개로 동작하기 때문에 겉으로 봤을 땐 view1는 움직이지 않고, view2가 움직인 것처럼 보이는 것이다.

![](https://i.imgur.com/h7m5uJ2.gif)

즉, view1의 bounds를 변경하면 view1의 자체적인 좌표는 변하지만, view1의 위치는 frame으로 설정되어 있기 때문에 겉으로 보이는 위치는 변하지 않는 것이다. 그러나 view1의 하위 뷰인 view2의 frame은 view1의 좌표로부터 계산되기 때문에 영향을 받는 것이다.
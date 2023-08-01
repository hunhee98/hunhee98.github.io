---
layout: post
title: "CGRect, CGPoint, CGSize 프로퍼티 이해하기"
date: 2023-08-01 20:14 +0900
tags:
- CoreGraphics
categories:
- iOS
---

UIKit을 학습하다보면 뷰에 관한 프로퍼티들 중 **`CGRect`**, **`CGPoint`**, **`CGSize`** 등 타입으로 선언된 속성들을 종종 보게 된다. 오늘은 이 CGRect, CGPoint, CGSize의 차이점과 각각 어떤 역할을 하는지 알아보자.

사실 이 3가지 타입은 서로 상관관계가 있어서 CGPoint -> CGSize -> CGRect 순으로 살펴보면 이해하는데 더 도움이 될 것 같다.

### [CGPoint](https://developer.apple.com/documentation/corefoundation/cgpoint)

>A structure that contains a point in two-dimensional coordinate system.

2차원 좌표계의 점을 포함하는 구조체, 쉽게 **x, y 값을 갖는 구조체**라고 생각하면 된다.
학창 시절 수학 시간에서 x축, y축을 갖는 그래프의 한 점을 표시할 때 (x, y)라고 표시했던 것을 떠올리면 얼추 똑같다.

iOS에서는 이 (x, y)를 CGPoint라는 구조체를 통해서 표현하는 것이다. 그렇다면 CGPoint 구조체가 어떻게 선언되어있는지 살펴보자.

```swift
public struct CGPoint {
  public init()
  public init(x: Double, y: Double)
  
  public var x: Double
  public var y: Double
}
```

코드 상으로도 CGPoint라는 구조체가 x, y를 저장 프로퍼티로 가지고 있는 것을 확인할 수 있다. CGPoint는 x와 y 값을 저장하는 구조체구나, iOS에서 2차원 좌표계의 한 점을 표현하는데 쓰이는 구조체구나 라고 이해해볼 수 있겠다.

### [CGSize](https://developer.apple.com/documentation/corefoundation/cgsize)
>A structure that contains width and height values.

CGSize는 너비 및 높이 값을 가지는 구조체다. 너비와 높이라고 하면 사각형을 뜻한다고 생각해볼 수 있겠지만, 그렇지 않다. 말 그대로 **`Size`**, 너비와 높이에 대한 값을 저장하는 구조체다. 코드로 어떻게 선언되어 있는지 살펴보자
```swift
public struct CGSize {
  public init()
  public init(width: Double, height: Double)
  
  public var width: Double
  public var height: Double
}
```

역시나 너비(width)와 높이(height)를 저장하는 프로퍼티를 갖고 있다.

### [CGRect](https://developer.apple.com/documentation/corefoundation/cgrect)
> A structure that contains the location and dimensions of a rectangle.

공식문서를 보면 rectangle(사각형)에 대한 locationI(위치)와 dimenstions(넓이, 면적)을 포함하는 구조체라고 설명한다.

자, 먼저 CGRect가 어떻게 선언되어 있는지 확인해보자

```swift
public struct CGRect {
  public init()
  public init(origin: CGPoint, size: CGSize)
  
  public var origin: CGPoint
  public var size: CGSize
}
```

CGPoint 타입의 origin이라는 프로퍼티와 CGSize 타입의 size 프로퍼티를 가지고 있다. 앞서 학습했던 CGPoint과 CGSize의 역할을 생각해보면 origin은 사각형의 위치, CGSize는 사각형의 크기에 대한 정보를 담당한다는 것을 유추해볼 수 있다.
하지만, 사각형의 위치를 x, y로 어떻게 나타낸다는거지? 라는 의문이 들 수 있다. 그래서 그림을 그려보았다.

![](https://i.imgur.com/np6sAaR.png)

위 그림처럼 사각형이 어디서부터 시작될지 결정하는 것이 origin이다. x, y값 지점에서 사각형을 size만큼 만드는 것이 CGRect다.

![](https://i.imgur.com/wvgVw1r.png)

뷰의 크기와 위치를 정하는 속성 중 frame이라는 것이 있는데 보다시피 타입이 CGRect로 되어있다. 즉, CGRect를 통해서 이 뷰가 어디서 시작하고 높이, 너비를 얼만큼 차지할 것인지 결정하는 것이다.
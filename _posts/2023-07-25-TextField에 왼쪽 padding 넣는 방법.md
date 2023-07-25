---
layout: post
title: "TextField에 왼쪽 padding 넣는 방법"
date: 2023-07-25 09:10 +0900
tags:
- UIKit
categories:
- iOS
---
UIKit의 TextField는 기본적으로 내부 패딩이 없다. 그래서 아래와 같은 몹시 불편한 상황이 연출되기도 함..

![](https://i.imgur.com/JejkOfm.png)

TextField의 **`leftView`**라는 속성을 통해서 내부 패딩을 구현할 수 있음.

![](https://i.imgur.com/8N4yKkx.png)

공식문서에 따르면 text field의 왼쪽에 표시되는 뷰 속성이라고 한다!

```swift
let paddingView = UIView(frame: CGRect(x: 0.0, y: 0.0, width: 16.0, height: 0.0))
textField.leftView = paddingView
textField.leftViewMode = .always
```
UIView 객체를 하나 만들어서 width 값을 원하는 패딩 값만큼 지정하고 textField의 leftView 속성에 할당한다. 이때 **`leftViewMode`** 속성을 **`always`**로 해주어야 우리가 원하던 텍스트 필드의 왼쪽 패딩이 정상적으로 구현된다.

**적용된 모습**

![](https://i.imgur.com/gjj1wns.png)

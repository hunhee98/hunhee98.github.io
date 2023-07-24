---
layout: post
title: "UILabel, UIImageView 터치 이벤트 추가하기"
date: 2023-07-24 09:10 +0900
tags:
- UIKit
categories:
- iOS
---
UILabel, UIImageView 등 기본적으로 터치 액션이 제공되지 않는 View에 터치 이벤트를 추가하는 방법에 대해 알아보자. **tapGestureRecognizer**를 통해서 기본적으로 사용자 상호작용을 지원하지 않는 View(UIView, UILabel, UIImageView, UITextView 등)에 터치 이벤트를 추가할 수 있다.

우선 터치 이벤트를 추가할 뷰 객체를 생성한다.
스토리보드를 사용하지 않고 코드로 뷰 객체를 생성했더라도 방법은 동일함!

```swift
@IBOutlet weak var testView: UIView!
```

testView를 터치했을 때 실행될 함수를 만든다.

```swift
@objc func testViewTapped() {
	print("터치 이벤트 발생")
}
```

tapGestureRecognizer를 생성하여 testView와 testViewTapped 함수를 연결한다.

```swift
let tapGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(testViewTapped))
	testView.addGestureRecognizer(testViewTapped)
```

추가로 testView의 isUserInteractionEnabled 속성을 true로 설정해주어야 터치 이벤트가 정상적으로 작동한다.

```swift
testView.isUserInteractionEnabled = true
```

짜잔..

![](https://i.imgur.com/LvRf2rt.gif)
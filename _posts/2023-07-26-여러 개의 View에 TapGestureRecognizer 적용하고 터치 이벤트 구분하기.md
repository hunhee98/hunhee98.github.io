---
layout: post
title: "여러 개의 View에 TapGestureRecognizer 적용하고 터치 이벤트 구분하기"
date: 2023-07-26 10:55 +0900
tags:
- UIKit
- TapGestureRecognizer
categories:
- iOS
---

기본적으로 터치 이벤트가 설정되어 있지 않는 View는 **`TapGestureRecognizer`**를 적용하여 터치 이벤트를 추가할 수 있다. 
*참고 - [UILabel, UIImageView 터치 이벤트 추가하기](https://walkerhilla.github.io/posts/UILabel,-UIImageView-%ED%84%B0%EC%B9%98-%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0/)*

만약 여러 개의 View에 터치 이벤트를 설정하고자 한다면 View들을 배열에 담아 **반복문** 또는 **forEach문**으로 TapGestureRecognizer를 추가한다면 코드가 깔끔할 것이다. 그래서 바로 작성해봄..

```swift

// 스토리보드로 뷰 객체를 생성한 경우 OutletCollection 형태의 뷰 프로퍼티
@IBOutlet var views: [UIView]!

// 코드로 뷰 객체를 생성한 경우 배열에 담았다고 가정하자
var views: [UIView] = [view1, view2, view3, view4]

func setupViewTapGesture() {
	views.forEach { view in 
		let tapGestureRecognizer = UITapGestureRecognizer(self, action: #selector(viewTapped))
		view.isUserInteractionEnabled = true
		view.addGestureRecognizer(UITapGestureRecognizer(target: self, action: #selector(viewTapped))
	}
}

@objc func viewTapped() {
	print("터치 됨")
}
```

배열에 넣은 뷰들 모두 터치 이벤트가 잘 작동한다. 그러나 한 가지 문제가 발생하였음. 바로 어떤 뷰가 터치 되었는지 구분할 수가 없었다. 이를 해결하기 위해 UITapGestureRecognizer 객체를 생성할 때 파라미터로 넘기는 `#selector(viewTapped))` 부분의 함수를 `#selector(viewTapped(_:)`로 수정하고 viewTApped() 메소드 역시 UITapGestureRecognizer를 인자로 받도록 수정해보자.

```swift
func setupViewTapGesture() {
	views.forEach { view in 
		let tapGestureRecognizer = UITapGestureRecognizer(self, action: #selector(viewTapped))
		view.isUserInteractionEnabled = true

		// viewTapped -> viewTapped(_:)로 수정함
		view.addGestureRecognizer(UITapGestureRecognizer(target: self, action: #selector(viewTapped(_:)))
	}
}

// 파라미터로 UITapGestureRecognizer 인스턴스를 받도록 수정함
@objc func viewTapped(_ sender: UITapGestureRecognizer) {
	
}
```

UITapGestureRecognizer가 자신이 등록된 뷰의 터치 이벤트를 인식해서 viewTapped 메소드를 실행하는데 이때 인자로 UITapGestureRecognizer 인스턴스를 넘겨주게 된다. UITapGestureRecognizer 인스턴스에는 등록된 view에 접근할 수 있는 **`view`** 속성이 존재한다. 
![](https://i.imgur.com/ApC1nQA.png)

```swift
@objc func viewTapped(_ sender: UITapGestureRecognizer) {
	print("\(sender.view.tag)")
}
```

view의 tag 값에 접근할 수 있다! 이제 열거형이나 조건문 제어 등으로 터치 이벤트를 분기처리해볼 수 있다.

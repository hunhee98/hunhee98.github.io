---
layout: post
title: "[iOS] View에 Custom State를 만들어보자"
date: 2023-09-06 15:16 +0900
tags:
  - CustomState
  - UIKit
categories:
  - iOS
---
## UIControl.State
UIKit에는 `UIControl` 를 상속 받는 뷰들에게 기본적인 State를 제공한다.   
바로 `UIControl.State` 라는 열거형이다.
이미 알게 모르게 많이들 써왔을 것이다.

![](https://i.imgur.com/I8KpFLV.png)
[UIControl.State | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uicontrol/state)

자 어쨌든 기본적으로 제공되는 이 State에는   
`normal` , `highlighted` , `selected` , `disabled`  등   
UIControl 서브클래스의 기본 상호작용에 맞춰 케이스가 열거 되어있다.   
그런데 우리가 프로젝트를 진행하다보면 내 앱에 맞는, 좀 더 **명시적인 이름**으로 상태 관리를 할 필요성이 있다.   
게다가 UIControl 서브 클래스가 아닌 뷰는 UIControl.State를 못 씀.   
그래서 **Custom State**를 만들어 활용하는 방안을 나름 생각해보았다.

## Custom State 구현 방식

설명을 위해서 다음과 같은 내용을 테이블 뷰로 만들어본다고 가정해봤다.

![](https://i.imgur.com/WvldcYG.png)

Cell이 가질 수 있는 상태는
- 서류 미열람
- 서류 열람
- 서류 불합격
- 서류 합격

이렇게 됨.   
Cell에 열거형으로 정의해보자.

```swift
enum JobHuntProgress {
	case notReviewed
	case reviewed
	case notQualified
	case qualified
}
```

그럼 이제 상태에 따라 Button이나 ImageView를 업데이트 해주어야 할 것이다.   

그런데 여기서 한 가지 고민되었던 부분이 있다.   
Cell이 가지는 상태를 열거형으로 정의해두었으니, Cell이 이 상태 값을 변수로 저장하고 있다가   
상태가 바뀔 때마다 셀 내 버튼이나 이미지 뷰 등을 업데이트해주면 될 일이다.

그런데 **View를 어떻게 업데이트할지 Cell이 굳이 알 필요**가 있을까?   
View의 배경색, 내용을 어떻게 바꿀지를 Cell이 굳이 알아야할까? 싶은 의문이 들었다.

그래서 View가 상태에 따라 어떻게 바뀔 지는 View에 구현을 해두고   
Cell은 View의 상태만 바꿔주도록 구현해보았다.

```swift
class StatefulImageView<T: Hashable>: UIImageView {
  private var stateImages: [T: UIImage] = [:]

  var currentState: T? {
    didSet {
      updateImageForCurrentState()
    }
  }

  func setImage(_ image: UIImage?, for state: T) {
    stateImages[state] = image
  }

  private func updateImageForCurrentState() {
    if let state = currentState, let img = stateImages[state] {
      self.image = img.withRenderingMode(.alwaysTemplate)
    }
  }
}
```

우선 제네릭을 활용해서 외부에서 상태에 대한 열거형을 주입 받도록 했음.   
이렇게 하면 재사용성을 높여볼 수 있다.

그리고 상태를 저장하는 `currentState` 변수,   
상태 별 Content를 저장하는 `stateImages` 딕셔너리를 구현했다.

왜 딕셔너리냐면, 상태에 따른 content를 `key: value` 형태로 저장하기 용이한 자료구조이기도 하고   
무엇보다 특정 상태에 저장된 Value가 없더라도 Optional 형태로 반환해서 **런타임 에러를 방지**할 수 있다..

`setImage` 메서드를 통해서 상태 별 이미지를 딕셔너리에 저장하고
`updateImageForCurrentState` 메서드를 통해서 현재 상태에 따라 뷰의 image를 업데이트한다.

Cell에서는 이걸 이렇게 써볼 수 있음

```swift

final class CustomCell: UITableViewCell {
  ...
  private lazy var progressImageView: StatefulImageView = {
    let view = StatefulImageView<JobHuntProgress>()
    view.setImage(UIImage(named: "image1"), for:  .notReviewed)
    view.setImage(UIImage(named: "image2"), for:  .reviewed)
    ...
    return view
  }()

  func 대충_셀_상태_업데이트_메소드( 대충 파라미터 ) {
    // 대충 업데이트 로직
    progressImageView.currentState = ?
  }
}
```

위와 같은 메커니즘으로 Button이나 다른 뷰에도 적용해볼 수 있음.   
근데 업데이트할 UI 속성이 많아질 수록 그에 맞게 업데이트 메소드도 일일히 구현해줘야하는 단점이 있다..
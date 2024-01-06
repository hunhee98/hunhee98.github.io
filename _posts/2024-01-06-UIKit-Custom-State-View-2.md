---
layout: post
title: "[UIKit] 프로토콜을 통해 상태 설정이 가능한 View를 만들어보자"
date: 2024-01-06 22:00 +0900
tags:
  - UIKit
  - CustomState
categories:
  - iOS
---
예전에 한 번 다뤘던 주제인데 보다 발전된 형태를 갖추게 되어 새롭게 글을 작성한다.   
이전 글 [[iOS] View에 Custom State를 만들어보자](https://walkerhilla.github.io/posts/UIKit-Custom-State-View/)

거두절미하고 본론부터 들어가자면   
본 주제는 뷰를 생성할 때 뷰의 상태를 정의하고, 상태에 따른 뷰의 설정 값을 미리 초기화하여
외부에서는 뷰의 상태만 변경하고, 뷰를 어떻게 바꿀 지는 모르도록 해보자 라는 아이디어에서 시작되었다.

그래서 이전 글에서는 `제네릭`을 활용하여 아래의 결과물이 나왔다.
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

`StatefulImageView`를 생성할 때 제네릭 문법을 통해서 상태를 열거한 열거형을 주입하고   
`setImage` 메서드로 상태에 따라 보여질 이미지를 미리 초기화해서 뷰 내부에서는 이를 딕셔너리 형태로 보관한다.   
외부에서는 `currentState` 를 변경하면 딕셔너리에서 해당 상태에 등록된 이미지를 표시한다.

당시에는 음,, 나름 잘 만들었는데? 라고 생각했지만, 추상화가 제대로 되지 않아 여러 단점이 존재했다.
1. 뷰의 종류마다 동일한 로직의 클래스를 만들어주어야 한다. UIButton, UILabel.. 등
2. 상태에 영향을 받는 property 마다 딕셔너리와 set, update 를 만들어주어야 한다.
3. StatefulImageView를 상속 받아 커스텀해야하는 상황이 발생한다.

최근 프로젝트를 진행하면서 예전에 만들어두었던 StatefulView 아이디어를 재사용하면서 추상화를 한 단계 더 거쳐보았다.
우선 제네릭과 상속 대신 프로토콜을 활용했다. 프로토콜을 선택한 이유는 외부에서 상태에 대한 열거형을 주입해주어야하는 제네릭 문법 대신, associatedType을 사용하면 더 자연스러운 캡슐화가 가능하다고 판단했다. 또한, 인터페이스를 준수하면서도 커스텀 로직을 둘 수 있어 상속 계층을 지니지 않아 더 깔끔하다고 느꼈다.

그래서 `StateConfigurable` 라고 프로토콜 이름을 지어보았음.   
어떤 뷰든 이 프로토콜을 준수하면 상태가 설정 가능한 뷰라는 의미를 전달하고 싶었다.

```swift
protocol StateConfigurable: UIView {
  associatedtype State: Hashable
  associatedtype Configuration

  var configurations: [State: Configuration] { get set }
  var currentState: State? { get set }

  func setState(_ config: Configuration, for state: State) -> Void
  func updateForCurrentState()
}
  

extension StateConfigurable {
  func setState(_ config: Configuration, for state: State) {
    self.configurations[state] = config
  }
}
```

사실 로직 자체는 이전과 99% 유사함. 다만, 상태에 영향을 받는 설정 값들도 추상화하여 프로토콜을 준수하는 객체가 Configuration이라는 타입으로 정의하게 만들었다.   
그래서 이 프로토콜을 준수하는 객체는 설정 값을 저장하는 구조체를 정의해서 사용해야 한다.


```swift
class RoundButton: UIControl, StateConfigurable {
  // MARK: - StateConfigurable Property
  var configurations: [State : Configuration] = [:]

  var currentState: State? {
    didSet {
      updateForCurrentState()
    }
  }
  
  enum State {
    case enabled
    case disabled
  }

  struct Configuration {
    let backgroundColor: UIColor
    let isEnabled: Bool

    init(backgroundColor: UIColor, isEnabled: Bool) {
      self.backgroundColor = backgroundColor
      self.isEnabled = isEnabled
    }
  }

  func updateForCurrentState() {
    guard let currentState,
          let config = configurations[currentState] else { return }
    backgroundColor = config.backgroundColor
    isEnabled = config.isEnabled
  }
}
```

별다른 코드 설명은 따로 않겠다. 외부에서는 이렇게 사용할 수 있음

```swift
 let button: RoundButton = RoundButton().then {
    typealias Configuration = RoundButton.Configuration

    let enabledConfig = Configuration(backgroundColor: SystemColor.primaryNormal.uiColor, isEnabled: true)
    let disabledConfig = Configuration(backgroundColor: SystemColor.gray300.uiColor, isEnabled: false)

    $0.setState(enabledConfig, for: .enabled)
    $0.setState(disabledConfig, for: .disabled)
  }
```

프로토콜을 준수하는 뷰 내부에서 상태 열거형, 상태에 영향을 받는 값을 저장하는 구조체를 정의하고   
뷰를 생성할 때는 상태에 따른 Configuration을 생성하여 설정한다.

그럼 외부에서는 버튼의 상태를 바꾸고 싶을 때 button.currentState를 변경하기만 할 뿐, 뷰를 어떻게 바꿀지까지 관여할 필요가 없게 된다.   
아 이미,, 생성할 때 설정해주니까 관여하는건가?... ㅋㅋㅋ
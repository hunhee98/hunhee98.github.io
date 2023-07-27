---
layout: post
title: "Swift 반복문 for-in과 forEach의 차이"
date: 2023-07-27 17:10 +0900
categories:
- Swift
---
swift에서 흔히 사용하는 **`for-in문`** 과 **`forEach문`** 은 겉보기에는 둘 다 **`반복문`** 처럼 동작하지만 사실 **forEach는 반복문이 아니다.**  가장 큰 차이점은 반복문 제어 가능 여부인데, 우선 for-in문과 forEach문이 기본적으로 어떻게 사용하고 어떻게 동작하는지 살펴보자

### for-in 반복문

[Swift 공식문서 Control Flow](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/controlflow) 의 설명을 인용하자면 배열, 숫자 범위, 문자열에 대해 반복하면서 요소에 접근할 수 있다. 
코드로 직접 확인해보자.

```swift

// 숫자 범위에 대한 for-in
for number in 1...5 { 
  print(number)
}

// print 1
// print 2
// print 3
// print 4
// print 5

// 배열에 대한 for-in
let names = ["Steve", "Jack", "Anna", "Brady"]
for name in names {
  print(name)
}

// print Steve
// print Jack
// print Anna
// print Brady

// 문자열에 대한 for-in
for char in "가나다라마" {
  print(char)
}

// print 가
// print 나
// print 다
// print 라
// print 마
```

for in문은 간단하게 말하면 `for 요소 in 시퀀스 { }` 구조다.  **`시퀀스`** 의 요소를 순서대로 꺼내서 for in문 블럭 내부에서 사용할 수 있다. 이때 for in문은 반복문이기 때문에 continue, break와 같은 반복문 제어 구문 역시 사용 가능하다.

```swift

for number in 1...5 {
  if number == 3 { break }
  print(number)
}

// print 1
// print 2

for number in 1...10 {
  if number % 2 == 0 { continue }
  print(number)
}

// print 1
// print 3
// print 5
// print 7
// print 9
```

break를 만나면 반복문은 그 즉시 탈출하여 종료된다. continue를 만날 경우엔 그 아래 코드를 실행하지 않고 다음 반복 주기로 건너뛰게 된다.

### forEach 클로져

forEach 역시 [Swift 공식문서 forEach](https://developer.apple.com/documentation/swift/array/foreach(_:))를 살펴보면 시퀀스의 각 요소에 대해 클로저를 반복문처럼 순서대로 호출한다. 라고 발번역을 해볼 수 있을 것 같다. 바로 코드부터 살펴보자

```swift

let numberWords = ["one", "two", "three"]

numberWods.forEach { word in
  print(word)
}

// print one
// print two
// print three
```

자 여기까지만 보면 위에서 살펴본 for in 반복문과 큰 차이가 없어보인다. 하지만, **forEach는 반복문이 아닌 클로져다.**  클로져가 뭔지 모른다면 그냥 함수라고 생각하면 된다. 함수에서는 **`continue`** 와 **`break`** 와 같은 반복문 제어 구문을 사용할 수 없다. 

### for in과 forEach의 차이

여기서 의문점이 들었다. forEach가 클로져라면, **`return`** 키워드를 통해 클로져를 탈출할 수 있지 않을까? 그렇다면 함수 안에서 for in문과 forEach를 사용했을 때 각각 내부에서 return 키워드를 사용하면 다르게 동작하지 않을까?

```swift

func forInTest() {
  for i in 1...5 {
    if i == 3 { return }
    print(i)
  }
  print("forInTest가 끝까지 실행되었습니다.")
}

forInTest()

// print 1
// print 2

func forEachTest() {
  (1...5).forEach { i in
    if i == 3 { return }
    print(i)
  }
  print("forEachTest가 끝까지 실행되었습니다.")
}

forEachTest()

// print 1
// print 2
// print 4
// print 5
// print forEachTest가 끝까지 실행되었습니다.
```

실제로 for in문과 forEach를 함수 안에 넣고 return 키워드를 사용했을 때 예상했던 것처럼 다르게 동작하였다.
for in문은 반복문이기 때문에 내부에서 return 키워드를 사용할 경우 **`forInText`** 함수를 탈출하였지만, forEach는 이 역시 함수이기 때문에 자기 자신을 탈출할 뿐 상위 Scope인 **`forEachTest`** 의 동작에는 아무런 영향을 미치지 않는다.

for in과 forEach의 내부적인 속도 차이가 존재할까? 라는 의문이 들었는데 관련된 자료를 찾기가 쉽지 않았다. 대신 알고리즘 문제를 풀이할 때 직접 겪었던 차이점이 있었다.

**문제:**
정수 `n` 이 주어질 때, n을 나누어 나머지가 1이 나오는 가장 작은 숫자 `s`를 구하시오
단, n은 3 이상 1,000,000 이하

**풀이:**

n을 나누었을 때 나머지가 1이 나오려면 s의 범위는 **2 ~ n-1**가 된다.
단순하게 2부터 n-1까지 반복하면서 n을 나누었을 때 나머지가 1이 되는 가장 작은 숫자를 반환해보자.

```swift
func solution(_ n: Int) -> Int {
  var answers: [Int] = []
  (2..<n).forEach { i in 
	if n % i == 1 {
		answers.append(i)
	}
  }
  return answers[0]
}

// forEach문은 2부터 n미만까지의 범위에 대해 순서대로 반복하면서 각 요소를 n에 나누었을 때 나머지가 1이 되는 숫자를 answers 배열에 추가한다. 그리고 answers의 첫번째 요소를 반환한다.
// 첫번째 요소 = 가장 먼저 저장된 숫자 = 제일 작으면서 n을 나누었을 때 나머지가 1이 나오는 숫자

func solution(_ n: Int) -> Int {
  var answer: Int = 0
  for i in 2..<n {
	if n % 1 == 1 {
	  answer = i
	  break
	}
  }
  return answer
}

// for in문 역시 2부터 n미만까지 순서대로 반복하면서 각 요소를 n에 나눈다.
// 이때 나머지가 1이 나오는 숫자가 있을 경우 이 숫자를 answer에 저장하면서 반복문을 종료한다.
```

위 알고리즘 문제에서는 n의 범위가 클수록 forEach문이 더 많이 반복하게 된다. 정답은 이미 찾았음에도 시퀀스의 요소 수만큼 반복해야하기 때문이다. 반면에 for in문은 반복문 제어를 통해서 도중에 반복문을 탈출하여 불필요한 동작을 최소화할 수 있다.
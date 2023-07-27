---
layout: post
title: "ViewController에 Static TableView Cell 사용하기"
date: 2023-07-27 20:22 +0900
tags:
- UIKit
- TableView
categories:
- iOS
---
스토리보드에서 Static Table View를 UIViewController에 그냥 넣을 경우 **`static table views are only valid when embedded in uitableviewcontroller instances`** 라는 에러가 발생한다. 

![](https://i.imgur.com/Zzjtgma.png)

번역해보자면 `Static Table View`는 오로지 **UITableViewController에 임베드**된 경우에만 쓸 수 있다! 그렇다면 Static Table View를 어떻게 `UITableViewController`에 임베드하는지 알아보자.

우선 스토리보드에서 `UIViewController`를 하나 추가한다. 기존에 만들어둔 게 있다면 내부에 추가한 뷰를 모두 삭제하자. 그리고 `ContainerView`를 UIViewController에 추가한다.

![](https://i.imgur.com/1q8tcCR.png)

그러면 위와 같이 자동으로 임베드된 뷰 컨트롤러와 `Segue` 가 있을텐데 이거 둘 다 지우자. 그리고 UITableViewController를 하나 만들어서 임베드하면 된다.

![](https://i.imgur.com/jH277Zy.gif)

이제 임베드된 UITableViewController 내부에 있는 table View의 Content를 `Static Cells` 로 바꿔서 쓰면 된다.
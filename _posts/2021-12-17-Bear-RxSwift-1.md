---
layout: post
title: RxSwift 
subtitle: RxSwift가 왜 필요할까?
categories: 곰튀김의 RxSwift 4시간 끝내기
tags: [RxSwift]
---

**ReactiveX** - 마이크로소프트에서 처음 만들었다. 적용법이 비슷해서 RxSwift를 알면 다른 언어들에서도 쉽게 응용이 가능하다. 

처음 과제는 비동기적으로 인디케이터와 Json Data, Timer 등이 함께 움직이게 하는 것이다.
![image](https://user-images.githubusercontent.com/76729543/146561997-6af1aafc-46d7-416d-a7e3-202b0ca11417.png)
![image](https://user-images.githubusercontent.com/76729543/146562009-b1068803-f33f-4e1a-a7d1-907194a1c51a.png)

DispatchQueue와 Escaping Handler를 통한 Swift 내에서의 비동기(async) 구동 방식은 대략 이렇다.

```swift
func downloadJson(listUrl: String, downloadJsonHandler: @escaping (String?) -> ()) {
        DispatchQueue.global().async {
            let url = URL(string: listUrl)!
            let data = try! Data(contentsOf: url)
            let json = String(data: data, encoding: .utf8)
            DispatchQueue.main.async {
                downloadJsonHandler(json)
            }
        }
    }

@IBAction func onLoad() {
        editView.text = ""
        setVisibleWithAnimation(activityIndicator, true)
        downloadJson(listUrl: MEMBER_LIST_URL) { json in
            self.editView.text = json
            self.setVisibleWithAnimation(self.activityIndicator, false)
        }
    }
```

하지만 이런 식으로 구현을 했을 때 중첩된 코드들, 계속된 들여쓰기는 코드 가독성을 떨어뜨린다. 

> 그래서 Rx의 핵심은 `downloadJsonHandler: @escaping (String?) -> ()` 를 그냥 `String?`으로 받아버릴 수는 없을까?


# [RxCocoa] RxSwift + RxCocoa Useful tips

RxSwift + RxCocoa 사용할 경우, 유용하게 사용할 수 있는 간단한 함수들 정리

## .bindNext

UIButton Tap의 이벤트를 받을 때마다 함수 실행하게 해주는 sugar.

```swift
func reload() {

}

func setup() {
    reloadButton.rx.tap
        //.subscribe(onNext: { [weak self] in self?.reload()})
        .bindNext(reload)
        .disposed(by: disposeBag)
}
```

실제로 RxSwift로 버튼을 다음과 구현한다면, 탭의 리로드할 때 함수가 여러번 불리게 될 수 있기 때문에 다음과 같이 작성하여야 한다. 

```swift 
func reload() {

}

func setup() {
    reloadButton.rx.tap
        .debounce(0.3, scheduler: MainScheduler.instance)
        //debounce 함수로 일정 시간 안에 불리는 것은 무시할 수도 있습니다
        .map { !$0 }
        .do(onNext: {
            print("Reload Button Tapped")
            Analytics.buttonReload.send()
            //중간에 이벤트에 대한 로그를 넣을 수도 있다.
        })
        .bindNext(reload)
        .disposed(by: disposeBag)
}
```

UI 말고도 앱에서는 네트워크 이벤트를 많이 사용하는데, 동기적으로 사용하면 UI가 블럭되거나, 스레드에서 처리하더라도 UI로 넘어갈 때 괴로움을 많이 겪게 됩니다. 이런 어려움도 RxSwift + .bindNext로 해결할 수 있다.

```swift
func reload() {
    API.default.request(.getPage)
        .map { json in Page(json: json)}
        .observe(on: MainScheduler.instance)
        //.subscribe(onNext: { [weak self] page in
        // self?.display(page: page) 
        //})
        .bindNext(display)
        .disposed(by: disposeBag)
}
```

getPage라는 API 요청을 보내면 JSON 형태의 옵저버블이 나오는 예제. map으로 JSON을 Page로 변환하는 체이닝 메서드를 붙이고, 구독을 해서 Page를 이용해서 디스플레이를 하는 로직을 붙일 수 있다. 이 코드를 더 swift를 통해 축약을 하면 다음과 같이 만들 수 있다.

```swift
func reload() {
    API.default.request(.getPage)
        .map { Page(json: $0)}
        .observe(on: MainScheduler.instance)
        .bindNext(display)
        .disposed(by: disposeBag)
}
```

## replacingOccurrences

String 형식의 event 중에서 원하는 문자를 바꿀 수 있다.

```swift
Observable.Just|(“800x600”)
    .map{$0.replacingOccurrences(9f: “x”, with: “/“)} // 800/600
    .subscribeOn(ConcurrentDispatchQueueScheduler(sos: .default)) 
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: self.imageView.image = $0 }
```


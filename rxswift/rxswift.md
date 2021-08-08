# [RxSwift] RxSwift Basic

배워도 배워도 헷갈리는 RxSwift 이번에는 정복하자.

iOS에서 비동기 프로그래밍이 필요한 순간들

- 버튼을 눌렀을 때의 반응
- 텍스트필드에 포커스가 잡힌 경우
- 인터넷에서 크기가 큰 이미지 파일을 받는 경우
- 디스크에 데이터를 저장하는 경우
- 오디오를 실행하는 경우

 iOS프로그래밍에서 UIKit을 통해 비동기를 사용했던 순간

- NotificationCenter : 백그라운드 진입 후 몇 초 있다가 메시지 알림
- The delegate pattern : tableView의 didSelectRowAt과 같은 메소드
- closure

예시 코드

```swift
func downloadJson(_ url: String, _ completion: @escaping (String?) -> Void) {
    // 비동기 처리방식이기 때문에 @escaping을 사용해주어야 한다. 그렇지 않을 경우, 오류. 
    // 밑의 return 해야 할 값과 처리시간이 다르기 떄문에, @escaping을 사용
    DispatchQueue.global().async {
        let url = URL(string: url)!
        let data = try! Data(contentsOf: url)
        let json = String(data: data, encoding: .utf8)
        //비동기 방식으로 함수 구현시, return 값으로 구현할 수 없기 떄문에
        //지금까지 completion을 이용하였다. 
        DispatchQueue.main.async {
            completion(json)
        }
    }
}

@IBAction func onLoad() {
    editView.text = ""
    setVisibleWithAnimation(activityIndicator, true)
    
    downloadJson(MEMBER_LIST_URL, { (json) in
        self.editView.text = json
        self.setVisibleWithAnimation(self.activityIndicator, false)
    })
    //이런 방법으로 구현시, 비동기식 작업이 추가되면 코드가 더러워진다. 
}
```

completion 방식이 아닌 return 방식으로 구현하면 코드가 더 간결하고 좋아질텐데?

해서 생긴 방법이 RxSwift를 비롯한 여러가지 유틸리티.

Declarative code, Reactive system와 같은 수 많은 장점이 있음.

RxSwift를 이용해서 비동기식으로 구현 해보면

```swift
func downloadJson(_ url: String) -> Observable<String?> {
    // 비동기로 생기는 데이터를 Observable로 감싸서 리턴
    return Observable.create() { f in
    // 관찰하고 있다가 나중에 데이터가 생기면 
        DispatchQueue.global().async {
            //이런 동작을 해줘 
            let url = URL(string: url)!
            let data = try! Data(contentsOf: url)
            let json = String(data: data, encoding: .utf8)
            
            DispatchQueue.main.async {
                // 이벤트 결과에 따라 이런 동작을 해줘 
                f.onNext(json)
                f.onCompleted()
                //onCompleted를 해주지 않으면 클로져가 사라지지 않기 떄문에
                //순환참조 문제로 .subscribe [weak self]
                //를 해주어야 한다.
            }
        }
        return Disposables.create()
    }
    
}

@IBAction func onLoad() {
    editView.text = ""
    setVisibleWithAnimation(activityIndicator, true)
    
    downloadJson(MEMBER_LIST_URL)
    //Observable로 오는 데이터를 받아서 처리하는 방법
        .subscribe { event in
        //.subscribe를 해주면 이벤트 결과에 따라 처리해줄 수 있다.
            switch event {
            case let .next(json):
                self.editView.text = json
                self.setVisibleWithAnimation(self.activityIndicator, false)
                
            case .completed:
                break
            case .error:
                break
            }
        }
}

```

이렇게 구현할 수 있고 더 제대로 구현하면

(순환참조에 관해 더 알고 싶다면 [여기](https://iamchiwon.github.io/2018/08/13/closure-mem/))

```swift
func downloadJson(_ url: String) -> Observable<String?> {
    return Observable.create() { emitter in
        
        let url = URL(string: url)!
        
        let task =  URLSession.shared.dataTask(with: url) { (data, _, err) in
            guard err == nil else {
                emitter.onError(err)
                return
            }
            
            if let dat = data, let json = String(data: dat, encoding: .utf8) {
                emitter.onNext(json)
            }
            emitter.onCompleted()
        }
        
        task.resume()
        
        return Disposables.create() {
            task.cancel()
        }
    }
}
```

구현할 수 있다.
근데 이렇게 보면 코드가 더 간결해보이지는 않은데???

왜 다들 RxSwift를 좋아할까?

-> Sugar API를 이용하면 더 간결하고 강력하게 사용가능함

```swift
func sendHello() -> Observable<String?> {
    //return Observable.create { emitter in 
        //emitter.onNext("Hello World")
        //emiiter.onCompleted()
        //return Disposables.create()
    //}
    // 데이터 하나 보낼 때는 이거 다 쓰기 귀찮으니깐,
    // just 하나로 해결하자. 
    return Observable.just("Hello World")

    // from을 사용할 경우, array를 한번에 해결 가능
    return Observable.from(["Hello", "World"])
}

downloadJson(MEMBER_LIST_URL)
//    .subscribe { event in
//        switch event {
//        case let .next(json):
//            self.editView.text = json
//            self.setVisibleWithAnimation(self.activityIndicator, false)
//            
//        case .completed:
//            break
//        case .error:
//            break
//        }
//    }
// .next 경우만 쓰면 되는데 이렇게 다 써야할까?? 
// 귀찮은데 onNext의 경우만 써주자. 
    .subscribe(onNext: { 
        self.editView.text = json
        self.setVisibleWithAnimation(self.activityIndicator, false)
    })

```

그럼 sugar API를 이용해서 전의 코드를 더 간결하게 만들어보자.

```swift
@IBAction func onLoad() {
    editView.text = ""
    setVisibleWithAnimation(activityIndicator, true)
    
    downloadJson(MEMBER_LIST_URL)
        .observeOn(MainScheduler.instance)
        //Rxswift에서 Main async를 표현하는 방법; operator
        .subscribe(onNext: {
            self.editView.text = json
            self.setVisibleWithAnimation(self.activityIndicator, false)
        })
        .disposed(by: disposeBag
}
```

훨씬 깔끔하고 직관적으로 만들어진다.

```swift
var example: Observable<Int> = Observable.just(1000)
```

그런데 만약에 위와 같은 값을 꾸준히 업데이트 해야하고 값이 변함에 따라 UI가 변해야 하는 상황에서도
Rxswift를 사용가능할까?

Observable 외부에서는 값을 컨트롤 할 수 있을까?

이럴때 사용하는게 Subject

외부에서도 값을 변하게 할 수 있고 통제할 수 있다.

```swift
var example: PublishSubject<Int> = PublishSubject()

example
    .scan(0, accumulator: +)
    //전 값과 다음 값의 합을 리턴
    .subscribe(onNext: {
        self.examplelabel.text = $0
    })
    .disposed(by: disposeBag)

example.onNext(100)
```

RxSwift의 매력중 하나, 이렇게 구현하면

매번 값이 변함에 따라 updateUI()와 같은 함수를 만들어 줄 필요가 없다.

Rxswift가 알아서 해결해준다.

또 RxSwift과 MVVM 패턴과 찰떡 궁합인 이유를 보자

```swift
class MenuListViewModel {
    lazy var menuObservable = BehaviorSubject<[Menu]>(value: [])

    lazy var itemsCount = menuObservable.map {
        $0.map { $0.count }.reduce(0, +)
        //reduce를 이용하면 총합을 구할 수 있다. 
    }

    lazy var totalPrice = menuObservable.map {
        $0.map { $0.count * $0.price }.reduce(0, +)
    }

    init() {
        let menus: [Menu] = [
            Menu(name: "Apple", price: 100, count: 0),
            Menu(name: "Apple", price: 100, count: 0),
            Menu(name: "Apple", price: 100, count: 0),
            Menu(name: "Apple", price: 100, count: 0)
        ]

        menuObservable.onNext(menus)
    }
}
```

이렇게 [Menu]의 값이 추가되거나 수정되면 itemsCount와 totalPrice도 바뀌는 코드를 간단히 구현할 수 있다.

이렇게 편한 RxSwift를 RxCocoa를 이용하면 더 편해진다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    MenuListViewModel.menuObservable
        .bind(to: tableView.rx.items(cellIdentifier: cellId,
         cellType: MenuItemTableViewCell.self)) { index, item, cell in
         
            cell.title.text = item.name
            cell.price.text = "\(item.price)"
            cell.count.text = "\(item.count)"
         }
         .disposed(by: disposeBag)
        //이렇게 UITableDataSource를 쓰지 않아도 구현이 가능하다. 
        //tableview.register를 무조건 넣어줘야지 작동가능 

    MenuListViewModel.itemCount
        .map { "\($0)" }
        .bind(to: itemCountLabel.rx.text)
        //이 한 줄로 UI에 바로 삽입이 가능하다. 
        .disposed(by: disposeBag)
}
```

그 외 유용하게 사용하는 방법

디바이스를 가로로 할지, 세로로 할지 정하는 코드 

```swift
UIDevice.rx.orientation
  .subscribe(onNext: { current in
    switch current {
    case .landscape:
      // Re-arrange UI for landscape
    case .portrait:
      // Re-arrange UI for portrait
    }
  })
```

특정 상황의 경고문

```swift
 UIDevice.rx.orientation
  .filter { value in
    return value != .landscape
  }
  .map { _ in
    return "Portrait is the best!"
  }
  .subscribe(onNext: { string in
    showAlert(text: string)
  })
```

## References

[Awesome Swift Korean Lecture / RxSwift](https://github.com/ClintJang/awesome-swift-korean-lecture/blob/master/README.md#rxswift)

[RxSwift 알아보기(ReactiveX 에 대해서) - 01](https://magi82.github.io/ios-rxswift-01/)

[A Decision Tree of Observable Operators](http://reactivex.io/documentation/operators.html)

[A Decision Tree of Observable Operators(한국어)](http://reactivex.io/documentation/ko/operators.html)

[RxSwift를 이용하여 콜백 지옥 탈출하기](https://www.youtube.com/watch?v=jCT-eUaD-d4)

[RxSwift+MVVM 4시간에 끝내기](https://github.com/iamchiwon/RxSwift_In_4_Hours)

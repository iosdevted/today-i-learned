# [RxSwift] Share Operators

## share() 연산자를 사용해야하는 이유

MVVM설계

```swift
// ViewModel 정의
class SomeViewModel {
	let result = userTappedButton
    				.flatMapLatest {apiService.rx.getFriend() }
	name = result.map {$0.name}
    phone = result.map {$0.phone}
}

// ViewController에서 ViewModel과 UI연결
class {
	...
    viewModel.name
    	.bind(to: lblName.rx.text) // UI와 ViewModel간의 연결
        .disposed(by: bag)
        
    viewModel.phone
    	.bind(to: lblPhone.rx.text)
        .disposed(by: bag)
}
```

MVVM에서 연결된 결과

```
Button -> ViewModel -> API Service -> ViewModel -> (name, phone)
```

문제점 

subscriber1(name), subscriber2(phone)는 각각 각자의 stream을 사용하는 것

아래와같이 map연산자를 사용한다면, 더욱더 추가적인 2개의 독립 자원과 스트림이 필요

```swift
viewModel.name
	.map {$0.isEmpty}
	.bind(to: lblName.rx.text) // UI와 ViewModel간의 연결
	.disposed(by: bag)
        
viewModel.phone
	.map {$0.isEmpty}
   	.bind(to: lblPhone.rx.text)
	.disposed(by: bag)
```

## share() 연산자 사용하여 해결

```swift
class SomeViewModel{
	let result = userTappedButton
    				.flatMapLatest {apiService.rx.getFriend()}
                    .share() // subscriber들에게 스트림의 자원들을 공유
	name = result.map{$0.name}
    phone = result.map{$0.phone}
}
```

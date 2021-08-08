# [RxSwift] Subjects

(실제 앱 구동시) run time시에  Observable에 값을 추가하여 emit이 발생하게끔 해주는 대리인

Observable에 값을 추가하는 대상은 Observer라 부르며(추상적인 개념), 
Observable과 Observer 기능을 둘 다 하는 것이 바로 Subjects.

## PublishSubjects

empty상태로 시작, 새로운 이벤트만 subscriber에게 emit. 구독된 순간 새로운 이벤트 수신을 알리고 싶을때 사용. 시간에 민감한 데이터를 모델링 할 경우(실시간 경매 앱 - 10:00am 경매 시작일 경우, 10:01am에 접속했을 때 알림이 보내질 필요가 없는 경우)

```swift
example(of: "PublishSubject") {
 
     let subject = PublishSubject<String>()

     subject.onNext("Is anyone listening?")

     let subscriptionOne = subject
         .subscribe(onNext: { (string) in
             print(string)
         })

     subject.on(.next("1")) //Print: 1
     subject.onNext("2")    //Print: 2
 }
```

-> 출판사에서 "Is Anyone Listening?"이란 내용 받음(.onNext)

-> 구독(.subscribe)

-> 현재 벌어진 일만 emit함(.subscribe했을 경우 "Is anyone listening?"은 과거에 발생했던 일이라 emit하지 않음)

-> 뒤에 1과 2 이벤트 추가시(현재 벌어진 일), 이것들은 emit함

## BehaviorSubject

하나의 초기값으로 시작, 최신 값(.next)만 새로운 subscriber에게 emit. 뷰를 가장 최신의 데이터로 미리 채우기에 용이(유저 프로필 화면을 BehaviorSubject에 바인딩)

```swift
enum MyError: Error {
    case anError
}

    example(of: "BehaviorSubject") {

        // BehaviorSubject는 초기값이 필수 이므로 초기화값을 삽입
        let subject = BehaviorSubject(value: "Initial value")
        let disposeBag = DisposeBag()
        
        subject.onNext("X")

        subject
            .subscribe{
                print("1)", $0)
            }
            .disposed(by: disposeBag)
        // print : 1) next(X)
        
        subject.onError(MyError.anError)
        // print : 1) error(anError)
        
        subject
            .subscribe {
                print("2)", $0)
            }
            .disposed(by: disposeBag)
        // print : 2) error(anError)
    }
```

## ReplaySubject

버퍼 사이즈를 지정하며, 버퍼 사이즈만큼 새로운 subscriber에게 emit

```swift
example(of: "ReplaySubject") {

    let subject = ReplaySubject<String>.create(bufferSize: 2)
    let disposeBag = DisposeBag()

    subject.onNext("1")
    subject.onNext("2")
    subject.onNext("3")

    subject
        .subscribe {
            print("1)", $0)
        }
        .disposed(by: disposeBag)
    /* prints
     1) next(2)
     1) next(3)
     */
    
    subject
        .subscribe {
            print("2)", $0)
        }
        .disposed(by: disposeBag)
    /* prints
     2) next(2)
     2) next(3)
     */
    
    
    subject.onNext("4")
    /* prints
     1) next(4)
     2) next(4)
     */
                
    subject.onError(MyError.anError)
    /* prints
     1) error(anError)
     2) error(anError)
     */
//   subject.dispose()
    
    subject.subscribe {
        print("3)", $0)
        }
        .disposed(by: disposeBag)
    /* prints:
     3) next(3)
     3) next(4)
     3) error(anError)
     */
}
```
## PublishRelay, BehaviorRelay

 오직 .next 이벤트만 emit함 (.completed, .error무시, non-terminating에서 유용하게 사용)

- PublishRelay : PublishSubject를 단순히 wrap한 것이며 .next만 가능하고 기능 동일

- BehaviorRelay : BehaviorSubject를 단순히 wrap한 것이며 .next만 가능하고 기능 동일
realy의 핵심은 절대 끝나지 않음을 보장하는 것

```swift
import RxSwift
import RxCocoa // 필요

example(of: "PublishRelay") {
    let relay = PublishRelay<String>()
    
    let disposeBag = DisposeBag()
    
    relay.accept("Knock knock, anyone home?")
    
    relay
      .subscribe(onNext: {
        print($0)
      })
      .disposed(by: disposeBag)

    relay.accept("1") // print: 1
    
    // error와 onCompleted()사용 불가
//     relay.accept(MyError.anError)
//     relay.onCompleted()
    
}
```

- relay의 추가는 .accept()로 접근
- error, onCompleted() 사용 불가

```swift
import RxSwift
import RxCocoa

example(of: "BehaviorRelay") {

  let relay = BehaviorRelay(value: "Initial value")
  let disposeBag = DisposeBag()
  
  relay.accept("New initial value")
  
  relay
    .subscribe {
      print("1)", $0)
    }
    .disposed(by: disposeBag)
    // print : 1) next(New initial value)
    
    // 1
    relay.accept("1")
    // print : 1) next(1)
    
    // 2
    relay
      .subscribe {
        print("2)", $0)
      }
      .disposed(by: disposeBag)
    // print : 2) next(1)
    
    // 3
    relay.accept("2")
    /* prints
     2) next(2)
     */
}
```

.accept()로 다가가며 error, onCompleted를 못 쓴다는 것만 제외하면 BehaviorSubject와 동일


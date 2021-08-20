# [RxSwift] Filtering Operators

## Ignoring operators

### ignoreElements()

```swift
/// ignoreElements
example(of: "ignoreElements") {
  // 1
  let strikes = PublishSubject<String>()

  let disposeBag = DisposeBag()

  // 2
  strikes
    .ignoreElements()
    .subscribe { _ in
      print("You're out!")
    }
    .disposed(by: disposeBag)

    strikes.onNext("X") // print : none of print
    strikes.onCompleted() // print : You're out!
}
```

### elementAt()

입력한 인덱스 번호만 이벤트 수신

```swift
example(of: "elementAt") {
    
    let strikes = PublishSubject<String>()
    
    let disposeBag = DisposeBag()
    
    strikes
        .elementAt(2) // 인덱스로 2번(3번째 추가한 아이템 선택)
        .subscribe(onNext: { item in
            print(item)
        })
        .disposed(by: disposeBag)
    
    strikes.onNext("first")
    strikes.onNext("second")
    strikes.onNext("third") // print : third
}
```

### filter

emit할 값에 대한 조건을 명시적으로 설정

```swift
example(of: "filter") {
  let disposeBag = DisposeBag()

  Observable.of(1, 2, 3, 4, 5, 6)

    .filter { $0 % 2 == 0 }

    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

/* prints
 2
 4
 6
 */
```

## Skipping operators

### skip(_:)

입력한 갯수만큼 이벤트를 skip

```swift
example(of: "skip") {
  let disposeBag = DisposeBag()

  Observable.of("A", "B", "C", "D", "E", "F")

    .skip(3)
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

/* prints
 D
 E
 F
 */
```

### skipWhile

- 조건에 만족하는 것을 skip, 조건이 맞는 원소가 발견되고 그 값이 연속적이면 계속 skip, 단 1회성(휘발성)

 - 아래 예제에서 최초의 값이 2로 나누어 떨어지지 않으면, 필터링 하지 않고 모두 emit

```swift
example(of: "skipWhile") {
    let disposeBag = DisposeBag()
    
    Observable.of(2, 2, 2, 3, 4, 4, 2)
        
        .skipWhile { $0 % 2 == 0 }
        .subscribe(onNext: {
            print($0)
        })
        .disposed(by: disposeBag)
}

/* prints
 3
 4
 4
 2
 */
```

### skipUntil

또 다른 subject를 두어서, 이 이벤트가 발생하기 전까지 event들을 skip

```swift
example(of: "skipUntil") {
  let disposeBag = DisposeBag()

  let subject = PublishSubject<String>()
  let trigger = PublishSubject<String>()

  subject
    .skipUntil(trigger)
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
    
    subject.onNext("A")
    subject.onNext("B")
    trigger.onNext("X")
    subject.onNext("C")
    // print: C
}
```

## Taking operators

### take(_:)

입력한 갯수만큼만 이벤트 허용

```swift
example(of: "take") {
  let disposeBag = DisposeBag()

  // 1
  Observable.of(1, 2, 3, 4, 5, 6)
    // 2
    .take(3)
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

/* prints
  1
  2
  3
*/
```

### takeWhile(_:)

특정 조건까지 emit

```swift
example(of: "takeWhile") {
  let disposeBag = DisposeBag()

  Observable.of(2, 2, 4, 4, 6, 6, 2)

    .enumerated()

    .takeWhile { index, integer in

      integer % 2 == 0 && index < 3
    }
	
    // 원소에 접근하는 중요한 작업
    .map { $0.element }

    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

/* prints
 2
 2
 4
 */
```

## Distinct operators

### distinctUntilChanged()

중복값 방지

```swift
example(of: "distinctUntilChanged") {
  let disposeBag = DisposeBag()

  // 1
  Observable.of("A", "A", "B", "B", "A")
    // 2
    .distinctUntilChanged()
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

/* prints
 A
 B
 A
 */
```


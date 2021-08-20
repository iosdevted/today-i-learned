# [RxSwift] Combining Operators & Triggers

## Prefixing and concatenating

### startWith(value)

- 기존 리스트에 첫 번째에 추가

```swift
example(of: "startWith") {
  // 1
  let numbers = Observable.of(2, 3, 4)
  
  // 2
  let observable = numbers.startWith(1)
  _ = observable.subscribe(onNext: { value in
    print(value)
    // 1, 2, 3, 4
  })
}
```

### concat([])

※ 단, Observable.contat()에 들어가는 것은 무조건 동일 타입이어야 함

```swift
example(of: "Observable.concat") {
  // 1
  let first = Observable.of(1, 2, 3)
  let second = Observable.of(4, 5, 6)
  
  // 2
  let observable = Observable.concat([first, second])
  
  observable.subscribe(onNext: { value in
    print(value)
  })
}
```

###  merge

두 개의 Observable Sequence를 하나의 Observable Sequence로 합침

```swift
example(of: "merge") {
  // 1
  let left = PublishSubject<String>()
  let right = PublishSubject<String>()
  
  // 2
  let source = Observable.of(left.asObservable(), right.asObservable())
  
  // 3
  let observable = source.merge()
  let disposable = observable.subscribe(onNext: { value in
    print(value)
  })
  
  // 4
  var leftValues = ["Berlin", "Munich", "Frankfurt"]
  var rightValues = ["Madrid", "Barcelona", "Valencia"]
  repeat {
      switch Bool.random() {
      case true where !leftValues.isEmpty:
          left.onNext("Left:  " + leftValues.removeFirst())
      case false where !rightValues.isEmpty:
          right.onNext("Right: " + rightValues.removeFirst())
      default:
          break
      }
  } while !leftValues.isEmpty || !rightValues.isEmpty
 
  // 5
  left.onCompleted()
  right.onCompleted()
}

/* prints
Left:  Berlin
Right: Madrid
Left:  Munich
Right: Barcelona
Left:  Frankfurt
Right: Valencia
*/
```

### zip

```swift
example(of: "zip") {
    enum Weather {
        case cloudy
        case sunny
    }
    let left: Observable<Weather> = Observable.of(.sunny, .cloudy, .cloudy, .sunny)
    let right = Observable.of("Lisbon", "Copenhagen", "London", "Madrid", "Vienna")
    let observable = Observable.zip(left, right) { weather, city in
        return "It's \(weather) in \(city)"
    }
    _ = observable.subscribe(onNext: { value in
        print(value)
    })
}

/* prints :
 It's sunny in Lisbon
 It's cloudy in Copenhagen
 It's cloudy in London
 It's sunny in Madrid
 */
```

## Triggers

특정 시점에 observable들을 받아들여야 하는 경우에 이용

### withLatestFrom

```swift
example(of: "withLatestFrom") {
  // 1
  let button = PublishSubject<Void>()
  let textField = PublishSubject<String>()
  
  // 2
  let observable = button.withLatestFrom(textField)
  _ = observable.subscribe(onNext: { value in
    print(value)
  })
  
  // 3
  textField.onNext("Par")
  textField.onNext("Pari")
  textField.onNext("Paris")
  button.onNext(())
  button.onNext(())
}

/*
Paris
Paris
*/
```

### sample

- withLatestFrom과 기능 동일, 단 1회성 emit

```swift
example(of: "sample") {
  // 1
  let button = PublishSubject<Void>()
  let textField = PublishSubject<String>()
  
  // 2
  let observable = button.sample(textField)
  _ = observable.subscribe(onNext: { value in
    print(value)
  })
  
  // 3
  textField.onNext("Par")
  textField.onNext("Pari")
  textField.onNext("Paris")
  button.onNext(())
  button.onNext(())
}

/*
Paris
*/
```

## switches

subscriber가 어떤 sequence의 이벤트를 수신할지 결정
 
### amb

- ambiguous(모호한) : 먼저 온 sequence만 구독

- server에서 대량의 데이터가 오는 경우 사용

```swift
example(of: "amb") {
  let left = PublishSubject<String>()
  let right = PublishSubject<String>()
  
  // 1
  let observable = left.amb(right)
  let disposable = observable.subscribe(onNext: { value in
    print(value)
  })
  
  // 2
  left.onNext("Lisbon")
  right.onNext("Copenhagen")
  left.onNext("London")
  left.onNext("Madrid")
  right.onNext("Vienna")
  
  left.onCompleted()
  right.onCompleted()
}

/* print
Lisbon
London
Madrid
*/
```

## sequence내의 요소들간 결합

higher-order function에서의 reduce와 기능 동일

### reduce(::)

```swift
example(of: "reduce") {
    let source = Observable.of(1, 3, 5, 7, 9)

    // 1
    let observable = source.reduce(0, accumulator: +)
    observable.subscribe(onNext: { print($0) } )
}
```

```swift
let observable2 = source.reduce(0, accumulator: { summary, newValue in
    return summary + newValue
})
observable2.subscribe(onNext: { print($0) })
```

### scan(_:accumulator:)

- reduce(::)와 같이 작동하지만 리턴값이 Observable이며 하나의 값으로 나오는게 아니고 누적되며 emit

```swift
 example(of: "scan") {
     let source = Observable.of(1, 3, 5, 7, 9)

     let observable = source.scan(0, accumulator: +)
     observable.subscribe(onNext: { print($0) })

     /* Prints:
      1
      4
      9
      16
      25
     */
 }
```
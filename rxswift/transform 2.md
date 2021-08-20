# [RxSwift] Transforming Operators

## Transforming elements

### toArray

- 개별 원소들 -> 하나의 배열로

```swift
example(of: "toArray") {
  let disposeBag = DisposeBag()

  Observable.of("A", "B", "C")

    .toArray()
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}

// prints : ["A", "B", "C"]
```

### map

이벤트를 mapping하여 주어진 식을 통해 새로 변경

```swift
example(of: "map") {
  let disposeBag = DisposeBag()

  // 1
  let formatter = NumberFormatter()
  formatter.numberStyle = .spellOut

  // 2
  Observable<Int>.of(123, 4, 56)
    // 3
    .map {
      formatter.string(for: $0) ?? ""
    }
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}
```

### enumerated and map

```swift 
example(of: "enumerated and map") {
  let disposeBag = DisposeBag()

  // 1
  Observable.of(1, 2, 3, 4, 5, 6)
    // 2
    .enumerated()
    // 3
    .map { index, integer in
      index > 2 ? integer * 2 : integer
    }
    // 4
    .subscribe(onNext: {
      print($0)
    })
    .disposed(by: disposeBag)
}
/* prints
 1
 2
 3
 8
 10
 12
 */
```

## Transforming inner observables

###  flatMap

각 하나의 sequence에서 각 event에 대해 sequence를 만든 후,

최종적으로 하나의 sequence로 만드는 것

map get value from stream and return another value of whatever type, result is Observable< whatever type >.

flatMap get value from stream and return an Observable of whatever type.

```swift 
struct Student {
    var score: BehaviorSubject<Int>
}

 example(of: "flatMap") {
     let disposeBag = DisposeBag()
     
     // 1
     let ryan = Student(score: BehaviorSubject(value: 80))
     let charlotte = Student(score: BehaviorSubject(value: 90))
     
     // 2
     let student = PublishSubject<Student>()
     
     // 3
     student
         .flatMap{
             $0.score
         }
         // 4
         .subscribe(onNext: {
             print($0)
         })
         .disposed(by: disposeBag)
     
     // 5
     student.onNext(ryan)    // Print: 80
     
     // 6
     ryan.score.onNext(85)   // Print: 80 85
     
     // 7
     student.onNext(charlotte)   // Print: 80 85 90
     
     // 8
     ryan.score.onNext(95)   // Print: 80 85 90 95
     
     // 9
     charlotte.score.onNext(100) // Print: 80 85 90 95 100
 }
```

```swift
func foo(_ number: Int) -> Observable<String> {
    return Observable.just(String(number))
}

Observable.just(1)
    .flatMap { (number) -> Observable<String> in
        return foo(number)
}
```
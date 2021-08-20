# [Swift] class didSet or struct didSet

```swift
class Foo {
    var items = [1,2,3,4,5]
    var bar: Int = 0 {
        didSet {
            items += [bar]
        }
    }
}

var test: Foo = Foo() {
    didSet {
        println("I want this to be printed after changing number in test")
    }
}

test.bar = 1 // Nothing happens
```

`didSet`이 reference type에 달려있을 떄

- `didSet` observer가 달려있는 애(test)는 class 인스턴스로 reference type임.
- reference type의 instance는 제자리에서 변할 수 있음 (in place)
- 즉, test자체를 바꾼게 아니라 test.number을 바꿨기 때문에 아무일도 일어나지 않음
- test = Foo() 이렇게 하면 didSet 동작

`didSet`이 value type에 달려있을 떄

- value type은 비록 그렇게 보일지라도 실제로는 제자리에서 변할 수 없음 (not mutable in place)
- value type의 instance를 변경했다고 하면, 다른 instance로 원래 instance를 변경하는 것임
- test.bar = 1 이어도 인스턴스가 변경되는 것이기 때문에 didSet 동작

```swift
@IBOutlet weak var modifyButton: UIButton! {
    didSet {
        modifyButton.layer.cornerRadius = 9
    }
}
```

Outlet의 didSet은 언제 trigger 되는가

- Outlet 프로퍼티는 class가 막 초기화 될 때는 nil 상태
- nib으로부터 object가 초기화 될 때에 값을 갖게 됨
- 모든 Outlet 이 nil이 아님을 확신할 수 있을때는 viewDidLoad 때
- 따라서 Outlet에 있는 didSet 옵저버는 viewDidLoad 바로 전에 호출
- 따라서 Outlet 프로퍼티 다룰 때 조심해야 함. 예를들어 prepareForeSegue때 접근하려고 하면 nil 받게 될 것

## References

[Is there a way to get didSet to work when changing a property in a class?](https://stackoverflow.com/questions/29453735/is-there-a-way-to-get-didset-to-work-when-changing-a-property-in-a-class)

[When/How - Outlet -> didSet](https://stackoverflow.com/questions/38197785/when-how-outlet-didset)

[[Swift] class와 struct에서 didSet의 차이](https://sujinnaljin.medium.com/swift-class와-struct에서-didset의-차이-f784e34ea33f)
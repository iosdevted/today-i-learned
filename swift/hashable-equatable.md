# [Swift] Hashable & Equatable

> Hashable; A type that provides an integer hash value.  
> 정수 Hash 값을 제공하는 타입의 프로토콜

- set 또는 dictionary의 key로 hashable을 준수하는 모든 타입을 사용할 수 있다.
- swift에서 dictionary는 Dictionary<Key Type, Value Type> 형태로 쓰인다.
- 이때 유일한 제약사항은 Key Type이 반드시 해시가능한 타입이어야 한다(Hashable)
- 즉, 그 자체로 유일하게 표현이 가능한 방법을 제공해야한다는 뜻 이다.
- swift의 기본타입(String, Int, Double…)은 기본적으로 해시가능한 것들로 dictionary의 key type으로 사용가능하다.
- 또한 swift의 enum(열거형) 또한 해시 가능하다.

표준 라이브러리의 많은 타입들 또한 hashable을 준수한다.

stirng, integer, floating-point, boolean values, set이 기본적으로 hash value를 제공하며(이때 set은 hashable한 것만 들어갈 수 있다) 자신만의 사용자 정의 타입도 hash가 가능할 수 있다. associated valuew없이 열거형(enum)을 정의하면, hashable 준수가 자동으로 적용되고, hash value 프로퍼티를 추가해 사용자 정의 타입에 hashable 준수를 추가할 수도 있다.

타입의 hash value 프로퍼티에 의해 제공되는 hash값은 동일하게 비교되는 임의의 두 인스턴스에 대해 동일한 정수이다.

즉, 같은 타입의 인스턴스인 a와 b의 경우 a==b이면 a.hashvalue == b.hashvalue가 된다는 것을 의미한다.
그러나 반대의 경우에는 true가 아닐 수도 있다. 동일한 hash값을 가진 두개의 인스턴스가 서로 동일할 필요는 없기 때문이다.

hash 값은 프로그램 실행에 따라 동일하지 않을 수 있다. 향후 실행에 사용할 hash값을 저장하지 않아야 한다

```swift
struct GridPoint {
    var x: Int
    var y: Int
}

extension GridPoint: Hashable {
    static func == (lhs: GridPoint, rhs: GridPoint) -> Bool {
        return lhs.x == rhs.x && lhs.y == rhs.y
    }

    func hash(into hasher: inout Hasher) {
        hasher.combine(x)
        hasher.combine(y)
    }
}
```

> Equatable; A type that can be compared for value equality. 

Equatable은 값의 비교가 가능함을 보장해주는 프로토콜이다. 이 프로토콜을 채택한 타입들은 == 연산자나 != 연산자를 사용해 값을 비교할 수 있다. Array에서 contains(_:) 메소드를 사용할 수 있는 것도 Array가 Equatable 프로토콜을 채택하고 있기 때문이다.

객체의 해시값이 같다는 사실은 두 객체가 같다는 것을 보장해주지 않는다. 따라서 Hashable 프로토콜은 Equatable 프로토콜을 상속하여 객체가 같기 위한 연산자를 정의해주는 것이다.

## Reference

[Zedd - Hashable](https://zeddios.tistory.com/498)

[지헤의 개발공부로그 - Hashable](https://www.zehye.kr/swift/2020/07/22/swift_hashable/)
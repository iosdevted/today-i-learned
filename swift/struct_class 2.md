# [Swift] Struct or Class

Inheritance is reserved for classes and this is one of the biggest differences. Only use classes when you need inheritance or when you should works with objective-C code.

## 공통점

- 프로퍼티 정의가 가능합니다.
- 메소드 정의가 가능합니다.
- initializer 정의가 가능합니다.
- extension이 가능합니다.
- protocol이 가능합니다.

## 차이점

- 구조체는 value type, 클래스는 reference type 입니다.
- 구조체는 상속이 불가능합니다.
- 구조체에서는 AnyObject로 타입캐스팅이 불가능합니다.
- 구조체는 생성자를 구현하지 않을 시 기본 initializer를 사용할 수 있습니다.
- 클래스는 reference counting으로 메모리 관리가 가능합니다.

## Structs

- Simpler
- Faster
- Deep copies
- True immutability
- No memory leaks
- Threadsafe

```swift
struct UserData {
    var name: String
    var age: Int
    mutating func updateUser(_ name: String, _ age: Int) {
		// 구조체는 value type이기 때문에 method 안에 property 변경이 불가능하다.
		// 그래서 mutating을 붙이면 해당 객체가 다시 생선되면서 변경이 가능하게 된다.
        self.name = name
        self.age = age
        
        print("이름 : \(name), 나이 : \(age)")
    }
}
var shark = UserData(name: "상어", age: 16)
print("이름 : \(shark.name), 나이 : \(shark.age)")
// 이름 : 상어, 나이 : 16
var candy = shark
candy.name = "캔디"
candy.age = 30
print("이름 : \(shark.name), 나이 : \(shark.age)")
// 이름 : 상어, 나이 : 16
print("이름 : \(candy.name), 나이 : \(candy.age)")
// 이름 : 캔디, 나이 : 30
shark.updateUser("띠용", 33)
// 이름 : 띠용, 나이 : 33
```

## Classes

- Has Inheritance
- Works with objective-C code

```swift
class Phone {
    var name: String
    var color: String
    
    // struct와 달리 class는 이니셜라이즈를 지정해야합니다.
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
    
    func updatePhone(name: String, color: String) {
        self.name = name
        self.color = color
        
        print("폰 : \(name), 색상 : \(color)")
    }
}
let iPhone8 = Phone(name: "iPhone8", color: "red")
print("폰 : \(iPhone8.name), 색상 : \(iPhone8.color)")
// 폰 : iPhone8, 색상 : red
var iPhoneXs = iPhone8
iPhoneXs.name = "iPhoneXs"
iPhoneXs.color = "grey"
print("폰 : \(iPhone8.name), 색상 : \(iPhone8.color)")
// 폰 : iPhoneXs, 색상 : grey
//Reference Type은 이렇게 된다
print("폰 : \(iPhoneXs.name), 색상 : \(iPhoneXs.color)")
// 폰 : iPhoneXs, 색상 : grey
iPhoneXs.updatePhone(name: "iPhoneX", color: "black")
// 폰 : iPhoneX, 색상 : black
```

## References

[https://sujinnaljin.medium.com/swift-class-func-vs-static-func-7e6feb264147](https://sujinnaljin.medium.com/swift-class-func-vs-static-func-7e6feb264147)
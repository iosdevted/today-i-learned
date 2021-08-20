# [Swift] Initialization

let b = Boo()가 있다면 사실 원래의 의미는 "let b = Boo.init()"

## init의 존재 이유

모든 저장 프로퍼티들은 초기화 되어야하는데 init 키위드가 이것을 도와줌

```swift
class Test{
    var a:Int! // nil로 초기화
    var b:Int? // nil로 초기화
    var c: Int // init으로 초기화
    var d: Int // error
    
    init(tmp: Int){
        self.c = tmp
    }    
}
```

d변수는 초기화 되지 않아서 error

## 초기화의 종류

### super.init()

연쇄적인 초기화

```swift
class A{
    var a: Int
    
    init(value: Int) {
        self.a = value
    }
}
 
class B: A{
    init(value1: Int, value2: Int) {
        super.init(value: value1)
    }
}
 
class C: B {
    init(value1: Int, value2: Int, value3: Int) {
        super.init(value1: value1, value2: value2)
    }
}

```

### Designated Initializer

서브 클래싱시 꼭 초기화 해줘야 하는 메소드 : init(...)으로 작성

required init은 서브클래스에서 super.init이 아닌, 직접 구현해야 하는 초기화메소드

Write the required modifier before the definition of a class initializer to indicate that every subclass of the class must implement that initializer.

You must also write the required modifier before every subclass implementation of a required initializer, to indicate that the initializer requirement applies to further subclasses in the chain. You do not write the override modifier when overriding a required designated initializer.

UIView, UIViewController를 서브클래싱하는 코드를 작성하면서 init(frame:)을 오버라이딩하는 코드를 작성하면, Xcode는 매번 init?(coder:)가 정의되지 않았다는 컴파일 에러를 낸다. 일단 이 에러를 해결하려면 간단하게 부모 클래스의 이니셜라이저를 그대로 호출해주기만 하면 된다.

```swift
required override init?(coder aDecoder: NSCoder!) {
    super.init(coder: aDecoder)
}
```

이 init(coder:)는 NSCoding이라는 프로토콜에 정의되어 있다. 이 프로토콜은 표준 키 기반 아카이브를 통해서 직렬화가능한 클래스에 적용되는데, UI를 구성할 수 있는, 그러니까 nib 파일에 들어갈 수 있는 모든 클래스는 이 프로토콜을 따르고 있다. 그런데 이때 요 이니셜라이저는 conveience가 붙지 않은, 지정 이니셜라이저이다. 그리고 또 한가지, Swift는 기본적으로 서브클래스가 수퍼클래스의 이니셜라이저들을 상속받지 않은 것을 원칙으로 하고 특별한 경우에 자동 상속을 허용한다. 이니셜라이저의 자동상속은 다음의 조건을 만족해야 한다.

1. 만약 자식 클래스에서 추가된 저장 프로퍼티가 모두 디폴트 값을 가지고 있고, 추가적인 지정 이니셜라이저를 작성하지 않았다면(여기서는 추가되는 지정이니셜라저 혹은 지정이니셜라이저의 오버라이드가 모두 포함된다.) 부모 클래스의 모든 지정 이니셜라이저를 상속받는다.
2. 부모의 이니셜라이저를 상속받았거나, 아니면 부모의 모든 지정 이니셜라이저를 오버라이드했다면 부모의 편의 이니셜라이저를 자동으로 상속받는다.

여기서 문제 상황은 2번이다. init(frame:)을 오버라이드했다는 것은 부모의 일부 지정 이니셜라이저를 오버라이드했다는 것이다. 이 경우에는 이니셜라이저 상속을 포기한 것이 되고, init(coder:) 는 자동으로 상속되지 않기 때문에 수동으로 직접 작성해야 한다.

```swift
class A{
    var a: Int
    
    init(value: Int) {
        self.a = value
    }
    
    required init (val1: Int, val2: Int, val3: String){
        self.a = val1
    }
}
 
class B: A{
    required init(val1: Int, val2: Int, val3: String) {
        super.init(value: val1)
    }
}

```

### Convenience Initializer

```swift
class Person {
    var name: String
    var age: Int
    var gender: String
    init(name: String, age: Int, gender: String) {
        self.name = name
        self.age = age
        self.gender = gender
    }
    convenience init(age: Int, gender: String) {
        self.init(name: "temp", age: age, gender: gender)
    }
}
```

파라미터로 넘겨주지 않으 값은 내가 임의로 지정해줄 수 있다.


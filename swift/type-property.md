# [Swift] Type Property

Instance Property: 특정 Instance에 속한 Property를 말한다. 이 Property는 새로운 Instance가
생성 될 때마다 새로운 Property도 같이 생성된다.

Type Property: 특정 타입에 속한 Property로 그 타입에 해당하는 단 하나의 Property만 생성된다.
이 타입 프로퍼티는 특정 타입의 모든 인스턴스에 공통으로 사용되는 값을 정의할때 유용하다.

타입 프로퍼티를 선언을 위해서는 `static` 키워드를 사용한다.

static 키워드를 사용한 변수는 클래스가 메모리에 올라갈 때 자동으로 생성이 된다. 즉, 인스턴스(객체) 생성 없이 바로 사용가능 하다. 
그러므로, 객체를 생성하지 않아도 되니까 편리하고 속도도 빠르다.

하지만 클래스에서는 `static`과 `class` 이렇게 2가지 형태로 타입 프로퍼티를 선언할 수 있는데
두 가지 경우의 차이는 class로 선언된 프로퍼트는 서브클래스에서 오버라이드 가능.

```swift
struct AudioChannel {
    static let threholdlevel = 10
    static var maxInputLevelForAllChannels = 0

    var currentLevel: Int = 0 {
        didSet {
            if currentLevel > AudioChannel.thresholdLevel {
                currentLevel = AudioChannel.thresholdLevel
            }
            if currentLevel > AudioChannel.maxInputLevelForAllChannels {
                AudioChannel.maxInputLevelForAllChannels = currentLevel
            }
        }
    }
}
```

인스턴스 프로퍼티는 인스턴스 생성할 때마다 초깃값에 해당하는 값이 프로퍼티의 값이 되고,
인스턴스 마다 다른 값을 지닐 수 있다.

하지만 타입 프로퍼티는 인스턴스의 생성 여부와 상관없이 타입 프로퍼티 자체로 값은 하나이며,
그 타입의 모든 인스턴스가 공통으로 사용한다.

자주 변하지 않는 일정한 값이나 설정 정보같은 공용자원에 대한 접근에 있어서 매번 메모리에 로딩하거나 값을 읽어들이는 것보다 일종의 '전역변수'와 같은 개념을 통해서 접근하는 것이 비용도 줄이고 효율을 높일 수있다.

즉, `모든 인스턴스에서 공용으로 접근하고 값을 병경한 변수를 정의할 떄 아주 유용하므로, 이것이
타입 프로퍼티를 사용하는 핵심 이유`
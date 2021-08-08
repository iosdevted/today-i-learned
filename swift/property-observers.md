# [Swift] Property Observers

Property Observers(프로퍼티 옵저버)를 정의해서 프로퍼티 값의 변경을 모니터링 할 수 있습니다. 프로퍼티 옵저버는 자신이 정의한 "저장 프로퍼티"에 추가 할 수 있으며, super class(부모클래스)를 상속한 프로퍼티에도 추가 할 수 있습니다.

Property Observer는 property 값의 변화를 관찰하고 이에 응답한다.

새로운 값이 현재값과 동일하더라도 속성의 값이 set 될 때 마다 호출된다.

`lazy stored property`를 제외하고, 저의된 stored property에 property observer를 추가할 수 있다.

또한, 하위 클래스 내의 프로퍼티를 재정의하여, 상속된 프로퍼티(stored property or computed property 어느것이든)에도 프로퍼티 옵저버를 추가할 수 있습니다.
오버라이드(override)되지 않은 computed property에 대한 property observer는 computed property setter에서 해당 값의 변경을 관찰하고 이에 응답할 수 있으므로 정의 할 필요 없습니다.


정리하자면, 원래는 "저장 프로퍼티"에만 프로퍼티 옵저버를 추가할 수 있는데, 부모클래스를 상속하는 하위클래스 프로퍼티는 저장프로퍼티든, 연산프로퍼티든 프로퍼티 옵저버를 추가할 수 있나보네요.

willSet - 값이 저장되기 직전에 호출

didSet - 새로운 값이 저장된 직후에 호출

willSet observer를 구현했다면, 새로운 프로퍼티의 값이 constant parameter로 전될된다.

willSet 구현의 일부로, 이 constant parameter의 이름을 지정할 수 있다. 구현 내의 constant parameter 이름과 괄호를 쓰지 않으면 newValue라는 기본 constant parameter 이름으 사용하여 constant parameter를 사용할 수 있다. 

## global variable vs local variable

global variable - function, method, closure or type context 외부에 정의되는 변수

local variable - function, method, closure or type context 내부에 정의되는 변수

1. 전역상수(global constant)와 전역변수(global variable)은 항상 게으르게 연산된다.
== 즉, 필요할 때 초기화.
2. 전역상수(global constant)와 전역변수(global variable)은 연산 프로퍼티와는 다르게 lazy키워드가 필요없다.
3. 지역상수(local constant)와 지역변수(local variable)은 게으르게 연산되지 않는다.

## References

[<The Swift Programming Language (Swift 4) - Properties>](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

[Zedd - Properties - Property Observers(프로퍼티 옵저버)](https://zeddios.tistory.com/247)


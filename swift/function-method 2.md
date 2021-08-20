# [Swift] Function or Method

Function: Function은 특정 작업을 수행하는 '코드조각', global, local이던 `독립된 기능`을 수행하는 단위

```swift
import Foundation

func length(value1: String, value2: String) -> Bool {
    return value1.characters.count < value2.characters.count 
}
```

이와 같이 '코드조각' 하는 일을 알 수 있는 이름을 '함수의 이름" 으로 부여

`하지만 function은 method를 포함하고 있다.`

Method: Class, Struct, enum에 포함되어 있는 "Function"을 Method라고 지칭,

다른 말로는 'class function'이라고도 한다.

```swift
class Person { 
    //This is a method which acts only on Person type 
    func personGreeting() { 
        greet(yourName: "Santosh", category: .Person) 
        } 
    }
```
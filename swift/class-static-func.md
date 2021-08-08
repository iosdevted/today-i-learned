# [Swift] class func or static func

스위프트에는 세가지 함수 종류가 있다. 앞에 붙는 키워드에 따라서 `instance` / `class` / `static`으로 나뉜다.

```swift
class Sample {

    // 1. Instance 함수
    func myInstanceFunc() {}

    // 2. class 함수
    class func myClassFunc() {}

    // 3. static 함수
    static func myStaticFunc() {}
}

```

## class func vs static func

### 공통점

`static`와 `class` 메소드는 타입 메소드라고 부른다.

모두 class 객체보다는 class 자체와 연관되어있다. `() 생성자`를 통해서
인스턴스를 생성하지 않더라도 바로 접근이 가능하다.

```swift
Sample.myClassFunc()
Sample.myStaticFunc()
```

### 차이점

차이점은 해당 class를 상속 받은 `subclasss`에서 나타난다. 

class 함수 — override 가능
static 함수 — override 불가능

```swift
class SubSample : Sample {
//Good!
override class func myClassFunc() {}
//Compile Error
override static func myStaticFunc() {}
}
```

그렇기 때문에 상속이 불가능한 struct나 enum에서 class func을 정의하면 에러가 나겠죠?

## static func vs final class func

```swift
class Sample {
    class func myClassFunc() {}
    static func myStaticFunc() {}
}

//style 1 : final class
class SubSample1 : Sample {
    override final class func myClassFunc() {}
}

//style 2 : static
class SubSample2 : Sample {
    override static func myClassFunc() {}
}
```

둘 다 상속을 못 받고 취향차이로 원하는걸 골라서 사용하면 된다. 

## References

[class func vs static func](https://sujinnaljin.medium.com/swift-class-func-vs-static-func-7e6feb264147)
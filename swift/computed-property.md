# [Swift] Computed Property

값을 저장하지 않는 Computed property를 getter와 setter를 통해 다른 property와 간접적으로 값을 검생하고 정의할 수 있다.

```swift
class Point {
    var x: Int {
        get {
            return x
        }
        set(newValue) {
            x = newValue * 2
        }
    }
}
var p: Point = Point()
p.x = 12//error!
```

이 코드는 틀린코드. 언제나 set를 이용할 때는 저장소가 필요하다.

```swift
class Point {

    var tempX : Int = 1 //var _temp : Int = 1

    var x: Int {
        get {
            return tempX
        }
        set(newValue) { //newValue는 생략가능. 하지만 생략할 시 newValue가 default 키워드
            tempX = newValue * 2
        }

        //그러므로 이렇게 변경 가능
        //set {
        //    tempX = newValue * 2
        //}
    }
}

var p: Point = Point()
p.x = 12
```

computer property는 값을 저장하기 보다는 연산을 한다는 의미.

또한 반드시 var로 선언되어야 한다.

```swift
struct Point {
    var x = 0.0, y = 0.0 //이렇게 한 줄에 "저장 프로퍼티"를 선언했네요 :)
}

struct Size {
    var width = 0.0, height = 0.0//역시나 입니다.
}

struct Rect {

    //저번에 배운 CGRect와 비슷하다고 생각하시면 됩니다!!
    var origin = Point()//origin은 x,y좌표니 Point()타입으로 만들어주고

    var size = Size()//size는 너비와 높이를 가지니 Size()타입으로 만들어줍니다. 

    var center: Point {

        //center라 함은 어떤 사각형의 가운데인 "좌표"겠죠? 그러니 Point타입으로 선언해주고!! get과 set이 나오는 것을 보니 center는 "연산 프로퍼티"임을 알 수 있네요.

        get {
            let centerX = origin.x + (size.width / 2)
            let centerY = origin.y + (size.height / 2)
            //get에도 위처럼 새로 변수를 선언하고 값을 할당 할 수 있어요. 리턴만 제대로 해주면 된답니다.
            //리턴하는 타입은 center의 타입과 반드시 같아야해요. 즉 Point 타입.

            return Point(x: centerX, y: centerY)

        }
        set(newCenter) {
            origin.x = newCenter.x - (size.width / 2)
            origin.y = newCenter.y - (size.height / 2)
        }
    }
}

var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width: 10.0, height: 10.0))

let initialSquareCenter = square.center

print(initialSquareCenter)//(x: 5.0,y: 5.0)

square.center = Point(x: 15.0, y: 15.0)

print("square.origin is now at (\(square.origin.x), \(square.origin.y))")

// Prints "square.origin is now at (10.0, 10.0)"
```

연산 프로퍼티는 위 코드처럼 get, set둘다 가질 수 있지만, get만을 가질 수 있다.

get만 있는 computed property를 

Read-Only Computed Properties라고 한다.

`get만 있을 때는, get를 생략해도 된다.`

```swift
struct Cuboid {
    var width = 0.0, height = 0.0, depth = 0.0
    var volume: Double {
        return width * height * depth
    }
}
```

결론

1. 클래스, 구조체, 열거형에 사용된다.
2. var로 선언해야한다.
3. 클래스, 구조체, 열거형에 값을 저장할 저장프로퍼티가 하나 있어야한다.==> 연산프로퍼티 자기 자신을 리턴하거나 값을 지정할 수 없다.
4. get, set을 동시에 구현 가능하며, get만 구현하는 것도 가능. 하지만 set만 구현하는 것은 안된다!
5. set의 파라미터를 생략할 수 있으며 생략했을 시, newValue라는 키워드를 사용한다.

## References

[<The Swift Programming Language (Swift 4) - Properties>](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

[Zedd - Properties - Computed Property(연산 프로퍼티)](https://zeddios.tistory.com/245)
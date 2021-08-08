# [Swift] Various usages of Extension

기존 존재하는 클래스, 구조체, 열거형, 프로토콜 타입에 새로운 기능을 추가 할 수 있게 해준다

가독성있는 코드를 작성할때 사용할 뿐 아니라 외부 라이브러리, String, Int와 같은 Original 코드에 접근 할 수 없는 것에 기능을 추가할때 사용한다

- 계산 속성 추가
- 인스턴스 메소드 , 타입 메소드 추가
- 이니셜라이저 추가
- 서브스크립트 정의
- 새로운 중첩 타입 정의
- 프로토콜 추가

## Syntax

```swift
struct Size {
	var width = 0.0
	var height = 0.0
}

extension Size {
	var area: Double {
			return width * height
	}
}
//Equatble 프로토콜을 채용하여 extension 구현가능함
// 기존에 존재하는 메소드 오버라이드 불가
extension Size: Eqatable {
	public static func == (lhs: Size, rhs: Size) -> Bool {
		return lhs.width == rhs.width && lhs.height == rhs.height
	}
}
```

## Adding Properties

``` swift
// 저장속성은 Extension으로 추가할수 없지만 계산속성은 추가가능함
extension Date {
	var year: Int {
		let cal = Calendar.current 
		return cal.component(.year, from: self)
	}
}

extension Double {
    var km: Double { return self * 1_000.0 }
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}
```

## Adding Methods

```swift
extension Int {
    func repetitions(task: () -> ()) {
        for i in 0..<self {
            task()
        }
    }
}

3.repetitions {
    println("Goodbye!")
}
```

## Adding Initializers

```swift
Struct Size {
	var width = 0.0
	var height = 0.0
	/* 구조체에서 생성자를 아래와 같이 구현하면  디폴트 생성자가 구현이 되지 않는다 
근데 extension으로 추가해주면 디폴트 생성자도 구현이되고 여기서 생성자를 추가로 더 구현하지 않아도 되는 장점이있음
	init(value: Double){ 
		width = value
		height = value
	}
*/
}
extension Size {
	init(value: Double){
		width = value
		height = value
	}
Size()
Size(width: 12, height:10) 
```

## Adding Subscripts

```swift
extension Int {
    subscript(var digitIndex: Int) -> Int {
        var decimalBase = 1
            while digitIndex > 0 {
                decimalBase *= 10
                --digitIndex
            }
            return (self / decimalBase) % 10
    }print(a++)
}
746381295[0]
// returns 5
746381295[1]
// returns 9
746381295[2]
// returns 2
746381295[8]
// returns 7
```
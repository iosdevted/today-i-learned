# [Swift] Stored Property

Property는 값을 Class, Struct, Enum과 연결한다.

Swift의 Property에는 크게 세가지가 있다.

- Stored Property
- Computed Property
- Type Property

Stored Property는 Class와 Struct에서 constant와 variable 값을 instance의 일부로 저장한다.

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}

let rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// 초깃값을 주지 않았기 때문에

rangeOfThreeItems.firstValue = 6 //error!
rangeOfThreeItems.length = 10 //error!
```

firstValue가 var임에도 불구하고 에러가 나는 이유;

Struct는 Value type이기 떄문에!

Struct의 Instance가 var로 선언되면 해당 구조체의 property들은 변경할 수 있지만, let으로 

선언되면 모든 property가 let으로 선언된 것 같이 된다.

```swift
class FixedLengthRange {
    var firstValue: Int
    let length: Int

    init(firstValue : Int, length:Int) {
        self.firstValue = firstValue
        self.length = length
    }
}

let rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)

rangeOfThreeItems.firstValue = 3
rangeOfThreeItems.length = 10//error!
```

Struct와 달리 Class는 Reference Type이기 때문에 우너본에 바로 접근하기 떄문에, 에러가 발생하지 않는다.

```swift
class DataImporter {
    //DataImporter클래스는 외부파일에서 데이터를 가져오는 클래스!
    var filename = "data.txt"
    //데이터를 가져올 외부파일이름은 "data.txt"인가봐요 :)
}

class DataManager {
    lazy var importer = DataImporter()
    var data = [String]()
    //DataManager클래스는 데이터 관리하는 클래스에요. importer가 위에서 선언한 DataImporter의 인스턴스이며, lazy로 선언되었네요.
}

let manager = DataManager()//DataManager의 인스턴스를 만듭니다. 참고로, DataManager의 저장프로퍼티들은 초기값이 있으므로 init이 없어도 됩니다.
manager.data.append("Some data")//DataManager의 저장프로퍼티 data라는 배열에 어떤 데이터를 집어넣고,
manager.data.append("Some more data")//또 집어넣어요.

//하지만 아직까지 DataImporter의 인스턴스인 importer프로퍼티는 생성되지 않았습니다. lazy 저장프로퍼티이기 때문이죠.
```

importer 프로퍼티에 처음 액세스 할 때 만들어지게 된다. 그 전까지는 생생되지 않는다.

lazy stored property를 잘만 사용하면 성능도 올가가고, 공간낭비도 줄일 수 있다.

lazy property 같은 경우, 항상 변수로서 선언해야 한다.

왜냐하면 초기값이 검색되지 않을 수 있기 때문에.

그리고 lazy property가 여러 쓰레드가 동싱에 엑세스 된다면, 단 한 번만 초기화 된다는 보장이 없기 떄문에.

## References

[<The Swift Programming Language (Swift 4) - Properties>](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)

[Zedd - Properties - Stored Property(저장 프로퍼티)](https://zeddios.tistory.com/243)
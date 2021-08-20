# [Swift] Exception Handling

optional 타입은 오류가 발생할 경우, 오류에 대한 정보를 외부로 전달할 방법이 없다.

enum 타입을 이용해 오류타입명을 정의 하는 것이 좋다.

```swift
// enum형으로 error타입명 정의 ( 프로토콜을 구현한 것은 오류 타입으로 사용하라는 일종의 가독성 표시 )
enum DateParseError : Error {
    case overSizeString
    case incorrectData(part: String)
}
```

## Throws

 - 오류가 발생할 수 있다는 것 표현 : throws를 "->"앞에 표시(반환 전에 오류가 발생하면, 오류객체를 반환한다는 의미)

 - 오류가 발생됐다는 표현 : throw와 오류명 집어넣기(return하는 것과 유사)

```swift
func getNextYearAndThrows(paramYear: Int) throws -> Int {
    guard paramYear <= 2020 else {
        throw DataError.overSizeYear
    }
    
    guard paramYear >= 0 else {
        throw DataError.incorrectData(part: paramYear)
    }
    
    return paramYear+1
}
```

## do - try - catch

 - 마지막 catch에 <오류타입>을 작성하지 않으면 default문으로 됨
 - try! : 함수 강제 실행 (런타임 오류 발생 가능)
 - try? : 에러가 발생하면 nil리턴, 발생하지 않으면 Optional타입으로 리턴

 ```swift
func getNextYear(paramYear: Int) -> Int {
    var year: Int = 0
    do {
        year = try getNextYearAndThrows(paramYear: paramYear)
    } catch DataError.overSizeYear {
        print("년도 초과해서 입력하였습니다")
    } catch DataError.incorrectData(let part){
        print("입력한 값이 \(part)이므로 오류입니다.")
    } catch {
        print("default error catch")
    }
    
    return year
}

let a = getNextYear(paramYear: -999) // 입력한 값이 -999이므로 오류입니다.
 ```

 ```swift
// enum형으로 error타입명 정의
enum DataError : Error { // Error 프로토콜을 구현한 것은 오류 타입으로 사용하라는 일종의 가독성 표시
    case overSizeYear
    case incorrectData(part: Int)
}

// 오류가 나는 조건을 throws와 함께 배치
func getNextYearAndThrows(paramYear: Int) throws -> Int {
    guard paramYear <= 2020 else {
        throw DataError.overSizeYear
    }
    
    guard paramYear >= 0 else {
        throw DataError.incorrectData(part: paramYear)
    }
    
    return paramYear+1
}

// 실제로 응용프로그램에서 불러올 함수
func getNextYear(paramYear: Int) -> Int {
    var year: Int = 0
    do {
        year = try getNextYearAndThrows(paramYear: paramYear)
    } catch DataError.overSizeYear {
        print("년도 초과해서 입력하였습니다")
    } catch DataError.incorrectData(let part){
        print("입력한 값이 \(part)이므로 오류입니다.")
    } catch {
        print("default error catch")
    }
    
    return year
}

let a = getNextYear(paramYear: -999) // 입력한 값이 -999이므로 오류입니다.​
 ```

 ## try, try!, try?

  - try : 예외 발생시 catch로 예외 정보 던지며, 성공하는 경우 un wrapping된 값 반환
 - try? : 예외 발생시 nil 리턴, 성공하는 경우 옵셔널 값 반환
 - try! : 예외 발생시 runtime error, 성공하는 경우 unrapping된 값 반환 (nil값이 나오지 않음을 확신하는 경우 사용)
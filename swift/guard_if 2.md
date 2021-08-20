# [Swift] Optional Unwrapping

`guard` 뒤에 따라붙는 코드의 실행 결과가 true일 때 코드가 계속 실행됩니다.if 구문과는 다르게 `guard` 구문은 항상 `else` 구문이 뒤에 따라와야 합니다.만약 **guard 뒤 따라오는 `Bool`** 값이 false라면 else의 블록 내부 코드를 실행하게 됩니다.이 내부 코드에는 자신보다 **상위 코드 블록을 종료하는 코드**가 반드시 들어가게 됩니다.코드 블록 종료시 `return, break, continue, throw` 등 제어문 전환 명령을 사용합니다.

Bool타입의 값으로 guard문 동작시킬 수 있지만 **`옵셔널 바인딩`** 역할도 가능합니다.이렇게 사용시 `guard로 옵셔널 바인딩 된 상수`를 guard 구문 실행 코드 아래부터 **`함수 내부의 지역상수`처럼 사용 가능합니다.**

guard let으로 할 경우 ','(쉼표)로 `추가조건`을 나열할 수 있고 조건은 `Bool 타입` 값이어야 합니다. ','로 나열했을 때 `AND 논리연산자`와 같은 결과이고, ','를 '&&' 로 치환 가능

guard 구문 사용시 if 코드를 훨씬 간결하고 읽기 좋게 구성 가능합니다.

예외사항만을 처리하고 싶다면 guard 구문을 사용하는 것이 훨씬 간편합니다.

다만 guard는 return,break,continue,throw 등의 제어문 전환 명령어를 쓸 수 없는 상황이라면 사용이 불가능합니다. 함수나 메서드, 반복문 등 특정 블록 내부에 위치하지 않는다면 사용이 제한됩니다.

`if let은 열 번 중에 한 법은 nil 값이 나올 수 있는 값을 무시해도 되는 상황이고 guard let은 모든 상황, 매번 원하는 값이 나와야 하는 상황에서 사용한다. 즉 else 값이 나오면 안 되는 상황에서 사용한다.`

```swift 
//if let
func printName(){
  var value: String?
  value = "Ted"
  print(value) // Optional("Ted")
  if let name = value {
    print(name) // "Ted"
  } 
  //if문 안에서만 name 변수를 사용 가능.
}

//guard let
func printName(){
  var value: String?
  value = "Ted"
  print(value) // Optional("Ted")
  guard let name = value else { return }
  print(name) // "Ted"
  //name변수는 메소드 내에서 지역상수처럼 사용 가능.
}

guard let number = Double(displayLabel.text!) else {
	fatalError("Cannot convert display label text to a Double.")
} // 이렇게 애러 메시지가 나오게 할 수도 있다.
```
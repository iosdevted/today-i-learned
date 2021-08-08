# Automatic Reference Counting

iOS는 앱의 메모리 사용을 추적하고 관리하는 자동 참조 계수(이하 ARC)를 사용한다.
ARC는 인스턴스가 더이상 필요가 없을 때 클래스 인스턴스에 사용된 메모리를 자동적으로 해제한다.
간단한 것으로 프로퍼티, 상수, 변수에 레퍼런스가 지정될 때 여기에 들어있는 카운트를 증가시키고
프로퍼티, 상수, 변수가 해제되면 카운트를 감소시킨다.
보유한 카운트가 0이 되면 메모리를 해제하는 방식이다.
대부분의 경우에 메모리 작업은 잘 작동해서 메모리 관리를 생각할 필요가 없다.

하지만, 몇가지 경우에 ARC는 메모리 관리를 위한 코드 일부와 관련있는 더 많은 정보가 필요하다.

ARC를 쉽게 설명하자면 사람들이 있는데 서로의 멱살을 잡고(참조) 있다.
지금 상황에서는 상황이 종료(메모리 할당 해제)되지 않지만
멱살을 놓고(참조 해제) 대화를 하다보면 상황이 종료(메모리 할당 해제)된다.

## How ARC Works

클래스의 새로운 인스턴스를 만들때 마다,
ARC는 인스턴스에 대한 정보를 저장하기 위해 메모리 덩어리(chunk)를 할당한다.
이 메모리는 인스턴스의 저장 프로퍼티와 연관된 값과 함께,
인스턴스의 타입에 관한 정보를 유지한다.

추가적으로, 인스턴스가 더이상 필요하지 않을 때 ARC는 메모리를 다른 목적으로
사용할 수 있도록 인스턴스에 의해 사용된 메모리를 해제한다.
클래스 인스턴스가 더 이상 필요하지 않을 때 메모리 공간을 가지지 않는 것을 보장한다.

하지만 ARC가 사용중에 있는데 인스턴스를 할당 해제하면
인스턴스의 프로퍼티 접근이나 인스턴스 메소드의 더 이상 호출할 수 없다.
만약 인스턴스에 접근하려 한다면 크래쉬(Crash)가 난다.

필요한 인스턴스가 사라지지 않는지 확인해야 하고,
ARC는 각 클래스 인스턴스에 현재 참조중인 프로퍼티, 상수, 변수를 추적한다.
ARC는 인스턴스에 대해 활성화된 참조가 하나라도 있으면, 할당 해제하지 않는다.

프로퍼티, 상수, 변수에 클래스 인스턴스를 할당할 때마다
프로퍼티, 상수, 변수는 인스턴스에 강한 참조(strong reference)를 만든다.
그 참조는 강한 참조로 호출되기 때문에, 인스턴스를 강하게 유지하며 강한 참조가 남아있는 만큼은 메모리 할당을 해제하지 않는다.

ARC는 

- 컴파일 시 코드를 분석해서 자동으로 retain, release 코드를 생성해주는 것
- 참조된 횟수를 추적해 더 이상 참조되지 않는 인스턴스를 메모리에서 해제해주는 것

ARC는 자동으로 RC를 관리해주기 때문에 메모리 해제에 대한 개발자의 부담을 덜어주는 것입니다.

## How to manage ARC

### Reference Count + 1

- 인스턴스를 새로 생성할 때
- 기존 인스턴스를 다른 변수에 대입할 때

### Reference Count - 1

- 인스턴스를 가르키던 변수가 메모리에서 해제되었을 때
- nil이 지정되었을 때
- 변수에 다른 값을 대입한 경우
- 프로퍼티의 경우, 속해있는 클래스 인스턴스가 메모리에서 해제될 떄

## References

[개발자 소들이 - iOS) 메모리 관리 (1/3) - ARC(Automatic Reference Counting)](https://babbab2.tistory.com/26?category=831129)

[[Swift] 메모리 관리 ARC](http://jhyejun.com/blog/memory-management-arc)

[Automatic Reference Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html)
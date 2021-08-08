# Strong Reference Cycles; strong, weak, unowned

ARC의 한가지 문제점이라 하면 두 개의 객체가 상호 참조하는 경우와 같은
강한 순환 참조가 만들어 질 수 있다는 점이다.
이렇게 되면 이 순환 참조에 연관된 객체들은 레퍼런스 카운트가
0에 도달하지 않게 되어 메모리 누수가 발생하게 된다.

스위프트와 같이 ARC를 기반으로 하는 메모리 관리 모델에서는
어떻게 하면 강한 순환 참조를 발생시키지 않도록 하느냐가
메모리 관리상의 가장 큰 관심사가 된다.

강한 순환 참조에 대해서 쉽게 설명하면
A와 B가 있는데 서로 멱살을 잡고(참조) 있다.
둘 중 누가 먼저 멱살을 놓기 시작(참조 해제) 해야
이 험악한 상황이 종료(메모리 할당 해제) 될텐데
계속 서로 멱살을 안 놓다 보니 험악한 상황(메모리 누수)이 지속되고 있는 것이다.
이 상황이 바로 강한 순환 참조를 뜻한다.

강한순환참조를 해결하기 위해 이러한 관계 속에서 weak, unowned를 정의할 수 있습니다. 이 과정은 이후 설명되어질 주제, [Resolving Strong Reference Cycles Between Class Instances](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html#ID52)에서 설명되어집니다만, 강한순환참조를 해결하는 방법에 대해 배우기 전에 왜 참조순환이 발생하는지 이해하는 것은 매우 유용하니 참고 하시기 바랍니다.


|                    |  strong  |    weak   |   unowned |
|--------------------|----------|-----------|-----------|
| Reference Counting |  O | X | X |
| 사용 시점 |   Default | 강한 순환 참조가 발생할 경우 |  강한 순환 참조가 발생할 경우, 참조하는 인스턴스가 먼저 메모리에서 해제될 가능성이 없는 경우 |
| 특징 | 강한 순환 참조로 인해 Memory leak이 발생할 수 있음 | 참조하던 인스턴스가 해제되면 자동으로 nil을 할당 | 참조하던 인스턴스가 먼제 메모리에서 해제되면, 헤재된 주소값을 계속 들고 있음(에러로 이어질 가능성이 높음) | 

## Delegate 순환참조

```swift
vc.delegate = self
```

delegate를 하기 위해서는 일을 시키는 객체와 일을 하는 객체 두 개가 무조건 있어야 하는데, 위의 코드로 객체를 연결시켜줌으로 인해 FirstViewController와 SecondViewController는 서로를 소유하는 상황이 됩니다.

즉, 다시 말해 FirstViewController에서 SecondViewController 객체를 만듦으로 SecondViewController를 소유하고, 
SecondViewController의 delegate를 FirstViewController로 연결해줌으로써 FirstViewController를 소유하는 순환참조가 됩니다.

이 문제를 해결하기 위해서는 `SecondViewController의 delegate에 weak를 붙이면 되는데` 그렇게 되면

FirstViewController만 SecondViewController를 소유하기 때문에 순환참조가 발생하지 않게 됩니다.

## References

[Resolving Strong Reference Cycles Between Class Instances](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html#ID52)

[iOS) 메모리 관리 (2/3) - strong , weak, unowned, 순환 참조](https://babbab2.tistory.com/26?category=831129)

[iOS 개발문서 - 강한순환참조(strong reference cycle) 문제](https://0urtrees.tistory.com/67)

[상어의 개발 블로그 - iOS::ARC, strong, weak, unowned](https://shark-sea.kr/entry/iOS-ARC-strong-weak-unowned)
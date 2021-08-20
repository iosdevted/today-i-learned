# [Swift] COW (Copy-on-Write)

There are several advantages to using value-types, like ensuring that values have independent state. When we copy values (the effect of assignment, initialization, and argument passing) the program will create a new copy of the value. For some large values these copies could be time consuming and hurt the performance of the program.

A fully stack allocated value type will not need reference counting, but a value type with inner references will unfortunately inherit this ability.

```swift
final class ExampleClass {
  let exampleString = "Ex value"
}
struct ExampleStruct {
  let ref1 = ExampleClass()
  let ref2 = ExampleClass()
  let ref3 = ExampleClass()
  let ref4 = ExampleClass()
}
```

This way of having inner reference types is an expensive operation that requires many calls to malloc/free and a significant reference counting overhead each time you copy.

`COW enables value types to be referenced when they are copied just like reference types. The real copy only happens when you have an already existing strong reference and you are trying to modify that copy`

```swift
let array = [1,2,3] //address A
var notACopy = array //still address A
notACopy += [4,5,6] //now address B
```

## References

[Copy on Write in Swift](https://medium.com/@nitingeorge_39047/copy-on-write-in-swift-b44949436e4f)

[개발자 소들이 - COW (Copy-on-Write)](https://babbab2.tistory.com/18?category=828998)

[Swift Memory - COW (Copy On Write)](https://nsios.tistory.com/56)
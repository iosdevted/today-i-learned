# Value Semantics


Types in Swift fall into one of two categories: first, “value types”, where each instance keeps a unique copy of its data, usually defined as a `struct`, `enum`, or `tuple`. The second, “reference types”, where instances share a single copy of the data, and the type is usually defined as a `class`.

## Reference Types vs Value Types

The most basic distinguishing feature of a value type is that copying — the effect of assignment, initialization, and argument passing — creates an independent instance with its own unique copy of its data:

```swift
struct MyValueType {
  var a: Int
}
var V1 = MyValueType(a: 1)
var V2 = V1
V2.a = 42
print(V1)
// Prints "MyValueType(a: 1)"
print(V2)
// Prints "MyValueType(a: 42)"
```

Copying a reference, on the other hand, implicitly creates a shared instance. After a copy, two variables then refer to a single instance of the data, so modifying data in the second variable also affects the original, e.g.:

```swift
class MyReferenceType {
  var a: Int
  init(a: Int) {
    self.a = a
  }
}
var R1 = MyReferenceType(a: 1)
var R2 = R1
R2.a = 42
print(R1.a, R2.a)
// Prints "42 42"
```

One of the primary reasons to choose value types over reference types is the ability to more easily reason about your code. If you always get a unique, copied instance, you can trust that no other part of your app is changing the data under the covers. This is especially helpful in multi-threaded environments where a different thread could alter your data out from under you. This can create nasty bugs that are extremely hard to debug.

**Use a value type when:**

- Comparing instance data with `==` makes sense
- You want copies to have independent state
- The data will be used in code across multiple threads
<br />
<br />

**Use a reference type (e.g. use a `class`) when**:

- Comparing instance identity with `===` makes sense
- You want to create shared, mutable state
<br />
<br />

In Swift, `Array`, `String`, and `Dictionary` are all value types. They behave much like a simple `int` value in C, acting as a unique instance of that data. You don’t need to do anything special — such as making an explicit copy — to prevent other code from modifying that data behind your back. Importantly, you can safely pass copies of values across threads without synchronization. In the spirit of improving safety, this model will help you write more predictable code in Swift.

## Reference Semantics vs Value Semantics

With value semantics, a variable and the data assigned to the variable are logically unified. Since variables exist on the stack, value types in Swift are said to be stack-allocated. To be precise, all value type instances will not always be on the stack. Some may exist only in CPU registers while others may actually be allocated on the heap. In a logical sense though, value type instances can be thought of as being contained in the variables to which they are assigned. There is a one-to-one relationship between the variable and the data. The value held by a variable cannot be manipulated independently of the variable.

With reference semantics, on the other hand, the variable and the data are distinct. Reference type instances are allocated on the heap and the variable contains only a reference to the location in memory where the data is stored. It is possible and quite common for there to be multiple variables with references to the same instance. Any of these references can be used to manipulate the instance.

## References
More information on Markdown can be found at the following links:

- [Medium - Understanding Swift Value Semantics](https://medium.com/@JimmyMAndersson/understanding-swift-value-semantics-d84d57b937a2)
- [Raywnderlich - Reference vs Value Types in Swift](https://www.raywenderlich.com/9481-reference-vs-value-types-in-swift)
- [Blog - When and How to use Value and Reference Types in Swift](https://khawerkhaliq.com/blog/swift-value-types-reference-types/)
- [Apple Developer - Value and Reference Types](https://developer.apple.com/swift/blog/?id=10)
- [Hackernoon - Semantics in Swift](https://hackernoon.com/semantics-in-swift-66d1c19b33f1)
- [Swiftbysundell - Utilizing value semantics in Swift](https://www.swiftbysundell.com/articles/utilizing-value-semantics-in-swift/)

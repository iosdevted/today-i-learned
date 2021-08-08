# [Swift] Weak Must Not be Applied to Non-class-bound

This error occurs when a protocol that does not follow the class is declared and used as a weak variable.

Let's take an example.

The delegate is declared using the protocol.

```swift
protocol MyDelegate {
    func runDelegateMethod()
}
```

Declare a delegate followed the protocol declare a delegate in a class above as weak. The reason for declaring it weak is to avoid the Retain Cycle.

```swift 
class ClassProtocolTest: MyDelegate {
    weak var delegate: MyDelegate?
    func runDelegateMethod() {}
}
```

When you build this code causes the error mentioned above. Why?

Protocols can be used for classes, structures and enums. Since the protocol declared earlier does not know whether it is used in a class or a struct or enum, the unowned or weak keywords used for reference count management cannot be used.

To resolve this error, declare the protocol to follow the class and specify that it can only be used by the class.

```swift
protocol MyDelegate: class {
    func runDelegateMethod()
}
```

If you declare it like this, you can build without errors.
# [Swift] @escaping closure

When a closure escapes a function, it means that the closure is passed as an argument to that function, but is executed after the function returns. The concept that arguments of a function can be used outside of a function by escaping the scope of the function overrides the concept of scope of variables that we previously knew. This is because the local variable declared in the function goes beyond the scope of the local variable and is valid outside the function.

Having regular local variables (mainly values: Int, String, etc.) alive outside of a function doesn't seem much different from bringing a global variable to the function and giving it a new value. So, the concept of an escape like this doesn't seem very meaningful. However, Escaping of Closures is useful in that you can write a function so that function B executes only when function A is finalized.

By using Escaping Closure, you can set the order of execution between functions.

Escaping Closure allows closure arguments to be outlived from a function. Since Swift 3, by default, closures that come as arguments of a function cannot be used outside the function. In other words, it is basically impossible to store the closure in a storage outside the function or execute the closure in another thread using GCD. However, this can be used through Escaping Closure, and if you put the @escaping keyword in front of the closure type, the closure becomes an Escaping Closure.

```swift
// Example of storing the closure outside the function
// an array to store the closure
var completionHandlers: [() -> Void] = []

func withEscaping(completion: @escaping () -> Void) {
    // Store the closure in the completionHandlers array outside the function
    completionHandlers.append(completion)
}

func withoutEscaping(completion: () -> Void) {
    completion()
}

class MyClass {
    var x = 10
    func callFunc() {
        withEscaping { self.x = 100 }
        withoutEscaping { x = 200 }
    }
}
let mc = MyClass()
mc.callFunc()
print(mc.x)
completionHandlers.first?()
print(mc.x)

// 결과
// 200
// 100
```

## References

[What do mean @escaping and @nonescaping closures in Swift?](https://medium.com/swiftcommmunity/what-do-mean-escaping-and-nonescaping-closures-in-swift-d404d721f39d)

[탈출 클로저 (@escaping closure)](https://jinios.github.io/swift/2018/06/13/escaping/)
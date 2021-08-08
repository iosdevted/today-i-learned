# Singleton Pattern

The singleton pattern is a method used when you want to create an object for a specific purpose and use it for common use. Mainly, environment settings, login information are stored in objects created for a specific purpose, and data is used by making them accessible from multiple objects. This object is kept in memory until the program is executed and terminated unless it is arbitrarily freed from memory.

The objects of the singleton pattern mainly used in iOS development are as follows.

```swift
let screen = UIScreen.main
let userDefaults = UserDefaults.standard
let application = UIApplication.shared
let fileManager = FileManager.default
let notification = NotificationCenter.default
```

## When to use Singleton

The singleton pattern is a method in which there is only one specific object in the app, and other objects share the contents of it.

```swift
class SingletonClass {
    static let shared = SingletonClass()
    var x = 0
}
```

When defining a class, a type property of the same type as the corresponding class is created inside so that access is possible without creating an object. At this time, it is declared as a static global variable, but since this property is created delayed, it is not stored in memory until the first SingletonClass is created.

```swift
class SingletonClass {
    static let shared = SingletonClass()
    var x = 0

    private init() {

    }
}
```

If the singleton object is not intended to be created multiple times, it should be prevented to solve the problem of creating a new object and losing its uniqueness. That way, you can set the class initializer as private to prevent creating another instance from outside.


## References

[Swift Singletons: A Design Pattern to Avoid (With Examples](https://matteomanferdini.com/swift-singleton/)

[NAROTi - Singleton Pattern](https://velog.io/@naroti/iOS-개발-Singleton-Pattern-q4k3uzgf0n)
# Protocol Oriented Programming

Protocol-oriented programming has been making strides in the Swift community in recent years. It is more of an extension rather replacement of the object-oriented paradigm – a prelude to evolution rather than a revolution. But it still provides tons of benefits for both developers and organizations. Find out all about them in this example-rich introduction to protocol-oriented programming with Swift.

## Why Protocol-Oriented Programming?

Protocols allow you to group similar methods, functions and properties. Swift lets you specify these interface guarantees on `class`, `struct` and `enum` types. Only `class` types can use base classes and inheritance.

An advantage of [protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html) in Swift is that objects can conform to multiple protocols.

When writing an app this way, your code becomes more modular. Think of protocols as building blocks of functionality. When you add new functionality by conforming an object to a protocol, you don’t build a whole new object. That’s time-consuming. Instead, you add different building blocks until your object is ready.

## What Is Protocol-Oriented Programming? 

Protocol-Oriented Programming is a new programming paradigm ushered in by Swift 2.0. In the Protocol-Oriented approach, we start designing our system by defining protocols. We rely on new concepts: `protocol extensions`, `protocol inheritance`, and `protocol compositions`. The paradigm also changes how we view semantics. In Swift, value types are preferred over `classes`. However, object-oriented concepts don’t work well with `structs` and `enums`: a `struct` cannot inherit from another `struct`, neither can an `enum` inherit from another `enum`. So inheritancefa - one of the fundamental object-oriented concepts - cannot be applied to value types. On the other hand, value types can inherit from protocols, even multiple protocols. Thus, with POP, value types have become frst class citizens in Swift.

## Start with a Protocol 

> “Don’t start with a class, start with a protocol.” 

If you model an abstraction using classes, you’ll need to rely on inheritance. The superclass defines the core functionality and exposes it to subclasses. A subclass can completely override that behavior, add specific behavior, or get all the work done by the superclass. This works nicely until you realize that you need more functionality from a different class. Swift, just like many other programming languages, does not support multiple inheritance. Following the class-first approach, you’d have to keep adding new functionality to your superclass or otherwise create new intermediary classes, thereby complicating the issue. Protocols, on the other hand, serve as blueprints rather than parents. A protocol models abstraction by describing what the impleentation types shall implement.

```swift
protocol Entity {
    var name: String {get set}
    static func uid() -> String
}
```

What it tells us is that adopters of this protocol will be able to create an entity, assign it a name and generate its unique identifier by implementing the type method uid().

One type can model multiple abstractions, since any type - including value types - can implement multiple protocols. This is a huge benefit over class inheritance. You can separate the concerns by creating as many protocols and protocol extensions as needed. Say good-bye to monolithic superclasses! The only caveat is that protocols define a template abstractly -- with no implementation. Here’s where protocol extensions come to the rescue.

## Extending Protocols With Default Implementations

Protocols serve as blueprints: they tell us what adopters shall implement, but you can’t provide implementation within a protocol. What if we need to define default behavior for conforming types? Protocol extensions are the way to go! In Swift, you can extend a protocol and provide default implementation for methods, computed properties, subscripts and convenience initializers. 

```swift
struct Order: Entity {
    var name: String
    let uid: String = Order.uid()
}
let order = Order(name: "My Order")
print(order.uid)
// 4812B485-3965-443B-A76D-72986B0A4FF4
```

## Protocol Inheritance

A protocol can inherit from other protocols and then add further requirements on top of the requirements it inherits. In the following example, the protocol Persistable inherits from the Entity protocol I introduced earlier. It adds the requirement to save an entity to file and load it based on its unique identifier.

```swift
protocol Persistable: Entity {
    func write(instance: Entity, to filePath: String)
    init?(by uid: String)
}
```

The types that adopt the Persistable protocol must satisfy the requirements defined in both the Entity and the Persistable protocol.Protocol inheritance is a powerful feature which allows for more granular and flexible designs.

## Protocol Composition

Swift does not allow multiple inheritance for classes. However, Swift types can adopt multiple protocols. Sometimes you may find this feature useful.

Here’s an example: let’s assume that we need a type which represents an Entity.

We also need to compare instances of given type. And we want to provide a custom description, too.

We have three protocols which define the mentioned requirements:

- Entity
- Equatable
- CustomStringConvertible

If these were base classes, we’d have to merge the functionality into one superclass; however, with POP and protocol composition, the solution becomes:

```swift
struct MyEntity: Entity, Equatable, CustomStringConvertible {
    var name: String
    // Equatable
    public static func ==(lhs: MyEntity, rhs: MyEntity) -> Bool {
        return lhs.name == rhs.name
    }
    // CustomStringConvertible
    public var description: String {
        return "MyEntity: \(name)"
    }
}
let entity1 = MyEntity(name: "42")
print(entity1)
let entity2 = MyEntity(name: "42")
assert(entity1 == entity2, "Entities shall be equal")
```

This design not only is more flexible than squeezing all the required functionality into a monolithic base class but also works for value types.

## Are There Any More Advantages To Protocols?

Protocols are not all about information hiding. Our second example shows how protocols (together with generics) can make your programs more flexible, as well as reduce the amount of code you need to write.

```swift
class Arithmetic {
    static func add(a: Int, b: Int) -> Int {
        return a + b
    }
}
```
What you see is a method performing a simple addition and returning the result, and it’s not doing anything wrong per se.

However, when we look closer we realize that we’re going to need to write a lot of these to cover all the different types of numbers that you may want to add together. Floats will need a method, Doubles will need one, etc. We want to implement a single function that accepts several different types and provides a correct answer, but we also want to specify some constraints on what those types can be (it wouldn’t, for example, make sense to try to add two Strings in this situation).

```swift
class Arithmetic {
    static func add<Type: Numeric>(a: Type, b: Type) -> Type {
        return a + b
    }
}
```

We now have a function that’s using a protocol as a generic constraint. This allows us to handle any number of types with the same method, the condition being that the type must implement the `Numeric` protocol and thereby allow us to perform arithmetic operations on it.

## Conclusion

If you’re coming from a more traditional object-oriented system where inheritance is more common, try to think as your first protocol as being a base class. You can then create new protocols by inheriting from that initial protocol, and write extensions so they have default implementations.

However, a better approach is to write lots of small protocols that each do specific, individual things: one to make products purchasable, one to make them serializable, one to make them searchable, and so on. You can then add default implementations to those extensions, which means you can add functionality to existing types just by making them conform to your protocol.

In this respect protocol-oriented programming is sort of a similar approach to multiple inheritance from languages such as C++. However, because protocol extensions can’t include state you don’t get any of the cruft, and Swift’s constraint-based conflict resolution is easy enough for everyone to understand.
<br /> 

## References
More information on Markdown can be found at the following links:

- [Raywenderlich Protocol-Oriented-Programming Tutorial](https://www.raywenderlich.com/6742901-protocol-oriented-programming-tutorial-in-swift-5-1-getting-started)
- [Apple The swift programming language Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
- [Medium Introduction to protocol oriented programming](https://medium.com/swlh/introduction-to-protocol-oriented-programming-1ff3862f9a3c)
- [Apple Protocol-Oriented Programming in Swift](https://developer.apple.com/videos/play/wwdc2015/408/)
- [Hacking with swift What is protocol-oriented programming?](https://www.hackingwithswift.com/example-code/language/what-is-protocol-oriented-programming)
- [Medium How Protocol Oriented Programming saved my day?](https://medium.com/ios-os-x-development/how-protocol-oriented-programming-in-swift-saved-my-day-75737a6af022)
- [Blog Protocol Oriented Programming in Swift](https://www.pluralsight.com/guides/protocol-oriented-programming-in-swift)

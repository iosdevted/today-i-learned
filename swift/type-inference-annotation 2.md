# [Swift] Type Inference vs Type Annotation

Swift is a type-safe language. A type safe language encourages you to be clear about the types of values your code can work with. If part of your code expects a String, you can’t pass it an Int by mistake.

```swift
var welcomeMessage: String
welcomeMessage = 22 // this would create an error because you  
//already specified that it's going to be a String
```

## Type Inference

If you don’t specify the type of value you need, Swift uses type inference to work out the appropriate type. Type inference enables a compiler to deduce the type of a particular expression automatically when it compiles your code, simply by examining the values you provide.

```swift
var meaningOfLife = 42 // meaningOfLife is inferred to be of type Int
meaningOfLife = 55 // it Works, because 55 is an Int
```

```swift
var meaningOfLife = 42 // 'Type inference' happened here, we didn't specify that this an Int, the compiler itself found out.
meaningOfLife = 55 // it Works, because 55 is an Int
meaningOfLife = "SomeString" // Because of 'Type Safety' ability you will get an 
//error message: 'cannot assign value of type 'String' to type 'Int'' 
```

## Optimize Compiler Performance(Type Annotation)

Type Inference takes more time than Type Annotation.

To optimize compiler performance, try to write the code like below.

```swift
let names : [String] = ["John", "Ali", "Jane", " Taika"]
```

## References

[Official Swift Document - The Basics](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID322)

[개발자 소들이 - 타입 추론(Type Inference) vs 타입 어노테이션(Type Annotation)](https://babbab2.tistory.com/14?category=828998)
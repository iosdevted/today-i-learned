# Basics of Unit Testing

A Unit test is very small function that you write to test some part of your code.(Unit tests are automated tests that run and validate a piece of code (known as the “unit”) to make sure it behaves as intended and meets its design.)

Unit tests have their own target in Xcode and are written using the XCTest framework. A subclass of XCTestCase contains test methods to run in which only the methods starting with “test” will be parsed by Xcode and available to run.

Unit Test - Testing isolated small pieces of code with Fake or Mock dependencies

Integration Test - App code is tested without faking database or HTTP connections

UI Test - Automated tests to test User Interface Interaction and Behavior

```swift
/// A simple struct containing a list of users.
struct UsersViewModel {
    let users: [String]

    var hasUsers: Bool {
        return !users.isEmpty
    }
}

/// A test case to validate our logic inside the `UsersViewModel`.
final class UsersViewModelTests: XCTestCase {

    /// It should correctly reflect whether it has users.
    func testHasUsers() {
        let viewModel = UsersViewModel(users: ["Antoine", "Jaap", "Lady"])
        XCTAssertTrue(viewModel.hasUsers)
    }
}
```

## Test-Driven Development Life Cycle

1. Red: Write Unit test that fails

2. Green: Write App code to make Unit test pass

3. Refactor: Clean up and Imporve Unit test and app code

4. Repeat: Repeat these steps until all your app features are built and tested

## Unit Test Best Practices

- Fast: Unit tests run fast

- Independent: Unit tests are independent

- Repeatable: Unit tests are repeatable

- Self-Validating: Unit test validates itself

- Thorough & Timely: Cover edge cases

- We should not try to make our unit test test mulitple functions in the single unit test method.

- Our test code is just as important as our application code

- Although it’s a target for plenty, 100% coverage should not be your main goal when writing tests. Make sure to test at least your most important business logic at first as this is already a great start. Reaching 100% can be quite time consuming while the benefits are not always that big. In fact, it might take a lot of effort to even reach 100%.

- Write a test before fixing a bug

## Life Cycle

1. override class func setUp()

2. override func setUpWithError()

3. test methods
(addTeardownBlock is called when test method ends)

4. override func tearDownWithError()

5. overrride class func tearDown()

## Unit Test Method Requirements

```swift
func testColorIsRed() {
    
    // Some code here

}
```

- Each test method must begin with the prefix test
- Test method does not take arguments
- Test method does not return a value

### Pattern

```swift
func test<System Under Test>_<Condition Or State Change>_<Expected Result>() {
    // Some code here
}
```

### Example

```swift
func testSignupFormModel_WhenInformationProvided_passwordShouldMatchRepeatPassword() {
    // Some code here
}
```

## Code Coverage

[Apple Archive](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/07-code_coverage.html)

[Zedd - Code Coverage](https://zeddios.tistory.com/1141)

![Screen Shot 2021-04-18 at 9 30 57 PM](https://user-images.githubusercontent.com/50784573/115146351-194b4f00-a091-11eb-8726-0214aa081262.png)

## Test Method Code Structure; Arrange, Act, Assert

```swift
func testSignupFormModel_WhenInformationProvided_PasswordsShouldMatch() {

    // Arrange
    let firstName = "ted"
    let email = "test@test.com"
    let password = "1234567"
    let sut = SignupFormModel(firstName: firstName, email: email, password: password)

    // Act 
    let passwordsMatch = sut.doPasswordsMatch()

    // Assert
    XCTAssertTrue(passwordsMatch, "Expected True value for 'passwordsMatch but apparently it is FALSE", file: "SignupFormModelTests.swift", line: 36)
}
```

## Assertions

- XCTAssertTrue(expression, "optional description") - Asserts that an expression is true

- XCTAssertFalse(expression, "optional description") - Asserts that an expression is false

- XCTAssertNil(expression, "optional description") - Asserts that an expression is nil

- XCTAssertNotNil(expression, "optional description") - Asserts that an expression is not nil

- XCTUnwrap - Asserts that an expression is not nil and returns the unwrapped value.

- XCTAssertEqual(expression1, expression2, "optional description")

- XCTAssertNotEqual(expression1, expression2, "optional description")

- XCTAssertGreaterThan(expression1, expression2, "optional description")

- XCTAssertLessThan(expression1, expression2, "optional description")

- XCTAssertLessThanOrEqual(expression1, expression2, "optional description")

- XCTAssertEqualWithAccuracy(expression1, expression2, accuracy, "optional description") - Asserts that two expressions have the same value within a certain accuracy.

- XCTFail("optional description") - Generates a failure immediately and unconditionally.

- XCTAssertThrowsError(expression, "optional description") - Asserts that an expression throws an error

- XCTAssertNoThrow(expression, "optional description") - Asserts that an expression does not throw an error

## Example

```swift
import XCTest
@testable import unit_test

class SignupFormModelValidatorTests: XCTestCase {
    
    var sut: SignupFormModelValidator!

    override func setUp() {
        sut = SignupFormModelValidator()
    }
    
    override func tearDown() {
        sut = nil
    }

    func testSignFormModelValidator_WhenValidFirstNameProvided_ShouldReturnTrue() {
        
        // Arrange
        // Act
        let isFirstNameValid = sut.isFirstNameValid(firstName: "Ted")
        
        // Assert
        XCTAssertTrue(isFirstNameValid, "The isFirstNameValid() Should Have returned TRUE for a valid first name but returned FALSE")
    }
    
    func testSignupFormModelValidator_WhenTooShortFirstNameProvided_ShouldReturnFalse() {
        
        // Arrange
        // Act
        let isFirstNameValid = sut.isFirstNameValid(firstName: "a")
        
        // Assert
        XCTAssertFalse(isFirstNameValid, "The isFirstNameValid() Should Have returned False for a first name that is shorter than \(SignupConstants.firstNameMinLength) characters but it has returned TRUE")
    }
    
    func testSignupForModelValidator_WhenTooLongFirstNameProvided_ShouldReturnFalse() {
        
        // Arrange
        // Act
        let isFirstNameValid = sut.isFirstNameValid(firstName: "TedTedTedTedTedTed")
        
        XCTAssertFalse(isFirstNameValid, "The isFirstNameValid() Should Have returned False for a first name that is longer than \(SignupConstants.firstNameMaxLength) characters but it has returned TRUE")
    }
}
```

## References

[iOS Unit Testing and UI Testing Tutorial](https://www.raywenderlich.com/21020457-ios-unit-testing-and-ui-testing-tutorial)
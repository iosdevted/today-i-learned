# TDD; Test-Driven Development

TDD is where you first write a test that fails, and then you write code to make it pass, and then you refactor your code to be as simple as possible.

What is the difference between Unit Test and Test-Driven Development? In Unit Test you write code to test your code. You first write code then you write tests to verify your logic and code that you wrote. But in TDD, you first write tests that fail and then write code to make the tests pass, and then you refactor your code.

Writing automated tests is seen as not real work and boring compared to building new features. TDD, however, turns testing into a design activity. We use our tests to make sure our code is doing what we want it to do. It also keeps code as simple as possible so it’s easier to understand and modify, especially since developers spend more time reading code than writing it.

```swift
import XCTest
@testable import unit_test

class SignupFormModelValidatorTests: XCTestCase {

    override func setUpWithError() throws {

    }

    override func tearDownWithError() throws {
    }
    //First, You make the function before making app code
    func testSignFormModelValidator_WhenValidFirstNameProvided_ShouldReturnTrue() {
        
        // Arrange
        let sut = SignupFormModelValidator()
        
        // Act
        let isFirstNameValid = sut.isFirstNameValid(firstName: "Ted")
        // Write code to make the tests pass
        
        // Assert
        XCTAssertTrue(isFirstNameValid, "The isFirstNameValid() Should Have returned TRUE for a valid first name but returned FALSE")
        // Refactor your code to be as simple as possible.
    }
    
}
```
# Architecture Patterns; MV(X) essentials

There was a time, not too long back, when designing an iOS app meant relying on one of the three design patterns, namely, the singleton pattern, the decorator pattern, and the bridge design pattern. But, since these patterns started throwing up issues of interactions among client and server, **iOS moved towards the newer and better patterns of MVP, MVVM, MVC, and Viper.**

## Why start by choosing the appropriate app architecture?

Mainly, it helps eliminate the possibility of having a nightmare when trying to implement additional features or debug some issues. The less code you write, the less chance there is of something going wrong, and the app architectures help prevent code duplication, bad programming practices, and messy code. This leads to a good base for a healthy code environment.

## MV(X) essentials

Nowadays we have many options when it comes to architecture design patterns:

- [MVC (Model-View-Controller)](https://en.wikipedia.org/wiki/Model–view–controller)

- [MVP (Model-View-Presenter)](https://en.wikipedia.org/wiki/Model–view–presenter)

- [MVVM (Model-View-View Model)](https://en.wikipedia.org/wiki/Model_View_ViewModel)

- [VIPER](https://www.objc.io/issues/13-architecture/viper/)

Before explaining the architectural patterns, basic concepts of Model and View should be explained: 

- Models - responsible for the domain data or a [data access layer](https://en.wikipedia.org/wiki/Data_access_layer) which manipulates the data, think of `‘Person’` or `‘PersonDataProvider’` classes.

- Views - responsible for the presentation layer (`GUI`), for iOS environment think of everything starting with `‘UI’` prefix.

- Controller/Presenter/ViewModel - the glue or the mediator between the `Model` and the `View`, in general responsible for altering the `Model` by reacting to the user’s actions performed on the `View` and updating the `View` with changes from the `Model`. 

Having entities divided allows us to:

- understand them better (as we already know)

- reuse them (mostly applicable to the View and the Model)

- test them independently

## MVC (Model-View-Controller)


```swift
import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

class GreetingViewController : UIViewController { // View + Controller
    var person: Person!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.greetingLabel.text = greeting
        
    }
    // layout code goes here
}
// Assembling of MVC
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
view.person = model;
```

## MVP (Model-View-Presenter)

MVC는 View와 Model사이의 의존성이 존재했지만 MVP에서는 의존성을 제거
presenter는 `Model로 부터 데이터를 받는 역할을 대신 실천`, 또한 `view controller의 생명주기에 관여하지 않음`
presenter는 view에 쉽게 접근 가능
 
단순히 Controller의 부하만 떨어뜨린것이 아닌 "테스트 용이성"의 장점도 탄생

```swift
import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
}

class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of MVP
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter
```

## MVVM (Model-View-View Model)

View속에 Controller가 내제되어 있으며, View Model은 View에 대한 데이터 정보를 모르고(뷰 자체 안에서 처리) `데이터 바인딩 실현*`(contorller에 대한 부하 감소)
 
MVC에서는 Controller에 많은 부하를 주었기 때문에 View쪽으로 Controller를 보내고 비어있는 Controller자리에 중재역할을 할 View Model 개념을 삽입한 것 뿐
 
MVP는 Presenter가 View의 정보를 알고 update하지만, `MVVM에서는 View Model이 View의 정보를 모름`
 - `View와 View Model은 1대 n 관계`
 
* 데이터 바인딩(binding) : View와 View Model 사이의 양방향 데이터 흐름 (옵저버 프로퍼티의 didSet과 같은 것이 바인딩의 예 -단방향 바인딩)


```swift
import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingViewModelProtocol: class {
    var greeting: String? { get }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())? { get set } // function to call when greeting did change
    init(person: Person)
    func showGreeting()
}

class GreetingViewModel : GreetingViewModelProtocol {
    let person: Person
    var greeting: String? {
        didSet {
            self.greetingDidChange?(self)
        }
    }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())?
    required init(person: Person) {
        self.person = person
    }
    func showGreeting() {
        self.greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
    }
}

class GreetingViewController : UIViewController {
    var viewModel: GreetingViewModelProtocol! {
        didSet {
            self.viewModel.greetingDidChange = { [unowned self] viewModel in
                self.greetingLabel.text = viewModel.greeting
            }
        }
    }
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self.viewModel, action: "showGreeting", forControlEvents: .TouchUpInside)
    }
    // layout code goes here
}
// Assembling of MVVM
let model = Person(firstName: "David", lastName: "Blaine")
let viewModel = GreetingViewModel(person: model)
let view = GreetingViewController()
view.viewModel = viewModel

```

## VIPER

**LEGO building experience transferred into the iOS app design**

### 기본 구조

[VIPER-Template](https://github.com/zafarivaev/VIPER-Template)

### 역할 

- **Interactor** — contains business logic related to the data (**Entities**) or networking, like creating new instances of entities or fetching them from the server. For those purposes you’ll use some *Services* and *Managers* which are not considered as a part of VIPER module but rather an external dependency.

- **Presenter** — contains the UI related (but UIKit *independent*) business logic, invokes methods on the **Interactor**.

- **View** - 뷰의 기능만을 수행하게 만든다. 다른 모든 작업은 presenter나 interactor에게 맡긴다.

- **Entities** — your plain data objects, not the *data access layer,* because that is a responsibility of the **Interactor**.

- **Router** — responsible for the segues between the VIPER **modules**.


```swift

import UIKit

struct Person { // Entity (usually more complex e.g. NSManagedObject)
    let firstName: String
    let lastName: String
}

struct GreetingData { // Transport data structure (not Entity)
    let greeting: String
    let subject: String
}

protocol GreetingProvider {
    func provideGreetingData()
}

protocol GreetingOutput: class {
    func receiveGreetingData(greetingData: GreetingData)
}

class GreetingInteractor : GreetingProvider {
    weak var output: GreetingOutput!
    
    func provideGreetingData() {
        let person = Person(firstName: "David", lastName: "Blaine") // usually comes from data access layer
        let subject = person.firstName + " " + person.lastName
        let greeting = GreetingData(greeting: "Hello", subject: subject)
        self.output.receiveGreetingData(greeting)
    }
}

protocol GreetingViewEventHandler {
    func didTapShowGreetingButton()
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

class GreetingPresenter : GreetingOutput, GreetingViewEventHandler {
    weak var view: GreetingView!
    var greetingProvider: GreetingProvider!
    
    func didTapShowGreetingButton() {
        self.greetingProvider.provideGreetingData()
    }
    
    func receiveGreetingData(greetingData: GreetingData) {
        let greeting = greetingData.greeting + " " + greetingData.subject
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var eventHandler: GreetingViewEventHandler!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()

    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.eventHandler.didTapShowGreetingButton()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of VIPER module, without Router
let view = GreetingViewController()
let presenter = GreetingPresenter()
let interactor = GreetingInteractor()
view.eventHandler = presenter
presenter.view = view
presenter.greetingProvider = interactor
interactor.output = presenter

```

## References

More information on Markdown can be found at the following links:

- [Apple Model-View-Controller](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [Github awesome iOS architecture](https://github.com/onmyway133/awesome-ios-architecture)
- [Blog K7 Tech Agency](https://k7tech.agency/blog/ios-app-architecture-patterns)
- [Medium Which design architecture is suitable for iOS](https://medium.com/@vigneshwaran_89879/mvc-vs-mvvm-vs-mvp-vs-viper-which-design-architecture-is-suitable-for-ios-bb1f32328bd8)
- [Medium iOS Architecture Patterns](https://medium.com/ios-os-x-development/ios-architecture-patterns-ecba4c38de52)

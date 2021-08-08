# Design Patterns; Creational, Structural, Behavioral design

As a developer, you might already be familiar with the notion of design patterns in object-oriented programming. They were first authoritatively described and cataloged in Design Patterns: Elements of Reusable Object-Oriented Software, by Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides (commonly referred to as the “Gang of Four”). That book, originally published in 1994, was soon followed by other books and articles that further explored and elaborated design patterns in object-oriented systems.

## Design Pattern

> A design pattern is a template for a design that solves a general, recurring problem in a particular context. It is a tool of abstraction that is useful in fields like architecture and engineering as well as software development. 

A design pattern abstracts the key aspects of the structure of a concrete design that has proven to be effective over time. The pattern has a name and identifies the classes and objects that participate in the pattern along with their responsibilities and collaborations. It also spells out consequences (costs and benefits) and the situations in which the pattern can be applied. A design pattern is a kind of template or guide for a particular design; in a sense, a concrete design is an “instantiation” of a pattern. **Design patterns are not absolute**. There is some flexibility in how you can apply them, and often things such as programming language and existing architectures can determine how the pattern is applied.

Several themes or principles of design influence design patterns. These design principles are rules of thumb for constructing object-oriented systems, such as “encapsulate the aspects of system structure that vary” and “program to an interface, not an implementation.” They express important insights. For example, if you isolate the parts of a system that vary, and encapsulate them, they can vary independently of other parts of the system, especially if you define interfaces for them that are not tied to implementation specifics. You can later alter or extend those variable parts without affecting the other parts of the system. You thus eliminate dependencies and reduce couplings between parts, and consequently the system becomes more flexible and easier to change.

## Types of design patterns

Before i describe the most common architecture patterns in Swift, you should first learn the three types of software design patterns and how they differ:

### Creational

Creational software design patterns deal with object creation mechanisms. They try to instantiate objects in a manner suitable for the particular situation. Here are several creational design patterns:

- **Factory Method** - This pattern makes the codebase more flexible to add or remove new types.

- **Abstract Factory**

- **Builder**

- **Singleton** - The Singleton design pattern ensures a class only has one instance, and provides a global point of access to it.

- **Prototype**

### Structural

Structural design patterns aim to simplify the design by finding an easy way of realizing relationships between classes and objects. These are some structural architecture patterns:

- **Adapter** - This pattern converts the interface of a class into another interface that clients expect. Adapter lets classes work together that couldn’t otherwise because of incompatible interfaces. It decouples the client from the class of the targeted object.

- **Bridge**

- **Facade** - This pattern provides a single interface to a complex subsystem. Instead of exposing the user to a set of classes and their APIs, you only expose one simple unified API.

- **Decorator** - This pattern dynamically adds behaviors and responsibilities to an object without modifying its code. It’s an alternative to subclassing where you modify a class’s behavior by wrapping it with another object.

- **Composite** - This pattern composes related objects into tree structures to represent part-whole hierarchies. 

- **Flyweight**

- **Proxy** - This pattern provides a surrogate, or placeholder, for another object in order to control access them. This pattern is structurally similar to the Decorator pattern but it serves a different purpose; Decorator adds behavior to an object whereas Proxy controls access to an object.

### Behavioral

Behavioral design patterns identify common communication patterns between entities and implement these patterns. Behavioral design patterns include:

- **Chain of Responsibility** 

- **Template Method** - This pattern defines the skeleton of an algorithm in an operation, deferring some steps to subclasses.

- **Command** - This pattern encapsulates a request as an object, thereby letting you parameterize clients with different requests, queue or log requests, and support undoable operations.

- **Iterator** - This pattern provides a way to access the elements of an aggregate object (that is, a collection) sequentially without exposing its underlying representation.

- **Mediator** - This pattern defines an object that encapsulates how a set of objects interact. Mediator promotes loose coupling by keeping objects from referring to each other explicitly, and it lets you vary their interaction independently.

- **Memento** - In this pattern saves your stuff somewhere. Later on, this externalized state can be restored without violating encapsulation; that is, private data remains private.

- **Observer** - This pattern defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically. 

- **Strategy** - This pattern allows you to change the behaviour of an algorithm at run time. Using interfaces, we are able to define a family of algorithms, encapsulate each one, and make them interchangeable, allowing us to select which algorithm to execute at run time.

- **State**

- **Visitor**

# Most frequently used design patterns in Swift

I'm going to provide only the essential information about each software design pattern – namely, how it works from the technical point of view and when it should be applied. We’ll also give an illustrative example in the Swift programming language.

### Builder

> The Builder pattern is a creational design pattern that allows you to create complex objects from simple objects step by step. This design pattern helps you use the same code for creating different object views.

The Builder design pattern calls for separating the construction of an object from its own class. The construction of this object is instead assigned to special objects called builders and split into multiple steps. To create an object, you successively call builder methods. And you don’t need to go through all the steps – only those required for creating an object with a particular configuration.

**You should apply the Builder design pattern**

- When you want to avoid using a telescopic constructor (when a constructor has too many parameters, it gets difficult to read and manage)

- When your code needs to create different views of a specific object.

- When you need to compose complex objects.

**Example**

Suppose you’re developing an iOS application for a restaurant and you need to implement ordering functionality. You can introduce two structures, Dish and Order, and with the help of the OrderBuilder object, you can compose orders with different sets of dishes.

```swift
// Design Patterns: Builder
import Foundation

// Models
enum DishCategory: Int {
    case firstCourses, mainCourses, garnishes, drinks
}

struct Dish {
    var name: String
    var price: Float
}

struct OrderItem {
    var dish: Dish
    var count: Int
}

struct Order {
    var firstCourses: [OrderItem] = []
    var mainCourses: [OrderItem] = []
    var garnishes: [OrderItem] = []
    var drinks: [OrderItem] = []
    
    var price: Float {
        let items = firstCourses + mainCourses + garnishes + drinks
        return items.reduce(Float(0), { $0 + $1.dish.price * Float($1.count) })
    }
}

// Builder
class OrderBuilder {
    private var order: Order?
    
    func reset() {
        order = Order()
    }
    
    func setFirstCourse(_ dish: Dish) {
        set(dish, at: order?.firstCourses, withCategory: .firstCourses)
    }
    
    func setMainCourse(_ dish: Dish) {
        set(dish, at: order?.mainCourses, withCategory: .mainCourses)
    }
    
    func setGarnish(_ dish: Dish) {
        set(dish, at: order?.garnishes, withCategory: .garnishes)
    }
    
    func setDrink(_ dish: Dish) {
        set(dish, at: order?.drinks, withCategory: .drinks)
    }
    
    func getResult() -> Order? {
        return order ?? nil
    }
    
    private func set(_ dish: Dish, at orderCategory: [OrderItem]?, withCategory dishCategory: DishCategory) {
        guard let orderCategory = orderCategory else {
            return
        }
        
        var item: OrderItem! = orderCategory.filter( { $0.dish.name == dish.name } ).first
        
        guard item == nil else {
            item.count += 1
            return
        }
        
        item = OrderItem(dish: dish, count: 1)
        
        switch dishCategory {
        case .firstCourses:
            order?.firstCourses.append(item)
        case .mainCourses:
            order?.mainCourses.append(item)
        case .garnishes:
            order?.garnishes.append(item)
        case .drinks:
            order?.drinks.append(item)
        }
    }
}

// Usage
let steak = Dish(name: "Steak", price: 2.30)
let chips = Dish(name: "Chips", price: 1.20)
let coffee = Dish(name: "Coffee", price: 0.80)

let builder = OrderBuilder()
builder.reset()
builder.setMainCourse(steak)
builder.setGarnish(chips)
builder.setDrink(coffee)

let order = builder.getResult()
order?.price

// Result:
// 4.30
```

### Adapter 

> Adapter is a structural design pattern that allows objects with incompatible interfaces to work together. In other words, it transforms the interface of an object to adapt it to a different object.

An adapter wraps an object, therefore concealing it completely from another object. For example, you could wrap an object that handles meters with an adapter that converts data into feet.

**You should apply the Adapter design pattern**

- when you want to use a third-party class but its interface doesn’t match the rest of your application’s code.

- when you need to use several existing subclasses but they lack particular functionality and, on top of that, you can’t extend the superclass.

**Example**

Suppose you want to implement a calendar and event management functionality in your iOS application. To do this, you should integrate the EventKit framework and adapt the Event model from the framework to the model in your application. An Adapter can wrap the model of the framework and make it compatible with the model in your application.

```swift
// Design Patterns: Adapter
import EventKit

// Models
protocol Event: class {
    var title: String { get }
    var startDate: String { get }
    var endDate: String { get }
}

extension Event {
    var description: String {
        return "Name: \(title)\nEvent start: \(startDate)\nEvent end: \(endDate)"
    }
}

class LocalEvent: Event {
    var title: String
    var startDate: String
    var endDate: String
    
    init(title: String, startDate: String, endDate: String) {
        self.title = title
        self.startDate = startDate
        self.endDate = endDate
    }
}

// Adapter
class EKEventAdapter: Event {
    private var event: EKEvent
    
    private lazy var dateFormatter: DateFormatter = {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "MM-dd-yyyy HH:mm"
        return dateFormatter
    }()
    
    var title: String {
        return event.title
    }
    var startDate: String {
        return dateFormatter.string(from: event.startDate)
    }
    var endDate: String {
        return dateFormatter.string(from: event.endDate)
    }
    
    init(event: EKEvent) {
        self.event = event
    }
}

// Usage
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "MM/dd/yyyy HH:mm"

let eventStore = EKEventStore()
let event = EKEvent(eventStore: eventStore)
event.title = "Design Pattern Meetup"
event.startDate = dateFormatter.date(from: "06/29/2018 18:00")
event.endDate = dateFormatter.date(from: "06/29/2018 19:30")

let adapter = EKEventAdapter(event: event)
adapter.description
```

### Decorator 

> The Decorator pattern is a structural design pattern that allows you to dynamically attach new functionalities to an object by wrapping them in useful wrappers.

No wonder this design pattern is also called the Wrapper design pattern. This name describes more precisely the core idea behind this pattern: you place a target object inside another wrapper object that triggers the basic behavior of the target object and adds its own behavior to the result.

**You should apply the Decorator design pattern**

- when you want to add responsibilities to objects dynamically and conceal those objects from the code that uses them.

- when it’s impossible to extend responsibilities of an object through inheritance.

**Example**

Imagine you need to implement data management in your iOS application. You could create two decorators: EncryptionDecorator for encrypting and decrypting data and EncodingDecorator for encoding and decoding.

```swift
// Design Patterns: Decorator
import Foundation

// Helpers (may be not include in blog post)
func encryptString(_ string: String, with encryptionKey: String) -> String {
    let stringBytes = [UInt8](string.utf8)
    let keyBytes = [UInt8](encryptionKey.utf8)
    var encryptedBytes: [UInt8] = []
    
    for stringByte in stringBytes.enumerated() {
        encryptedBytes.append(stringByte.element ^ keyBytes[stringByte.offset % encryptionKey.count])
    }
    
    return String(bytes: encryptedBytes, encoding: .utf8)!
}

func decryptString(_ string: String, with encryptionKey: String) -> String {
    let stringBytes = [UInt8](string.utf8)
    let keyBytes = [UInt8](encryptionKey.utf8)
    var decryptedBytes: [UInt8] = []
    
    for stringByte in stringBytes.enumerated() {
        decryptedBytes.append(stringByte.element ^ keyBytes[stringByte.offset % encryptionKey.count])
    }
    
    return String(bytes: decryptedBytes, encoding: .utf8)!
}

// Services
protocol DataSource: class {
    func writeData(_ data: Any)
    func readData() -> Any
}

class UserDefaultsDataSource: DataSource {
    private let userDefaultsKey: String
    
    init(userDefaultsKey: String) {
        self.userDefaultsKey = userDefaultsKey
    }
    
    func writeData(_ data: Any) {
        UserDefaults.standard.set(data, forKey: userDefaultsKey)
    }
    
    func readData() -> Any {
        return UserDefaults.standard.value(forKey: userDefaultsKey)!
    }
}

// Decorators
class DataSourceDecorator: DataSource {
    let wrappee: DataSource
    
    init(wrappee: DataSource) {
        self.wrappee = wrappee
    }
    
    func writeData(_ data: Any) {
        wrappee.writeData(data)
    }
    
    func readData() -> Any {
        return wrappee.readData()
    }
}

class EncodingDecorator: DataSourceDecorator {
    private let encoding: String.Encoding
    
    init(wrappee: DataSource, encoding: String.Encoding) {
        self.encoding = encoding
        super.init(wrappee: wrappee)
    }
    
    override func writeData(_ data: Any) {
        let stringData = (data as! String).data(using: encoding)!
        wrappee.writeData(stringData)
    }
    
    override func readData() -> Any {
        let data = wrappee.readData() as! Data
        return String(data: data, encoding: encoding)!
    }
}

class EncryptionDecorator: DataSourceDecorator {
    private let encryptionKey: String
    
    init(wrappee: DataSource, encryptionKey: String) {
        self.encryptionKey = encryptionKey
        super.init(wrappee: wrappee)
    }
    
    override func writeData(_ data: Any) {
        let encryptedString = encryptString(data as! String, with: encryptionKey)
        wrappee.writeData(encryptedString)
    }
    
    override func readData() -> Any {
        let encryptedString = wrappee.readData() as! String
        return decryptString(encryptedString, with: encryptionKey)
    }
}

// Usage
var source: DataSource = UserDefaultsDataSource(userDefaultsKey: "decorator")
source = EncodingDecorator(wrappee: source, encoding: .utf8)
source = EncryptionDecorator(wrappee: source, encryptionKey: "secret")
source.writeData("Design Patterns")
source.readData() as! String
```

### Facade 

> The Facade pattern is a structural design pattern that provides a simple interface to a library, framework, or complex system of classes.

Imagine that your code has to deal with multiple objects of a complex library or framework. You need to initialize all these objects, keep track of the right order of dependencies, and so on. As a result, the business logic of your classes gets intertwined with implementation details of other classes. Such code is difficult to read and maintain.

The Facade pattern provides a simple interface for working with complex subsystems containing lots of classes. The Facade pattern offers a simplified interface with limited functionality that you can extend by using a complex subsystem directly. This simplified interface provides only the features a client needs while concealing all others.

**You should apply the Facade design pattern**

- when you want to provide a simple or unified interface to a complex subsystem.

- when you need to decompose a subsystem into separate layers.

**Example**

Lots of modern mobile applications support audio recording and playback, so let’s suppose you need to implement this functionality. You could use the Facade pattern to hide the implementation of services responsible for the file system (**FileService**), audio sessions (**AudioSessionService**), audio recording (**RecorderService**), and audio playback (**PlayerService**). The Facade provides a simplified interface for this rather complex system of classes.

```swift
// Design Patterns: Facade
import AVFoundation

// Services (may be not include in blog post)
struct FileService {
    private var documentDirectory: URL {
        return FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
    }
    
    var contentsOfDocumentDirectory: [URL] {
        return try! FileManager.default.contentsOfDirectory(at: documentDirectory, includingPropertiesForKeys: nil)
    }
    
    func path(withPathComponent component: String) -> URL {
        return documentDirectory.appendingPathComponent(component)
    }
    
    func removeItem(at index: Int) {
        let url = contentsOfDocumentDirectory[index]
        try! FileManager.default.removeItem(at: url)
    }
}

protocol AudioSessionServiceDelegate: class {
    func audioSessionService(_ audioSessionService: AudioSessionService, recordPermissionDidAllow allowed: Bool)
}

class AudioSessionService {
    weak var delegate: AudioSessionServiceDelegate?
    
    func setupSession() {
        try! AVAudioSession.sharedInstance().setCategory(AVAudioSessionCategoryPlayAndRecord, with: [.defaultToSpeaker])
        try! AVAudioSession.sharedInstance().setActive(true)
        
        AVAudioSession.sharedInstance().requestRecordPermission { [weak self] allowed in
            DispatchQueue.main.async {
                guard let strongSelf = self, let delegate = strongSelf.delegate else {
                    return
                }
                
                delegate.audioSessionService(strongSelf, recordPermissionDidAllow: allowed)
            }
        }
    }
    
    func deactivateSession() {
        try! AVAudioSession.sharedInstance().setActive(false)
    }
}

struct RecorderService {
    private var isRecording = false
    private var recorder: AVAudioRecorder!
    private var url: URL
    
    init(url: URL) {
        self.url = url
    }
    
    mutating func startRecord() {
        guard !isRecording else {
            return
        }
        
        isRecording = !isRecording
        recorder = try! AVAudioRecorder(url: url, settings: [AVFormatIDKey: kAudioFormatMPEG4AAC])
        recorder.record()
    }
    
    mutating func stopRecord() {
        guard isRecording else {
            return
        }
        
        isRecording = !isRecording
        recorder.stop()
    }
}

protocol PlayerServiceDelegate: class {
    func playerService(_ playerService: PlayerService, playingDidFinish success: Bool)
}

class PlayerService: NSObject, AVAudioPlayerDelegate {
    private var player: AVAudioPlayer!
    private var url: URL
    weak var delegate: PlayerServiceDelegate?
    
    init(url: URL) {
        self.url = url
    }
    
    func startPlay() {
        player = try! AVAudioPlayer(contentsOf: url)
        player.delegate = self
        player.play()
    }
    
    func stopPlay() {
        player.stop()
    }
    
    func audioPlayerDidFinishPlaying(_ player: AVAudioPlayer, successfully flag: Bool) {
        delegate?.playerService(self, playingDidFinish: flag)
    }
}

// Facade
protocol AudioFacadeDelegate: class {
    func audioFacadePlayingDidFinish(_ audioFacade: AudioFacade)
}

class AudioFacade: PlayerServiceDelegate {
    private let audioSessionService = AudioSessionService()
    private let fileService = FileService()
    private let fileFormat = ".m4a"
    private var playerService: PlayerService!
    private var recorderService: RecorderService!
    weak var delegate: AudioFacadeDelegate?
    
    private lazy var dateFormatter: DateFormatter = {
        let dateFormatter = DateFormatter()
        dateFormatter.dateFormat = "yyyy-MM-dd_HH:mm:ss"
        return dateFormatter
    }()
    
    init() {
        audioSessionService.setupSession()
    }
    
    deinit {
        audioSessionService.deactivateSession()
    }
    
    func startRecord() {
        let fileName = dateFormatter.string(from: Date()).appending(fileFormat)
        let url = fileService.path(withPathComponent: fileName)
        recorderService = RecorderService(url: url)
        recorderService.startRecord()
    }
    
    func stopRecord() {
        recorderService.stopRecord()
    }
    
    func numberOfRecords() -> Int {
        return fileService.contentsOfDocumentDirectory.count
    }
    
    func nameOfRecord(at index: Int) -> String {
        let url = fileService.contentsOfDocumentDirectory[index]
        return url.lastPathComponent
    }
    
    func removeRecord(at index: Int) {
        fileService.removeItem(at: index)
    }
    
    func playRecord(at index: Int) {
        let url = fileService.contentsOfDocumentDirectory[index]
        playerService = PlayerService(url: url)
        playerService.delegate = self
        playerService.startPlay()
    }
    
    func stopPlayRecord() {
        playerService.stopPlay()
    }
    
    func playerService(_ playerService: PlayerService, playingDidFinish success: Bool) {
        if success {
            delegate?.audioFacadePlayingDidFinish(self)
        }
    }
}

// Usage
let audioFacade = AudioFacade()
audioFacade.numberOfRecords()

// Result:
// 0
```

### Template Method

> The Template Method pattern is a behavioral design pattern that defines a skeleton for an algorithm and delegates responsibility for some steps to subclasses. This pattern allows subclasses to redefine certain steps of an algorithm without changing its overall structure.

This design pattern splits an algorithm into a sequence of steps, describes these steps in separate methods, and calls them consecutively with the help of a single template method.

**You should apply the Template Method design pattern**

- when subclasses need to extend a basic algorithm without modifying its structure.

- when you have several classes responsible for quite similar actions (meaning that whenever you modify one class, you need to change the other classes).

**Example**

Suppose you’re working on an iOS app that must be able to take and save pictures. Therefore, your application needs to get permissions to use the iPhone (or iPad) camera and image gallery. To do this, you can use the **PermissionService** base class that has a specific algorithm. To get permission to use the camera and gallery, you can create two subclasses, **CameraPermissionService** and **PhotoPermissionService**, that redefine certain steps of the algorithm while keeping other steps the same.

```swift
// Design Patterns: Template Method
import AVFoundation
import Photos

// Services
typealias AuthorizationCompletion = (status: Bool, message: String)

class PermissionService: NSObject {
    private var message: String = ""
    
    func authorize(_ completion: @escaping (AuthorizationCompletion) -> Void) {
        let status = checkStatus()
        
        guard !status else {
            complete(with: status, completion)
            return
        }
        
        requestAuthorization { [weak self] status in
            self?.complete(with: status, completion)
        }
    }

    func checkStatus() -> Bool {
        return false
    }
    
    func requestAuthorization(_ completion: @escaping (Bool) -> Void) {
        completion(false)
    }
    
    func formMessage(with status: Bool) {
        let messagePrefix = status ? "You have access to " : "You haven't access to "
        let nameOfCurrentPermissionService = String(describing: type(of: self))
        let nameOfBasePermissionService = String(describing: type(of: PermissionService.self))
        let messageSuffix = nameOfCurrentPermissionService.components(separatedBy: nameOfBasePermissionService).first!
        message = messagePrefix + messageSuffix
    }
    
    private func complete(with status: Bool, _ completion: @escaping (AuthorizationCompletion) -> Void) {
        formMessage(with: status)
        
        let result = (status: status, message: message)
        completion(result)
    }
}

class CameraPermissionService: PermissionService {
    override func checkStatus() -> Bool {
        let status = AVCaptureDevice.authorizationStatus(for: .video).rawValue
        return status == AVAuthorizationStatus.authorized.rawValue
    }
    
    override func requestAuthorization(_ completion: @escaping (Bool) -> Void) {
        AVCaptureDevice.requestAccess(for: .video) { status in
            completion(status)
        }
    }
}

class PhotoPermissionService: PermissionService {
    override func checkStatus() -> Bool {
        let status = PHPhotoLibrary.authorizationStatus().rawValue
        return status == PHAuthorizationStatus.authorized.rawValue
    }
    
    override func requestAuthorization(_ completion: @escaping (Bool) -> Void) {
        PHPhotoLibrary.requestAuthorization { status in
            completion(status.rawValue == PHAuthorizationStatus.authorized.rawValue)
        }
    }
}

// Usage
let permissionServices = [CameraPermissionService(), PhotoPermissionService()]

for permissionService in permissionServices {
    permissionService.authorize { (_, message) in
        print(message)
    }
}

// Result:
// You have access to Camera
// You have access to Photo
```

## References

More information on Markdown can be found at the following links:

- [Medium Real World: iOS Design Patterns](https://medium.com/cocoaacademymag/real-world-ios-design-patterns-3e5aad172094)
- [Apple Cocoa Fundamentals Guide](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaDesignPatterns/CocoaDesignPatterns.html#//apple_ref/doc/uid/TP40002974-CH6-SW6)
- [Blog iOS Design Patterns](https://stfalcon.com/en/blog/post/ios-patterns)
- [Blog Software design patterns](https://rubygarage.org/blog/swift-design-patterns?utm_source=mybridge&utm_medium=blog&utm_campaign=read_more)
- [Medium iOS: Design Patterns](https://medium.com/@chetan15aga/ios-design-patterns-f478abd78132)


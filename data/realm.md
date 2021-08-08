# [Realm] Basic Example Code

Realm은 작업 속도가 빠른 모바일용 데이터베이스 라이브러리 오픈소스

기존 데이터를 읽어오던 방식과는 전혀 다른 방식.

Realm은 객체 형태로 데이터를 읽어오고 객체 형태로써 바로 DB에 저장이 가능하게 함으로써,

기존의 가공을 하는 과정을 해결한 솔류션.

## Install

```pod
pod 'RealmSwift'
```

## Practice

프로퍼티를 생성할 때 @objc와 dynamic 키워드를 붙여줍니다. dynamic 키워드를 붙이는 이유는 Runtime 일때, Realm이 자동으로 변수의 변화를 탐지하기 위해서이고 이러한 과정이 objective-c를 통해 이루어지기 때문에 @objc 키워드를 덧붙입니다.

```swift
// Define your models like regular Swift classes
class Dog: Object { //Realm을 사용할 클래스에는 Object를
    @objc dynamic var name = "" //@objc dynamic 키워드를 꼭 붙여야한다. 
    @objc dynamic var age = 0
}
class Person: Object {
    @objc dynamic var name = ""
    @objc dynamic var picture: Data? = nil // optionals supported
    let dogs = List<Dog>()
}

// Use them like regular Swift objects
let myDog = Dog()
myDog.name = "Rex"
myDog.age = 1
print("name of dog: \(myDog.name)")

// Get the default Realm
let realm = try! Realm()

// Query Realm for all dogs less than 2 years old
let puppies = realm.objects(Dog.self).filter("age < 2")
puppies.count // => 0 because no dogs have been added to the Realm yet

// Persist your data easily
try! realm.write {
    realm.add(myDog)
}

// Queries are updated in realtime
puppies.count // => 1

// Query and update from any thread
DispatchQueue(label: "background").async {
    autoreleasepool {
        let realm = try! Realm()
        let theDog = realm.objects(Dog.self).filter("age == 1").first
        try! realm.write {
            theDog!.age = 3
        }
    }
}

// Read
//realm 데이터베이스를 불러올때 반환값은 Results<>타입의 값으로 반환됩니다. 따라서 읽어온 데이터를 받을 변수의 타입을 Results<>로 지정해야 합니다
var dog: Results<Dog>
// realm 데이터베이스를 불러올때는 반환타입이 Results<>타입이다.
dog = realm.objects(Dog.self)
// realm.objects(모델클래스명.self)

//Update
//realm 데이터베이스에 등록된 데이터들의 변경은 realm.write 안에서만 이루어져야 합니다.
do {
    try realm.write{
        myDog.name = "Ted"
    }
} catch {
	print("Error Update \(error)")
}

//Delete
do{
    try realm.write{
        realm.delete(myDog)
    }
} catch {
	print("Error Delete \(error)")
}
```

ViewDidLoad()에 함수를 호출해서 Documenet 폴더의 주소를 찾아내기.

```swift
func getDocumentsDirectory() -> URL {
    let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
    let documentsDirectory = paths[0]
    return documentsDirectory
}
```

## References

[Realm Getting Started](https://docs.mongodb.com/realm-legacy/docs/swift/latest/)

[Realm Database Tutorial for iOS](https://www.youtube.com/watch?v=PmsJW59rNY8)

[간단한 Swift Realm 라이브러리 사용법](https://tonyw.tistory.com/3)
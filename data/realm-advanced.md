# [Realm] Things that I realized

Realm을 사용하면서 깨달은 점 끄적끄적. 확실히 사용하기 편하다 하지만 몇가지 조심해야할 점이 있다.

## Position of Instance

```swift
try! realm.write() { 
    // 1
    let languages = [English, French, Korean]
    
    for language in languages {
        // 2
        let realmLanguage = RealmLanguage()
        realmLanguage.name = language
        realm.add(realmLanguage)
    }
}
```

`let realmLanguage = RealmLanguage()` 이 부분의 코드를 1번 자리에 두고 계속 사용하였다.

```swift
RealmLanguage(name: Korean)
RealmLanguage(name: Korean)
RealmLanguage(name: Korean)
```

하지만 워와 같이 원하지 않은 이상한 결과만 계속 나왔다. 생각해보면 간단하게 해결할 수 있었던 문제였지만, 고민없는 코딩으로 몇 시간을 허비하였다.

Realm을 이용하요 Array 값 하나 하나를 입력해줄 때는 반드시 2번자리에 넣어주어야 한다. 그렇지 않으면 3개 값을 넣었다면 마지막 변수의 값으로 모두 바뀐다.

## NSPredicate

CoreData를 이용했을 떄와 같이 NSPredicate를 이용해 특정 Object를 추출해서 사용할 수 있다.

```swift
NSPredicate(format: "universe == %@", "Star Wars"))
NSPredicate(format: "name < %@", "F")) 
NSPredicate(format: "universe IN %@", ["Aliens", "Firefly", "Star Trek"])
NSPredicate(format: "name BEGINSWITH %@", "E"))
NSPredicate(format: "name BEGINSWITH[c] %@", "e"))
NSPredicate(format: "NOT name BEGINSWITH[c] %@", "e"))
```

## RealmManager

RealmManager를 만들어서 편하게 사용하자. 내가 이용했던 RealmManager는 아래와 같다.

```swift
//
//  RealmManager.swift
//  Multilingual News
//
//  Created by Ted on 2021/04/23.
//

import Foundation
import RealmSwift

let realmObject = try! Realm()

class RealmManager: NSObject {
    
    static let shared = RealmManager()
    
    func retrievePredicatedDataForObject(_ T : Object.Type, with criteria: NSPredicate) -> [Object] {
        
        var objects = [Object]()
        for result in realmObject.objects(T).filter(criteria) {
            objects.append(result)
        }
        return objects
    }
    
    func retrieveAllDataForObject(_ T : Object.Type) -> [Object] {
        
        var objects = [Object]()
        for result in realmObject.objects(T) {
            objects.append(result)
        }
        return objects
    }
    
    func deleteAllDataForObject(_ T : Object.Type) {
        
        self.delete(self.retrieveAllDataForObject(T))
    }
    
    func replaceAllDataForObject(_ T : Object.Type, with objects : [Object]) {
        
        deleteAllDataForObject(T)
        add(objects)
    }
    
    func add(_ object : Object) {
        
        try! realmObject.write {
            
            realmObject.add(object)
        }
    }
    
    func add(_ objects : [Object], completion : @escaping() -> Void) {
        
        try! realmObject.write {
            
            realmObject.add(objects)
            completion()
        }
    }
    
    func add(_ objects : [Object]) {
        
        try! realmObject.write {
            
            realmObject.add(objects)
        }
    }
    
    func update(_ block: @escaping () -> Void) {
        
        try! realmObject.write(block)
    }
    
    func delete(_ objects : [Object]) {
        
        try! realmObject.write {
            realmObject.delete(objects)
        }
    }
    
    func getDocumentsDirectory() -> URL {
        let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
        let documentsDirectory = paths[0]
        return documentsDirectory
    }
}
```
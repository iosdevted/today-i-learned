# [Core Data] Basic Example Code

1) In-Memory 방식이 존재 : 사용 하려는 모든 데이터는 우선 메모리에 로딩되는 과정이 존재

 - 코어 데이터 : 읽고 쓰는 모든 데이터는 원칙적으로 메모리에 로드된 다음에 처리
                       (영구 저장소를 아예 사용하지 않고 순수하게 인메모리 방식으로만 사용하는 것이 가능)
* In - Memory란? 말 그대로, Disc에 저장하지 않고 휘발성으로 테스트 같이 잠깐 이용하려고 하는 경우에 In-Memory로 사용가능
 - 다른 DB방식 : 효율성을 위해 읽기 목적의 데이터 일부만 메모리에 로드
 
2) 엔터티(Entity)를 통해 데이터 저장 구조 정의
(다른 DB방식은 일반적으로 테이블을 통해 데이터 저장 구조를 정의)
 
3) 엔터티(Entity) : 데이터가 저장될 구조

 - 어트리뷰트(Attribute) : 엔터티의 하위 속성들을 정의하는 역할
 - 릴레이션(Relation) : 엔터티끼리의 관계 정의
 - 페치속성(Fatched Properties) : 템플릿 형태로 만들어 놓은 것(반복되는 요청이나 값만 바꾸어 비슷한 요청들을 묶어놓은 것)

4) 데이터를 객체로 취급 (테이블의 행, 레코드 하나하나를 독립된 객체로 사용)
 (사원 정보의 레코드 정보 2개를 읽을 땐 2개의 사원 객체가 생성)
 
5) 데이터 접근 방법 : DAO패턴
   SQLite에서 DAO클래스를 만들고 그 객체로 접근하는 것과 같이 사용 (단, core data에서는 DAO객체를 자동으로 제공)
 
6) 관리 객체(Managed Object) : MO패턴
   VO(Value Object)패턴과 동일, core data에서는 MO패턴이라 하며 MO클래스의 프로퍼티를 엔터티의 각 어트리뷰트와 직접 연결시키는 방식을 사용(*ORM매핑)
 *ORM(Object Relation Mapping) 매핑 : 객체와 관계형데이터 베이스를 자동으로 mapping시켜주는 방식

## Structure

1) 관리 객체(Managed Obejct) : NSManagedObject
 table에서 레코드를 읽을 때 core data에서는 객체가 생성되는데, 이 객체를 저장하는 자료형
예) 직원들의 데이터를 다룰 때 DB에서 직원들의 정보를 읽어오면 이것을 그대로 사용하지 않고 VO인스턴스에 담아 사용, 이때 VO가 관리 객체에 해당
 
2) 관리 객체 컨텍스트(Managed Object Context)
 핵심적인 두 가지 역할
 (1) MO를 가지고 CRUD역할 (Core Data에서 생성되는 모든 관리 객체는 컨텍스트에 담겨 관리)
    - 컨텍스트에 담긴 객체는 영구 저장소로 보내 저장, 삭제 가능
    - core data는 메모리에 로드된 상태로 처리되는데, 이 때의 메모리가 "컨텍스트"를 의미
 (2) "영구 저장소"와 "영구 저장소 코디네이터"에 대한 관리자 역할
    - 읽기와 쓰기를 영구 저장소에 요청 (DAO패턴과 유사)
 
3) 영구 저장소 코디네이터(Persistent Store Coordinator)
 컨텍스트와 직접 데이터를 주고 받으면서 다양한 영구 저장소들의 접근을 조정하고 입출력을 담당
 흐름 : 컨텍스트가 데이터 요청 -> 코디네이터가 요청을 받고, 영구 저장소에서 데이터 탐색 -> 코디 네이터가 MO인스턴스 생성하여 반환
 
4) 관리 객체 모델(Managed Obejct Model)
엔터티(Entity)의 구조를 정의하는 객체인 동시에 이를 바탕으로 MO패턴의 모델 클래스를 참조
※ MO vs MOM(Managed Object Model)
 - MOM : 클래스이자 형식이고 구조를 의미
                데이터를 CRUD하지 않으며 관리 객체의 각 요소를 제대로 담을 수 있도록 저장 데이터를 구조화
 - MO : MOM을 바탕으로 생성된 인스턴스
 
5) 영구 객체 저장소(Persistent Obejct Store)
 - 초기에는 직접 읽을 수 있으며 디버깅에 용이한 XML저장소 타입을 사용하며,
   앱을 배포할 당시 대량의 데이터를 고려하여 SQLite데이터베이스를 사용하는 것이 용이

## Example

```swift
//
//  PersistenceManager.swift
//  one-article
//
//  Created by Ted on 2021/03/24.
//

import UIKit
import CoreData

class PersistenceManager {

    static let shared: PersistenceManager = PersistenceManager()

    var persistentContainer: NSPersistentContainer = {
        let container = NSPersistentContainer(name: "Setting")

        container.loadPersistentStores(completionHandler: { (_, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        return container
    }()

    var context: NSManagedObjectContext {
        return self.persistentContainer.viewContext
    }

    func saveContext() {
        if context.hasChanges {
            do {
                try context.save()
                print("Data Saved to Context")
            } catch {
                // Replace this implementation with code to handle the error appropriately.
                // fatalError() causes the application to generate a crash log and terminate.
                // You should not use this function in a shipping application, although it may be useful during development.
                let nserror = error as NSError
                fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
            }
        }
    }

    func fetch<T: NSManagedObject>(request: NSFetchRequest<T>) -> [T] {
        do {
            let fetchResult = try self.context.fetch(request)
            return fetchResult
        } catch {
            print(error.localizedDescription)
            return []
        }
    }

    @discardableResult
    func insertLanguage(language: Setting) -> Bool {
        let entity = NSEntityDescription.entity(forEntityName: "Languages", in: self.context)
        if let entity = entity {
            let managedObject = NSManagedObject(entity: entity, insertInto: self.context)
            managedObject.setValue(language.isChecked, forKey: "isChecked")
            managedObject.setValue(language.title, forKey: "title")
            managedObject.setValue(language.code, forKey: "code")
            managedObject.setValue(language.icon, forKey: "icon")

            do {
                // self.context.mergePolicy = NSMergeByPropertyObjectTrumpMergePolicy
                try self.context.save()
                return true
            } catch {
                print(error.localizedDescription)
                return false
            }
        } else {
            return false
        }
    }

    @discardableResult
    func delete(object: NSManagedObject) -> Bool {
        self.context.delete(object)

        do {
            try self.context.save()
            return true
        } catch {
            return false
        }
    }

    func count<T: NSManagedObject>(request: NSFetchRequest<T>) -> Int? {
        do {
            let count = try self.context.count(for: request)
            return count
        } catch {
            return nil
        }
    }

    @discardableResult
    func deleteAll<T: NSManagedObject>(request: NSFetchRequest<T>) -> Bool {
        do {
            let request: NSFetchRequest<NSFetchRequestResult> = T.fetchRequest()
            let delete = NSBatchDeleteRequest(fetchRequest: request)

            try self.context.execute(delete)
            return true
        } catch {
            print(error.localizedDescription)
            return false
        }
    }
}
```

```swift
//
//  InitialCoreData.swift
//  Multilingual News
//
//  Created by Ted on 2021/04/06.
//

import UIKit
import CoreData

class InitialCoreData {

    static let standard: InitialCoreData = InitialCoreData()

    func saveInitialData(persistenceManager: PersistenceManager) {
        let language1 = Setting(isChecked: true, title: "English", code: "us", icon: "united-states-of-america")
        let language2 = Setting(isChecked: true, title: "French", code: "fr", icon: "france")
        let language3 = Setting(isChecked: true, title: "Japanese", code: "jp", icon: "japan")
        let language4 = Setting(isChecked: true, title: "Korean", code: "kr", icon: "south-korea")
        let language5 = Setting(isChecked: false, title: "Chinese", code: "cn", icon: "china")
        let language6 = Setting(isChecked: false, title: "Russian", code: "ru", icon: "russia")
        let language7 = Setting(isChecked: false, title: "German", code: "de", icon: "germany")
        let language8 = Setting(isChecked: false, title: "Italian", code: "it", icon: "italy")
        let language9 = Setting(isChecked: false, title: "Portuguese", code: "pt", icon: "portugal")
        let language10 = Setting(isChecked: false, title: "Dutch", code: "nl", icon: "netherlands")
        let language11 = Setting(isChecked: false, title: "Swedish", code: "se", icon: "sweden")
        let language12 = Setting(isChecked: false, title: "Norwegian", code: "no", icon: "norway")

        persistenceManager.insertLanguage(language: language1)
        persistenceManager.insertLanguage(language: language2)
        persistenceManager.insertLanguage(language: language3)
        persistenceManager.insertLanguage(language: language4)
        persistenceManager.insertLanguage(language: language5)
        persistenceManager.insertLanguage(language: language6)
        persistenceManager.insertLanguage(language: language7)
        persistenceManager.insertLanguage(language: language8)
        persistenceManager.insertLanguage(language: language9)
        persistenceManager.insertLanguage(language: language10)
        persistenceManager.insertLanguage(language: language11)
        persistenceManager.insertLanguage(language: language12)
    }
}
```

```swift
// Fetch
let request: NSFetchRequest<Contact> = Contact.fetchRequest()
let fetchResult = PersistenceManager.shared.fetch(request: request) // [Contact]

// Save
let walker = Person(name: "Walker", phoneNumber: "010-1234-5678", shortcutNumber: 2)
PersistenceManager.shared.insertPerson(person: walker)

// Delete
let request: NSFetchRequest<Contact> = Contact.fetchRequest()
let fetchResult = PersistenceManager.shared.fetch(request: request)
PersistenceManager.shared.delete(object: fetchResult.last!)

// DeleteAll
let request: NSFetchRequest<Contact> = Contact.fetchRequest()
PersistenceManager.shared.deleteAll(request: request)
let arr = PersistenceManager.shared.fetch(request: request)
if arr.isEmpty {
    print("clean") // Print "clean"
}
```

## Merge Conflict

[Core Data Merge Conflict](https://developer.apple.com/forums/thread/652073)

[NSMergeConflict for NSManagedObject](https://stackoverflow.com/questions/7877248/nsmergeconflict-for-nsmanagedobject)

[NSPersistentContainer concurrency for saving to core data](https://stackoverflow.com/questions/42733574/nspersistentcontainer-concurrency-for-saving-to-core-data)

## References

[Save json to CoreData as String and use the String to create array of objects](https://stackoverflow.com/questions/51869261/save-json-to-coredata-as-string-and-use-the-string-to-create-array-of-objects/51873317)

[Getting Started with Core Data Tutorial](https://www.raywenderlich.com/7569-getting-started-with-core-data-tutorial)

[Saving Locations in Core Data](https://www.raywenderlich.com/books/ios-apprentice/v8.2/chapters/32-saving-locations)

[김종권의 iOS 앱 개발 알아가기 - 코어 데이터(Core Data)의 개념](https://ios-development.tistory.com/89)
# [RxSwift] Traits

Swift has a powerful type system that can be used to improve the correctness and stability of applications and make using Rx a more intuitive and straightforward experience.

Traits help communicate and ensure observable sequence properties across interface boundaries, as well as provide contextual meaning, syntactical sugar and target more specific use-cases when compared to a raw Observable, which could be used in any context. For that reason, Traits are entirely optional. You are free to use raw Observable sequences everywhere in your program as all core RxSwift/RxCocoa APIs support them.

## How they work

```swift
struct Single<Element> {
    let source: Observable<Element>
}

struct Driver<Element> {
    let source: Observable<Element>
}
```

You can think of them as a kind of builder pattern implementation for Observable sequences. When a Trait is built, calling .asObservable() will transform it back into a vanilla observable sequence.

## 설명

1. `Single` : 성공이냐, 실패냐를 따지는 one-time 일에 적합 (파일 다운로드, 디스크로딩)

 - Single(.success(value)) = .next + .completed
 - Single(.error(value))
 
2. `Completable` : single과 기능이 유사하지만, value값을 emit하지 않음. 즉, 일이 제대로 됐는지만 검토할 때 사용(파일 쓰기)

- Completable(.completed)
- Completable(.error(value))
 
3. `Maybe` : Single + Completable

```swift
example(of: "Single") {
     
     let disposeBag = DisposeBag()
     
     enum FileReadError: Error {
         case fileNotFound, unreadable, encodingFailed
     }
     
     func loadText(from name: String) -> Single<String> {

         return Single.create{ single in
             
             let disposable = Disposables.create()
             
             guard let path = Bundle.main.path(forResource: name, ofType: "txt") else {
                 single(.error(FileReadError.fileNotFound))
                 return disposable
             }
             
             guard let data = FileManager.default.contents(atPath: path) else {
                 single(.error(FileReadError.unreadable))
                 return disposable
             }
             
             guard let contents = String(data: data, encoding: .utf8) else {
                 single(.error(FileReadError.encodingFailed))
                 return disposable
             }
             
             single(.success(contents))
             return disposable
         }
     }
    
    // test
    loadText(from: "myFile")
           .subscribe{
               switch $0 {
               case .success(let string):
                   print(string)
               case .error(let error):
                   print(error)
               }
           }
           .disposed(by: disposeBag)
    
    // prints: fileNotFound
    
 }
```
# [RxSwift] observeOn vs subscribeOn

There are two main operators that work with schedulers, observeOn and subscribeOn.

If you want to perform work on a different scheduler just use observeOn(scheduler) operator.

You would usually use observeOn a lot more often than subscribeOn.

In case observeOn isn't explicitly specified, work will be performed on whichever thread/scheduler elements are generated.

If you want to perform work on a different scheduler just use observeOn(scheduler)operator.

If you want to start sequence generation (subscribe method) and call dispose on a specific scheduler, use subscribeOn(scheduler).

```swift
// observeOn과 subscribeOn을 사용한 예제
Observable<Int>.create { observer in
    observer.onNext(1)
    observer.onNext(2)
												
    print("Hi \(Thread.isMainThread)")

    observer.onCompleted()
    return Disposables.create()
}
.observeOn(MainScheduler.instance)
.subscribeOn(ConcurrentDispatchQueueScheduler(qos: .background))
.subscribe(onNext: { el in	    
    print("onNext \(el) \(Thread.isMainThread)") 
}, onDisposed: {(
    print("dispose \(Thread.isMainThread)")
)})

/*
	onNext 1 true
	onNext 2 true
	Hi false
	dispose true
*/
```


```swift
// 네트워킹으로 받아온 데이터로 background에서 observable을 생성하고
// observable이 방출하는 element들은 main에서 처리하는 코드
// 1. observable을 선언. 아직 subscribe가 되지 않아 생성되진 않은 상태.
let todoObservable: Observable<[String]> = NetworkService.loadTodoList()

todoObservable
  // 2. observable을 background에서 생성.
  .subscribeOn(ConcurrentDispatchQueueScheduler(qos: .default))
  // 3. 이후부터는 main에서 처리하도록 함.
  .observeOn(MainScheduler.instance)
  .subscribe(onNext: { todoList in
      print("Todo: \(todoList)")
  })
  .disposed(by: disposeBag)
```

## Scheduler

### MainScheduler

- 메인 쓰레드에서 가장 위에 존재(UI와 high-priority tasks를 진행 ,,, heavy task는 피해야함(API request등)

- UI를 갱신하려면 이 스케줄러로 변경하여 사용
 
### SerialDispatchQueueScheduler

- background에서 추출하는 일을 처리할 때 사용 (Firebase를 사용할 때 서버의 endpoint에 너무 많은 pressured을 줄일 수 있음)
 
### ConcurrentDispatchQueueScheduler

- SerialDispatchQueueScheduler와 같이 추출하는 일을 처리할 때 사용, 단 병렬적
 (작업량이 많은 일에 사용)
 
### TestScheduler

- 테스트를 위한 것이며, production code에는 사용하지 않음

- RxTeat 라이브러리에 존재

## References

[observeOn vs. subscribeOn](http://rx-marin.com/post/observeon-vs-subscribeon/)

[[RxSwift] Scheduler 제대로 알아보기](https://sweepty.medium.com/rxswift-scheduler-제대로-알아보기-f2e26aeb829d)

[ReactiveX/RxSwift](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Schedulers.md)
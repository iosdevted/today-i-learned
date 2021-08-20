# [RxSwift] Observer vs Observable

In stream programming there are two main interfaces: Observable and Observer.

Observable is for the consumer, it can be transformed and subscribed:

```swift
observable.map(x => ...).filter(x => ...).subscribe(x => ...)
```

Observer is the interface which is used to feed an observable source:

```swift
observer.next(newItem)
```

We can create new Observable with an Observer:

```swift
var observable = Observable.create(observer => { 
    observer.next('first'); 
    observer.next('second'); 
    ... 
});
observable.map(x => ...).filter(x => ...).subscribe(x => ...)
```

Or, we can use a Subject which implements both the Observable and the Observer interfaces:

```swift
var source = new Subject();
source.map(x => ...).filter(x => ...).subscribe(x => ...)
source.next('first')
source.next('second')
```

## Reference 

[Stackoverflow](https://stackoverflow.com/questions/47537934/what-is-the-difference-between-observable-and-a-subject-in-rxjs)
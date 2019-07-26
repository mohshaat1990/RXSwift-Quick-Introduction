# RXSwift

## Introduction

- RXSWIFt is an asynchronous programming library that based on using Observables
- Observables is sequence of data or events that you can react to such as data coming back from web service even taps by a user

![Screen Shot 2019-07-26 at 6 12 31 PM](https://user-images.githubusercontent.com/11280137/61965726-355fb080-afd1-11e9-92f9-eea25c5a9968.png)

- This called marble diagram
- The horizontal line represents time 
- Circles marbles if you will are data or events that occur over time 
- And remember that pretty much every thing in RXSwift is observable sequence or something that reacts to an observable sequence 
- what RXSWIFt enables you to do is right asynchronous code that is way more streamlined and easier to follow than using the various patterns and apis you may be used to using from the iOS SDk
- Ascynchroncity like Buttons taps,Keyboard animations,Downloading data,Processing images,Writing to disk,Playing audio/video
- IOS Sdk Apis do the same as RXSwift Notification Center, KVO/KVC,GCD and OperationQueue, Closures, Target Action , Delegation
- RXSwift Patterns  
1- Observer  : you will create observer Sequences
2- iterator  : and iterate over this Sequences

## Observable 

- Emits an events containing element 
- Subscribers can react to each  event emitted 

### Observable Life Cycles 

![Screen Shot 2019-07-26 at 6 58 25 PM](https://user-images.githubusercontent.com/11280137/61968099-72c73c80-afd7-11e9-82be-3f66865f519d.png)
![Screen Shot 2019-07-26 at 6 58 36 PM](https://user-images.githubusercontent.com/11280137/61968103-75299680-afd7-11e9-9829-333dde3bd564.png)

- Next 
  - elements e.g, integers or taps
- error 
   - observable is terminated 
- completed 
   - observable is terminated 

### Pod Installation 

```swift
pod 'RxSwift', '~> 4.0'
```
- use it in any view Controller 

```swift
import RxSwift
```

### Creating Observables 

```swift
let episodev = "episodev"
let episodevI = "episodevI"
let episodevII = "episodevII"

let mostPopualr: Observable<String> =
Observable<String>.just(episodev)
let originalTrilogy = Observable.of(episodev,episodevI,episodevII)
let PurelTrilogy = Observable.of([episodev,episodevI,episodevII])
let sequeltrilogy = Observable.from([episodev,episodevI,episodevII])
```
### Subscribe to Observable 
```swift
let observable = Observable.of(episodev,episodevI,episodevII)

observable.subscribe{ event in
print(event)
}
```
### result will be 

```swift
next(episodev)
next(episodevI)
next(episodevII)
completed
```
```swift
observable.subscribe{ event in
print(event.element ?? "")
}
```
### result will be 
```swift
episodev
episodevI
episodevII
```
## handle On Next Only 
```swift
observable.subscribe(onNext:{element in
print(element)
})
```
### result will be 
```swift
episodev
episodevI
episodevII
```
## handle On Completed Only 
```swift
let observableCompleted = Observable<Void>.empty()
observableCompleted.subscribe(onNext: {element in
print(element)
},onCompleted: {
print("Completed")
})
// will print Completed
```
## will Never Completed 
```swift
let observableNever = Observable<Void>.never()
observableNever.subscribe(onNext: {element in
print(element)
},onCompleted: {
print("Completed")
})
```
## to Cancel Subscribtion 
-  you should ensure to cancel subscribtion to avoid lake of memory in this case not appear because events completed 
```swift 
let subscribtion = observable.subscribe(onNext:{element in
print(element)
})
subscribtion.dispose()
```
### using DisposeBag

- DisposeBag calls dispose when it's own deinit is called. It means when DisposeBag loses a reference from UIViewController its retainCount goes to 0 so it will be deallocated and it will call dispose on all the disposables
- this pattern that will be used 
  - create observable
  - subscribe to it
  - add it to disposeBag
```swift 
let disposeBag = DisposeBag()
let subscribtion = observable.subscribe(onNext:{element in
print(element)
}).disposed(by: disposeBag)
```
## Create Observable 

```swift
let disposeBag = DisposeBag()

enum Droid: Error {
case OU812
}

Observable<String>.create { observer in
observer.onNext("R2-D2")
observer.onError(Droid.OU812)
observer.onNext("C-3PO")
observer.onNext("K-2SO")
//    observer.onCompleted()

return Disposables.create()
}
.subscribe(
onNext: { print($0) },
onError: { print("Error:", $0) },
onCompleted: { print("Completed") },
onDisposed: { print("Disposed") }
)
.disposed(by: disposeBag)
/*
// result
R2-D2
Error: OU812
Disposed

*/
```
# RxSwift traits
- Swift has a powerful type system that can be used to improve the correctness and stability of applications and make using Rx a more intuitive and straightforward experience.

## Single

- A Single is a variation of Observable that, instead of emitting a series of elements, is always guaranteed to emit either a single element or an error.
- Emits exactly one element, or an error.
- One common use case for using Single is for performing HTTP Requests that could only return a response or an error, but a Single can be used to model any case where you only care for a single element, and not for an infinite stream of elements.
```swift
func getRepo(_ repo: String) -> Single<[String: Any]> {
return Single<[String: Any]>.create { single in
let task = URLSession.shared.dataTask(with: URL(string: "https://api.github.com/repos/\(repo)")!) { data, _, error in
if let error = error {
single(.error(error))
return
}

guard let data = data,
let json = try? JSONSerialization.jsonObject(with: data, options: .mutableLeaves),
let result = json as? [String: Any] else {
single(.error(DataError.cantParseJSON))
return
}

single(.success(result))
}

task.resume()

return Disposables.create { task.cancel() }
}
}


getRepo("ReactiveX/RxSwift")
.subscribe(onSuccess: { json in
print("JSON: ", json)
},
onError: { error in
print("Error: ", error)
})
.disposed(by: disposeBag)
```
## Completable

 - A Completable is a variation of Observable that can only complete or emit an error. It is guaranteed to not emit any elements.

- Emits zero elements.
- Emits a completion event, or an error.
```swift
cacheLocally()
.subscribe { completable in
switch completable {
case .completed:
print("Completed with no error")
case .error(let error):
print("Completed with an error: \(error.localizedDescription)")
}
}
.disposed(by: disposeBag)

cacheLocally()
.subscribe(onCompleted: {
print("Completed with no error")
},
onError: { error in
print("Completed with an error: \(error.localizedDescription)")
})
.disposed(by: disposeBag)
```
##  Maybe

A Maybe is a variation of Observable that is right in between a Single and a Completable. It can either emit a single element, complete without emitting an element, or emit an error.

Note: Any of these three events would terminate the Maybe, meaning - a Maybe that completed can't also emit an element, and a Maybe that emitted an element can't also send a Completion event.

- Emits either a completed event, a single element or an error.

- You could use Maybe to model any operation that could emit an element, but doesn't necessarily have to emit an element.
```swift
func generateString() -> Maybe<String> {
return Maybe<String>.create { maybe in
maybe(.success("RxSwift"))

// OR

maybe(.completed)

// OR

maybe(.error(error))

return Disposables.create {}
}
}
generateString()
.subscribe(onSuccess: { element in
print("Completed with element \(element)")
},
onError: { error in
print("Completed with an error \(error.localizedDescription)")
},
onCompleted: {
print("Completed with no element")
})
.disposed(by: disposeBag)
```

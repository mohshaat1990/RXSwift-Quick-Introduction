# RXSwift

## Introduction

- RXSWIFt is an asynchronous programming library that based on using Observables
- synchronously, you wait for it to finish before moving on to another task. When you execute something asynchronously, you can move on to another task before it finishes.
- Observables is sequence of data or events that you can react to such as data coming back from web service even taps by a user

![Screen Shot 2019-07-26 at 6 12 31 PM](https://user-images.githubusercontent.com/11280137/61965726-355fb080-afd1-11e9-92f9-eea25c5a9968.png)

- This called marble diagram
- The horizontal line represents time 
- Circles marbles if you will are data or events that occur over time 
- And remember that pretty much every thing in RXSwift is observable sequence or something that reacts to an observable sequence 
- what RXSWIFt enables you to do is right asynchronous code that is way more streamlined and easier to follow than using the various patterns and apis you may be used to using from the iOS SDk
- Ascynchroncity like Buttons taps,Keyboard animations,Downloading data,Processing images,Writing to disk,Playing audio/video
- IOS Sdk Apis do the same as RXSwift Notification Center, KVO/KVC,GCD and OperationQueue, Closures, Target Action , Delegation

```
@objc class Person: NSObject {
    @objc  dynamic var name = "Taylor Swift"
}


let person = Person()
person.observe(\Person.name, options: .new) { person, change in
    print("I'm now called \(person.name)")
}
person.name = "daf"
```
- RXSwift Patterns  
1- Observer  : you will create observer Sequences
2- iterator  : and iterate over this Sequences

## When to User Rx Swift

- UI components. Observe changes in UI components and respond to them. Forget about delegates.
- TableView's and CollectionView’s data sources  
- Information-sharing between controllers and objects (the reactive approach uses observables)
- The network layer (use observables instead of callbacks)

## Observable 

- Emits an events containing element 
- Subscribers can react to each  event emitted 

## Observables and Observers

Two key concepts are the Observable and the Observer.
An Observable emits notifications of change.
An Observer subscribes to an Observable and gets notified when that Observable has changed.
You can have multiple Observers listening to an Observable. When the Observable changes, it will notify all its Observers.

# Subject

- A Subject is a sort of bridge or proxy that is available in some implementations of ReactiveX that acts both as an observer and as an Observable. Because it is an observer, it can subscribe to one or more Observables, and because it is an Observable, it can pass through the items it observes by reemitting them, and it can also emit new items.
- subjects can be both Observable (can be subscribed to), observer(add new elements)

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

<img width="518" alt="Screen Shot 2020-10-13 at 1 21 01 PM" src="https://user-images.githubusercontent.com/11280137/95854251-f729ea80-0d56-11eb-8c0a-b339bba853d7.png">

<img width="527" alt="Screen Shot 2020-10-13 at 1 23 42 PM" src="https://user-images.githubusercontent.com/11280137/95854568-6dc6e800-0d57-11eb-97fc-3ece2e193a47.png">

<img width="426" alt="Screen Shot 2020-10-13 at 1 30 06 PM" src="https://user-images.githubusercontent.com/11280137/95855129-39076080-0d58-11eb-9cc5-843f4a8a95c6.png">

- Emit variable amount of values in a sequence and then emits a complete notification

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
func cacheLocally() -> Completable {
return Completable.create { completable in
// Store some data locally
...
...

guard success else {
completable(.error(CacheError.failedCaching))
return Disposables.create {}
}

completable(.completed)
return Disposables.create {}
}
}
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
# Side Effects using do 

```swift
let observable = Observable<Any>.never()
let disposeBag = DisposeBag()
observable
.do(
onSubscribe: {
print("About to subscribe")
},
onDispose: {
print("Disposed")
})
.subscribe(
onNext: { element in
print(element)
},
onCompleted: {
print("Completed")
},
onDisposed: {
print("Disposed")
})
.disposed(by: disposeBag)
```
# Subject

- A Subject is a sort of bridge or proxy that is available in some implementations of ReactiveX that acts both as an observer and as an Observable. Because it is an observer, it can subscribe to one or more Observables, and because it is an Observable, it can pass through the items it observes by reemitting them, and it can also emit new items.
- subjects can be both Observable (can be subscribed to), observer(add new elements)

###Subject Types
  1- Publish Subject
  2- Behavior Subject
  3- Reply Subject
  4- Variable

#### Publish Subject

  - Start Empty
  - Emits new next events o new subscribers
  - any events added to publish subjects before a Subscribe subscribes not be recieved by subscribers
  
 ![Screen Shot 2019-07-27 at 12 23 23 PM](https://user-images.githubusercontent.com/11280137/61993273-64296580-b069-11e9-941e-c21ba82b58a8.png)
```swift
let quotes = PublishSubject<String>()
quotes.onNext("1")
let subscribtion = quotes.subscribe { element in
print(element)
}

quotes.onNext("2")
quotes.onNext("3")
quotes.onNext("4")
/*
next(2)
next(3)
next(4)
*/
```
#### Behavior Subject

- Start with initial Value
- Reply initial / last value to new subscribers
- example when you developing user login screen you might initial text field with last loged in userName

![Screen Shot 2019-07-27 at 12 42 42 PM](https://user-images.githubusercontent.com/11280137/61993428-09453d80-b06c-11e9-9161-e6c455298962.png)
```swift
let quotes = BehaviorSubject<String>(value: "0")
//quotes.onNext("1") 
let subscribtion = quotes.subscribe { element in
print(element)
}
quotes.onNext("2")
quotes.onNext("3")
quotes.onNext("4")
/*
next(0)
//next(1)
next(2)
next(3)
next(4)
*/
```
#### Reply Subject

- Starts Empty with a buffer size
- Reply buffer to new subscribers

![Screen Shot 2019-07-27 at 1 13 31 PM](https://user-images.githubusercontent.com/11280137/61993734-575c4000-b070-11e9-874e-f6e9b1bd8161.png)

```swift
let quotes = ReplaySubject<String>.create(bufferSize: 2)
quotes.onNext("0")
quotes.onNext("1")
quotes.onNext("2")
let subscribtion = quotes.subscribe { element in
print(element)
}

quotes.onNext("3")
quotes.onNext("4")
quotes.onNext("5")
/*
// next(0) will not print because buffer size 2
next(1)
next(2)
next(3)
next(4)
next(5)
*/
```
### Variable Subject 
- wraps abehaviorSubject
- Starts with initial Value
- Reply initial / latest value to new subsribers 
- Guaranteed not to fil (cannot emit error events)
- automatically completes (you dont add completely as other subject)
- stateful(stores last value to access any time)

```swift
let disposeBag = DisposeBag()
let quotes = BehaviorSubject<String>(value: "0")
let variable = Variable(quotes)
print(variable.value)
variable.asObservable().subscribe{
print($0)
}.disposed(by: disposeBag)

/*
next(0)
completed
*/
```
# Filtering Operators
- emit only those items from an Observable that pass a predicate test
![Screen Shot 2019-07-27 at 6 46 05 PM](https://user-images.githubusercontent.com/11280137/61997625-7d033c80-b0a4-11e9-9230-c778fc0996fa.png)
)
- The Filter operator filters an Observable by only allowing items through that pass a test that you specify in the form of a predicate function.

### Ignore Elements  Filters
  - ignoreElements will ignore .next event elements. It will, however, allow through stop events, i.e., .completed or .error events. It is useful when you only want to be notified when an observable has terminated, via a .completed or .error event
  ```swift
  let strikes = PublishSubject<String>()
  
  let disposeBag = DisposeBag()
  
  strikes
  .ignoreElements()
  .subscribe { _ in
  print("You're out!")
  }
  .addDisposableTo(disposeBag)
  
  strikes.onNext("X")
  strikes.onNext("X")
  strikes.onNext("X")
  strikes.onCompleted()
  /*
  // result completed only will print
    You're out!
  */
```
### elementAt  Filters
- elementAt takes the index of the element you want to receive, and it ignores everything else.
```swift
let strikes = PublishSubject<String>()

let disposeBag = DisposeBag()
strikes
.elementAt(2)
.subscribe(onNext: { elements in
print(elements)
})
.addDisposableTo(disposeBag)

strikes.onNext("X0")
strikes.onNext("X1")
strikes.onNext("X2")
/*
// result completed only will print
 X2
*/
```
### filter 
- emit only those items that pass a predicate test 

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5, 6)
.filter { integer in
integer % 2 == 0
}
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
2
4
6
*/
```
### Skipping operators 
- The skip operator allows you to ignore from the 1st to the number you pass as its parameter

```swift
let disposeBag = DisposeBag()

Observable.of("A", "B", "C", "D", "E", "F")
.skip(1)
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
B
C
D
E
F
*/
```
### skipWhile
- discard items until a specified condition becomes false
```swift
let disposeBag = DisposeBag()
Observable.of(2, 4, 3, 2, 4)
.skipWhile { integer in
integer % 2 == 0
}
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
3
2
4
*/
```
###  skipUntil
- discard items until a second Observable emits an item
```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()
subject
.skipUntil(trigger)
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)

subject.onNext("A")
subject.onNext("B")

trigger.onNext("X")
subject.onNext("C")
/*
// result completed only will print
C
*/
```
###  Taking operators
- take emit only the first n items
```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5, 6)
.take(3)
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
1
2
3
*/
```
###  takeWhileWithIndex
- mirror items until a specified condition becomes false
```swift
let disposeBag = DisposeBag()

Observable.of(2, 2, 4, 4, 6, 6)
.takeWhileWithIndex { integer, index in
integer % 2 == 0 && index < 3
}
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
2
2
4
*/
```
###  takeUntil
- discard any items after a second Observable emits an item or terminates
```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
.takeUntil(trigger)
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)

subject.onNext("1")
subject.onNext("2")

trigger.onNext("X")
subject.onNext("3")
/*
// result completed only will print
1
2
*/
```
###  Distinct operators
- distinctUntilChanged prevents duplicates that are right next to each other
```swift
let disposeBag = DisposeBag()

Observable.of("A", "A", "B", "B", "A")
.distinctUntilChanged()
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
/*
// result completed only will print
A
B
A
*/
```
# Transforming Operators
- Observables emit elements individually, but you will frequently want to work with collections, such as when you’re binding an observable to a table or collection view, which you’ll learn how to do later in the book. A convenient way to transform an observable of individual elements into an array of all those elements is by using toArray

![Screen Shot 2019-07-27 at 8 25 05 PM](https://user-images.githubusercontent.com/11280137/61998192-aa53e880-b0ac-11e9-8b00-c327d428b364.png)

```swift
example(of: "toArray") {

let disposeBag = DisposeBag()
Observable.of("A", "B", "C")
.toArray()
    .subscribe(onSuccess: { (array) in
        print(array)
    }).disposed(by: disposeBag)
/*
--- Example of: toArray ---
["A", "B", "C"]

*/
```
- RxSwift’s map operator works just like Swift’s standard map, except it operates on observables. In the marble diagram, map takes a closure that multiplies each element by 2.
![Screen Shot 2019-07-27 at 8 27 47 PM](https://user-images.githubusercontent.com/11280137/61998211-01f25400-b0ad-11e9-97e6-1446efa85be5.png)

```swift
example(of: "map") {

let disposeBag = DisposeBag()

// 1
let formatter = NumberFormatter()
formatter.numberStyle = .spellOut

// 2
Observable<NSNumber>.of(123, 4, 56)
// 3
.map {
formatter.string(from: $0) ?? ""
}
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
}
```
###Transforming Inner Observables
```swift
struct Student {

var score: Variable<Int>
}
```
- Student is structure that has a score property that is a Variable. RxSwift includes a few operators in the flatMap family that allow you to reach into an observable and work with its observable properties. You’re going to learn how to use the two most common ones here.
```swift
example(of: "flatMap") {

let disposeBag = DisposeBag()

// 1
let ryan = Student(score: Variable(80))
let charlotte = Student(score: Variable(90))

// 2
let student = PublishSubject<Student>()

// 3
student.asObservable()
.flatMap {
$0.score.asObservable()
}
// 4
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)
}
student.onNext(ryan)  // will print 80
```
### flatMapLatest 
- works just like flatMap to reach into an observable element to access its observable property, it applies a transform and projects the transformed value onto a new sequence for each element of the source observable. Those elements are flattened down into a target observable that will provide elements to the subscriber. What makes flatMapLatest different is that it will automatically switch to the latest observable and unsubscribe from the the previous one.
```swift
example(of: "flatMapLatest") {

let disposeBag = DisposeBag()

let ryan = Student(score: Variable(80))
let charlotte = Student(score: Variable(90))

let student = PublishSubject<Student>()

student.asObservable()
.flatMapLatest {
$0.score.asObservable()
}
.subscribe(onNext: {
print($0)
})
.addDisposableTo(disposeBag)

student.onNext(ryan)

ryan.score.value = 85

student.onNext(charlotte)

// 1
ryan.score.value = 95

charlotte.score.value = 100
}
/*
--- Example of: flatMapLatest ---
80
85
90
100
*/
```
- Changing ryan’s score here will have no effect. It will not be printed out. This is because flatMapLatest has already switched to the latest observable, for charlotte

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

![Screen Shot 2019-07-26 at 7 08 37 PM](https://user-images.githubusercontent.com/11280137/61968647-d867f880-afd8-11e9-8b2a-b81d0b566d2c.png)

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

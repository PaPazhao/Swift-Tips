# Combine

## `Publisher` 

可以随时间传递一个序列的类型。

一个`Publisher`发布元素到一个或者多个[`Subscriber`](https://developer.apple.com/documentation/combine/subscriber) 实例。订阅者([`Subscriber`](https://developer.apple.com/documentation/combine/subscriber))的 `Input` 和 `Failure` 关联类型必须和发布者的`Output` 和`Failure` 类型必须匹配。发布者实现  [`receive(subscriber:)`](https://developer.apple.com/documentation/combine/publisher/3229093-receive) 方法接受一个订阅者。此后，发布者发布者可以在订阅者调用以下方法:

- [`receive(subscription:)`](https://developer.apple.com/documentation/combine/subscriber/3213655-receive): 确认订阅请求并返回订阅实例。订阅服务器使用订阅从发布者请求元素，并可以使用它取消发布。

- [`receive(_:)`](https://developer.apple.com/documentation/combine/subscriber/3213653-receive):  将一个元素从发布者传递到订阅服务器

- [`receive(completion:)`](https://developer.apple.com/documentation/combine/subscriber/3213654-receive): 通知订阅者发布已结束，无论是正常还是有错误



每个发布者都必须遵守此合同，下游订阅者才能正常运行。Publisher上的扩展定义了各种运算符，您可以组合这些运算符来创建复杂的事件处理链.每个运算符返回一个实现Publisher协议的类型。这些类型中的大多数以扩展形式存在于Publishers枚举中。例如，map（_ :)运算符返回Publishers.Map的实例。

 For example, you can use an *AnySubject* instance and publish new elements imperatively with its *send(_:)* method. You can also add the `@Published` annotation to any property to give it a publisher that returns an instance of [`Published`](https://developer.apple.com/documentation/combine/published), which emits an event every time the property’s value changes.

您可以使用Combine框架提供的几种类型之一，而不是自己实现Publisher。例如，可以使用 AnySubject 实例，并强制使用其 *send(_:)*  发布新元素方法。您还可以将 @Published 注释添加到任何属性，为其提供一个发布者，该发布者返回已发布实例，该实例在每次属性的值更改时发出事件

有四种类型的 messages:
     subscription - A connection between `Publisher` and `Subscriber`.
value -序列中的元素

- - error - 序列因为错误终结 (`.failure(e)`).
- complete - 序列成功完成 (`.finished`).

`.failure` 和 `.finished` 是 terminal messages.
///
每个发布者必须遵守此协议

```swift

public protocol Publisher {

    /// publisher 发布元素的类型
    associatedtype Output

    /// 这个 publisher 可能发布的错误类型.
    ///
    ///  如果这个 `Publisher` 永远不会发布 errors 则使用 `Never` 
    associatedtype Failure : Error

    /// 调用这个函数，把指定的订阅器(`Subscriber`) 附加到这个发布者(`Publisher`)
    ///
    /// - SeeAlso: `subscribe(_:)`
    /// - Parameters:
    ///     - subscriber: 要附加到这个 `Publisher` 的订阅者。一旦附加，就开始发布值
    func receive<S>(subscriber: S) where S : Subscriber, Self.Failure == S.Failure, Self.Output == S.Input
}
```

- ` associatedtype Output`
`publisher` 发布元素的类型


- `associatedtype Failure : Error`
这个 publisher 可能发布的错误类型.如果这个 `Publisher` 永远不会发布 errors 则使用 `Never` 


- `func receive<S>(subscriber: S) where S : Subscriber, Self.Failure == S.Failure, Self.Output == S.Input`
调用这个函数，把指定的订阅器(`Subscriber`) 附加到这个发布者(`Publisher`). 参数: `subscriber`: 要附加到这个 `Publisher` 的订阅者。一旦附加，就开始发布值.





### 指定从 Publisher 接收元素的调度器

```swift
extension Publisher {
  
    /// - 参数:
    ///   - scheduler: Publisher 发射元素使用的调度器
    ///   - options: Scheduler options that customize the element delivery.
    /// - Returns: 一个在指定的调度器发射元素的 Publisher.
    public func receive<S>(on scheduler: S, 
                           options: S.SchedulerOptions? = nil) 
  	-> Publishers.ReceiveOn<Self, S> where S : Scheduler
}
```

你可以使用 `receive(on:options:)` 操作在指定的调度器接受结果，比如在主循环上执行 UI 操作。

`subscribe(on:options:)` 影响上游消息，
`receive(on:options:)` 改变了下游消息的执行上下文。

下面的例子中，`jsonPublisher` 执行在 `backgroundQueue` ，但是从从他接受的元素执行在 `RunLoop.main`.

```swift
let jsonPublisher = MyJSONLoaderPublisher() // Some publisher.
let labelUpdater = MyLabelUpdateSubscriber() // Some subscriber that updates the UI.

jsonPublisher
   .subscribe(on: backgroundQueue)
   .receiveOn(on: RunLoop.main)
   .subscribe(labelUpdater)
```



### ObservableObject 协议

一种带有发布者的对象类型，该对象在对象更改之前发射。

```swift
public protocol ObservableObject : AnyObject {
    /// The type of publisher that emits before the object has changed.
    associatedtype ObjectWillChangePublisher : Publisher = ObservableObjectPublisher where Self.ObjectWillChangePublisher.Failure == Never

    /// Publisher<Any, Never>: 在对象更改之前发射消息的发布者。 
    var objectWillChange: Self.ObjectWillChangePublisher { get }
}	
```

示例:

```swift
class Contact: ObservableObject {
    @Published var name: String
  	@Published var age: Int
  	init(name: String, age: Int) {
      	self.name = name
      	self.age = age
    }
  	
  	func haveBirthday() -> Int {
      	age += 1
      	return age
    }
} 

let john = Contact(name: "John", age: 24)
john.objectWillChange.sink { _ in print("\(john.age) will change")}
print(john.haveBirthday())
// print "24 will change"
// print "25"
```









AnyObject

```swift

/// 所有类阴式遵守的协议
/// The protocol to which all classes implicitly conform.
///
/// 当需要无类型对象的灵活性时，或者使用桥接的Objective-C方法和返回无类型结果的属性时，可以使用“ AnyObject”。
/// AnyObject可用作任何类，类类型或仅类协议的实例的具体类型。例如：
/// You use `AnyObject` when you need the flexibility of an untyped object or
/// when you use bridged Objective-C methods and properties that return an
/// untyped result. `AnyObject` can be used as the concrete type for an
/// instance of any class, class type, or class-only protocol. For example:
///
class FloatRef {
    let value: Float
    init(_ value: Float) {
        self.value = value
    }
}

let x = FloatRef(2.3)
let y: AnyObject = x
let z: AnyObject = FloatRef.self

/// `AnyObject` can also be used as the concrete type for an instance of a type
/// that bridges to an Objective-C class. Many value types in Swift bridge to
/// Objective-C counterparts, like `String` and `Int`.
///
let s: AnyObject = "This is a bridged string." as NSString
print(s is NSString)
// Prints "true"

let v: AnyObject = 100 as NSNumber
print(type(of: v))
// Prints "__NSCFNumber"

/// The flexible behavior of the `AnyObject` protocol is similar to
/// Objective-C's `id` type. For this reason, imported Objective-C types
/// frequently use `AnyObject` as the type for properties, method parameters,
/// and return values.
///
/// Casting AnyObject Instances to a Known Type
/// ===========================================
///
/// Objects with a concrete type of `AnyObject` maintain a specific dynamic
/// type and can be cast to that type using one of the type-cast operators
/// (`as`, `as?`, or `as!`).
///
/// This example uses the conditional downcast operator (`as?`) to
/// conditionally cast the `s` constant declared above to an instance of
/// Swift's `String` type.
///
///     if let message = s as? String {
///         print("Successful cast to String: \(message)")
///     }
///     // Prints "Successful cast to String: This is a bridged string."
///
/// If you have prior knowledge that an `AnyObject` instance has a particular
/// type, you can use the unconditional downcast operator (`as!`). Performing
/// an invalid cast triggers a runtime error.
///
///     let message = s as! String
///     print("Successful cast to String: \(message)")
///     // Prints "Successful cast to String: This is a bridged string."
///
///     let badCase = v as! String
///     // Runtime error
///
/// Casting is always safe in the context of a `switch` statement.
///
///     let mixedArray: [AnyObject] = [s, v]
///     for object in mixedArray {
///         switch object {
///         case let x as String:
///             print("'\(x)' is a String")
///         default:
///             print("'\(object)' is not a String")
///         }
///     }
///     // Prints "'This is a bridged string.' is a String"
///     // Prints "'100' is not a String"
///
/// Accessing Objective-C Methods and Properties
/// ============================================
///
/// When you use `AnyObject` as a concrete type, you have at your disposal
/// every `@objc` method and property---that is, methods and properties
/// imported from Objective-C or marked with the `@objc` attribute. Because
/// Swift can't guarantee at compile time that these methods and properties
/// are actually available on an `AnyObject` instance's underlying type, these
/// `@objc` symbols are available as implicitly unwrapped optional methods and
/// properties, respectively.
///
/// This example defines an `IntegerRef` type with an `@objc` method named
/// `getIntegerValue()`.
///
///     class IntegerRef {
///         let value: Int
///         init(_ value: Int) {
///             self.value = value
///         }
///
///         @objc func getIntegerValue() -> Int {
///             return value
///         }
///     }
///
///     func getObject() -> AnyObject {
///         return IntegerRef(100)
///     }
///
///     let obj: AnyObject = getObject()
///
/// In the example, `obj` has a static type of `AnyObject` and a dynamic type
/// of `IntegerRef`. You can use optional chaining to call the `@objc` method
/// `getIntegerValue()` on `obj` safely. If you're sure of the dynamic type of
/// `obj`, you can call `getIntegerValue()` directly.
///
///     let possibleValue = obj.getIntegerValue?()
///     print(possibleValue)
///     // Prints "Optional(100)"
///
///     let certainValue = obj.getIntegerValue()
///     print(certainValue)
///     // Prints "100"
///
/// If the dynamic type of `obj` doesn't implement a `getIntegerValue()`
/// method, the system returns a runtime error when you initialize
/// `certainValue`.
///
/// Alternatively, if you need to test whether `obj.getIntegerValue()` exists,
/// use optional binding before calling the method.
///
///     if let f = obj.getIntegerValue {
///         print("The value of 'obj' is \(f())")
///     } else {
///         print("'obj' does not have a 'getIntegerValue()' method")
///     }
///     // Prints "The value of 'obj' is 100"
```

```swift
extension Subscribers {
    /// 发布信号结束: 表示发布者由于正常完成或错误而没有产生其他元素的信号, 
    public enum Completion<Failure> where Failure : Error {
        case finished  					//  Publisher 正常完成
        case failure(Failure)		// Publisher 由于错误而停止发布 
    }
}
```





















































































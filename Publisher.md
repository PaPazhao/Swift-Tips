# Publisher 



## 协议定义

------

声明一个类型可以随时间传递一系列值. Publisher 的主要功能就是: 发布事件数据；被 Subscriber 订阅

消息有四种：
- subscription - “发布者”和“订阅者”之间的连接。
- value - 序列中的元素。
- error - 序列以错误（`.failure（e）`）结尾。
- complete - 序列成功结束（`.finished`）。

**`.failure` 和 `.finished` 都是终端消息(terminal messages)。**



## 协议原型

------

```swift
/// 每个 `Publisher` 都必须遵守此协议。
public protocol Publisher {
    /// 该发布者发射元素的类型  
    associatedtype Output
 
    /// 该发布者可能发送的错误类型，如不发射错误，使用 `Never`    
    associatedtype Failure : Error
  
    /// 将指定的订阅者绑定到该发布者，`Publisher` 实例必须实现这个方法。`Subscriber` 会调用这个方法
    /// 通过 `subscribe(_:)` 方法把 `Subscriber` 绑定到这个 `Publisher` 时 
    /// receive<S>(subscriber: S) 被调用。 
    ///  
    /// - Parameters:
    ///     - subscriber: 绑定到此发布者的订阅者 subscriber， 一旦绑定，就开始接受数据
    func receive<S>(subscriber: S) where 
                            S : Subscriber, 
                            Self.Failure == S.Failure, 
                            Self.Output == S.Input
}
```



## 订阅

------


### receive(subscriber : ) -  **Required**

- 功能

  把指定定的订阅者附加到该发布者

  通过 `subscribe(_:)`  来将指定的 Subscriber 附加到此发布者时这个函数被调用

  Publisher 的实现必须实现这个方法 

  Implementations of [`Publisher`](dash-apple-api://load?topic_id=3204679&language=swift) must implement this method.

  提供的 subscribe(_:)  实现调用此方法。

  

- 函数原型
```swift
extension Publisher {
      /// This function is called to attach the specified `Subscriber` to this `Publisher` by `subscribe(_:)`
    ///
    /// - Parameters:
    ///     - subscriber: 附加到此`Publisher` 的订阅者，一旦附加，它就可以开始接收值.
    func receive<S>(subscriber: S) where S : Subscriber, 
                            Self.Failure == S.Failure, 
                            Self.Output == S.Input
}
```



### subscribe(_ subscriber : S)

- 功能
  

把指定定的订阅者附加到该发布者. 

订阅消息始终调用此函数，而不要调用 `receive(subscriber:)`。因为 `subscribe(_:)` 的实现调用了 `receive(subscriber:)` , 该方法必须实现

  

- 函数原型

```swift
extension Publisher {
		/// - subscriber: 附加到此“发布者”的订阅者。附加后，订户可以开始接收值。
		public func subscribe<S>(_ subscriber: S) where 
                            S : Subscriber,
                            Self.Failure == S.Failure, 
                            Self.Output == S.Input
}
```



### subscribe(_ subject: S)

- 功能

  把指定定的 subject 附加到该发布者.   

  

- 函数原型

```swift
extension Publisher {
  	/// - subject: 附加到该发布者的主题
    public func subscribe<S>(_ subject: S) -> AnyCancellable where 
                S : Subject, 
                Self.Failure == S.Failure, 
                Self.Output == S.Output
}
```



## 链接简单的订阅者

------

### assign(to:  on: )

- 功能 

将发布者的输出分配给对象的属性, 当您希望发布者每当产生新的元素时都赋值给定属性时，请使用 `assign(to:on:)` 订阅者。
Failure 是 Never 时可用

- 函数原型

```swift
extension Publisher where Self.Failure == Never {

    /// 将发布者中的每个元素赋值给对象的属性 
    ///
    /// - 参数:
    ///   - keyPath: 指示要分配的属性的关键路径.   
    ///   - object: 包含属性的对象。订阅者在每次收到新值时都会赋值给对象的属性。
    /// - Returns: 一个 AnyCancellable实例 实例; 当您不再希望发布者自动给属性赋值时，请在此实例
  	///            上调用 cancel（）。反初始化此 AnyCancellable 实例也将取消自动赋值。
    public func assign<Root>(to keyPath: ReferenceWritableKeyPath<Root, Self.Output>, 
                             on object: Root) -> AnyCancellable
}
```


- 示例

For example, given a type `MyViewModel` with the property `date`, the following code uses a `Timer.TimerPublisher` to set the `date` property once a second.

例如，给定具有日期属性的MyViewModel 类型，以下代码使用Timer.TimerPublisher每秒设置一次日期属性。

```swift
let cancellable = Timer.publish(every: 1, on: .main, in: .default)
    .autoconnect()
    .assign(to: \MyViewModel.date, on: viewModel)
```



## 指定调度器
------

### receive(on: options: )

- 功能

指定在那个调度器上接收来自 publisher 的元素。他更改下游消息的执行上下文。

- 函数原型

```swift
extension Publisher { 
    /// - Parameters:
    ///   - scheduler: 发布者用于交付元素的调度器 
    ///   - options: 自定义元素交付的调度器选项
    /// - Returns: 使用指定的调度程序交付元素的 publisher.
    public func receive<S>(on scheduler: S, 
                           options: S.SchedulerOptions? = nil) 
  	                       -> Publishers.ReceiveOn<Self, S> where S : Scheduler
}
```


- 示例

在以下示例中，jsonPublisher 使用 backgroundQueue 处理请求。但是，它使用 RunLoop.main 将元素发送到订阅服务器labelUpdater

```swift
let jsonPublisher = MyJSONLoaderPublisher() // Some publisher.
let labelUpdater = MyLabelUpdateSubscriber() // Some subscriber that updates the UI.

jsonPublisher
    .subscribe(on: backgroundQueue)  	/// 后台发送数据
    .receiveOn(on: RunLoop.main) 			/// 在主线程接收
    .subscribe(labelUpdater)
```



### subscribe(on:  options: )

- 功能
  指定在哪个调度器上执行 subscribe, cancel, 和 request操作 

     

- 函数原型

```swift
extension Publisher {
    /// - 参数:
    ///   - scheduler: 接收上游消息的调度器 
    ///   - options: 定制元素交付的选项  
    /// - Returns: 在指定的调度程序上执行上游操作的发布者 
    public func subscribe<S>(on scheduler: S, 
                             options: S.SchedulerOptions? = nil) 
  													-> Publishers.SubscribeOn<Self, S> where S : Scheduler
}
```

- 示例

`subscribe(on:)` 更改上游的执行上下文. 下面的示例, `ioPerformingPublisher` 使用 `backgroundQueue` 处理请求. 但是, 他使用 `RunLoop.main` 向订阅者发送元素。 

```swift
let ioPerformingPublisher == // Some publisher.
let uiUpdatingSubscriber == // Some subscriber that updates the UI.

ioPerformingPublisher
    .subscribe(on: backgroundQueue)
    .receiveOn(on: RunLoop.main)
    .subscribe(uiUpdatingSubscriber)
```



### sink(receiveCompletion :  receiveValue: ) 

- 功能

  接收消息，基于闭包的行为附加一个订阅者，使订阅者具有基于闭包的行为
  此方法创建订阅者，并在返回订阅者之前立即请求无限数量的值

  

- 函数原型

```swift
extension Publisher {
    /// sink: 接收
    /// - parameter receiveComplete: 完成时执行的闭包.
    /// - parameter receiveValue: 接受到值的时候执行的闭包
    /// - Returns: 一个 cancellable 实例; 当结束自动接收赋值操作时使用
    public func sink(receiveCompletion: @escaping ((Subscribers.Completion<Self.Failure>) -> Void), 
                     receiveValue: @escaping ((Self.Output) -> Void)) -> AnyCancellable
}
在收到值或完成后执行提供的闭包的订户
```



### sink(receiveValue:)  

将具有基于闭包行为的订阅者附加到永不失败的发布者。

```swift
extension Publisher where Self.Failure == Never {

    /// 基于闭包的行为附加一个订阅者
    ///
    /// 这个方法创建一个订阅者， 并且在返回订阅者之前立即请求无限数量的值  
    /// - parameter receiveValue: 在接收一个值时执行的闭包
    /// - Returns: 一个 cancellable 实例; 当结束接收赋值操作时使用
    public func sink(receiveValue: @escaping ((Self.Output) -> Void)) -> AnyCancellable
}

```


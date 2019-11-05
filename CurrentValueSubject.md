# CurrentValueSubject

可以存储一个值的 Subject, 每当接收到新的 send value 时更新，并向下游发送。
订阅时立马把这个保存的值发送给订阅者。跟 PassthroughSubject 的区别是他可以存储一个值，被订阅的瞬间向订阅者发送这个值。

```swift
final public class CurrentValueSubject<Output, Failure> : Subject where Failure : Error {

    /// subject 包装的值，每当这个值更新时向订阅者发布一个新的元素。
    final public var value: Output

    /// 给定包装的初始值。
    public init(_ value: Output)

    /// 跟上游任何新的订阅者建议需求？？？？？
    final public func send(subscription: Subscription)

    /// 附加订阅者
    final public func receive<S>(subscriber: S) where 
                            Output == S.Input, 
                            Failure == S.Failure, 
                            S : Subscriber

    /// 发送一个值
    final public func send(_ input: Output)

    /// 发射一个结束信号
    final public func send(completion: Subscribers.Completion<Failure>)
}
```


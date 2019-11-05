# Subscriber

可以声明一个接收发布者收入的数据类型的协议



```swift
/// A protocol that declares a type that can receive input from a publisher.
@available(OSX 10.15, iOS 13.0, tvOS 13.0, watchOS 6.0, *)
public protocol Subscriber : CustomCombineIdentifierConvertible {

    /// 接收的数据类型 The kind of values this subscriber receives.
    associatedtype Input

    /// 接收错误，如果从不接收 error 则使用
    associatedtype Failure : Error

    /// 告诉订阅者，他成功的订阅了 Publisher 并可以请求数据了。 使用收到的 Subscription 像订阅者请求数据。
    /// subscription: 发布者和订阅者之间连接的订阅
    func receive(subscription: Subscription)

    /// 告诉订阅者: 发布者发送了一个元素 
    ///
    /// - Parameter input: 已经发布的元素。
    /// - Returns:  一个需求实例: 指示订户希望接收多少元素
    func receive(_ input: Self.Input) -> Subscribers.Demand

    /// 告诉订阅者，Publisher已经完成发布。
    func receive(completion: Subscribers.Completion<Self.Failure>)
}
 
extension Subscriber where Self.Input == Void {
    public func receive() -> Subscribers.Demand
}

```


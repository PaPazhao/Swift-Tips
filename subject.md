# Subject

Subject 是一个 Publisher，你可以调用他的 `send()` 方法手动注入值，subject 把这些值变换成数据流。他可以将传统指令式的异步API里的事件和信号转换到响应式世界。

```swift
public protocol Subject : AnyObject, Publisher { 
    /// 向 subscriber 发送一个值
    func send(_ value: Self.Output)

    /// 向 subscriber 发送一个 completion 信号
    func send(completion: Subscribers.Completion<Self.Failure>)

    /// 为该主题提供建立对任何新上游订阅的需求的机会
    func send(subscription: Subscription)
}

extension Subject where Self.Output == Void {
    /// 发信号给订阅者
    public func send()
}
```






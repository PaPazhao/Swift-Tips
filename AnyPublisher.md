# AnyPublisher

一个擦除类型的 `Publisher` . 使用 `AnyPublisher` 来包装发布者，该发布者的类型包含您不想向订阅者或其他发布者公开的详细信息。

```swift
public struct AnyPublisher<Output, Failure> : CustomStringConvertible where 	                                     Failure : Error {

    /// 使用参数中的 publisher: P创建一个擦除类型的 Publisher 
    @inlinable public init<P>(_ publisher: P) where 
                            Output == P.Output, 
                            Failure == P.Failure, P : Publisher
}
 
extension AnyPublisher : Publisher { 
    // 必须实现的 Publisher 协议方法: 绑定订阅者，被 `subscribe(_:)` 自动调用。
    @inlinable public func receive<S>(subscriber: S) where 
                            Output == S.Input, 
                            Failure == S.Failure, S : Subscriber
}

```




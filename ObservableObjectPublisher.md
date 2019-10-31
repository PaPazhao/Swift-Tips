# ObservableObjectPublisher

ObservableObject 的默认 Publisher ，从可观察对象发布更改的发布者


- 类定义

```swift
/// ObservableObject 的默认发布者  
final public class ObservableObjectPublisher : Publisher {

    public init()

    /// Publisher 协议方法实现，调用`subscribe(_:)`时，自动被调用
    final public func receive<S>(subscriber: S) where S : Subscriber, 
    												S.Failure == ObservableObjectPublisher.Failure, 
    												S.Input == ObservableObjectPublisher.Output

    final public func send()
}
```


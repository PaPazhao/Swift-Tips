# AnyCancellable

一个类型擦除 Cancellable 对象，在取消后将执行提供的闭包。当析构的时候(取消初始化后)，AnyCancellable 实例自动调用 `cancel()` 

 Subscriber 实现者可以使用此类型来提供一个 ‘cancellation token’,  从而使调用者可以取消发布者，但不能使用 `Subscription` 对象来请求 items ( ) 

```swift
/// Subscriber implementations can use this type to provide a “cancellation token” that makes it possible for a caller to cancel a publisher, but not to use the `Subscription` object to request items.
final public class AnyCancellable : Cancellable, Hashable {

    /// - cancel: `cancel()` 方法执行时调用的闭包
    public init(_ cancel: @escaping () -> Void)

    public init<C>(_ canceller: C) where C : Cancellable
 
    final public func cancel()
}

extension AnyCancellable {
    /// Stores this AnyCancellable in the specified collection. 
    final public func store<C>(in collection: inout C) where C : RangeReplaceableCollection,
  																																			C.Element == AnyCancellable
    /// Stores this AnyCancellable in the specified set. 
    final public func store(in set: inout Set<AnyCancellable>)
}

```


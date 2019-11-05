# PassthroughSubject

```swift
final public class PassthroughSubject<Output, Failure> : Subject where Failure : Error {

    public init()

    /// Provides this Subject an opportunity to establish demand for any new upstream subscriptions  
    final public func send(subscription: Subscription)
 
    /// 附加 `Subscriber`， 被 subscribe(_:)` 调用 
    final public func receive<S>(subscriber: S) where 
                            Output == S.Input, 
                            Failure == S.Failure, S : Subscriber

    /// Sends a value to the subscriber.
    final public func send(_ input: Output)

    /// Sends a completion signal to the subscriber. 
    final public func send(completion: Subscribers.Completion<Failure>)
}
```


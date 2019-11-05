# Subscription

表示订阅者与发布者的连接的协议

```swift
 
 xx are class constrained because a `Subscription` has identity -
 defined by the moment in time a particular subscriber attached to a publisher.

取消 `Subscription` 必须保证线程安全 
你仅仅可以取消 Subscription 一次
/// Canceling a subscription frees up any resources previously allocated by attaching the `Subscriber`.
@available(OSX 10.15, iOS 13.0, tvOS 13.0, watchOS 6.0, *)
public protocol Subscription : Cancellable, CustomCombineIdentifierConvertible {

    /// Tells a publisher that it may send more values to the subscriber.
    func request(_ demand: Subscribers.Demand)
}
```

不透明类型就是: 

- 返回的这个类型其实是确定的，编译器知道是什么类型。也就是说对编译器透明，而对编程人员不透明。
- 在返回透明类型的函数中如果有多处返回，那么必须保证这个多处返回的类型必须相同，否则编译报错，报错的原因是返回类型是确定的类型，不是泛型。

协议类型:

- 一个泛型函数：参数要求符合某种协议(SomeProtocol), 返回值要求遵守和形参同样的协议。这样的函数不能做嵌套。

    ```swift
    protocol SomeProtocol {
        func someMethod()
    }
    struct SomeStruct: SomeProtocol {
        func someMethod() {
            print("someMethod")
        }
    }
    
    func topLevel<T: SomeProtocol>(params: T) -> T {
        // .....
    }
    
    
    func bottomLevel<T: SomeProtocol>(params: T) -> T {
        // .....
    }
    
    let instance = SomeStruct()
    topLevel(params: bottomLevel(params: instance)) 
    // 编译错误, 泛型函数不能做嵌套。因为 bottomLevel 返回的类型 T 是一个协议类型，协议类型 T 并没有遵守他本身，不能作为 topLevel 的参数。
    ```

    
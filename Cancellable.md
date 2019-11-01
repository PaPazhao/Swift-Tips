# Cancellable

指示可以取消活动或动作的协议。调用 `cancel()` 释放所有分配的资源, 他也可以停止相应的Timers、网络请求或者磁盘访问。 

```swift
public protocol Cancellable {
    func cancel()
}

extension Cancellable {
    /// 存储这个 Cancellable 对象在指定的 Collection 里面 
    public func store<C>(in collection: inout C) where C : RangeReplaceableCollection, 	
  																											C.Element == AnyCancellable

    /// 存储这个 Cancellable 对象在指定的 Set 里面  
    public func store(in set: inout Set<AnyCancellable>)
}
```






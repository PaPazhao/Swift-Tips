---
typora-root-url: ./images
---

### # Operator

## scan

通过一个闭包变换数据源的元素。这个Publisher保存上一次变换后的结果，并把这个暂存的结果和当前收到的元素作为参数提供给变换闭包。

```swift
extension Publisher {
    ///   - initialResult: 暂存 nextPartialResult 上次计算结果。 
    ///   - nextPartialResult: 闭包: (initialResult, Output) -> nextPartialResult 
    public func scan<T>(_ initialResult: T, // 
                    _ nextPartialResult: @escaping (T, Self.Output) -> T) 
                    -> Publishers.Scan<Self, T>

}
```

![scan](./images/scan.png)

- 示例

```swift
let pub = (0...5)
    .publisher
    .scan(0, { return $0 + $1 })
    .sink(receiveValue: { print ("\($0)", terminator: " ") })
   // Prints "0 1 3 6 10 15 ".
```



## tryScan

功能类似 Scan，区别是 tryScan 的闭包可能 throws 一个错误。如果闭包 throws 一个错误,  publisher 失败结束, 后续的数据不会接收到

```swift
extension Publisher {
    /// Transforms elements from the upstream publisher by providing the current element to an error-throwing closure along with the last value returned by the closure.
    ///
    ///  
    /// - Parameters:
    ///   - initialResult: The previous result returned by the `nextPartialResult` closure.
    ///   - nextPartialResult: An error-throwing closure that takes as its arguments the previous value returned by the closure and the next element emitted from the upstream publisher.
    /// - Returns: A publisher that transforms elements by applying a closure that receives its previous return value and the next element from the upstream publisher.
    public func tryScan<T>(_ initialResult: T, 
                            _ nextPartialResult: @escaping (T, Self.Output) throws -> T) 
                            -> Publishers.TryScan<Self, T>
}
```

```
{
    "body": {
        "json": { 
            "aid": "", 
            "fid": 26, 
            "sortId": 1, 
            "isHidden": 0, 
            "typeId": 0,  
            "typeOption": "{\"gender\":2,\"ganyan\":\"得到的\",\"age\":\"77\",\"suozaidi\":4,\"detail_content\":\"想念你的等待\"}", 
            "isShowPostion": 1, 
            "title": "不行不行不行下班", 
            "longitude": "116.47769165039063", 
            "content": "[{\"infor\":\"说的是\\n你的巴登巴登八点多吧\",\"type\":0}]", 
            "isOnlyAuthor": 0, 
            "isAnonymous": 0
        }
    }
}


{
    "body": {
        "json": {
            "fid": 22,  
            "isShowPostion": 1,  
            "typeOption": "{}", 
            "isOnlyAuthor": 0, 
            "sortId": 1, 
            "isHidden": 0, 
            "content": "[{\"type\":0,\"infor\":\"对宝宝的巴登巴登巴登巴登吧\"}]", 
            "title": "的巴登巴登斑斑点点八点半吧",  
            "aid": "", 
            "typeId": 33, 
            "isAnonymous": 0
        }
    }
}
```

## compactMap

- 使用提供的闭包进行变换
- 闭包接收一个 value， 返回一个可选值: T?
- 过滤掉闭包返回的 nil 只发送有值的数据

```swift
extension Publisher {
    public func compactMap<T>(_ transform: @escaping (Self.Output) -> T?) 
                                -> Publishers.CompactMap<Self, T>
}
```

## tryCompactMap

- 使用提供的闭包进行变换，这个闭包可能 throw error
- 闭包接收一个 value， 返回一个可选值: T?
- 过滤掉闭包返回的 nil 只发送有值的数据

```swift
extension Publisher {
    /// Calls an error-throwing closure with each received element and publishes any returned optional that has a value.
    ///
    /// If the closure throws an error, the publisher cancels the upstream and sends the thrown error to the downstream receiver as a `Failure`.
    /// - Parameter transform: an error-throwing closure that receives a value and returns an optional value.
    /// - Returns: A publisher that republishes all non-`nil` results of calling the transform closure.
    public func tryCompactMap<T>(_ transform: @escaping (Self.Output) throws -> T?) 
                                -> Publishers.TryCompactMap<Self, T>
}
```


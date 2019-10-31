# ObservableObject

可被订阅的对象,   一个带有 publisher 的对象类型,  这个 Publisher 在对象改变之前发射. 


```swift
public protocol ObservableObject : AnyObject {
    /// Publisher 的类型，这个 Publisher 在对象改变之前发射
    associatedtype ObjectWillChangePublisher : Publisher = ObservableObjectPublisher where 																								Self.ObjectWillChangePublisher.Failure == Never
	
    /// 一个 publisher，在对象改变之前发射
    var objectWillChange: Self.ObjectWillChangePublisher { get }
}

extension ObservableObject where Self.ObjectWillChangePublisher == ObservableObjectPublisher {

    /// A publisher that emits before the object has changed.
    public var objectWillChange: ObservableObjectPublisher { get }
}
```



默认情况下， ObservableObject 将合成一个 objectWillChange 发布者， 在他的 @Published 属性变化之前发射

```swift
class Contact: ObservableObject {
    @Published var name: String
    @Published var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func haveBirthday() -> Int {
        age += 1
        return age
    }
}

let john = Contact(name: "John Appleseed", age: 24)
john.objectWillChange.sink { _ in print("\(john.age) will change") }
print(john.haveBirthday())
// Prints "24 will change"
// Prints "25"
```


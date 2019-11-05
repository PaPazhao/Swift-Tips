# Binding

数据和 View 进行绑定。使用 Binding 在 View 及其基础 model 之间创建双向连接. 比如你可以在 Toggle 和 一个 Bool 类型的 State 属性直接创建一个 Binding。 和控件进行交互会更改 Bool 的值，更改 Bool 的值发生让 Toggle 更新其显示状态。

和控件进行交互会更改 Bool 的值，并且使 Bool 的值发生突变会导致切换更新其显示状态

您可以通过访问 @State 的绑定属性来获得 @State 的绑定。您还可以将 $ 前缀运算符与 State 的任何属性一起使用来创建绑定

```swift

/// 一个值和更改他的手段 A value and a means to mutate it.
@propertyWrapper @dynamicMemberLookup 
public struct Binding<Value> {

    /// The transaction used for any changes to the binding's value.
    public var transaction: Transaction

    /// Initializes from functions to read and write the value.
    public init(get: @escaping () -> Value, set: @escaping (Value) -> Void)

    /// Initializes from functions to read and write the value.
    public init(get: @escaping () -> Value, set: @escaping (Value, Transaction) -> Void)

    /// Creates a binding with an immutable `value`.
    public static func constant(_ value: Value) -> Binding<Value>
	/// 绑定引用的值。值的赋值将在读取时立即可见（假设绑定表示一个可变的位置），但是它们引起的视图更改可以与赋值异步处理
    /// 绑定引用的值。 The value referenced by the binding. Assignments to the value  will be immediately visible on reading (assuming the binding  represents a mutable location), but the view changes they cause  may be processed asynchronously to the assignment.
    public var wrappedValue: Value { get nonmutating set }

    /// 绑定值，通过在 @Binding 属性上访问 `$foo` 作为“unwrapped”
    /// The binding value, as "unwrapped" by accessing `$foo` on a `@Binding` property.
    public var projectedValue: Binding<Value> { get }

    /// Creates a new `Binding` focused on `Subject` using a key path.
    // 使用关键路径创建一个新的 `Binding`，其对象为 `Subject`
    public subscript<Subject>(dynamicMember keyPath: WritableKeyPath<Value, Subject>) 
    		-> Binding<Subject> { get }
}

extension Binding {

    /// Creates an instance by projecting the base value to an optional value.
    public init<V>(_ base: Binding<V>) where Value == V?

    /// Creates an instance by projecting the base optional value to its unwrapped value, or returns `nil` if the base value is `nil`.
    public init?(_ base: Binding<Value?>)

    /// Creates an instance by projecting the base `Hashable` value to an  `AnyHashable` value.
    public init<V>(_ base: Binding<V>) where 
                            Value == AnyHashable, 
                            V : Hashable
}
 
extension Binding {

    /// 创建一个新的 Binding，它将对任何更改应用 `transaction`  
    public func transaction(_ transaction: Transaction) -> Binding<Value>

    /// 创建一个新的 Binding，它将对任何更改应用动画 `animation`
    public func animation(_ animation: Animation? = .default) -> Binding<Value>
}
 
extension Binding : DynamicProperty {
}
```

# State

给定类型的持久值，View 通过该值读取和监视该值. SwiftUI 管理所有 State 属性的存储。 

- 当 State 的值改变了 View 使外观无效并重新计算自己的 `body`
- 使用 State 作为给定 View 的单一数据源。
- 一个 State 实例并不是值本身，这是一种读取和改变值的手段。
- 要访问 State 的基础值，请使用其 value 属性。 
- 仅能从View 的 body 内部（或从 body 调用的函数）访问 State 属性。因为这个原因你应该把你的State属性声明为private. 以防止View 的调用者从外部访问它。 
- 你可以从具有 binding 属性的 state 获得 binding ，也可以使用 $ 前缀运算符。

```swift
/// 
一个链接的View属性，该属性实例化类型为Value的持久状态值，从而允许视图读取和更新其值
A linked View property that instantiates a persistent state  value of type `Value`, allowing the view to read and update its value. 
实例化类型为Value的持久状态值
允许 View 来读写他们的值
@propertyWrapper public struct State<Value> : DynamicProperty {

    public init(wrappedValue value: Value)
    
    public init(initialValue value: Value)

    /// 当前的 state 值
    public var wrappedValue: Value { get nonmutating set }

    /// 投影属性 Produces the binding referencing this state value 产生引用该状态值的绑定
    public var projectedValue: Binding<Value> { get }
}

extension State where Value : ExpressibleByNilLiteral {
    /// Initialize with a nil initial value.
    @inlinable public init()
}

```





# DynamicProperty

一个存储变量，用于更新视图的外部属性。在重新计算 View 的 body 之前，View 会给这些属性提供值。  
属性更新触发 UI 更新的桥梁

目的: 实现 View 的属性更新自动触发对应的 UI 更新

```swift
/// 表示 `View` 类型的一个存储属性，该变量从视图的某些外部属性动态更新。这些变量将在调用 `body()` 之前立即获得有效值。
public protocol DynamicProperty {
	/// Represents a stored variable in a `View` type that is dynamically  updated 
    /// from some external property of the view. These variables  will be given valid values
    /// immediately before `body()` is called.
    /// 调用顺序: 任何存储在`self`动态属性值更新 -> update() -> View 的 `body`
    mutating func update()
}

extension DynamicProperty { 
	/// 调用顺序: self的 属性更新 -> update() -> UI 更新(View 的 `body`)
    public mutating func update()
}
```

#  ExpressibleByNilLiteral

可以使用 nil 初始化的类型。`nil` 在 Swift 中有特殊的含义 - 没有价值的值。只有 `Optional` 遵守了 `ExpressibleByNilLiteral` 协议，不建议用在其他方面。

```swift
public protocol ExpressibleByNilLiteral {
    /// Creates an instance initialized with `nil`.
    init(nilLiteral: ())
}
```



# Transaction



```swift
public struct Transaction {
    @inlinable public init()
}

extension Transaction {
	/// 创建一个事务并分配其动画属性
    public init(animation: Animation?)
	/// 与当前状态更改关联的动画（如果有）。
    public var animation: Animation?
	/// 一个布尔值，指示视图是否应禁用动画
    public var disablesAnimations: Bool
}

extension Transaction {
    /// 一个布尔值，指示事务是否源自产生一系列值的动作. 如果 transaction 是通过 “连续” 动作创建的，则为True
    /// 比如: 拖动滑块而不是点击一次
    public var isContinuous: Bool
}

/// Returns the result of executing `body()` with `transaction` installed as the thread's current transaction. 
public func withTransaction<Result>(_ transaction: Transaction, 
                                    _ body: () throws -> Result) rethrows -> Result
```



当前状态处理更新的上下文

使用事务在视图层次结构的视图之间传递动画

状态更改的根事务来自于已更改的绑定，以及通过调用  `withTransaction(_:_:)` or `withAnimation(_:_:)`. 设置的所有全局值。
### 动态预览

Xcode 预览使用了动态替换 body 属性的特性，但是他有一些局限：当 body 外的部分改变整个 ContentView 需要整体重新编译, 此时必须再次点击 Resume 按钮才能重新预览。 快捷键 `Option + Command + P`

```swift
struct ContentView: View {
    var name = "zhaoli"
    var body: some View {
        Text("Hello world \(name)") 
    }
}
```

### 动态字体

[Apple 人机交互指南](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/typography/)





### 动态缩放字体

- 自定义字体缩放

```swift
guard let customFont = UIFont(name: "CustomFont-Light", size: UIFont.labelFontSize) else {
    fatalError("Failed to load the "CustomFont-Light" font.")
}
label.font = UIFontMetrics(forTextStyle: .headline).scaledFont(for: customFont)
label.adjustsFontForContentSizeCategory = true
```

[Scaling Fonts Automatically](https://developer.apple.com/documentation/uikit/uifont/scaling_fonts_automatically)



```swift
struct ContentView: View {
    var name = "zhaolee"
    var body: some View {
        VStack {
            Spacer()
            Text("+")
                .font(.title)
                .foregroundColor(.white)
                .padding(.horizontal, 50)
                .padding([.top, .bottom], 10)
                .background(Color.orange)
            Spacer()
        }
    }
}
```

`padding` 把当前的`view` 包裹在一个新的`view` 里面，并在四周填充空白。他的参数四个方向单独指定，也可以组合方式指定。





将此视图放置在具有指定宽度和高度约束的不可见框架中。

调用此方法时，请始终至少指定一个尺寸特征。传递'nil'或省略一个特征以指示框架应采用此视图的大小调整行为，并受其他非`nil'参数的约束。

 此视图建议的尺寸是建议框架的尺寸，受指定的任何限制所限制，并且指定了任何理想尺寸，并替换了建议中的相应零尺寸。

  如果在给定维度中未指定最小约束或最大约束，则框架将采用其子项在该维度中的尺寸调整行为。如果在维度中指定了两个约束，则框架将无条件地采用为该约束建议的大小。详细来说，任一尺寸的框架尺寸为：

- 如果指定了最小约束，并且父级为框架建议的尺寸小于此视图的尺寸，则建议尺寸将被限制为该最小值。

- 如果指定了最大约束，并且父级为框架建议的尺寸大于此视图的尺寸，则建议的尺寸将被限制为该最大值。

- 否则，此视图的大小。
- 参数：
- minWidth：生成的帧的最小宽度。
- IdealWidth：生成的帧的理想宽度。
- maxWidth：结果框架的最大宽度。
- minHeight：结果框架的最小高度。
- IdealHeight：生成的框架的理想高度。
- maxHeight：结果框架的最大高度。
- 对齐：此视图在结果框架内的对齐。
请注意，当帧的大小恰好与此视图的大小匹配时，大多数对齐值都没有明显的作用。

- 返回：该视图具有由调用的非nil给出的灵活尺寸

Positions this view within an invisible frame with the specified width  and height constraints.

Always specify at least one size characteristic when calling this  method. Pass `nil` or leave out a characteristic to indicate that the frame should adopt this view's sizing behavior, constrained by  the other non-`nil` arguments.

 The size proposed to this view is the size proposed to the frame,  limited by any constraints specified, and with any ideal dimensions  specified replacing corresponding nil dimensions in the proposal.

  If you no minimum or maximum constraint is specified in a given  dimension, the frame adopts the sizing behavior of its child in that  dimension. If both constraints are specified in a dimension, the frame  unconditionally adopts the size proposed for it, clamped to the constraints.  In detail, the size of the frame in either dimension is:

- If a minimum constraint is specified and the size proposed for the   frame by the parent is less than the size of this view, the proposed   size, clamped to that minimum.

- If a maximum constraint is specified and the size proposed for the   frame by the parent is greater than the size of this view, the   proposed size, clamped to that maximum.

- Otherwise, the size of this view.
- Parameters:
- minWidth: The minimum width of the resulting frame.
- idealWidth: The ideal width of the resulting frame.
- maxWidth: The maximum width of the resulting frame.
- minHeight: The minimum height of the resulting frame.
- idealHeight: The ideal height of the resulting frame.
- maxHeight: The maximum height of the resulting frame.
- alignment: The alignment of this view inside the resulting frame.
Note that most alignment values have no apparent effect when the size of the frame happens to match that of this view.

- Returns: A view with flexible dimensions given by the call's non-`nil`
  

## Modifier 

- 像 font, foregroundColor 定义在具体的类型（比如Text）然后返回同样的类型（Text）的原地modifier

- 想padding, background 这样定义在 View extension 中，把原来的 View 进行包装并返回新的 View 的封装 modifier

  封装 Modifier 的使用要注意顺序，
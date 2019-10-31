# Dark Model

##  如何实现



## 配色

### 颜色适配

有两种方式实现颜色的自适应:

- 使用语义色替代传统的固定颜色
使用语义色是可以确保在不同的系统外观模式下(浅色模式或者深色模式)呈现一致性的UI效果，语义色根据功能命名而不是颜色本身

UIKit 为应用的 UI 元素的前景和背景颜色提供标准颜色对象。这些颜色对象的名称反映其预期用途，而不是特定的颜色值。
    
除上述情况外，标准颜色对象在使用提供的 UIColor 对象时会自动适应暗模式更改。如果直接检索颜色值或使用其他类型（如 CGColor），则必须自行处理暗模式更改。

UI Element Colors
Label Colors
label
secondaryLabel
tertiaryLabel
quaternaryLabel

FIll Colors
systemFill
secondarySystemFill
tertiarySystemFill
quaternarySystemFill

Text Colors
placeholderText


Standard Content Background Colors
systemBackground
secondarySystemBackground
tertiarySystemBackground

Grouped Content Background Colors
systemGroupedBackground
secondarySystemGroupedBackground

Separator Colors
separator

Link Color
link

Nonadaptable Colors
darkText
lightText




Standard Colors
使用提供的 UIColor 对象时，系统颜色对象会自动适应暗模式更改，但固定阴影颜色无法适应。
systemBlue
systemGreen
systemIndigo 靛蓝
systemOrange
systemPink 粉红色
systemPurple 紫色
systemRed
systemTeal 浅蓝
systemYellow
systemGray
systemGray2
systemGray3
systemGray4
systemGray5
systemGray6








- 在 asset catalog 中自定义颜色

```swift
extension UIColor {
    /// 纯代码自定义动态色
    /// 纯代码自定义动态色不能在 IB 里面引用
    static var customAccent: UIColor {
        if #available(iOS 13, *) {
            return UIColor { traitCollection -> UIColor in
                if traitCollection.userInterfaceStyle == .dark {
                    return MaterialUI.orange300
                } else {
                    return MaterialUI.orange600
                }
            }
        } else {
            return MaterialUI.orange600
        }
    }
    
    /// 使用 Asset catelog 创建颜色
    /// Asset catelog  支持在 IB 里面引用
    /// 在 Asset catelog 里面新建一个 Color Set, 
    /// 添加颜色 LightAndDarkHeaderColor， 外观选择 Any，Dark
    @available(iOS 11.0, *)
    var customAccent: UIColor! {
        return UIColor(named: "LightAndDarkHeaderColor")
    }
}
```

[多线程编程指南](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i)

[Swift 指针](https://juejin.im/post/5d7c2207f265da03ea5aabf7)

[iOS实践检查清单](https://github.com/Binlogo/iOS-Practice-Checklist)


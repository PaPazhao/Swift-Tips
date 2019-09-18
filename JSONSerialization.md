## JSONSerialization

### Foundation 对象转换为 JSON 对象

- API

```swift
 
/// 把 Foundation 对象转换为 JSON 数据，如果对象不能生成有效的 JSON 会抛出异常。
/// 设置 NSJSONWritingPrettyPrinted 选项讲会生成可读性高的带空格的 JSON， 如果不设置将生成压缩 JSON。 
open class func data(withJSONObject obj: Any, options opt: JSONSerialization.WritingOptions = []) throws -> Data
```

- 实例

  ```swift
  let infoDictonary: [String : Any] = [
      "name" : "zhaoli",
      "age" : 10,
      "mobile" : ["+86-15311449999", "+86-01088888888"],
      "address" : [
          "city" : "Beijing",
          "street" : "朝阳大悦城 10 层 1003"
      ]
  ]
  guard
      let data = try? JSONSerialization.data(withJSONObject: infoDictonary, options: []),
      let str = String(data: data, encoding: .utf8) else {
          return
  }
  print(str)
  ```

- 结果

  ```json
  /// options: [] 输出
  {"mobile":["+86-15311449999","+86-01088888888"],"name":"zhaoli","address":{"city":"Beijing","street":"朝阳大悦城 10 层 1003"},"age":10}
  
  /// options: [.prettyPrinted] 输出
  {
    "mobile" : [
      "+86-15311449999",
      "+86-01088888888"
    ],
    "name" : "zhaoli",
    "address" : {
      "city" : "Beijing",
      "street" : "朝阳大悦城 10 层 1003"
    },
    "age" : 10
  }
  
  /// options: [.prettyPrinted, .sortedKeys] 输出
  {
    "address" : {
      "city" : "Beijing",
      "street" : "朝阳大悦城 10 层 1003"
    },
    "age" : 10,
    "mobile" : [
      "+86-15311449999",
      "+86-01088888888"
    ],
    "name" : "zhaoli"
  }
  ```




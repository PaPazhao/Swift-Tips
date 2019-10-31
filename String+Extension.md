# String Extension

---

- URL 编码

  通过这个扩展把 URL 中非法字符用 % 语法表示

  ```swift
  extension String {
      var urlEscaped: String {
          return addingPercentEncoding(withAllowedCharacters: .urlHostAllowed)!
      }
  }
  ```

  


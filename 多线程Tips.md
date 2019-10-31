- GCD 障碍

  如果有一个并发队列用来读写一个数据对象，读操作可以并行，如果并发读写一个数据则必须保证在执行写入时不会有读操作，必须等待写入完成后才读，否则读取的数据会出现错误。使用GCD的 dispatch_barrier 实现。

  ```swift
  /// case 1
  let writer = DispatchWorkItem(falg: .barrier) {
    	// write data
  }
  let dataQueue = DispatchQueue(label: "data", attributes: .concurrent)
  dataQueu.async(execute: writer)
  
  /// case 2
  DispatchQueue.global().sync(flags: .barrier) {
      if _shared == nil {
          _shared = DAKeychain()
      }
  }
  ```

- 
#  内存

这里指 Unsafe Rust 下的数据布局、内存管理和使用相关规范。

## 列表

- [P.UNS.MEM.01 要注意选择合适的结构体、元组、枚举的数据布局](./mem/P.UNS.MEM.01.md)
- [P.UNS.MEM.02 不能修改其它进程/动态库的内存变量](./mem/P.UNS.MEM.02.md)
- [P.UNS.MEM.03 不能让 `String/Vec` 自动 `Drop` 其它进程/动态库的内存数据](./mem/P.UNS.MEM.03.md)
- [P.UNS.MEM.04 尽量用可重入(reentrant)版本的 C-API 或系统调用](./mem/P.UNS.MEM.04.md)
- [P.UNS.MEM.05 如果需要使用位域，推荐使用第三方库](./mem/P.UNS.MEM.05.md)
- [G.UNS.MEM.01 使用 `MaybeUninit<T>` 来处理未初始化的内存](./mem/G.UNS.MEM.01.md)
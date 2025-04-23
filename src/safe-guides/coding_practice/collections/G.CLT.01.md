## G.CLT.01 非必要情况下，不要使用`LinkedList`，而用`Vec`或`VecDeque`代替

**【级别】** 建议

**【描述】**

一般情况下，有 `Vec`和`VecDeque` 性能更好。`LinkedList` 存在内存浪费，缓存局部性（Cache Locality）比较差，无法更好地利用 CPU 缓存机制，性能很差。

只有在有大量的 列表 拆分 和 合并 操作时，才真正需要链表，因为链表允许你只需操作指针而非复制数据来完成这些操作。 

**【Lint 检测】**

| lint name                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [linkedlist](https://rust-lang.github.io/rust-clippy/master/#linkedlist) | yes           | no           | pedantic   | allow     |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api = true 
```
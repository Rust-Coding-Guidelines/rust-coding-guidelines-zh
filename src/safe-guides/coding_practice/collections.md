# 集合类型

Rust 中的集合类型包括四大类：

-  线性序列： [`Vec`](https://doc.rust-lang.org/stable/std/vec/struct.Vec.html), [`VecDeque`](https://doc.rust-lang.org/stable/std/collections/struct.VecDeque.html), [`LinkedList`](https://doc.rust-lang.org/stable/std/collections/struct.LinkedList.html)
- 映射集：[`HashMap`](https://doc.rust-lang.org/stable/std/collections/hash_map/struct.HashMap.html), [`BTreeMap`](https://doc.rust-lang.org/stable/std/collections/struct.BTreeMap.html)
- 集合： [`HashSet`](https://doc.rust-lang.org/stable/std/collections/hash_set/struct.HashSet.html), [`BTreeSet`](https://doc.rust-lang.org/stable/std/collections/struct.BTreeSet.html)
- 其他： [`BinaryHeap`](https://doc.rust-lang.org/stable/std/collections/struct.BinaryHeap.html)

---

## P.CLT.01  根据集合各自的特点选择合适的集合类型

**【描述】**

Rust 标准库内置的集合类型，在安全和性能方面还是比较靠谱的。需要仔细阅读标准库中各类集合类型的优缺点来选择合适的类型。

**下列场景考虑 `Vec`**

-  你想要一个可动态增长大小（堆分配）的数组
- 你想要一个栈结构
- 你想要集合元素按特定顺序排序，并且仅需要在结尾追加新元素
- 你可能只是想临时收集一些元素，并且不关心它们的实际存储

**下列场景考虑 `VecDeque`**

- 你想要一个可以在头尾两端插入元素的 `Vec`
- 你想要一个队列，或双端队列

**下列场景考虑`LinkedList`**

- 你非常确定你真的需要一个双向链表

**下列场景考虑 `Hashmap`**

- 你需要一个 KV  集合
- 你想要一个缓存

**下列场景考虑 `BTreeMap`**

- 你需要一个可以排序的 `HashMap`
- 你希望可以按需获取一系列元素
- 你对最小或最大的 KV 感兴趣
- 你想要找到比某物小或大的最大或最小键

**下列场景考虑使用 `Set` 系列**

- 你只是需要一个 集合

**下列场景考虑使用 `BinaryHeap`**

- 你想存储一堆元素，但只想在任何给定时间内处理 最大 或 最重要的元素
- 你想要一个优先队列



## G.CLT.01   非必要情况下，不要使用`LinkedList`，而用`Vec`或`VecDeque`代替

**【级别】** 建议

**【描述】**

一般情况下，有 `Vec`和`VecDeque` 性能更好。`LinkedList` 存在内存浪费，缓存局部性（Cache Locality）比较差，无法更好地利用CPU 缓存机制，性能很差。

只有在有大量的 列表 拆分 和 合并 操作时，才真正需要链表，因为链表允许你只需操作指针而非复制数据来完成这些操作。 

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [linkedlist](https://rust-lang.github.io/rust-clippy/master/#linkedlist) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api=true 
```




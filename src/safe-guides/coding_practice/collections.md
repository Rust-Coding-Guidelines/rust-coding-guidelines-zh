# 集合类型

Rust 中的集合类型包括四大类：

-  线性序列： [`Vec`](https://doc.rust-lang.org/stable/std/vec/struct.Vec.html), [`VecDeque`](https://doc.rust-lang.org/stable/std/collections/struct.VecDeque.html), [`LinkedList`](https://doc.rust-lang.org/stable/std/collections/struct.LinkedList.html)
- 映射集：[`HashMap`](https://doc.rust-lang.org/stable/std/collections/hash_map/struct.HashMap.html), [`BTreeMap`](https://doc.rust-lang.org/stable/std/collections/struct.BTreeMap.html)
- 集合： [`HashSet`](https://doc.rust-lang.org/stable/std/collections/hash_set/struct.HashSet.html), [`BTreeSet`](https://doc.rust-lang.org/stable/std/collections/struct.BTreeSet.html)
- 其他： [`BinaryHeap`](https://doc.rust-lang.org/stable/std/collections/struct.BinaryHeap.html)

## 列表

- [P.CLT.01 根据集合各自的特点选择合适的集合类型](./collections/P.CLT.01.md)
- [P.CLT.02 创建HashMap、VecDeque时，可以预先分配大约足够的容量来避免后续操作中产生多次分配](./collections/P.CLT.02.md)
- [G.CLT.01 非必要情况下，不要使用LinkedList，而用Vec或VecDeque代替](./collections/G.CLT.01.md)
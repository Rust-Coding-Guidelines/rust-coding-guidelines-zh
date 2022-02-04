## G.TYP.ARR.03  当数组元素为原生数据类型（Primitive），排序时宜使用非稳定排序

**【级别】** 建议

**【描述】**

稳定排序会消耗更多的内存和 CPU 周期，相对而言，非稳定排序性能更佳。

当然，在必须要稳定排序的场合，不应该使用非稳定排序。

注： `Vec<T>` 动态数组也适用此规则

**【反例】**

```rust
let mut vec = vec![2, 1, 3];
vec.sort();  // stable sort
```

**【正例】**

```rust
let mut vec = vec![2, 1, 3];
vec.sort_unstable(); // unstable sort
```

**【例外】**

```rust
// https://docs.rs/crate/solana-runtime/1.7.11/source/src/accounts_db.rs#:~:text=clippy%3a%3astable_sort_primitive
 pub fn generate_index(&self, limit_load_slot_count_from_snapshot: Option<usize>) {
        let mut slots = self.storage.all_slots();
        #[allow(clippy::stable_sort_primitive)]
        slots.sort(); // The business requirement here is to use stable sort 
        // ...
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [stable_sort_primitive](https://rust-lang.github.io/rust-clippy/master/#stable_sort_primitive) | yes           | no           | **perf**   | warn  |

当确实需要稳定排序时，需要修改该 lint 的设置为 `allow`。


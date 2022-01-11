## G.UNS.SafeAbstract.04  不要随便在公开的 API 中暴露裸指针

**【级别】** 要求

**【描述】**

在公开的API中暴露裸指针，可能会被用户修改为空指针，从而有段错误风险。

**【正例】**

```rust
use cache;


/**

    `cache crate` 内部代码：

    ```rust
    pub enum Cached<'a, V: 'a> {
        /// Value could not be put on the cache, and is returned in a box
        /// as to be able to implement `StableDeref`
        Spilled(Box<V>),
        /// Value resides in cache and is read-locked.
        Cached {
            /// The readguard from a lock on the heap
            guard: RwLockReadGuard<'a, ()>,
            /// A pointer to a value on the heap
            // 漏洞风险
            ptr: *const ManuallyDrop<V>,
        },
        /// A value that was borrowed from outside the cache.
        Borrowed(&'a V),
    }
**/
fn main() {
    let c = cache::Cache::new(8, 4096);
    c.insert(1, String::from("test"));
    let mut e = c.get::<String>(&1).unwrap();

    match &mut e {
        cache::Cached::Cached { ptr, .. } => {
            // 将 ptr 设置为 空指针，导致段错误
            *ptr = std::ptr::null();
        },
        _ => panic!(),
    }
    // 输出：3851，段错误
    println!("Entry: {}", *e);
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制参考】

Lint需要检测在 pub 的结构体、枚举等类型中有裸指针字段或变体，对开发者发出警告，要注意考虑其安全性

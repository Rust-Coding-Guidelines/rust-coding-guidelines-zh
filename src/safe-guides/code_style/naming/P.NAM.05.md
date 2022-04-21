## P.NAM.05 用于访问或获取数据的 `getter` 类方法通常不要使用 `get_` 前缀

**【描述】**

因为 Rust 所有权语义的存在，此例子中两个方法的参数分别是共享引用 `&self` 和 独占引用 `&mut self`，分别代表了 getter 的语义。

也存在一些例外情况可以用 `get_` 前缀。

**【反例】**

```rust
pub struct First;
pub struct Second;

pub struct S {
    first: First,
    second: Second,
}

impl S {
    // 不符合：访问成员函数名字不用get_前缀。
    pub fn get_first(&self) -> &First {
        &self.first
    }

    // 不符合：
    // 同样不建议 `get_mut_first`, or `mut_first`.
    pub fn get_first_mut(&mut self) -> &mut First {
        &mut self.first
    }

    // set_前缀是可以的
    pub fn set_first(&mut self, f: First) -> &mut First {
        self.first = f;
    }
}
```


**【正例】**

```rust
pub struct First;
pub struct Second;

pub struct S {
    first: First,
    second: Second,
}

impl S {
    // 符合
    pub fn first(&self) -> &First {
        &self.first
    }

    // 符合
    pub fn first_mut(&mut self) -> &mut First {
        &mut self.first
    }

    // set_前缀是可以的
    pub fn set_first(&mut self, f: First)  {
        self.first = f;
    }
}
```

**【例外】**

但也存在例外情况：只有当需要显式的语义来通过`getter`获取某种数据，才会使用`get`命名。例如，`Cell::get`可以访问一个`Cell`的内容。

对于进行运行时验证的getter，例如边界检查，可以考虑添加一个 Unsafe 的`_unchecked` 配套方法。一般来说，会有以下签名。

```rust
// 进行一些运行时验证，例如边界检查
fn get(&self, index: K) -> Option<&V>;
fn get_mut(&mut self, index: K) -> Option<&mut V>;
// 没有运行时验证，用于在某些情况下提升性能。比如，在当前运行环境中不可能发生越界的情况。
unsafe fn get_unchecked(&self, index: K) -> &V;
unsafe fn get_unchecked_mut(&mut self, index: K) -> &mut V;
```

getter 和类型转换 ([G.NAM.02](./G.NAM.02.md)) 之间的区别很小，大部分时候不那么清晰可辨。比如 [`TempDir::path`](https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.path) 可以被理解为临时目录的文件系统路径的 getter ，而 [`TempDir::into_path`](https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.into_path) 负责把删除临时目录时转换的数据传给调用者。

因为 `path` 方法是一个 getter ，如果用 `get_path` 或者 `as_path` 会造成信息冗余。

[`TempDir::path`]: https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.path
[`TempDir::into_path`]: https://docs.rs/tempdir/0.3.7/tempdir/struct.TempDir.html#method.into_path

来自标准库的例子：

- [`std::io::Cursor::get_mut`](https://doc.rust-lang.org/std/io/struct.Cursor.html#method.get_mut)
- [`std::ptr::Unique::get_mut`](https://doc.rust-lang.org/std/ptr/struct.Unique.html#method.get_mut)
- [`std::sync::PoisonError::get_mut`](https://doc.rust-lang.org/std/sync/struct.PoisonError.html#method.get_mut)
- [`std::sync::atomic::AtomicBool::get_mut`](https://doc.rust-lang.org/std/sync/atomic/struct.AtomicBool.html#method.get_mut)
- [`std::collections::hash_map::OccupiedEntry::get_mut`](https://doc.rust-lang.org/std/collections/hash_map/struct.OccupiedEntry.html#method.get_mut)
- [`<[T]>::get_unchecked`](https://doc.rust-lang.org/std/primitive.slice.html#method.get_unchecked)


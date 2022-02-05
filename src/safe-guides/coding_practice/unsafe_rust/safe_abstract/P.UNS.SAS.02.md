## P.UNS.SAS.02  Unsafe 代码编写者有义务检查代码是否满足安全不变式

**【描述】**

安全不变式（见 [Unsafe 代码术语指南](./glossary.md) ）是 Rust 里的安全函数，在任何有效输入的情况下，都不应该发生任何未定义行为。

可以从以下三个方面来检查：

1. 逻辑一致性。
2. 纯洁性。相同的输入总是要返回相同的输出。
3. 语义约束。传入的参数要合法，满足数据类型。

**【正例】**

该代码是为 `Borrow<str>` 实现 join 方法内部调用的一个函数 `join_generic_copy` 的展示。 在 `join_generic_copy` 内部，会对 `slice` 进行两次转换，而在 `spezialize_for_lengths!` 宏内部，调用了 `.borrow()` 方法，如果第二次转换和第一次不一样，而会返回一个未初始化字节的字符串。

这里， `Borrow<B>` 是高阶类型，它内部 `borrow` 的一致性其实并没有保证，可能会返回不同的 slice，如果不做处理，很可能会暴露出未初始化的字节给调用者。

```rust
// CVE-2020-36323: a higher-order invariant bug in join()
fn join_generic_copy<B, T, S>(slice: &[S], sep: &[T]) -> Vec<T> 
where T: Copy, B: AsRef<[T]> + ?Sized, S: Borrow<B>
{
    let mut iter = slice.iter();

    // `slice`is converted for the first time
    // during the buffer size calculation.
    let len = ...;  // `slice` 在这里第一次被转换	
    let mut result = Vec::with_capacity(len);
    // ...
    unsafe {
        let pos = result.len();
        let target = result.get_unchecked_mut(pos..len);
 
        // `slice`is converted for the second time in macro
        // while copying the rest of the components.
        spezialize_for_lengths!(sep, target, iter; // `slice` 第二次被转换
        0, 1, 2, 3, 4);
 
        // Indicate that the vector is initialized
        result.set_len(len);
    }
    result
}

// PoC: a benign join() can trigger a memory safety issue
impl Borrow<str> for InconsistentBorrow {
    fn borrow(&self) -> &str {
        if self.is_first_time() {
            "123456"
        } else {
            "0"
        }
    }
}

let arr: [InconsistentBorrow; 3] = Default::default();
arr.join("-");
```

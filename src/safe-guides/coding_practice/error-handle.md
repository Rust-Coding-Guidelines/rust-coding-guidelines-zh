# 错误处理

Rust 为了保证系统健壮性，将系统中出现的非正常情况划分为三大类：

1. 失败
2. 错误
3. 异常

Rust 语言针对这三类非正常情况分别提供了专门的处理方式，让开发者可以分情况去选择。

- 对于失败的情况，可以使用断言工具。
- 对于错误，Rust 提供了基于返回值的分层错误处理方式，比如 Option 可以用来处理可能存在空值的情况，而 Result 就专门用来处理可以被合理解决并需要传播的错误。
- 对于异常，Rust 将其看作无法被合理解决的问题，提供了线程恐慌机制，在发生异常的时候，线程可以安全地退出。

---

## P.ERR.01   当传入函数的参数值因为超出某种限制可能会导致函数调用失败，应该使用断言

**【描述】**

当传入函数的某个参数值可能因为超出某种限制，比如超出数组长度的索引、字符串是否包含某个字符、数组是否为空等，应该使用断言。

【正例】

```rust
// From: std::vec::Vec::swap_remove
#[stable(feature = "rust1", since = "1.0.0")]
pub fn swap_remove(&mut self, index: usize) -> T {
    #[cold]
    #[inline(never)]
    fn assert_failed(index: usize, len: usize) -> ! {
        panic!("swap_remove index (is {}) should be < len (is {})", index, len);
    }

    let len = self.len();
   
    if index >= len {
        // 此处使用断言方法，虽然不是标准库内置断言宏，但也是一种断言
        assert_failed(index, len);
    }
    unsafe {
        // We replace self[index] with the last element. Note that if the
        // bounds check above succeeds there must be a last element (which
        // can be self[index] itself).
        let last = ptr::read(self.as_ptr().add(len - 1));
        let hole = self.as_mut_ptr().add(index);
        self.set_len(len - 1);
        ptr::replace(hole, last)
    }
}
```



## P.ERR.02  当函数的返回值 或者 结构体字段的值 可能为空时，请使用 `Option<T>` 

**【描述】**

在某些其他语言中，如果函数的返回值 或 结构体字段的值 可能为空时，通常会设置一个 “哨兵值（Sentinel Value）” 来应对这种问题，比如使用一个 `nil` 或 `-1` 等特殊值来判断这类情况。

但是，在 Rust 中不需要这样，Rust 提供了 `Option<T>` 类型就是专门用于应对这类情况。

【正例】

```rust
struct Config {
    must: String,
    opt: Option<String>,
}

// OR

fn main() {
    let sentence = "The fox jumps over the dog";
    let index = sentence.find("fox");

    if let Some(fox) = index {
        let words_after_fox = &sentence[fox..];
        println!("{}", words_after_fox);
    }
}
```

 

## P.ERR.03   当程序中有不可恢复的错误时，应该让其 Panic

**【描述】**

如果遇到无法恢复的错误，则需要让程序 Panic。

相关 Clippy Lint： [if_then_panic](https://rust-lang.github.io/rust-clippy/master/#if_then_panic) 

【正例】

```rust
fn boot(ptr: *const usize) {
	if ptr.is_null() {
        panic!("ptr is null! boot failed!")
    }
    // or
    assert!(ptr.is_null(), "ptr is null! boot failed!");
}
```





---



## G.ERR.01  在处理 `Option<T>` 和 `Result<T, E>` 类型时，不要随便使用 `unwrap`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [unwrap_used](https://rust-lang.github.io/rust-clippy/master/#unwrap_used) | yes           | no           | restriction | allow |

### 【描述】

当 `Option<T>` 和 `Result<T, E>`类型的值分别是 `None` 或 `Err` 时，直接对其 `unwrap()` 会导致程序恐慌！

只有确定在  `Option<T>` 和 `Result<T, E>`类型的值不可能是 `None` 或 `Err` 时，才可以直接 `unwrap()`

【正例】

```rust
fn select(opt: Option<String>) {
    opt.expect("more helpful message");  // 可以用 expect 方法来处理 None 的情况
}
// OR
fn select(opt: Result<String, ()>) {
    res.expect("more helpful message");  // 可以用 expect 方法来处理 Err 的情况
}
```

【反例】

```rust
fn select(opt: Option<String>) {
    opt.unwrap();  // 可以用 expect 方法来处理 None 的情况
}
// OR
fn select(opt: Result<String, ()>) {
    res.unwrap();  // 可以用 expect 方法来处理 Err 的情况
}
```








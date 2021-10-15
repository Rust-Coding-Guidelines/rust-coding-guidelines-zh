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

## P.ERR.04   当程序中需要处理错误时，应该使用 `Result<T, E>` 和 `?` 操作符 

**【描述】**

当需要处理错误时，为了保证 程序的健壮性，应该尽可能处理错误。

【正例】

```rust
let res: Result<usize, ()> = Ok(1);
res?;   // Ok::<(), ()>(())
```

【反例】

在实现原型类项目的时候，可以“快、糙、猛”地使用 `expect`  。但是要进生产环境，需要合理地处理错误。

```rust
let res: Result<usize, ()> = Ok(1);
res.expect("one"); // 如果有 Err， expect会 Panic ！

```



## P.ERR.05  在确定  `Option<T>` 和 `Result<T, E>`类型的值不可能是 `None` 或 `Err` 时，请用   `expect` 代替  `unwrap()`

**【描述】**

当需要处理的   `Option<T>` 和 `Result<T, E>` 类型的值，永远都不可能是 `None` 或 `Err` 时，虽然直接 `unwrap()` 也是可以的，但使用 `expect` 会有更加明确的语义。

> `expect` 的语义： 
>
> 我不打算处理 `None` 或 `Err` 这种可能性，因为我知道这种可能性永远不会发生，或者，它不应该发生。但是 类型系统并不知道它永远不会发生。所以，我需要像类型系统保证，如果它确实发生了，它可以认为是一种错误，并且程序应该崩溃，并带着可以用于跟踪和修复该错误的栈跟踪信息。

所以在指定 `expect` 输出消息的时候，请使用肯定的描述，而非否定，用于提升可读性。

【正例】

```rust
// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic 并提供错误信息方便调试，或者让使用者知道原因
// expect 里输出的描述信息，使用肯定的内容，整体代码可读性更高，更能突出 expect 的语义
let config = Config::read("some_config.toml").expect("Provide the correct configuration file"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("Provide the correct Ip addr");
}
```

【反例】

```rust
// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic ，但这里并没有提供错误信息，对于调试或使用都没有帮助
let config = Config::read("some_config.toml").unwrap();

// or
// expect 的输出描述使用否定式内容，可读性不好
let config = Config::read("some_config.toml").expect("No configuration file provided"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().unwrap();
}

// or
// expect 的输出描述使用否定式内容，可读性不好
fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("IP addr parse failed!");
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



## G.ERR.02    不要滥用 `expect`，请考虑用 `unwrap_or_` 系列方法代替

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [expect_fun_call](https://rust-lang.github.io/rust-clippy/master/#expect_fun_call) | yes           | no           | perf        | warn  |
| [expect_used](https://rust-lang.github.io/rust-clippy/master/#expect_used) | yes           | no           | restriction | allow |

### 【描述】

使用 `expect` 的时候请遵循 `expect` 的语义，不要滥用。参考 ： **P.ERR.05** 。

但是对于一些存在“副作用”的函数，在 遇到 `None` 或 `Err` 时，可能需要返回一些指定的值。这个时候用 `expect` 就不太符合语义。

如果你的用法完全符合 `expect` 语义，那么可以设置 `#![allow(clippy::expect_fun_call]`

【正例】

```rust
let foo = Some(String::new());
let err_code = "418";
let err_msg = "I'm a teapot";
foo.unwrap_or_else(|| panic!("Err {}: {}", err_code, err_msg));  // 你可以根据场景选择性使用 panic! 或者 不 panic!
```

【反例】

```rust
let foo = Some(String::new());
let err_code = "418";
let err_msg = "I'm a teapot";
foo.expect(&format!("Err {}: {}", err_code, err_msg)); 
// or
foo.expect(format!("Err {}: {}", err_code, err_msg).as_str());  
```

【例外】

完全符合 `expect` 语义的使用。

```rust
#![allow(clippy::expect_fun_call]

// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic 并提供错误信息方便调试，或者让使用者知道原因
let config = Config::read("some_config.toml").expect("Provide the correct configuration file"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("Provide the correct Ip addr");
}
```


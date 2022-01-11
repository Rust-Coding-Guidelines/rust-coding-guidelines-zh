## G.MAC.02   在多个地方使用`println!` 或 `panic!` 之类的内置宏 时，可以将其包装到函数内，使用 `#[cold]` 和 `#[inline(never)]` 属性避免其内联，从而避免编译文件膨胀

### 【级别：建议】

建议按此规范执行

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

### **【描述】**

因为像 `println!` 或 `panic!` 之类的宏，如果到处使用，就会到处展开代码，会导致编译文件大小膨胀。尤其在嵌入式领域需要注意。

【正例】

```rust
#[inline(never)]
#[cold]
#[track_caller] // 为了定位 panic 发生时的调用者的位置
fn unwrap_failed(msg: &str, error: &dyn fmt::Debug) -> ! {
    panic!("{}: {:?}", msg, error)
}

pub fn expect(self, msg: &str) -> T {
    match self {
        Ok(t) => t,
        Err(e) => unwrap_failed(msg, &e),
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => unwrap_failed("called `Result::unwrap_err()` on an `Ok` value", &t),
        Err(e) => e,
    }
}
```

【反例】

```rust
pub fn expect(self, msg: &str) -> T {
    match self {
        Ok(t) => t,
        Err(e) => panic!("{}: {:?}", msg, &e),
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => panic!("{}: {:?}", "called `Result::unwrap_err()` on an `Ok` value", &t),
        Err(e) => e,
    }
}
```

## G.MAC.02 使用宏时应该考虑宏展开会让编译文件膨胀的影响

**【级别】** 建议

**【描述】**

在多个地方使用 `println!` 或 `panic!` 之类的内置宏时，可以将其包装到函数内，使用 `#[cold]` 和 `#[inline(never)]` 属性避免其内联，从而避免编译文件膨胀。

因为像 `println!` 或 `panic!` 之类的宏，如果到处使用，就会到处展开代码，会导致编译文件大小膨胀。尤其在嵌入式领域需要注意。

**【反例】**

```rust
pub fn expect(self, msg: &str) -> T {
    match self {
        Ok(t) => t,
        Err(e) => panic!("{}: {:?}", msg, &e),  // 不符合
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => panic!("{}: {:?}", "called `Result::unwrap_err()` on an `Ok` value", &t), // 不符合
        Err(e) => e,
    }
}
```

**【正例】**

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
        Err(e) => unwrap_failed(msg, &e), // 符合
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => unwrap_failed("called `Result::unwrap_err()` on an `Ok` value", &t),  // 符合
        Err(e) => e,
    }
}
```
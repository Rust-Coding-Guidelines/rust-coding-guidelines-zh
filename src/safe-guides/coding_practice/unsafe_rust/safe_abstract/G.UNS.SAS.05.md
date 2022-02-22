## G.UNS.SAS.05  在抽象安全方法的同时，也建议为性能考虑而增加相应的 Unsafe 方法

**【级别】** 建议

**【描述】**

在 Rust 标准库中有很多后缀有 `_unchecked` 的方法，都对应一个没有该后缀的同名方法，比如 `get() / get_unchecked()`。

**【正例】**

```rust
/// 假如调用环境可以保证地址是非空，那么可以使用这个 "_unchecked" 的函数
#[inline(always)]
pub unsafe fn io_read_u32_unchecked(ioaddr: usize) -> u32 {
    let val = ptr::read_volatile(ioaddr as *const u32);
    trace!("io_read_u32 {:#x}={:#x}", ioaddr, val);
    val
}

/// 安全抽象版本
#[inline(always)]
pub unsafe fn io_read_u32() -> Result<u32, MyError> {
    let ioaddr = ioaddr as * const u32;
    if ioaddr.is_null() {
        return Err(MyError::Content("io_read_u32 addr is null!"));
    }
    unsafe {
        let val = ptr::read_volatile(ioaddr);
        trace!("io_read_u32 {:#x}={:#x}", ioaddr, val);
        ok(val)
    }
}   
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |


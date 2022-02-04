## P.ERR.03   当程序中有不可恢复的错误时，应该让其 Panic

**【描述】**

如果遇到无法恢复的错误，则需要让程序 Panic。

相关 Clippy Lint： [if_then_panic](https://rust-lang.github.io/rust-clippy/master/#if_then_panic) 

**【正例】**

```rust
fn boot(ptr: *const usize) {
	if ptr.is_null() {
        panic!("ptr is null! boot failed!")
    }
    // or
    assert!(ptr.is_null(), "ptr is null! boot failed!");
}
```
## P.UNS.FFi.03  当使用来自 C 的指针时，如果该指针需要管理内存，则需要为包装该指针的 Rust 类型实现 Drop trait

**【描述】**

Rust 里通过结构体包装该指针，并且为该结构体实现 Drop 来保证相关资源可以安全释放。

**【正例】**

下面示例中 `*mut sys::VMContext` 是来自于外部的 `C-ABI` 指针，它需要管理内存，所以在 Rust 这边使用结构体包装该指针，并实现 `Drop`，通过 `Drop` 来调用 `C-ABI` 回调函数来释放内存。

```rust
pub struct Vm {
    pub(crate) ctx: *mut sys::VMContext,
}

impl Drop for Vm {
    fn drop(&mut self) {
        if !self.ctx.is_null() {
            unsafe { sys::VMDelete(self.ctx) };
        }
    }
}    
```
## P.UNS.SAS.08  函数参数是不可变借用的时候，返回值不应该是可变借用

**【描述】**

即便函数签名中没有 `unsafe`，但开发者无法保证它函数头部实现中不含 Unsafe代码。

当通过 Unsafe 安全抽象一个函数的时候，要注意符合规则中描述的签名约定：不能输入一个不可变借用，返回一个可变的，这是违反 Rust安全准则的。

当然，当函数被标识为 `unsafe` 时，是允许这种情况的。

**【反例】**

这个来自 Rust 官方的一个示例，这样的签名导致 Rust 出现了一个严重 bug，[来源](https://github.com/rust-lang/rust/issues/39465)

```rust
// 该函数未加 unsafe，被认为是安全的
// 但是函数签名违反了 Rust 的安全规则，不应该不可变借用进去，可变借用返回
pub fn as_mut_slice(&self) -> &mut [T] {
    unsafe {
        slice::from_raw_parts_mut(self.ptr as *mut T, self.len())
    }
}    
```

**【正例】**

```rust
// 修正以后的代码：https://github.com/rust-lang/rust/pull/39466/files
pub fn as_mut_slice(&mut self) -> &mut [T] {
    unsafe {
        slice::from_raw_parts_mut(self.ptr as *mut T, self.len())
    }
} 
```

**【例外】**
```rust
// From: https://docs.rs/crate/wasmer/2.0.0/source/src/externals/memory.rs

/// Retrieve a mutable slice of the memory contents.
///
/// # Safety
///
/// This method provides interior mutability without an UnsafeCell. Until
/// the returned value is dropped, it is undefined behaviour to read or
/// write to the pointed-to memory in any way except through this slice,
/// including by calling a wasm function that reads the memory contents or
/// by resizing this Memory.
// 这里为 unsafe 函数，则允许这种情况
#[allow(clippy::mut_from_ref)]
pub unsafe fn data_unchecked_mut(&self) -> &mut [u8] {
	let definition = self.vm_memory.from.vmmemory();
	let def = definition.as_ref();
	slice::from_raw_parts_mut(def.base, def.current_length.try_into().unwrap())
}
```
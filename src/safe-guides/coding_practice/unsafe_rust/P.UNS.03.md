## P.UNS.03  要精确使用 `unsafe` 块的范围

**【描述】**

比如只有对调用 C 函数或其他 `unsafe` 函数时才使用 `unsafe` 块，而不要将多余的代码都包入 `unsafe` 块中。

**【反例】**

```rust
pub fn io_read_u32(ioaddr: usize) -> Result<u32, MyError> {
	// ...
	unsafe {
		let val = ptr::read_volatile(ioaddr);
		trace!("io_read_u32 {:#x}={:#x}", ioaddr, val);
		Ok(val)
	}
}
```

**【正例】**

```rust
pub fn io_read_u32(ioaddr: usize) -> Result<u32, MyError> {
	// ...
	let val = unsafe { ptr::read_volatile(ioaddr) };
	trace!("io_read_u32 {:#x}={:#x}", ioaddr, val);
	Ok(val)
}
``` 
## G.UNS.PTR.02  禁止将不可变指针手工转换为可变指针

**【级别】** 建议

**【描述】**

因为将不可变指针手工转换为可变指针可能会引发未定义行为。通常有这种需求，合法的手段是使用 `UnsafeCell<T>`。

**【 反例】**

```rust
fn x(r: &i32) {
    unsafe {
        *(r as *const _ as *mut _) += 1;
    }
}
```

**【正例】**

 ```rust
use std::cell::UnsafeCell;

fn x(r: &UnsafeCell<i32>) {
    unsafe {
        *r.get() += 1;
    }
}
 ```

**【例外】**

也有例外情况，当明确知道这种转换会出现什么风险的时候，可以使用，或者在找到合适的解决办法之前 作为一种临时方案，但要加上注释。

```rust
// https://docs.rs/crate/solana-runtime/1.7.11/source/src/append_vec.rs
#[allow(clippy::cast_ref_to_mut)]
fn set_data_len_unsafe(&self, new_data_len: u64) {
    // UNSAFE: cast away & (= const ref) to &mut to force to mutate append-only (=read-only) AppendVec
    unsafe {
        *(&self.meta.data_len as *const u64 as *mut u64) = new_data_len;
    }
}

// https://docs.rs/crate/mmtk/0.6.0/source/src/policy/space.rs
// This is a temporary solution to allow unsafe mut reference. We do not want several occurrence
// of the same unsafe code.
// FIXME: We need a safe implementation.
#[allow(clippy::cast_ref_to_mut)]
#[allow(clippy::mut_from_ref)]
unsafe fn mut_self(&self) -> &mut Self {
    &mut *(self as *const _ as *mut _)
}
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [cast_ref_to_mut](https://rust-lang.github.io/rust-clippy/master/#cast_ref_to_mut) | yes           | no           | **correctness** | deny  |

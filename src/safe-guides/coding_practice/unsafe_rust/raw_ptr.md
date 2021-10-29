# 裸指针操作



---



## P.UNS.PTR.01   建议使用 `NonNull<T>` 来替代 `*mut T`

**【描述】**





---



## G.UNS.PTR.01   当指针类型被强转为和当前内存对齐不一致的指针类型时，禁止对其解引用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_ptr_alignment](https://rust-lang.github.io/rust-clippy/master/#cast_ptr_alignment) | yes           | no           | style      | warn  |

### 【描述】

该 Lint 会检查是否出现 指针类型被强转为和当前内存对齐不一致的指针类型 的情况，要注意不要对这类强转后的指针进行解引用操作，否则会有未定义行为。

【正例】

```rust
fn main() {
    let a = (&1u8 as *const u8) as *const u8;
    let b = (&mut 1u8 as *mut u8) as *mut u8;

    let c =  (&1u8 as *const u8).cast::<u8>();
   
    // safe
    unsafe { *a }; 
    // safe
    unsafe { *b }; 
    // safe
    unsafe { *c }; 
}
```

【反例】

```rust
fn main() {
    let a = (&1u8 as *const u8) as *const u16;
    let b = (&mut 1u8 as *mut u8) as *mut u16;

    let c =  (&1u8 as *const u8).cast::<u16>();

    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *a }; 
    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *b }; 
    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *c }; 
}

```

## G.UNS.PTR.02   禁止将不可变指针手工转换为可变指针

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [cast_ref_to_mut](https://rust-lang.github.io/rust-clippy/master/#cast_ref_to_mut) | yes           | no           | **correctness** | deny  |

### 【描述】

因为将不可变指针手工转换为可变指针可能会引发未定义行为。通常有这种需求，合法的手段是使用 `UnsafeCell<T>`。

【正例】

 ```rust
use std::cell::UnsafeCell;

fn x(r: &UnsafeCell<i32>) {
    unsafe {
        *r.get() += 1;
    }
}
 ```

【 反例】

```rust
fn x(r: &i32) {
    unsafe {
        *(r as *const _ as *mut _) += 1;
    }
}
```

【例外】

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

## G.UNS.PTR.03   尽量使用 `pointer::cast` 来代替 使用 `as` 强转指针

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [ptr_as_ptr](https://rust-lang.github.io/rust-clippy/master/#ptr_as_ptr) | yes           | no           | **correctness** | deny  |

### 【描述】

使用 `pointer::cast` 方法转换更加安全，它不会意外地改变指针的可变性，也不会将指针转换为其他类型。

【正例】

```rust
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr.cast::<i32>();
let _ = mut_ptr.cast::<i32>();
```

【反例】

```rust
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr as *const i32;
let _ = mut_ptr as *mut i32;
```


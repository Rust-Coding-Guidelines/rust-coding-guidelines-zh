# 裸指针操作



Rust提供了`*const T`（不变）和`*mut T`（可变）两种指针类型。因为这两种指针和C语言中的指针十分相近，所以叫其原生指针（Raw Pointer）。

原生指针具有以下特点：

- 并不保证指向合法的内存。比如很可能是一个空指针。
- 不能像智能指针那样自动清理内存。需要像 C 语言那样手动管理内存。
- 没有生命周期的概念，也就是说，编译器不会对其提供借用检查。
- 不能保证线程安全。

可见，原生指针并不受Safe Rust提供的那一层“安全外衣”保护，所以也被称为“裸指针”。

---



## P.UNS.PTR.01   建议使用 `NonNull<T>` 来替代 `*mut T`

**【描述】**

尽量使用 [`NonNull`](https://doc.rust-lang.org/stable/std/ptr/struct.NonNull.html) 来包装 `*mut T`。

`NonNull` 的优势：

1. 非空指针。会自动检查包装的指针是否为空。
2. 协变。方便安全抽象。如果用裸指针，则需要配合 `PhantomData`类型来保证协变。

【正例】

```rust
use std::ptr::NonNull;

let mut x = 0u32;
let ptr = NonNull::<u32>::new(&mut x as *mut _).expect("ptr is null!");

if let Some(ptr) = NonNull::<u32>::new(std::ptr::null_mut()) {
    unreachable!();
}
```

【定制参考】

检测到包含 `*mut T`类型的结构体，应该给予开发者警告或建议去使用 `NonNull` 。



## P.UNS.PTR.02   使用指针类型构造泛型结构体时，需要使用 `PhantomData<T>` 来指定 `T`上的协变和所有权

**【描述】**

`PhantomData<T>` 是经常被用于 Unsafe Rust 中配合裸指针来指定协变和所有权的，为裸指针构建的类型保证安全性和有效性。否则，可能会产生未定义行为。

  参考： [`PhantomData<T>`  的型变（variance）模式表](https://doc.rust-lang.org/nomicon/phantom-data.html) 

【正例】

```rust
use std::marker;

struct Vec<T> {
    data: *const T, // *const for variance!
    len: usize,
    cap: usize,
    _marker: marker::PhantomData<T>, // 让 Vec<T> 拥有 T，并且让 指针有了协变
}
```

【反例】

```rust

// Vec<T> 不拥有类型 T，并且 data 字段的裸指针不支持协变
// 这样的话，是有风险的。
// 为 Vec<T> 实现的 Drop 可能导致 UB
struct Vec<T> {
    data: *const T, 
    len: usize,
    cap: usize,
}
```

【定制参考】

检测使用指针类型构造泛型结构体时，如果没有 `PhantomData<T>` 类型的字段，则需要警告开发者，要考虑 为裸指针配合`PhantomData<T>`来指定协变和所有权

## P.UNS.PTR.03   不要将裸指针在多线程间共享

**【描述】**

裸指针在 Rust 中不是线程安全的，将裸指针在多线程传递编译器也会编译出错。如果需要在多线程间共享裸指针，则考虑使用 `NewType` 模式来包装它。

【正例】

```rust
struct MyBox(*mut u8);

unsafe impl Send for MyBox {}
unsafe impl Sync for MyBox {}
```



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


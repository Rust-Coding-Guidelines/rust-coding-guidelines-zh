## G.UNS.PTR.01  当指针类型被强转为和当前内存对齐不一致的指针类型时，禁止对其解引用

**【级别】** 建议

**【描述】**

对于指针类型被强转为和当前内存对齐不一致的指针类型的情况，要注意不要对这类强转后的指针进行解引用操作，否则会有未定义行为。

**【反例】**

```rust
fn main() {
    let a = (&1u8 as *const u8) as *const u16; // 不符合
    let b = (&mut 1u8 as *mut u8) as *mut u16; // 不符合

    let c =  (&1u8 as *const u8).cast::<u16>(); // 不符合

    
    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *a }; 
    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *b }; 
    // Undefined Behavior: dereferencing pointer failed: alloc1411 has size 1, so pointer to 2 bytes starting at offset 0 is out-of-bounds
    unsafe { *c }; 
}

```

**【正例】**

```rust
fn main() {
    let a = (&1u8 as *const u8) as *const u8; // 符合
    let b = (&mut 1u8 as *mut u8) as *mut u8; // 符合

    let c =  (&1u8 as *const u8).cast::<u8>(); // 符合
   
    
    // safe
    unsafe { *a }; 
    // safe
    unsafe { *b }; 
    // safe
    unsafe { *c }; 
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_ptr_alignment](https://rust-lang.github.io/rust-clippy/master/#cast_ptr_alignment) | yes           | no           | style      | warn  |

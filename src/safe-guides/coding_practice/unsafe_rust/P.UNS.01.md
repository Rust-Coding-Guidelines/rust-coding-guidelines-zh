## P.UNS.01  不要为了逃避编译器安全检查而滥用 Unsafe Rust

**【描述】**

Unsafe Rust 有其应用范围和目标，不要为了逃避 编译器安全检查而随便滥用 Unsafe Rust，否则很可能引起未定义行为（UB）。

【反例】

```rust
// 该函数为滥用 unsafe 来跳过 Rust 借用检查 
// 强行返回本地变量的引用，最终引起 UB 未定义行为
fn abuse_unsafe_return_local_ref<'a>() -> &'a String {
    let s = "hello".to_string();
    let ptr_s_addr = &s as *const String as usize;
    unsafe{ &*(ptr_s_addr as *const String) }
}

fn main() {
    let s = abuse_unsafe_return_local_ref(); // error: Undefined Behavior: encountered a dangling reference (use-after-free)
}
```
## G.CTL.07   在Match分支的Guard语句中不要使用带有副作用的条件表达式

**【级别】** 建议

**【描述】**

因为在 mactch 分支中， 匹配几次就会执行 Guard 几次。如果携带副作用，会产生意料之外的情况。

**【反例】**

```rust
// 下面代码会输出两次 "ha"
fn main() {
    use std::cell::Cell;
    let i: Cell<i32> = Cell::new(0);
    match 1 {
        1 | _  // 这里匹配两次
            if { // 这个 Guard 条件表达式带有副作用：打印，因为匹配两次，所以会执行两次
                println!("ha");
                i.set(i.get() + 1);
                false
            } => {}
        _ => {}
    }
    assert_eq!(i.get(), 2);
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

**【定制化参考】**

可以检测 match分支中 Guard 的 if 表达式是否使用 `{}` ，如果是的话，发出警告，不要带有副作用。



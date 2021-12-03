# 联合体（Union）

Union 是没有 tag 的 Enum，Enum 是有 tag 的Union 。

内存布局 Union 和 Enum 相似。

正因为没有 tag，Rust 编译器无法检查当前使用的正是哪个变体，所以，访问 Union 的变体是 Unsafe 的。

---

## G.UNS.Union.01  除了与 C 打交道，尽量不要使用 Union

**【级别：必须】**

**【描述】**

Rust 支持 Union 就是为了和 C 打交道。如果不是 FFi ，就避免使用 Union。

一般情况下请使用 枚举 或 结构体代替。

使用 Copy 类型的值和 `ManuallyDrop` 来初始化 Union 的变体，不需要使用 Unsafe 块。

**【反例】**

```rust
union MyUnion {
    f1: u32,
    f2: f32,
}
```

**【正例】**

```rust
#[repr(C)]
union MyUnion {
    f1: u32,
    f2: f32,
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

这条规则如果需要定制 Lint，则可以检测 Union 联合体上方是否有 `#[repr(C)]`属性定义与C兼容的数据布局，如果没有则给予警告。

## G.UNS.Union.02   不要把联合体的不同变体用在不同生命周期内

**【级别：必须】**

**【描述】**

 对联合体的变体进行借用的时候，要注意其他变体也将在同一个生命周期内。抛开内存布局、安全性和所有权之外，联合体的行为和结构体完全一致，你可以将联合体当中结构体来进行判断。

**【反例】**

```rust
// ERROR: cannot borrow `u` (via `u.f2`) as mutable more than once at a time
fn test() {
    let mut u = MyUnion { f1: 1 };
    unsafe {
        let b1 = &mut u.f1;
//                    ---- first mutable borrow occurs here (via `u.f1`)
        let b2 = &mut u.f2;
//                    ^^^^ second mutable borrow occurs here (via `u.f2`)
        *b1 = 5;
    }
//  - first borrow ends here
    assert_eq!(unsafe { u.f1 }, 5);
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

检测函数内同一个联合体实例的不同变体被用于不同的生命周期内。

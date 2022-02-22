## G.UNS.FFi.02 在 FFi 中使用的类型应该拥有稳定布局

**【级别】** 要求

**【描述】**

**FFi-Safe**: 通过 FFi 外部传递的结构体类型都要满足内存布局的稳定性。

为结构体添加 `#[repr(C)]` 或 `#[repr(transparent)]` 可以让结构体拥有稳定的布局。

零大小类型在 C 中是无效的。也不要把 Rust 中的单元类型 `()` 和 C 中的 `void` 混为一谈。所以不应该在 FFi 中使用零大小类型（ZST）。

**【反例】**

```rust
// Foo 为零大小类型
// No FFi Safe
#[repr(C)]
pub struct Foo;

extern {
	fn get_some_instance() -> *mut Foo;
}
```

**【正例】**

```rust
// 如果 C 函数需要 opaque 类型，可以使用 libc::c_void 解决
extern crate libc;

extern "C" {
	pub fn foo(arg: *mut libc::c_void);
	pub fn bar(arg: *mut libc::c_void);
}

// 如果一定要使用零大小类型，比如 C 函数中返回一个结构体指针
// 可以按下面这种方式
#[repr(C)]
pub struct Foo { _unused: [u8; 0]}

// 理论上上面结构体应该是下面空枚举的一种等价模拟，因为现在 Rust 编译器还不支持给空枚举设置布局
// #[repr(C)]
pub enmu Foo{};

extern {
	fn get_some_instance() -> *mut Foo;
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------- | ---------- | ---------- |
| _         | no            | yes (warning) | _          | yes        |

【定制参考】

- 检测 extern 中使用的 Rust 结构体是否为零大小类型，对其产生警告
- 检测 extern 中使用的 Rust 结构体是否有 `#[repr(C)]` 或 `#[repr(transparent)]` 布局

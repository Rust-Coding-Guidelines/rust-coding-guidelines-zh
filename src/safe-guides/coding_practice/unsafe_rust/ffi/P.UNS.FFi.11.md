## P.UNS.FFI.11 如需引用指定为 `#[repr(packed)]` 内存布局的结构体成员字段要注意合理规避未定义行为

**【级别】** 要求

**【描述】**

Rust 中的引用类型要求必须是类型对齐的，当结构体使用了 `#[repr(packed)]` 设置内部对齐方式，而外部成员的地址又不是类型对齐时，对其成员的引用会引发编译期 `unaligned_references` 的警告，该警告在将来发布的 Rust 版本中会演变为错误。

注： 类型对齐是指一个类型的起始地址应该是其类型大小的整数倍，如 u32 的大小是4，则 u32 所在地址 addr 需要满足 addr % 4 == 0。

推荐解决方法：

1. 使用 `raw pointer` 代替引用，且使用 `ptr::read_unaligned` / `ptr::write_unaligned` 函数。
2. 可以复制结构体字段内容给一个本地变量，然后使用本地变量的引用。但是这个要求结构体必须实现 `Copy` trait。

对第二种方法的进一步说明：

- 结构体使用 `packed` 通常是为了匹配某种标准定义或二进制形式，在这种场景下结构体需要 `#[repr(C)]` 来稳定内存布局，通常也会通过 `#[derive(Copy)]` 来实现 `Copy` trait，这种情况不存在该问题；否则需要手动实现 `Copy` trait，或者通过增加中间变量，显示地发生 move，使用结束后再 move 回原结构体；
- 通过该方法访问的是临时拷贝的数据，那如果需要改变原数据怎么做？
  - 若通过某个引用修改其指向的数据，则该引用一定已在某个时间点被创建，那么在创建时，若其合法，则可正常对其操作，否则在创建时就已报 `unaligned_references`，又回到了该问题，对其操作可能会造成 `Undefined Behavior`；
  - 若结构体的成员地址不对齐，又需要进行修改，建议通过结构体来操作，如 `foo.baz = 4`，编译器能够完成该工作。
  
**【反例】**

```rust
#![deny(unaligned_references)]

#[repr(packed)]
pub struct Foo {
    field1: u64,
    field2: u8,
}

fn main() {
    unsafe {
        let foo = Foo { field1: 0, field2: 0 };
        let _ = &foo.field1; // UB Error. triggering the lint.
        println!("{}", foo.field1); // UB Error. An implicit `&` is added here, triggering the lint.
    }
}
```

**【正例】**

使用第二种方法。

```rust
#![deny(unaligned_references)]

#[derive(Copy, Clone)]
#[repr(packed)]
pub struct Foo {
    field1: u64,
    field2: u8,
}

fn main() {
    unsafe {
        let foo = Foo { field1: 0, field2: 0 };
		let field1 = foo.field1; // 此处会 Copy field1
        let _ = &field1;
        println!("{}", field1);
    }
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | yes           | _          | _        |

**【参考】**

[https://doc.rust-lang.org/stable/nightly-rustc/rustc_lint/builtin/static.UNALIGNED_REFERENCES.html](https://doc.rust-lang.org/stable/nightly-rustc/rustc_lint/builtin/static.UNALIGNED_REFERENCES.html)
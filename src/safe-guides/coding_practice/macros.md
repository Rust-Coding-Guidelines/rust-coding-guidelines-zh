# 宏

Rust  通过宏来支持元编程。其中宏有很多种，按实现方式可以分为两大类：声明宏（Declarative） 和 过程宏（Procedural）。

按功能效果，过程宏又可以分为三类：

1.  Bang 宏。类似于声明宏那样，像函数调用一样去使用的宏。
2. Derive 宏。用于为数据类型自动生成一些 语法项（item），比如 trait 、结构体、方法等。
3. Attrubutes 宏。用于更加通用的代码生成功能。

Rust 语言核心库和标准库，都内置了一些声明宏和过程宏，以方便开发者使用。

内置的属性宏按功能大体又可以分为四类：

1. 测试属性。`#[test]` 属性宏用于将某个函数标记为单元测试函数。
2. 诊断（[Diagnostic](https://doc.rust-lang.org/reference/attributes/diagnostics.html#diagnostic-attributes)）属性。用于在编译过程中控制和生成诊断信息。包括：
   1. `allow(c)`/ `warn(c)`/ `deny(c)`/ `forbid(c)`  等。
   2. `#[must_use]` 。
3. [代码生成属性](https://doc.rust-lang.org/reference/attributes/codegen.html)。包括：`inline` / `cold` / `\#[target_feature]` 等。
4. [编译时限制属性](https://doc.rust-lang.org/reference/attributes/limits.html)。包括：`recursion_limit ` / `type_length_limit` 。
5. [类型系统属性](https://doc.rust-lang.org/reference/attributes/type_system.html)。包括：`non_exhaustive` 。

**宏编程规范：**

使用宏时，需要从 `声明宏` 和 `过程宏` 各自的特性为出发点，来安全使用它。

- [声明宏规范](./macros/declarative.md)
- [过程宏规范](./macros/procedural.md)

**宏展开命令：**

```text
# 对单个 rs 文件
rustc -Z unstable-options --pretty expanded hello.rs
# 对项目里的二进制 rs 文件
cargo rustc --bin hello -- -Z unstable-options --pretty=expanded
```

---

## P.MAC.01  不要轻易使用宏

【描述】

能使用宏写出强大和用户友好的宏API的人，重点不是因为他们对宏如何实现掌握的好，而是因为他们也掌握了宏之外关于 Rust 的一切。

宏设计的重点在于宏生成什么样的代码，而不是宏如何生成代码。

宏只是将 Rust 语言特性以一种有趣的方式组合在一起能自动生成代码的创造力。

尤其是过程宏，它有一定复杂性，且很难调试，不卫生，也容易出错，不适合新手使用它。

【参考】

[Rust 社区顶级专家 Dtolnay 写的 宏学习案例 ](https://github.com/dtolnay/case-studies)

## P.MAC.02   在多个地方使用`println!` 或 `panic!` 之类的内置宏 时，可以将其包装到函数内，使用 `#[cold]` 和 `#[inline(never)]` 属性避免其内联，从而避免编译文件膨胀

【描述】

因为像 `println!` 或 `panic!` 之类的宏，如果到处使用，就会到处展开代码，会导致编译文件大小膨胀。尤其在嵌入式领域需要注意。

【正例】

```rust
#[inline(never)]
#[cold]
#[track_caller] // 为了定位 panic 发生时的调用者的位置
fn unwrap_failed(msg: &str, error: &dyn fmt::Debug) -> ! {
    panic!("{}: {:?}", msg, error)
}

pub fn expect(self, msg: &str) -> T {
    match self {
        Ok(t) => t,
        Err(e) => unwrap_failed(msg, &e),
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => unwrap_failed("called `Result::unwrap_err()` on an `Ok` value", &t),
        Err(e) => e,
    }
}
```

【反例】

```rust
pub fn expect(self, msg: &str) -> T {
    match self {
        Ok(t) => t,
        Err(e) => panic!("{}: {:?}", msg, &e),
    }
}

pub fn unwrap_err(self) -> E {
    match self {
        Ok(t) => panic!("{}: {:?}", "called `Result::unwrap_err()` on an `Ok` value", &t),
        Err(e) => e,
    }
}
```


## G.FUD.04   当Copy 类型的足够小的值作为函数参数时，应该按值（by-value）传入，而不是引用(by-ref)

**【级别】** 建议

**【描述】**

在函数参数为 Copy 类型 且 其值足够小的时候，一般情况下，会避免传引用。因为对于这种小的值，性能上和按引用传递是一样快的，并且在代码更容易编写和可读。包括一些小的 结构体，也推荐按值传递，但要注意【例外】示例所示的情况。

**【反例】**

```rust
#![warn(clippy::trivially_copy_pass_by_ref)]
fn foo(v: &u32) {}
```

**【正例】**

```rust
#![warn(clippy::trivially_copy_pass_by_ref)]
fn foo(v: u32) {}
```

**【例外】**

需要注意这种情况下，lint 检查工具会误报。

```rust
#[derive(Clone, Copy)]
struct RawPoint {
    pub x: u8,
}

#[derive(Clone, Copy)]
struct Point {
    pub raw: RawPoint,
}

impl Point {
    pub fn raw(&self) -> *const RawPoint {
        &self.raw
    }
    // 如果听信 lint 的建议，将上面的 raw 函数参数 self 的引用去掉就是 raw_linted 函数
    pub fn raw_linted(self) -> *const RawPoint {
        &self.raw
    }
}

fn main() {
    let p = Point { raw: RawPoint { x: 10 } };

    // This passes
    assert_eq!(p.raw(), p.raw());
    // This fails 事实上，如果去掉那个 self 的引用，该函数的行为就变了
    // 因为 结构体 Point 是 Copy 的，每次调用 raw_linted 方法，结构体实例就会被复制一次，得到的结果就不一样了
    assert_eq!(p.raw_linted(), p.raw_linted());
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [trivially_copy_pass_by_ref](https://rust-lang.github.io/rust-clippy/master/#trivially_copy_pass_by_ref) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api=true
# 考虑Copy按值而不是按引用传递的类型的最大大小（以字节为单位）。默认是None
trivial-copy-size-limit=None
```

**注意**，该 lint 没有考虑指针相关的情况，见例外示例。需要酌情考虑使用。例外示例来自  [rust-clippy/issues/5953](https://github.com/rust-lang/rust-clippy/issues/5953) 。
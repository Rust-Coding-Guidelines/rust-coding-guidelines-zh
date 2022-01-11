## G.FUD.01  函数参数最长不要超过五个

**【级别】** 建议

**【描述】**

为了提升代码可读性，函数的参数最长不宜超过五个。

**【反例】**

```rust
struct Color;
fn foo(x: f32, y: f32, name: &str, c: Color, w: u32, h: u32, a: u32, b: u32) {
    // ..
}
```

**【正例】**

想办法把过长的参数缩短。

```rust
struct Color;
// 此处使用 常量泛型（const generic） 来接收后面多个 u32 类型的参数
// 使用元组 缩短 2~3 个参数为一个参数
fn foo<T, const N: usize>(x: (f32, f32), name: &str, c: Color, last: [T; N]) {
    ;
}

fn main(){
    let arr = [1u32, 2u32];
    foo((1.0f32, 2.0f32), "hello", Color, arr);
    let arr = [1.0f32, 2.0f32, 3.0f32];
    foo((1.0f32, 2.0f32), "hello", Color, arr);
}
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [too_many_arguments](https://rust-lang.github.io/rust-clippy/master/#too_many_arguments) | yes           | no           | complexity | warn  |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 函数参数最长不要超过5个
too-many-arguments-threshold=5
```

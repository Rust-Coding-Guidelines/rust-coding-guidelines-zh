## P.MAC.Decl.05    使用宏替换（substitution）元变量的时候要注意选择合适的片段分类符（[fragment-specifier](https://doc.rust-lang.org/nightly/reference/macros-by-example.html#metavariables)）

  【描述】

 使用宏替换（substitution）元变量，就是指把已经进行过宏解析的 token 再次传给宏，需要注意，此时传入的 token，已经被看作是宏解析器解析后的 AST 节点了。

【正例】

满足示例这类正常匹配情况的目前只有 `tt`、`ident` 或者 `lifetime` 分类符。

```rust
macro_rules! capture_then_what_is {
    (#[$m:tt]) => {what_is!(#[$m])}; // 这里片段分类符用的是 tt
}

macro_rules! what_is {
    (#[no_mangle]) => {"no_mangle attribute"};
    (#[inline]) => {"inline attribute"};
    ($($tts:tt)*) => {concat!("something else (", stringify!($($tts)*), ")")};
}

fn main() {
    println!(
        "{}\n{}\n{}\n{}",
        what_is!(#[no_mangle]),
        what_is!(#[inline]),
        capture_then_what_is!(#[no_mangle]), // 被 capture_then_what_is 宏 解析过的token，还会被 what_is 二次处理
        capture_then_what_is!(#[inline]), // 被 capture_then_what_is 宏 解析过的token，还会被 what_is 二次处理
    );
}

// 输出：
// no_mangle attribute
// inline attribute
// no_mangle attribute
// inline attribute

```

【  反例】

```rust
macro_rules! capture_then_what_is {
    (#[$m:meta]) => {what_is!(#[$m])};   // 这里片段分类符用的是 meta
}

macro_rules! what_is {
    (#[no_mangle]) => {"no_mangle attribute"};
    (#[inline]) => {"inline attribute"};
    ($($tts:tt)*) => {concat!("something else (", stringify!($($tts)*), ")")};
}

fn main() {
    println!(
        "{}\n{}\n{}\n{}",
        what_is!(#[no_mangle]),
        what_is!(#[inline]),
        capture_then_what_is!(#[no_mangle]), // 被 capture_then_what_is 宏 解析过的token，不会再二次被 what_is 宏解析，所以按 tt 规则处理
        capture_then_what_is!(#[inline]), // 被 capture_then_what_is 宏 解析过的token，不会再二次被 what_is 宏解析，所以按 tt 规则处理
    );
}
// 输出：
// no_mangle attribute
// inline attribute
// something else (#[no_mangle])
// something else (#[inline])
```

## P.MAC.Decl.03    不要在片段分类符（[fragment-specifier](https://doc.rust-lang.org/nightly/reference/macros-by-example.html#metavariables)）跟随它不匹配的符号

  【描述】

`macro_rules!` 定义声明宏时，非终止的元变量匹配必须紧随一个已被决定可以在这种匹配之后安全使用的标记。

具体的规则参见：  [Follow-set Ambiguity Restrictions](https://doc.rust-lang.org/reference/macros-by-example.html#follow-set-ambiguity-restrictions)

【正例】

该示例中，元变量`$e1`的 片段分类符`expr` 是非终止的，所以后面需要跟随一个用于分隔的标记。

Rust 规定在 `expr` 片段分类符 后面可以合法地跟随 `=>` / `,` / `;` 。

```rust
#[macro_export]
macro_rules! foo {
    ( $e1:expr, $e2:expr) => {$e1; $e2}; 
}

```

【  反例】

对于 `[,]` 这样的分隔标记就是非法的。这是为了防止未来 Rust 语法变动导致宏定义失效。

```rust
#[macro_export]
macro_rules! foo {
    ( $e1:expr [,] $e2:expr) => {$e1; $e2}; 
}

```

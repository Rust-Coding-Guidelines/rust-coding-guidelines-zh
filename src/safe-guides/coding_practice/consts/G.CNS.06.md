## G.CNS.06  对于适用 `const fn` 的函数或方法宜尽可能地使用 `const fn`

**【级别】** 建议

**【描述】**

函数或方法缺失`const`关键词时无法被指派给常量。
但是要注意不是所有函数都能使用`const fn`，因为相比一般函数或方法，`const fn`在使用时会有限制，一些功能将无法在`const fn`内使用，例如迭代器。

**【反例】**

```rust
fn foo() -> usize {
    10
}

let bar: usize = foo();   // OK
const BAZ: usize = foo(); // ERROR
```

**【正例】**

```rust
const fn foo() -> usize {
    10
} 

let bar: usize = foo():   // OK
const BAZ: usize = foo(); // OK
```

**【例外】**

```rust
const fn foo() -> bool {
    for _i in 0..5 {}      // ERROR, 因为for loop默认不能用在const fn内（需要注明#![feature(const_for)]）
    false
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [missing_const_for_fn](https://rust-lang.github.io/rust-clippy/master/#missing_const_for_fn) | yes| no | Perf | warn |

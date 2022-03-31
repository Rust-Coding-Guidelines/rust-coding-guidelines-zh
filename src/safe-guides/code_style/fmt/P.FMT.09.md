## P.FMT.09  不同的场景，使用不同的空格风格

**【描述】**

1. 在冒号之后添加空格，在冒号之前不要加空格。
2. 在范围（range）操作符（`..`和`..=`）前后不要使用空格。
3. 在`+`或`=`操作符前后要加空格。

**【反例】**

```rust
// 不符合： 冒号之后未加空格
// 当 `space_after_colon=false`
fn lorem<T:Eq>(t:T) {
    let lorem:Dolor = Lorem {
        ipsum:dolor,
        sit:amet,
    };
}

// 不符合： 冒号之前加空格
// 当 `space_before_colon=true`
fn lorem<T : Eq>(t : T) {
    let lorem : Dolor = Lorem {
        ipsum : dolor,
        sit : amet,
    };
}

// 不符合： `..`前后加空格
// 当 `spaces_around_ranges=true`
let lorem = 0 .. 10;
let ipsum = 0 ..= 10;

// 不符合： `+`和`=`前后加空格
// 当 `type_punctuation_density="Compressed"`
fn lorem<Ipsum: Dolor+Sit=Amet>() {
    // body
    let answer = 1 + 2;
}
```

**【正例】**

```rust
// 符合
// 当 `space_after_colon=true`
fn lorem<T: Eq>(t: T) {
    let lorem: Dolor = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}

// 符合
// 当 `space_before_colon=false`
fn lorem<T: Eq>(t: T) {
    let lorem: Dolor = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}

// 符合
// 当 `spaces_around_ranges=false`
let lorem = 0..10;
let ipsum = 0..=10;

// 符合
// 当 `type_punctuation_density="Wide"`
fn lorem<Ipsum: Dolor + Sit = Amet>() {
    // body
    let answer = 1 + 2;
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`space_after_colon`](https://rust-lang.github.io/rustfmt/?#space_after_colon) | true（默认） | No |  在冒号后面要加空格|
| [`space_before_colon`](https://rust-lang.github.io/rustfmt/?#space_before_colon) | false（默认） | No| 在冒号前面不要加空格 |
| [`spaces_around_ranges`](https://rust-lang.github.io/rustfmt/?#spaces_around_ranges) | false（默认） | No| 在`..`和`..=`范围操作符前后不要加空格 |
| [`type_punctuation_density`](https://rust-lang.github.io/rustfmt/?#type_punctuation_density) | "Wide"（默认） | No| 在 `+`或`=`操作符前后要加空格（此处特指类型签名） |
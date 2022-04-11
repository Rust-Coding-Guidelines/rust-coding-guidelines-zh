## G.VAR.03  变量遮蔽功能应当合理使用

**【级别】** 建议

**【描述】**

变量遮蔽功能在功能上属于一种继承式可变。他会覆盖之前的变量绑定，而创建一个新的同名的变量绑定。

1. 在同一个作用域中，非必要时不宜通过新变量声明遮蔽旧变量声明的方式来修改变量。
2. 在子作用域内修改“哨兵变量”时，应该避免使用变量遮蔽功能，防止引起逻辑bug。
3. 如果使用变量遮蔽，禁止用不同类型的变量遮蔽前一个变量，如果实现同一个 `trait` 的可以例外。

**【反例】**

```rust
#![warn(clippy::shadow_reuse, clippy::shadow_same, clippy::shadow_unrelated)]

fn main() {
    let mut a = 0;
    {
        // 不符合：这里使用变量遮蔽，代码逻辑已经被改变
        // clippy::shadow_unrelated
        let a = 42;
    }
    
    a; // use a again

    let x = 2; 
    // 不符合： 将会改变x的值
    // clippy::shadow_reuse
    let x = x + 1; 

    // 不符合：只是改变引用级别
    // clippy::shadow_same
    let x = &x; 

    let y = 1;
    // 不符合：这里使用变量遮蔽逻辑已经被改变
    // clippy::shadow_unrelated
    let x = y; // 更早的绑定
    let z = 2;
    // 不符合：这里使用变量遮蔽逻辑已经被改变
    // clippy::shadow_unrelated
    let x = z; // 遮蔽了更早的绑定
}
```

**【正例】**

```rust
#![warn(clippy::shadow_reuse, clippy::shadow_same, clippy::shadow_unrelated)]

fn main() {
    let mut a = 0;
    {
        // 符合
        a = 42;
    }
    a;// use a again


    let x = 2;
    let y = x + 1; // 符合： 不改变x的值，声明新的变量y


    let ref_x = &x; // 符合：不改变x的绑定，声明新的变量
    let z = 2;
    let w = z; // 符合： 使用不同的名字
}
```


**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [shadow_reuse](https://rust-lang.github.io/rust-clippy/master/#shadow_reuse) | yes           | no           | restriction   | allow |
| [shadow_same](https://rust-lang.github.io/rust-clippy/master/#shadow_same) | yes           | no           | restriction   | allow |
| [shadow_unrelated](https://rust-lang.github.io/rust-clippy/master/#shadow_unrelated) | yes           | no           | restriction   | allow |


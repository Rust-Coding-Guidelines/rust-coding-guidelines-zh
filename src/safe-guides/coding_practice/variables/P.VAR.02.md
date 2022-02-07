## P.VAR.02 利用变量遮蔽功能保证变量安全使用

**【描述】**

在某些场景，可能会临时准备或处理一些数值，但在此之后，数据只用于检查而非修改。

那么可以将其通过变量遮蔽功能，重写绑定为不可变变量，来表明这种临时可变，但后面不变的意图。

**【正例】**

```rust
let mut data = get_vec();
data_sort(); //临时需要排序
let data = data; // 后面就不需要改动了，由编译器可以确保

// Here `data` is immutable
```

**【反例】**

```rust
let data = {
    let mut data = get_vec();
    data.sort();
    data // 虽然后面不再改动，但代码语义上没有表现出来先改变，后不变那种顺序语义
}

// Here `data` is immutable
```

---
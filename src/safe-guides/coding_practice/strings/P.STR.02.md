## P.STR.02   创建字符串时，宜预先分配大约足够的容量来避免后续操作中产生多次分配

**【描述】**

预分配足够的容量，避免后续内存分配，可以提升代码性能。

**【反例】**

```rust
let mut output = String::new();
```

**【正例】**

```rust
let mut output = String::with_capacity(input.len());
```



## P.TYP.Vec.02   创建动态数组时，宜预先分配足够容量，避免后续操作中产生多次分配

**【描述】**

预分配足够的容量，避免后续内存分配，可以提升代码性能。

**【反例】**

```rust
let mut output = Vec::new();
```

**【正例】**

```rust
let mut output = Vec::with_capacity(input.len());
```

---


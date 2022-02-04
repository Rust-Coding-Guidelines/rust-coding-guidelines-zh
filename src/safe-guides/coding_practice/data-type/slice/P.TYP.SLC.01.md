## P.TYP.SLC.01  宜使用切片迭代器来代替手工索引

**【描述】**

在 for 循环中使用索引是比较常见的编程习惯，但是这种方式是最有可能导致边界错误的。

利用 切片自带的方法，并利用迭代器，可以避免这种错误。

**【正例】**

```rust
let points: Vec<Coordinate> = ...;
let mut differences = Vec::new();

// 切片提供 windows 或 array_windows 方法返回迭代器
for [previous, current] in points.array_windows().copied() {
    differences.push(current - previous);
}
```

**【反例】**

```rust
let points: Vec<Coordinate> = ...;
let differences = Vec::new();

// 人工计算长度选择范围很可能会出错
for i in 1..points.len() [
  let current = points[i];
  let previous = points[i-1];
  differences.push(current - previous);
]
```


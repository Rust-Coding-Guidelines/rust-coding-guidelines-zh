## P.CLT.01 创建HashMap、VecDeque时，可以预先分配大约足够的容量来避免后续操作中产生多次分配

**【描述】**

预分配足够的容量，避免后续内存分配，可以提升代码性能。

**【反例】**

```rust
use std::collections::HashMap;
use std::collections::VecDeque;

fn main() {

    // HashMap
    let mut map = HashMap::new();
    map.insert("a", 1);
    map.insert("b", 2);
    map.insert("c", 3);
    println!("{:#?}", map);
    
    // VecDeque
    let mut deque = VecDeque::new();
    deque.push_back(1);
    deque.push_back(2);
    deque.push_back(3);
    println!("{:#?}", deque);
}
```

**【正例】**

```rust
use std::collections::HashMap;
use std::collections::VecDeque;

fn main() {

    // HashMap
    let mut map = HashMap::with_capacity(3);
    map.insert("a", 1);
    map.insert("b", 2);
    map.insert("c", 3);
    println!("{:#?}", map);
    
    // VecDeque
    let mut deque = VecDeque::with_capacity(3);
    deque.push_back(1);
    deque.push_back(2);
    deque.push_back(3);
    println!("{:#?}", deque);
}
```
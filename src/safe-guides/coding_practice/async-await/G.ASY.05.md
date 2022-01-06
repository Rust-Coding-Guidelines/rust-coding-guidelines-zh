## G.ASY.05  避免在异步处理过程中包含阻塞操作

**【级别：建议】**

**【描述】**

避免在异步编程中使用阻塞操作。

**【反例】**

不要在异步流程中使用阻塞操作函数

```rust
use std::error::Error;
use std::{fs, io};
    
async fn read_file() -> Result<String, std::io::Error> {
    fs::read_to_string("test.txt")
}
```

**【正例】**

使用异步运行时，如tokio提供的非阻塞函数

```rust
use tokio::fs;

async fn read_file() -> std::io::Result<()> {
    let _ = fs::read_to_string("test.txt").await?;
    Ok(())
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】
这条规则如果需要定制Lint，则可以扫描异步过程，找到黑名单定义的阻塞操作调用，进行告警。

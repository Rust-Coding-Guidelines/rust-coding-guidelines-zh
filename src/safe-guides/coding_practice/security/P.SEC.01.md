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


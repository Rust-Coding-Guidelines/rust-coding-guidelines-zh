# 开发环境

## 编辑器推荐

VSCode + Rust Analyzer 扩展 

其他辅助vscode 扩展：

[flowistry](https://github.com/willcrichton/flowistry) ，可以帮助开发者理解 Rust 程序。

## IDE 推荐

Clion

## 工具链安装

使用[ `Rustup`](https://github.com/rust-lang/rustup)。 如需替代安装方式，为了保证安全，最好选择官方推荐的替代安装方式。

## **Rust  版次（Edition）** **说明**

Rust从2015开始，每三年发布一个 Edition 版次：

> 1. Rust 2015 edition （Rust 0.1.0 ~ Rust 1.0.0）
> 2. Rust 2018 edition （Rust 1.0.0 ~ Rust 1.31.0）
> 3. Rust 2021 edition (Rust 1.31.0 ~ Rust 1.56.0 )

以此类推。Edition是向前兼容的。Edition 和语义化版本是正交的，不冲突。

关于 Edition 更详细的内容可以查看：[https://doc.rust-lang.org/edition-guide/](https://doc.rust-lang.org/edition-guide/)

## **稳定版、 开发版和测试版工具链**

Rust 工具链提供三种不同的发布渠道：

> 1. Nightly（开发版），每晚发布（release）一次。
> 2. Beta（测试版），每六周发布一次，基于Nightly版本。
> 3. Stable（稳定版），每六周发布一次，基于 beta版本。

注意：

> 1. 推荐使用 Stable Rust。
> 2. 在基于Nightly Rust 开发项目的时候，最好通过在项目中增加 rust-toolchain 文件来指定一个固定的版本，避免因为Nightly Rust 的频繁变更而导致项目编译问题。
> 3. 当在稳定版工作的时候，如果需要Nightly工具链，不需要整体上去切换工具链到Nightly，只需要再命令中指明Nightly就可以了。比如 `cargo +nightly fmt`。

## **包管理器 Cargo**

Cargo 是 Rust 项目必不可少的包管理器，除此之外，它也是一种工作流：

> 1. 可以用Cargo创建一个项目（bin/lib）
> 2. 可以用它编译项目
> 3. 可以用它生产项目的文档（依据文档注释）
> 4. 可以用它运行单元测试（test）和基准测试（bench）
> 5. 可以用它下载和管理crate依赖
> 6. 可以用它分发软件包，默认分发到 [crates.io](http://crates.io/) 上面
> 7. 可以为它编写插件，使用子命令的方式，扩展它的功能。

Cargo 通过 Cargo.toml 配置文件来管理 crate。

Toml 配置文件是一种最小化且无歧义的文件格式，Rust社区最常用Toml。可以通过 [toml.io](http://toml.io/) 进一步了解 Toml 的细节。

值得说明的是，在配置文件中如果有 [profile.*] 这种配置，需要引起注意，因为这类配置决定了编译器的调用方式，比如：

> 1. debug-assertions ，决定了是否开启debug断言。
> 2. overflow-checks，决定了是否检查整数运算溢出。

关于Cargo的更多细节可以查看：[https://doc.rust-lang.org/cargo/index.html](https://doc.rust-lang.org/cargo/index.html)

## 常用Cargo插件

**Clippy**

Clippy 是一个静态分析工具，它提供了很多检查，比如错误、 样式、 性能问题、 Unsafe UB问题等等。从1.29版本开始，Clippy可以用于 Stable Rust中。

可以通过 `rustup component add clippy` 来安装此 Cargo 插件。

细节参考：[https://github.com/rust-lang/rust-clippy](https://github.com/rust-lang/rust-clippy)

Clippy 的全部 lint 检查建议列表： [https://rust-lang.github.io/rust-clippy/master/](https://rust-lang.github.io/rust-clippy/master/)

**Rustfmt**

Rustfmt 是一个根据风格指南原则来格式化代码的工具。

可以通过 Rustup 来安装它： `rustup component add rustfmt`

Rustfmt 依赖的社区维护的 Rust风格指南：[https://github.com/rust-dev-tools/fmt-rfcs/tree/master/guide](https://github.com/rust-dev-tools/fmt-rfcs/tree/master/guide)

开发者也可以通过 `rustfmt.toml` 或 `.rustfmt.toml` 来定制团队统一的代码风格，比如：

```toml
# Set the maximum line width to 120
max_width = 120
# Maximum line length for single line if-else expressions
single_line_if_else_max_width = 40
```

**Rustfix**

从 Rust 2018 edition开始，Rustfix就被包含在 Rust 中。它可以用来修复编译器警告。

需要注意的是，在使用 cargo fix 进行自动修复警告的时候，需要开发者确认这个警告是否真的需要修复，并且要验证修复的是否正确。

**Cargo Edit**

Cargo Edit插件为Cargo扩展了三个命令：

> 1. Cargo add，在命令行增加新的依赖，而不需要去知道这个依赖的语义版本。
> 2. Cargo rm，在命令行删除一个指定依赖。
> 3. Cargo upgrade，在命令行升级一个指定依赖。

Cargo-edit地址：[https://github.com/killercup/cargo-edit](https://github.com/killercup/cargo-edit)

**Cargo Audit**

Cargo Audit 可以根据 Rust安全警报数据库（RestSec Advisory Database ）的漏洞数据，扫描crate以及它的所有依赖库，然后给出一份安全报告。

更多细节：[https://github.com/RustSec/cargo-audit](https://github.com/RustSec/cargo-audit)

Rust 安全警报数据库：[https://rustsec.org/](https://rustsec.org/)

**Cargo Outdated**

该插件可以检测依赖库是否有新版本可用。

更多细节：[https://github.com/kbknapp/cargo-outdated](https://github.com/kbknapp/cargo-outdated)

**Cargo Deny**

该插件可以检测依赖中的软件许可证（License），如果和开发者配置的不符合，则会拒绝使用该依赖。

更多细节：[https://github.com/EmbarkStudios/cargo-deny](https://github.com/EmbarkStudios/cargo-deny)

Cargo Deny Book： [https://embarkstudios.github.io/cargo-deny/](https://embarkstudios.github.io/cargo-deny/)

## Rustup 和 crates 国内镜像



###  加速 Rustup

我们需要指定 `RUSTUP_DIST_SERVER`（默认指向 https://static.rust-lang.org）和 `RUSTUP_UPDATE_ROOT` （默认指向https://static.rust-lang.org/rustup），这两个网站均在中国大陆境外，因此在中国大陆访问会很慢，需要配置成境内的镜像。

以下` RUSTUP_DIST_SERVER` 和 `RUSTUP_UPDATE_ROOT` 可以组合使用。

```toml
# 清华大学
RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup

# 中国科学技术大学
RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup

# 上海交通大学
RUSTUP_DIST_SERVER=https://mirrors.sjtug.sjtu.edu.cn/rust-static/


# 字节跳动
RUSTUP_DIST_SERVER="https://rsproxy.cn"
RUSTUP_UPDATE_ROOT="https://rsproxy.cn/rustup"
```



### 加速 crates 

将如下配置写入 `$HOME/.cargo/config` 文件：

```toml

# 放到 `$HOME/.cargo/config` 文件中
[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"

# 替换成你偏好的镜像源，比如 字节跳动的
replace-with = 'rsproxy'

# 清华大学
[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

# 中国科学技术大学
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"

# 上海交通大学
[source.sjtu]
registry = "https://mirrors.sjtug.sjtu.edu.cn/git/crates.io-index"

# rustcc社区
[source.rustcc]
registry = "git://crates.rustcc.cn/crates.io-index"

# 字节跳动 https://rsproxy.cn/
[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[registries.rsproxy]
index = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true

```

### 安装 Rust 

使用 字节跳动源：

```rust
# export the env above first
curl --proto '=https' --tlsv1.2 -sSf https://rsproxy.cn/rustup-init.sh | sh
```


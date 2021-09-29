# Cargo Deny 配置模板

[cargo-deny](https://github.com/EmbarkStudios/cargo-deny)  是检查 Cargo 依赖的一个 Lint 工具。它检查的范围包括：

- Licenses，检查依赖crate许可证是否合规。
- Bans， 检查被禁止使用的依赖 crate。
- Advisories ，检查有安全缺陷漏洞或停止维护的 依赖 crate。
- Source，检查依赖crate 的来源，确保只来自于可信任的来源。

以下是模板（参考 [vectordotdev/vector  的 deny.toml](https://github.com/vectordotdev/vector/blob/master/deny.toml)）：

```toml
[licenses]
allow = [
  "MIT",
  "CC0-1.0",
  "ISC",
  "OpenSSL",
  "Unlicense",
  "BSD-2-Clause",
  "BSD-3-Clause",
  "Apache-2.0",
  "Apache-2.0 WITH LLVM-exception",
  "Zlib",
]

unlicensed = "warn"
default = "warn"

private = { ignore = true }

[[licenses.clarify]]
name = "ring"
version = "*"
expression = "MIT AND ISC AND OpenSSL"
license-files = [
    { path = "LICENSE", hash = 0xbd0eed23 }
]

[advisories]
ignore = [
    # term is looking for a new maintainer
    # https://github.com/timberio/vector/issues/6225
    "RUSTSEC-2018-0015",

    # `net2` crate has been deprecated; use `socket2` instead
    # https://github.com/timberio/vector/issues/5582
    "RUSTSEC-2020-0016",

    # Type confusion if __private_get_type_id__ is overriden
    # https://github.com/timberio/vector/issues/5583
    "RUSTSEC-2020-0036",

    # stdweb is unmaintained
    # https://github.com/timberio/vector/issues/5585
    "RUSTSEC-2020-0056",
]
```


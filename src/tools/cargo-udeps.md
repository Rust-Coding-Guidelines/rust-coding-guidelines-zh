#  Cargo Udeps  

[cargo-udeps](https://github.com/est31/cargo-udeps) 检查 `Cargo.toml` 中未使用的依赖。

`cargo udeps` 对标的是` rustc` 的` unused_crate_dependencies lint`

虽然 rustc 也能检查一些未使用依赖，但是在 lib 和 bin 混合的项目中误报率高

```
RUSTFLAGS="-Dunused_crate_dependencies" cargo c
```

`cargo udeps` 的最大优点就是**几乎没有误报**。

但是检查力度不如` rustc unused_crate_dependencies lint `仔细，建议二者搭配使用


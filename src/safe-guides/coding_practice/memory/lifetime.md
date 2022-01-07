# 生命周期

生命周期（lifetime），也被叫做 生存期。可以理解为引用的有效范围。

---
<!-- toc -->
---

## P.MEM.Lifetime.01 生命周期参数命名尽量简单

**【描述】**

生命周期参数的命名应该尽量简单，可以使用表达一定语义的缩写。

因为生命周期参数的目的是给编译器使用，用于防止函数中出现悬垂引用。

适当简单的携带语义的缩写，可以最小化对业务代码的干扰。并且在生命周期参数较多的情况下，清晰地表达具体哪个引用属于哪个生命周期。

【正例】

```rust
/// 'cg = the duration of the constraint generation process itself.
struct ConstraintGeneration<'cg, 'cx, 'tcx> {
    infcx: &'cg InferCtxt<'cx, 'tcx>,
    all_facts: &'cg mut Option<AllFacts>,
    location_table: &'cg LocationTable,
    liveness_constraints: &'cg mut LivenessValues<RegionVid>,
    borrow_set: &'cg BorrowSet<'tcx>,
    body: &'cg Body<'tcx>,
}

```

【反例】

```rust

struct ConstraintGeneration<'a, 'b, 'c> {
    infcx: &'cg InferCtxt<'b, 'c>,
    all_facts: &'a mut Option<AllFacts>,
    location_table: &'a LocationTable,
    liveness_constraints: &'a mut LivenessValues<RegionVid>,
    borrow_set: &'a BorrowSet<'c>,
    body: &'cg Body<'c>,
}

```

## P.MEM.Lifetime.02  在需要的时候，最好显式地标注生命周期，而非利用编译器推断

**【描述】**

编译器可以推断你的代码做了什么，但它不知道你的意图是什么。

编译器对生命周期参数有两种单态化方式（生命周期参数也是一种泛型）：

- Early bound。一般情况下，`'a: 'b` 以及 `impl<'a'>`  这种方式是 early bound，意味着这些生命周期参数会在当前作用域单态化生命周期实例。
- Late bound。默认的 `'a`   或 `for<'a>` 是在实际调用它们的地方才单态化生命周期实例。

在不同的场景下，需要指定合适的单态化方式，才能让编译器明白你的意图。

在使用匿名生命周期 `'_` 的时候需要注意，如果有多个匿名生命周期，比如 `('_ ，'_)` ，每个匿名生命周期都会有自己的单独实例。


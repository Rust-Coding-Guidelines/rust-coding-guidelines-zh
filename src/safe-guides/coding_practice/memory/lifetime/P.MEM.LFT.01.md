## P.MEM.LFT.01 生命周期参数命名尽量有意义且简洁

**【描述】**

生命周期参数的命名应该尽量简单，可以使用表达一定语义的缩写。

因为生命周期参数的目的是给编译器使用，用于防止函数中出现悬垂引用。

适当简单的携带语义的缩写，可以最小化对业务代码的干扰。并且在生命周期参数较多的情况下，清晰地表达具体哪个引用属于哪个生命周期。

**【反例】**

```rust
struct ConstraintGeneration<'a, 'b, 'c> {
    infcx: &'c InferCtxt<'b, 'c>,
    all_facts: &'a mut Option<AllFacts>,
    location_table: &'a LocationTable,
    liveness_constraints: &'a mut LivenessValues<RegionVid>,
    borrow_set: &'a BorrowSet<'c>,
    body: &'c Body<'c>,
}
```

**【正例】**

```rust
// 增加 'cg 意义的文档注释
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
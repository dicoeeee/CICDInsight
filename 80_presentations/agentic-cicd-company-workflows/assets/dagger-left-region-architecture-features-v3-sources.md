---
asset: dagger-left-region-architecture-features-v3
status: proposed-for-review
as_of: 2026-08-03
---

# Dagger 左半区 V3：来源与表达边界

## 资产

- SVG：`dagger-left-region-architecture-features-v3.svg`，900 × 840，SHA-256 `edb1c06a45d9034e60cf93b77195a9401d1be83f4b5b4d22d9aa27f929073b55`。
- PNG：`dagger-left-region-architecture-features-v3.png`，1800 × 1680，SHA-256 `355eaa564bb5d9a295f878ab48180a634b34fc0923ab8af7d2fcda1ec6e7583a`。

## 图中主张

| 对象 | 有限主张 | 主要来源 |
|---|---|---|
| Delivery Module API | Dagger Module 以 Functions 暴露可调用、可组合的交付能力 | https://docs.dagger.io/features/reusability/；https://docs.dagger.io/core-concepts/functions/ |
| Typed Objects | Directory、File、Container、Service、Secret 等类型化对象可以进入 Function 与执行图 | https://docs.dagger.io/reference/api/internals/ |
| Execution Graph 与缓存 | Engine 对执行图求值；官方区分 Function Call Cache、Layer Cache 和 Cache Volumes | https://docs.dagger.io/features/caching/；https://docs.dagger.io/reference/api/internals/ |
| 运行位置 | 同一执行定义可以由本地、已有 CI 或远端 Engine 调用 | https://docs.dagger.io/reference/container-runtimes/ |
| Gradle 边界 | Gradle 以 Project / Task Graph 与可缓存 Task 输出为主要抽象；Dagger 将类型化容器对象和更外层交付流程纳入执行定义 | https://docs.gradle.org/current/userguide/build_lifecycle.html；https://docs.gradle.org/current/userguide/build_cache.html；https://docs.dagger.io/features/reusability/ |

详细对照审计见 `../../../00_sources/research-dagger-vs-gradle-execution-cache-2026-08-03.md`。

## 边界

- 完整图表达技术对象之间的关系，不表示固定的产品内部时序。
- Cache Volume 是显式可变工具状态，不属于内容寻址的 Function / Layer 结果缓存。
- Function Cache 位于 Module Function 调用边界，可跳过整个 Function Body；Layer Cache 位于 Function 内部的 Container、File、Directory 操作节点。Function Cache 未命中或设为 `never` 时，内部 Layer 仍可能命中。
- 图中 `RUN build`、`RUN test` 是 `ci(source)` 内部的容器操作，不是名为 `build()`、`test()` 的 Module Functions。
- “可移植”指执行定义，不保证身份、网络、架构和外部状态完全一致。
- Dagger 与 Gradle 可以组合，图中不表达替代关系。

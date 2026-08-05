---
asset: dagger-left-region-core-features-v2
status: proposed-for-review
as_of: 2026-08-03
---

# Dagger 左半区 V2：来源与表达边界

## 资产

- `dagger-left-region-core-features-v2.svg`：900 × 840，可编辑源图。
- `dagger-left-region-core-features-v2.png`：1800 × 1680，评审图。
- SVG SHA-256：`e72cd623a535451ad264192b71acb23d6a78064df6badb355fb7bb42f55dd371`。
- PNG SHA-256：`49e8e5b6d49a73d930b1e7c06def18d9db3d1a758212f0a36982a837296caa41`。

## 主张与来源

| 可见区域 | 有限主张 | 主要来源 |
|---|---|---|
| 可组合的 Module API | Dagger Module 将交付能力暴露为可发现、可调用、可组合的 Functions，并可返回 Container、Directory、File 等类型化对象 | https://docs.dagger.io/features/reusability/；https://docs.dagger.io/core-concepts/functions/ |
| 多粒度缓存与增量计算 | Dagger 官方区分 Function Call Cache、Layer Cache 与 Cache Volumes；Function / Layer 是结果复用层级，Cache Volume 是显式跨 Session 工具状态 | https://docs.dagger.io/features/caching/；https://docs.dagger.io/reference/api/internals/ |
| 可移植的执行定义 | Container、Directory、Service、Secret 等对象与 Function 进入执行图；同一执行定义可以在本地、已有 CI 或远端 Engine 上调用 | https://docs.dagger.io/reference/api/internals/；https://docs.dagger.io/reference/container-runtimes/ |
| 与 Gradle 对照 | Gradle 的核心模型围绕 Project、Task Graph 与可缓存 Task 输出；Dagger 的核心模型围绕 Module、类型化对象及更外层的容器化交付执行定义 | https://docs.gradle.org/current/userguide/build_lifecycle.html；https://docs.gradle.org/current/userguide/build_cache.html；https://docs.dagger.io/features/caching/；https://docs.dagger.io/features/reusability/ |

详细事实审计见 `../../../00_sources/research-dagger-vs-gradle-execution-cache-2026-08-03.md`。

## 表达边界

- 不把“都有 DAG”写成 Dagger 的独占优势；差异在核心对象与执行边界。
- 不写“Gradle 只缓存最终产物”；Gradle Build Cache 复用可缓存 Task 输出，Configuration Cache 另有独立机制。
- 不把 Cache Volume 表述为更细粒度的内容寻址结果缓存；它是显式可变状态通道。
- 不写 Dagger 替代 Gradle；二者可以组合使用。
- “可移植”限定为执行逻辑与大部分工具链定义，不代表身份、网络和外部状态完全一致。

---
title: Dagger 左区技术图事实审计（Function、Layer、Volume 与执行位置）
tags:
  - research/cicd
  - company/dagger
  - presentation/slide-07
  - fact-audit
status: complete
as_of: 2026-08-03
confidence: high
presentation_ready: false
---

# Dagger 左区技术图事实审计（Function、Layer、Volume 与执行位置）

> 审计对象：`80_presentations/agentic-cicd-company-workflows/assets/dagger-left-region-architecture-features-v3.svg`。
>
> 目的：纠正图中将 `build` / `test` 同时当作 Module Function 与 Layer 的混淆；保留 Dagger 的三层复用价值，但不把条件性能力画成无条件事实。
>
> 方法：仅查阅 Dagger 官方当前/版本化文档与官方 `dagger/dagger` 源码仓库。网页访问时间均为 2026-08-03（Asia/Shanghai）。

## 结论先行

当前图的核心意图是对的：**Function Call Cache 覆盖一次 Module Function 调用；该调用未命中时，内部 Dagger API 操作仍可独立复用；Cache Volume 为工具数据提供跨 Session 的加速状态。**

但 `RUN build`、`RUN test` 被画在 `ci(source)` 内并标成 `LAYER CACHE`，同时上方 API 又把 `build`、`test`画为 Function，语义上会让读者误解为“Module Function 就是 Layer”。这不是 Dagger 的层级关系。

应改成：

```text
模块对外 API（可单独调用）
  build(source) ───────────────→ Container / Directory
  test(source)  ───────────────→ Check / String
  publish(image) [cache=never] → Registry side effect / digest

选取其中一个纯 Function 作为缓存示例
  build(source) [Function Call Cache：适用时]
    └─ Dagger API operation graph
       ├─ Base image input:  from(node:21)
       ├─ Operation:         withDirectory(/src, source)   ← 结果可复用
       ├─ Operation:         withWorkdir(/src)
       ├─ Mount:             withMountedCache(/root/.npm, volume)
       └─ Operation:         withExec(npm install)         ← 结果可复用

Cache Volume（并列的可变工具数据）
  cacheVolume(node-21) --withMountedCache(/root/.npm)--> npm install 所在 Container
```

这样，`build` 是一项 **Module Function**，`withDirectory` / `withExec` 是 Function 内的 **Container operations**。`test(source)` 应画为另一个 Function，而不是把“测试 Function”当作同一 Function 内的 Layer。

## 逐项审计

| 图中/拟表达对象 | 判定 | 可核验边界与应采用表述 |
|---|---|---|
| `Delivery Module API` 中的 `ci`、`build`、`test`、`publish` | 有条件成立 | 只有被 SDK 标记/暴露为 Dagger Function 的成员才是可被 CLI/API 调用的 Module API。以 TypeScript 为例，只有 `@func()` 成员会暴露；未装饰成员仍是模块内部代码。不要把同一个方法既画成 public Function 又画成 Layer。 |
| 普通 helper 调用 | 不应画成独立 Function Call | 一个 public Function 的函数体可以调用普通语言方法；这仍是 Module runtime 内的普通代码调用。它不会仅因方法名为 `build()`/`test()` 就多出一个可独立命中的 Function Call Cache 边界。 |
| `FUNCTION CALL · ci(source)` 外框 | 可画，但必须加条件 | Function Call Cache 针对“调用 Module Function 返回的值”。命中条件包括 Module 源码、函数实参和 parent object state 相同；且实际 cache policy 不能为 `never`。因此不可把所有 `ci(source)` 都画成无条件“整体结果已缓存”。建议标注：`Function call cache（纯函数 / policy 允许时）`。 |
| Function cache miss 后的 Layer reuse | 成立 | 官方明确：Function miss 会执行函数体，但内部 `withExec`、`withFile`、`withDirectory` 等 operation 仍可命中 layer cache；`cache=never` 只要求 Function 重跑，不会关闭内部 Layer cache。 |
| `FROM` / `WITH src` / `RUN install` / `RUN build` / `RUN test` 每格都标 `LAYER CACHE` | 过度精确，应改 | 官方将 Layer 定义为容器构建的 step-wise instructions 与“某些修改 File/Directory 的 API 调用结果”。当前 SDK 文档能直接举证的是 builder operation 按输入自动 layer-cache，以及 `withDirectory`/`withExec`。`from` 应表为“base image input”，不应在页面上断言它与每个 `withExec` 一一对应为一个 Layer Cache。 |
| `RUN build`、`RUN test` 都在 `ci(source)` 内 | 技术上可能、表达上不宜 | 单一 public `ci()` 当然可以顺序执行两条命令；但若上方同时将 `build`、`test` 当作 public Module Functions，图中同名对象就发生层级混淆。演示缓存时选 `build(source)` 一个 Function，操作节点使用 `install dependencies` / `compile`，不要再用 `test` 作为 Layer 名。 |
| Cache Volume 指向 `RUN install` | 可画，但应说明“挂载”，不是层级包含 | 官方示例是 `.withMountedCache("/root/.npm", dag.cacheVolume("node-21"))` 后调用 `.withExec(["npm", "install"])`。Volume 是持久化的工具缓存目录，默认 scoped to module；不是 Function/Layers 的子级，也不是 source of truth。视觉用侧向“mount”箭头，而非从属嵌套或“第三层内容寻址缓存”。 |
| `Directory · File · Container · Service · Secret` 一起箭头流入 `ci(source)` | 过度泛化，应改 | 这些确为 Core Dagger Types，但一个 Function 只接受其签名声明的实际参数，或在函数内创建/返回对象。`ci(source)` 只应由 `source: Directory` 进入；可在旁注写“其他 Function 可接收 `Service` / `Secret` 等类型”。`Container` 通常也是 Function 的返回值和后续 operation 的承载对象，不能一概画成输入。 |
| `Artifact / OCI Image / Test Result` 同时从一个 `ci` 输出 | 容易误导，应拆分 | Dagger Function 一次返回一个声明的类型/值。典型 `build` 返回 `Container` 或 `Directory`，`test` 返回 `Check`/String，`publish` 产生 registry side effect 并可返回 digest。可以画为 API 中三个 Function 的各自结果，不能暗示一个 `ci` 必然同时交付三类输出。 |
| `Local / Existing CI / Remote Engine` 放在 Engine operation graph 下游 | 方向错误，应改 | 本地终端、CI 脚本、Agent tool 是**调用上下文**，不是执行图的输出。Dagger CLI/SDK 是 client，并连接/自动 provision Dagger Engine；“Existing CI”通常是 CI runner 调用 CLI。远程 Engine 需要配置的 custom runner/endpoint，不能与本地、CI当作自动等价三选项。应把它们画在 Engine 左侧，箭头为 `CLI / SDK → Dagger Engine → operation graph`。 |
| “同一执行定义可在不同环境运行” | 仅限定义复用，需保留边界 | Module 可从交互式本地、shell script 或 CI configuration 调用，官方也提供 custom/remote Engine 配置。这里应写“同一 Module 定义可由不同调用上下文触发”，而非“运行结果天然一致”。网络、凭证、CPU/平台、外部服务和 cache 是否预热仍会影响结果与性能。 |

## Function 与 Layer：应在备注中保留的严格解释

### 1. Function 是 Module 的 API 边界；Layer 是 Function 内部 operation 的复用边界

`@func()`（或对应语言的 Function 标记）将成员暴露为 Dagger Module API。Function 接受类型化输入、调用 Dagger API，并返回类型化输出。**Function cache** 缓存的是该 API 调用的返回值；命中时函数体不执行。

Layer cache 不要求也不产生新的 Module API。它服务于 Function 体内的 immutable builder operation，例如 `withDirectory`、`withFile`、`withExec`。同样输入下，可复用该 operation 的中间结果。

因此，只有下列关系可直接进入图：

```text
public @func build(source: Directory) → [Function Call Cache: policy permitting]
  └─ .from("node:21")                       [base image input]
     .withDirectory("/src", source)          [operation result may be layer-cached]
     .withMountedCache("/root/.npm", volume) [mounts tool-data volume]
     .withExec(["npm", "ci"])                [operation result may be layer-cached]
     .withExec(["npm", "run", "build"])     [operation result may be layer-cached]
```

### 2. `ci()` 可以作为组合 Function，但不宜充当本页缓存例子

若作者确实定义 `@func() ci(source)`，它可以构造 build/test 子图或运行多条 command。此时 `ci(source)` 的 Function Call Cache 可以作为外层边界。

但有两个现实限制：

1. 若 `ci()` 在语言 runtime 中直接调用普通 `build()` / `test()` helper，这些 helper 并不是 Dagger Engine 看到的独立 Function Call；
2. 若要表达可独立调用、可并行或可分别复用的 `build()`、`test()`，应将它们画为各自的 Dagger Function/API 调用，或用明确的原生并发代码将它们作为独立结果求值；不能仅靠把 shell command 名称放入 `ci()` 来暗示这一点。

因此页面最稳妥的做法是：上方列出 `build`/`test`/`publish` 三个 API，主缓存图只放大 `build(source)`。这样既能讲清 Function vs Layer，又不对模块作者的封装方式作假设。

### 3. Function Call Cache 的成立条件

可持久缓存的 Function 不等同于“任意 Function 都必然跳过执行”。官方机制是：Engine 以 Module source、arguments、parent object state 作为 Function Call Cache 的输入；同值时才有潜在 hit。Function cache 可配置 TTL、`session` 或 `never`。`never` 常用于部署、通知和其他外部副作用。

图上的条件化标签建议：

```text
Function Call Cache
same module source + args + parent state
policy ≠ never
```

不要在主图写 7 天 TTL：那是当前默认，而非保证保留时间；既有 module 也可能保持与新 module 不同的兼容行为。

## 可直接重画的替代结构

这是一个适用于“左上完整图 + 左下功能列表”的准确布局。它优先解释 Dagger 的机制，不虚构一条完整的 build-test-publish 单一流水线。

```text
┌──────────────────────── 左上：完整技术模型 ────────────────────────┐
│ 调用上下文                                                          │
│ Local shell / CI script / Agent tool                                │
│                │  CLI / SDK                                         │
│                ▼                                                     │
│          Dagger Engine                                               │
│                │  load Module                                        │
│                ▼                                                     │
│  Delivery Module public API                                         │
│  build(source: Directory) ───────────────→ Container / Directory    │
│  test(source: Directory, svc?: Service) ─→ Check / String           │
│  publish(image: Container) [cache=never] ─→ registry / digest       │
│                │                                                     │
│                └─ 放大 build(source) 的一次调用                     │
│                   [Function Call Cache：policy 允许且输入相同]      │
│                    └─ operation graph                               │
│                       base image → add source → install → compile   │
│                       [operation results reused when inputs match]  │
│                                      ▲                               │
│                Cache Volume ── mounted at /root/.npm ───────────────┘
└────────────────────────────────────────────────────────────────────┘

左下：核心能力
  01 可组合的 Module Functions
     将 build/test/publish 暴露为可调用的类型化 API。

  02 显式建模交付依赖
     用 Directory、Container、Service、Secret 表达实际输入和结果。

  03 两级结果复用 + 工具数据加速
     Function 命中跳过调用；未命中时复用 operation；Volume 保存包下载等工具数据。

  04 同一 Module 定义可由本地与 CI 触发
     调用上下文可变；Engine 位置、网络和凭证仍需分别配置。
```

### 图中文字替换建议

| 当前文字 | 替换为 | 原因 |
|---|---|---|
| `FUNCTION CALL · ci(source)` | `PUBLIC FUNCTION · build(source: Directory)` | 选一个真实、单一职责的缓存例子。 |
| `FUNCTION CACHE · 整体结果` | `Function Call Cache（policy 允许时）` | 反映 cache policy 与输入条件。 |
| `FROM / WITH src / RUN install / RUN build / RUN test` 均标 `LAYER CACHE` | `base image` / `add source` / `install dependencies` / `compile`，外加一个虚线框 `operation results reused when inputs match` | 避免主张每格是同构的 Docker Layer；消除 build/test Function 与 command 的同名歧义。 |
| `CACHE VOLUME · 工具数据` 上箭头 | `Cache Volume（e.g. /root/.npm）` 侧向箭头 `mounted into install container` | 明确是挂载的可变目录，不是 operation 的父节点。 |
| `TYPED OBJECTS` → `ci(source)` | `source: Directory` → `build`；旁注 `Other functions may use Service / Secret` | 避免暗示所有类型都会进入同一次调用。 |
| `SAME EXECUTION DEFINITION` 在 operation graph 之后 | `Invocation context` 在 Engine 之前 | 修正调用上下文与执行图的因果方向。 |
| `Local / Existing CI / Remote Engine` | `Local shell` / `CI script` / `configured remote Engine` | 只有 remote Engine 是 Engine placement；CI 是触发上下文。 |

## 已发现的不能进入正式页面的说法

- “所有 Module Function 的调用均会被完整缓存。”——错误；需满足 inputs 与 cache policy 条件，且条目可被清理。
- “Layer 就是 build/test Function。”——错误；Layer 是 Function 内 Dagger API operation 的结果复用。
- “每个 `from`、`withDirectory`、`withExec` 都是一个同构 Docker Layer。”——官方文档不足以支持这一逐格断言。
- “Cache Volume 是第三层内容寻址结果缓存。”——错误；它是可变的工具加速数据，空缓存必须仍正确。
- “Dagger 把本地、CI、Remote Engine 作为图的三个输出端。”——方向错误；它们是调用/Engine 部署语境。
- “同一 Module 定义自动消除环境差异。”——错误；仅说明可复用的定义与容器化执行模型，不取消外部依赖边界。

## 来源、状态与适用范围

| 来源 | 一手性质 | 版本 / 产品状态 | 访问时间 | 本审计的用途 |
|---|---|---|---:|---|
| [Dagger Functions](https://docs.dagger.io/core-concepts/functions/) | 官方当前文档 | 当前产品文档；未标发布日期或稳定性等级 | 2026-08-03 | Function 的 typed input / API operation / typed output 模型。 |
| [Dagger Function Caching](https://docs.dagger.io/extending/function-caching/) | 官方当前文档 | 当前产品文档；明确记录 v0.19.4 以前的兼容边界 | 2026-08-03 | Function cache key、TTL/session/never、Function 与 Layer 的精确区别。 |
| [Dagger Built-In Caching](https://docs.dagger.io/features/caching/) | 官方当前文档 | 当前产品文档；未标发布日期或稳定性等级 | 2026-08-03 | Layer / Volume / Function Call 三类缓存对象的总览。 |
| [Dagger Cache Volumes](https://docs.dagger.io/extending/cache-volumes/) | 官方当前文档 | 当前产品文档；未标发布日期或稳定性等级 | 2026-08-03 | `withMountedCache` 到 `npm install` 的挂载关系、module 默认 scope。 |
| [Dagger Arguments](https://docs.dagger.io/extending/arguments/) | 官方当前文档 | 当前产品文档；未标发布日期或稳定性等级 | 2026-08-03 | `Container` / `Directory` / `File` / `Service` / `Secret` 作为实际参数的类型边界。 |
| [Dagger Getting Started: Concepts](https://docs.dagger.io/getting-started/concepts/) | 官方当前文档 | 当前产品文档；未标发布日期或稳定性等级 | 2026-08-03 | Module 是 Function 集合；CLI/SDK、local/shell/CI 调用方式。 |
| [Dagger Custom Runner](https://docs.dagger.io/next/reference/configuration/custom-runner/) | 官方前瞻文档 | `/next/`；含 experimental 标识，不可包装为默认 GA 行为 | 2026-08-03 | 远程 Dagger Engine 需配置 endpoint；修正“Remote Engine”图示边界。 |
| [dagger/dagger README](https://github.com/dagger/dagger) | 官方开源仓库 | 当前仓库 README；本审计不据此断言最新稳定版本 | 2026-08-03 | CLI 是 Engine client、operation input-keyed / content-addressed 的高层实现定位。 |

### 版本状态更正

审计初稿曾把 `https://docs.dagger.io/0.21.7/...` 当作已发布版本化文档。主 Agent 复核页面后确认，该页面明确标注为 **unreleased documentation**，并指向 `0.21.4` 为 latest。因此 0.21.7 不进入正式页面证明链；V4 的具体 API 链改为当前 [Cache Volumes](https://docs.dagger.io/extending/cache-volumes/) 页面可以逐项核验的 `from(node:21) → withDirectory → withWorkdir → withMountedCache → withExec(npm install)`。

## 最终页面判断

本页可以继续以“**可组合 Function + operation 级增量复用 + 显式工具缓存**”作为 Dagger 的能力主张；V3 不应进入 PPT。V4 已按上述替代结构，仅放大 `build(source)` 的 Function/operation 层级，并以当前 Cache Volumes 示例作为具体 API 链证据。

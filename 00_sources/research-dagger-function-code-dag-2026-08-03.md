---
title: Dagger Function 代码截图与执行 DAG 的官方证据（2026-08-03）
tags:
  - research/cicd
  - company/dagger
  - presentation/slide-07
status: complete
as_of: 2026-08-03
confidence: high
presentation_ready: false
---

# Dagger Function 代码截图与执行 DAG 的官方证据（2026-08-03）

> 仅使用 Dagger 官方文档与 `dagger/dagger` 官方 GitHub Release。所有网页于 2026-08-03 访问；除 Release 外，文档页面均未标发布日期。本文为左半区图文设计的证据输入，不改变页面主张或批准状态。

## 写作提纲

1. 比较适合截图的两组官方 Function 代码；
2. 选择能和 DAG 一一对应、且半页可读的最小截图；
3. 固定代码、DAG、并发与缓存的事实边界；
4. 给出可直接落图的裁剪与文案建议。

## 结论

**推荐使用 5 行的 `Promise.all` Function 截图，不使用完整的 `lint` / `test` Check 截图。** 前者同时出现 `@func()`、类型化的 `source: Directory` 输入，以及两条明确的独立调用；其画出的 DAG 可以逐行对应，半页内仍可读。

官方代码原文（TypeScript SDK v0.21.7，文档行 443–449）：

```ts
@func()
async ci(source: Directory): Promise<void> {
  await Promise.all([
    this.lint(source).sync(),
    this.test(source).sync(),
  ])
}
```

截图来源：[TypeScript SDK | Dagger](https://docs.dagger.io/0.21.7/extending/sdks/typescript/) 的 **Concurrency** 小节，行 439–449。该页面明确说明：因为 API 是惰性的，独立 pipeline 可以用 `Promise.all` 一起等待。文档路径固定在 `0.21.7`；对应官方 Release 是 **v0.21.7，2026-06-17，非 prerelease**。截至访问日，官方仓库最新 Release 为 v0.21.8（2026-07-29）；因此截图应标为“官方 v0.21.7 SDK 示例”，不表述为“最新版本新增能力”。

## 两种截图方案的取舍

| 方案 | 官方来源 | 截图规模 | 与 DAG 的对应 | 结论 |
|---|---|---:|---|---|
| **A. `ci` + `Promise.all`** | [TypeScript SDK v0.21.7](https://docs.dagger.io/0.21.7/extending/sdks/typescript/) 439–449 | 7 行 | `source` 分叉为 `lint`、`test`，再由 `Promise.all` 汇合；逐行可对应 | **采用** |
| B. `lint` / `test` 两个 `@check()` | [TypeScript SDK next](https://docs.dagger.io/next/extending/sdks/typescript/) 522–559 | 38 行 | 共同 `Workspace → source`，两条 Check 分支 | 事实更完整，但半页不可读；且 `/next/` 不是已发布版本合同 |
| C. Cookbook `runAllTests` | [Cookbook / Builds](https://docs.dagger.io/cookbook/builds/) 1289–1338 | 50 行 | `test`、`lint`、`typecheck` 三分支与 `Promise.all` 对应 | 可作讲稿或备份，**不用于截图** |

方案 A 不是“只会写并发”的片段：`source: Directory` 是第一方工程对象类型，`lint(source)` 与 `test(source)` 是 Function 调用；官方 SDK 文档的类型表将 `Directory`、`Container`、`Service` 等列为 Dagger core types。其不足是没有展示两个函数体，故不应在图中杜撰 `npm ci`、镜像或共享环境节点。

## 从截图到 DAG 的逐项映射

建议第二张图只画四个执行节点：

```text
source: Directory
       ├──► lint(source) ──► sync
       └──► test(source) ──► sync
                    \       /
                 Promise.all
```

| 代码 | 图形元素 | 可以说明 |
|---|---|---|
| `source: Directory` | 左侧输入节点 | 任务以 `Directory` 这个类型化工程对象接收输入。 |
| `this.lint(source).sync()` | 上方分支 | `lint` 消费同一输入；`sync()` 要求获得执行结果。 |
| `this.test(source).sync()` | 下方分支 | `test` 消费同一输入；与 `lint` 没有代码级依赖边。 |
| `Promise.all([...])` | 右侧汇合节点 | 调用方显式等待两条独立分支都完成。 |
| Engine（图角注，不单独作为业务节点） | “将请求求值为 DAG”标签 | 官方 API Internals 说明每个 core API request 会被转换为计算结果所需的低层有向无环图。 |

**图下文字：**“同一份 `Directory` 同时驱动 lint 与 test；代码显式给出独立分支，Engine 再按依赖图求值。”

## 并发、惰性与缓存：可说与不可说

### 可说

- **并发：**此截图中的并发来自 `Promise.all` 对两条独立 Function 调用的显式声明；官方说明这会启动独立 pipeline 并一起等待。Dagger Cookbook 也把相同模式明确命名为“execute other Dagger Functions concurrently”。
- **DAG：**官方 [API Internals](https://docs.dagger.io/reference/api/internals/) 说明核心 API 每个 request 会变为为计算结果所需的低层 DAG，并使用缓存与其他优化。
- **惰性：**同一页面说明，只有在请求 leaf/scalar value 时才触发 resolution；未被使用的 `Container` 或 `Directory` 会被跳过。官方称这一模型用于优化和并行 query execution。
- **增量缓存：**官方 [Built-In Caching](https://docs.dagger.io/features/caching/) 与 TypeScript SDK 文档说明，builder operation 由输入内容建键并自动 layer-cache；官方 Overview 说明更改一个文件时，只有受影响的 operation 需要重跑。

### 不可说

- 不要写“Engine 从任意顺序 Function 调用中自动发现 `lint` / `test` 并发”。本例的扇出由 `Promise.all` 明确表达；`dagger check` 的并行执行也仅适用于独立 checks。
- 不要把截图画成 `Build → Test` 与 `Lint` 并行。官方 CI Quickstart 中 `Publish` 是先 `await test(source)`，再 build/publish，属于串行例子。
- 不要把 `Cache Hit` 画成该次运行的已观测结果；正确标签是“输入未变时可复用 / CACHE HIT（条件）”。

## 半页裁剪与落图建议

1. **截图（第一技术点）：**仅裁 [v0.21.7 TypeScript SDK 443–449](https://docs.dagger.io/0.21.7/extending/sdks/typescript/)；保留 `@func()`、`source: Directory`、`Promise.all`、`lint` 和 `test`，去掉网页导航、段落与行号。深色代码底、等宽字体，屏幕截图高度控制在约 7 行代码。
2. **DAG（第二技术点）：**用上文的两分支，而非三分支；保持 `source / lint / test / Promise.all` 与代码字面一致。两个分支同色，汇合点用深色描边；在角落加小标签 `Engine: DAG evaluation`。
3. **缓存图（第三技术点）：**继续采用灰色 `CACHE HIT` 与红色受影响路径；把“只重跑变化部分”改为条件性说明：**“输入变化只使受影响节点重跑；未变节点复用内容寻址缓存。”**

## 来源与状态

| 来源 | 一手性质 | 发布日期 / 状态 | 访问日期 | 支撑内容 |
|---|---|---|---:|---|
| [TypeScript SDK v0.21.7](https://docs.dagger.io/0.21.7/extending/sdks/typescript/) | Dagger 官方文档 | 文档 URL 为 v0.21.7；页面未标发布日期 | 2026-08-03 | 5 行并发 Function、`Directory`、`Promise.all`、自动 layer cache。 |
| [Dagger v0.21.7 Release](https://github.com/dagger/dagger/releases/tag/v0.21.7) | `dagger/dagger` 官方 GitHub Release | 2026-06-17；非 prerelease | 2026-08-03 | 版本化文档对应的已发布 Release。 |
| [Dagger v0.21.8 Release](https://github.com/dagger/dagger/releases/tag/v0.21.8) | `dagger/dagger` 官方 GitHub Release | 2026-07-29；Latest、非 prerelease | 2026-08-03 | 截至日最新 Release；避免把 v0.21.7 截图写成最新。 |
| [Cookbook / Builds](https://docs.dagger.io/cookbook/builds/) | Dagger 官方文档 | 页面未标发布日期 | 2026-08-03 | 三分支 `runAllTests` 的较长原始示例与并发命名。 |
| [API Internals](https://docs.dagger.io/reference/api/internals/) | Dagger 官方文档 | 页面未标发布日期 | 2026-08-03 | lazy evaluation、低层 DAG、缓存与并行优化。 |
| [Built-In Caching](https://docs.dagger.io/features/caching/) | Dagger 官方文档 | 页面未标发布日期 | 2026-08-03 | 输入不变时的缓存复用边界。 |
| [TypeScript SDK next](https://docs.dagger.io/next/extending/sdks/typescript/) | Dagger 官方文档 | `/next/`；未标稳定性/发布日期 | 2026-08-03 | 完整的 `lint` / `test` Check 函数作为备选，不当作 released contract。 |

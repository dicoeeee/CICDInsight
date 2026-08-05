---
asset: dagger-left-region-agent-iterations-v5
status: fact-audited-user-approved-visual-baseline
as_of: 2026-08-03
---

# Dagger 左半区 V5：事实来源与图形边界

## 资产

- SVG：`dagger-left-region-agent-iterations-v5.svg`，900 × 840，SHA-256 `02878b3874b7b44a92e09b468b95880dab8f931c5593903307447bbc61d5b53c`。
- PNG：`dagger-left-region-agent-iterations-v5.png`，1800 × 1680，SHA-256 `c608c62919ecb92b073b209441ba3a763814bc9279acf0c1ad67e827bd0be296`。
- 技术模型继承 V4 的逐对象事实审计：`../../../00_sources/research-dagger-diagram-fact-audit-2026-08-03.md`。

## 相对 V4 的可见变化

- 保留已核验的 Invocation Context、Dagger Engine、Module public API、Function Call Cache、Operation Graph 与 Cache Volume 层级，不改技术模型；
- 删除底部 Gradle 对照条，不再承担产品或构建工具比较任务；
- 在主图下增加“适合 Agent 多轮工程验证”说明，解释 Functions 可反复调用、输入变化会触发重新求值、未受影响的 Operation 可以复用；
- 四项核心能力改写为多轮验证的支撑机制：可反复调用、显式输入与环境、Operation 级增量复用、同一执行定义贯穿 Agent 与 CI。

## 可见主张与当前官方证据

| 可见对象 | 有限主张 | 当前官方来源 |
|---|---|---|
| Invocation Context → CLI/SDK → Engine | Dagger CLI 是 Engine client，可在交互式 shell、脚本或 CI runner 中使用并按需 provision Engine | https://docs.dagger.io/；https://docs.dagger.io/reference/api/internals/ |
| Delivery Module public API | Module 加载后动态扩展 API；Function 接受类型化输入并返回类型化输出 | https://docs.dagger.io/core-concepts/functions/；https://docs.dagger.io/extending/arguments/；https://docs.dagger.io/extending/return-types/ |
| Function Call Cache | 相同 Module code、arguments、parent object state 且缓存策略允许、条目存在时，Function 结果可以复用 | https://docs.dagger.io/extending/function-caching/ |
| Operation Graph | Engine 将请求转成低层 Operation DAG，并以惰性方式计算结果所需节点 | https://docs.dagger.io/reference/api/internals/ |
| Operation 级复用 | Function 未命中时，Function 内部的 `withDirectory`、`withExec` 等操作仍可命中缓存 | https://docs.dagger.io/extending/function-caching/ |
| Cache Volume | `withMountedCache` 可将跨 Session 的工具数据挂载进 Container | https://docs.dagger.io/extending/cache-volumes/；https://docs.dagger.io/features/caching/ |

## 表述边界

- “适合多轮工程验证”是基于可重复调用、显式输入和增量复用机制的分析判断，不是厂商性能指标；
- 输入变化会使依赖该输入的节点及其下游重新计算，不宣称任意代码变化都只重跑编译和测试；缓存收益取决于 Function 如何拆分输入与操作；
- 同一 Module 可供 Agent、本地与 CI 调用，不代表网络、凭证、外部服务或缓存状态自动一致；
- Dagger 原生 LLM、Env 和 Module-as-MCP 能力不进入本图主张。

## 评审状态

用户以“同意左侧v5”明确批准该 PNG 作为 Dagger 左区视觉基线。该批准不覆盖 Container Use 右区或整页视觉。

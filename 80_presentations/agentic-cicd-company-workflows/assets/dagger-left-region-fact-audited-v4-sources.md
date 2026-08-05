---
asset: dagger-left-region-fact-audited-v4
status: fact-audited-proposed-for-visual-review
as_of: 2026-08-03
---

# Dagger 左半区 V4：事实来源与图形边界

## 资产

- SVG：`dagger-left-region-fact-audited-v4.svg`，900 × 840，SHA-256 `ac227555080f86ddaacffa1ca9bbe31aec1d2fecd3e1afdb1212f1de47e31918`。
- PNG：`dagger-left-region-fact-audited-v4.png`，1800 × 1680，SHA-256 `37a569f03c793b3affcc0128ade6a48a308038783642f5feb5ac328a83605003`。
- 完整逐对象审计：`../../../00_sources/research-dagger-diagram-fact-audit-2026-08-03.md`。

## 可见主张与当前官方证据

| 可见对象 | 有限主张 | 当前官方来源 |
|---|---|---|
| Invocation Context → CLI/SDK → Engine | Dagger CLI 是 Engine client，可在交互式 shell、脚本或 CI runner 中使用并按需 provision Engine | https://docs.dagger.io/；https://docs.dagger.io/reference/api/internals/ |
| Delivery Module public API | Module 加载后动态扩展 API；Function 接受类型化输入并返回类型化输出 | https://docs.dagger.io/；https://docs.dagger.io/core-concepts/functions/；https://docs.dagger.io/extending/arguments/；https://docs.dagger.io/extending/return-types/ |
| `build(source: Directory) → Container` | 当前 Cache Volumes 页面提供该 Function 形态及 Node 容器 API 链 | https://docs.dagger.io/extending/cache-volumes/ |
| Function Call Cache | 缓存 Module Function 返回值；相同 Module code、arguments、parent object state 且 policy 允许、条目存在时可能命中 | https://docs.dagger.io/extending/function-caching/ |
| Operation Graph | Engine 将请求转成低层 operation DAG，并以惰性方式计算结果所需节点 | https://docs.dagger.io/reference/api/internals/ |
| 红线 operation | 当前 Function Caching 文档直接举例 `withDirectory`、`withExec` 等内部操作仍可命中 Layer Cache | https://docs.dagger.io/extending/function-caching/ |
| Cache Volume | 当前示例使用 `withMountedCache("/root/.npm", cacheVolume("node-21"))` 将跨 Session 工具数据挂载进 Container | https://docs.dagger.io/extending/cache-volumes/；https://docs.dagger.io/features/caching/ |

## 本次主动排除

- 不使用 `0.21.7` 作为正式证据：该页面明确标记为 unreleased，并指向 `0.21.4` 为 latest。
- 不把 `build()`、`test()` Module Functions 画成 Layer；内层只展示具体 Container API operations。
- 不把 base image、`withWorkdir`、`withMountedCache` 与 `withExec` 断言为完全同构的 Layer Cache 单元。
- 不把 Cache Volume 画成 Function / Layer 之下的第三层内容寻址缓存。
- 不让一个 Function 看起来必然返回 Artifact、OCI Image、Test Result 三种异构输出。
- 不把 Local、CI、Remote Engine 画成 operation graph 的三个输出端；主图仅保留当前稳定证据支持的本地/CI runtime。
- 不宣称相同 Module 定义自动保证网络、凭证、外部服务或缓存状态一致。

## 评审状态

事实审计已完成；图仍是视觉提案。用户批准 VIS-L4 前，不进入正式 PPTX。

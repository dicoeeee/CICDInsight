---
title: Model Context Protocol 专题
aliases:
  - MCP Deep Dive
  - MCP 协议专题
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/mcp
status: complete
as_of: 2026-07-15
topic_id: mcp-protocol
topic_type: technology
stages:
  - cross-stage
confidence: high
presentation_ready: true
refresh_after: 2026-07-29
---

# Model Context Protocol 专题

> [!abstract] 当前判断
> MCP 是 Agent 与外部能力之间的互操作协议，不是执行引擎、身份系统或安全边界。它最有价值的场景是多个 Agent 客户端共享远程工具、资源和集中治理；若只是一个 Harness 调用本地成熟 CLI，MCP 可能增加不必要的适配与上下文成本。

## 版本口径

- 截至 2026-07-15，当前正式发布规范为 [2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25/architecture)。
- 目标版本名为 `2026-07-28` 的 [Release Candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) 已于 2026-05-21 公开；最终规范计划在 2026-07-28 发布，晚于本报告观察日。
- 本专题把前者当作当前事实，把后者单独标为“候选方向”，不混写。

## 关键结论

1. 当前 MCP 通过 JSON-RPC、能力协商及 Tools/Resources/Prompts 提供统一的发现和调用契约。
2. Streamable HTTP 与 OAuth 框架推动 MCP 从本地开发工具走向远程、多租户和企业控制面。
3. Tool 数量会带来上下文税、选择错误和授权面膨胀；Toolset 收敛比 Server 数量更重要。
4. MCP 可以替代客户端对 CLI/API 的重复适配，却不会替代后台真实能力、业务授权和外部验证。
5. 2026-07 候选规范转向无状态核心、扩展框架和更标准的 HTTP 基础设施，说明早期会话式协议正在为企业规模化重构；其最终内容仍需正式发布后复核。
6. Anthropic Tool Search/Claude Code 已实现 MCP Tool 渐进式加载，但 `defer_loading` 与 `tool_reference` 属于 Anthropic Host/API 层，不是 MCP Core；上下文可见性也不能替代 Tool 授权。

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/mcp-protocol/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/mcp-protocol/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/mcp-protocol/20_evidence-map|证据矩阵]] |
| Case Map | 完成 | [[50_deepdives/mcp-protocol/30_case-map|案例比较]] |
| Labs | 已设计、未执行 | [[50_deepdives/mcp-protocol/40_labs/README|实验计划]] |
| Findings | 完成 | [[50_deepdives/mcp-protocol/50_findings|分析发现]] |
| Playbook | 完成 | [[50_deepdives/mcp-protocol/60_playbook|企业 Playbook]] |
| Report | 完成 | [[50_deepdives/mcp-protocol/90_report|专题报告]] |
| Research Notes | 完成 | [[50_deepdives/mcp-protocol/research-evidence|一手资料笔记]] |
| Progressive Loading | 完成 | [[50_deepdives/mcp-protocol/progressive-loading-research|Anthropic 渐进式加载证据]] |

## 与其他专题的边界

- CLI 的进程契约、结构化输出和 Runner 用法：[[50_deepdives/cli-agent-interface/README|CLI 专题]]。
- CLI-Anything 生成接口的方法和成熟度：[[50_deepdives/cli-anything/README|CLI-Anything 专题]]。
- 仅在选型层做交叉比较：[[50_deepdives/cli-vs-mcp-decision-guide|CLI 与 MCP 决策指南]]。
- 业界实践者倾向 CLI 的具体论点和证据：[[50_deepdives/cli-first-practitioner-arguments/README|CLI-First 实践者论点]]。

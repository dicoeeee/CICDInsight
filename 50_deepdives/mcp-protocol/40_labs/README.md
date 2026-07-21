---
title: MCP 专题实验计划
tags:
  - research/agentic-cicd
  - research/lab
  - tool/mcp
status: proposed
as_of: 2026-07-15
---

# MCP 专题实验计划

本轮完成实验设计，未把未执行结果混入报告。

| 实验 | 变量 | 指标 | 目的 |
|---|---|---|---|
| M1：Tool Loading | 10/50/200/1000 Tool；全量预载 vs Anthropic Tool Search vs Gateway 任务裁剪 | Definition Token、Recall@5、选择准确率、搜索/调用延迟、Cache Hit | 验证渐进式加载的收益边界 |
| M2：Direct CLI vs MCP | 同一底层 CLI | 首次成功、接入工时、延迟、可审计性 | 衡量协议增量价值 |
| M3：stdio vs Remote HTTP | 同一 Server | 启动、并发、升级、身份、故障恢复 | 验证部署模式 |
| M4：恶意 Server/Description | 注入、Schema 漂移、返回污染 | 拦截率、越权率、审计 | 验证 Host 信任边界 |
| M5：Scope/Allowlist | 不同任务身份与环境 | 最小权限、拒绝质量、误配置 | 验证企业控制面 |
| M6：Search Poisoning | 相似名称、恶意 Description、动态 Schema | 错误检索、危险 Tool 命中、Policy 拦截 | 验证 Catalog 检索的信任边界 |

执行时必须固定客户端、Server、模型、Tool Search Variant 和规范版本，并将搜索 Query、`tool_reference`、展开后的 Tool Schema、权限、原始调用与产物保存为独立证据。

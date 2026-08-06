---
title: CLI-First 实践者论点专题研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
---

# CLI-First 实践者论点专题研究边界

## 决策目标

收集和整理 2025H2—2026 年间，具有实际 Agent 构建经验的工程师和产品团队关于"为何倾向 CLI 而非 MCP"的具体论点、数据和案例，为企业选型提供一手实践证据。

## 纳入范围

- 具名实践者（博客、演讲、社交媒体）的公开论点，包含具体数据或案例；
- Token 成本、上下文效率的量化或定性比较；
- 安全分析：MCP 的 Tool Description 攻击面、CLI 的 OS 级安全优势；
- 真实工作流描述：哪些工具用 CLI、哪些用 MCP、为什么；
- 混合架构模式：CLI-first + MCP 补充的具体实现；
- 2024-11 至 2026-08 的一手来源。

## 非目标

- 不重新分析 CLI 或 MCP 的技术架构（已有专题覆盖）；
- 不把所有 Agent 工具链讨论简化为 CLI vs MCP 二元对立；
- 不把个别实践者的偏好外推为行业平均结论；
- 不忽略 MCP 的结构性优势（OAuth、多客户端、远程服务）；
- 不做没有一手证据的模型记忆推断。

## 术语

- **CLI-first：** 在 Agent 工具链中优先使用 CLI 工具，MCP 仅用于特定场景的架构偏好。
- **上下文成本：** 将工具 Schema 加载到 LLM 上下文所消耗的 token 数量。
- **Tool Poisoning：** 在 MCP Tool Description 中隐藏恶意指令的攻击方式。
- **Rug Pull：** MCP Tool 在获批后悄悄变更定义的攻击方式。
- **Lethal Trifecta：** 私有数据访问 + 不可信内容暴露 + 外部通信能力的危险组合。

## 验收标准

- 每个关键论点能回链到具名实践者的一手来源；
- 包含至少三个独立的 token 成本数据点；
- 安全分析引用至少两个独立研究或实战案例；
- 明确标注观点的适用边界和不能外推的条件；
- 区分"一手实践经验"和"二次评论"。

---
title: CLI 专题研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/cli
status: complete
as_of: 2026-07-15
---

# CLI 专题研究边界

## 决策目标

回答企业在 Agentic CI/CD 中何时应直接复用或建设 CLI、什么样的 CLI 才能成为可信机器接口，以及 CLI 与 API、SDK、GUI、MCP 之间的真实替代边界。

## 纳入范围

- 被 Agent 或自动化调用的业务 CLI、DevOps CLI 与 Agent CLI/Harness；
- 结构化输入输出、错误语义、状态、幂等、版本、身份、沙箱和审计；
- 代码检查至发布后恢复的跨阶段使用；
- 2025 年下半年以后、重点 2026 年的产品和工程趋势。

## 非目标

- 不把所有终端 Agent 产品做功能横评；
- 不深入 MCP 协议字段和 SDK 实现；
- 不把“能执行命令”视为已经取得生产授权；
- 不研究传统 CI/CD CLI 的历史演进。

## 术语

- **CLI：** 通过进程调用和标准输入输出暴露能力的命令行接口。
- **Agent-ready CLI：** 具有机器可发现、可解析、可重放、可约束和可验证契约的 CLI。
- **Agent CLI / Harness：** 运行在终端中的 Agent 主体，例如 Claude Code、Codex CLI、Copilot CLI 或 OpenCode；它与被调用的业务 CLI 不是同一层。
- **执行契约：** 输入、输出、退出状态、副作用、权限、版本和可观测性的联合约定。

## 验收标准

- 能给出 Agent-ready CLI 的可操作检查表；
- 能逐条件说明 CLI 何时可替代 MCP、何时不能；
- 至少覆盖四类真实 CLI 模式和四个 CI/CD 运行架构；
- 重要判断链接一手文档，并明确行业观点与事实的区别。

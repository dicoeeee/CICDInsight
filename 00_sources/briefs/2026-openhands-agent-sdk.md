---
title: OpenHands Software Agent SDK
source_id: openhands-agent-sdk-2025-11
organization: OpenHands
source_type: primary-research
published: 2025-11-01
verified: 2026-07-14
availability: open-source
confidence: medium
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - software-engineering-agent
  - agent-runtime-orchestration
  - evaluation
company_topics:
  - OpenHands
autonomy_levels:
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
  - open-source
---

# OpenHands Software Agent SDK

## 来源

- 标题：The OpenHands Software Agent SDK: A Composable and Extensible Foundation for Production Agents
- 组织或项目：OpenHands
- 发布日期：2025-11
- 链接：[arXiv:2511.03690](https://arxiv.org/abs/2511.03690)、[OpenHands Repository](https://github.com/OpenHands/OpenHands)
- 来源类型：原始论文与开源仓库
- 能力状态：开源 Agent SDK；企业目录采用不同许可证

## 一句话结论

OpenHands 展示了通用软件工程 Agent 的生产化基础，包括工具、运行时、可组合组件和远程执行，但其与 CI/CD 的关系主要是作为可嵌入执行 Agent，而非完整交付控制面。

## 可核验事实

- OpenHands Agent 能操作代码、终端和开发环境。
- SDK 强调可组合、可扩展以及从流行 OpenHands 框架重新设计 Agent 组件。
- GitHub 仓库在 2026 年保持高关注度和活跃更新，属于高 Star 软件工程 Agent 项目。
- 开源主体使用 MIT License，Enterprise 目录有独立许可。

## CI/CD 相关性

- 涉及阶段：测试执行、构建验证和代码修复。
- 工具类别：通用软件工程 Agent、Runtime 和 SDK。
- 自主等级：L2—L3。
- 涉及角色：Agent 平台研发、开发者和 CI 集成人员。

## 对洞察的价值

OpenHands 可作为平台原生 Agent 的对照：它证明通用 Agent 执行能力快速成熟，但 CI/CD 上下文、权限、门禁和审计仍需外部平台补齐。

## 限制与待验证项

- 高 Star 不等同于 CI/CD 生产采用。
- 本来源不能证明 OpenHands 已形成面向制品、发布或生产运维的完整闭环。

## 可引用判断

- 通用软件工程 Agent 提供执行能力，交付平台仍需负责上下文、权限、验证和审计。


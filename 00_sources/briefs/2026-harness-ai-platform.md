---
title: Harness AI Platform 2026 能力总览
source_id: harness-ai-platform-2026-07-15
organization: Harness
source_type: official-docs
published: 2026-07-15
verified: 2026-07-16
availability: mixed
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
  - artifact-supply-chain
  - infrastructure-deployment
  - release
  - post-release
tool_categories:
  - ci-pipeline
  - agent-runtime-orchestration
  - mcp
  - cli
  - governance
company_topics:
  - Harness
autonomy_levels:
  - L0
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness AI Platform 2026 能力总览

## 来源

- 标题：Overview of Harness AI
- 组织或项目：Harness
- 更新日期：2026-07-15
- 链接：[Harness Developer Hub](https://developer.harness.io/docs/platform/harness-ai/overview/)
- 来源类型：官方产品总览
- 能力状态：多数条目列为 GA；Unified CLI 3.0 为 Public Beta；账户可获得性仍需按模块核验

## 一句话结论

Harness 已把 AI/Agent 能力从 Pipeline Copilot 扩展到代码、CI、CD、GitOps、IaC、测试、安全、发布、SRE、成本和研发度量，并试图让 Pipeline 成为统一执行与治理控制面。

## 可核验事实

- 总览将 Harness Agents、DevOps Agent、MCP、PR Summary/Review、CI/CD Troubleshooting、GitOps Operations、STO/SCS Remediation、FME Release Agent、AI Scribe 和 AI Test Copilot 等列为 Generally Available。
- Unified CLI 3.0 为 Public Beta，定位为供人和 Agent 共同使用的统一 Harness 命令行入口。
- DevOps Agent 能创建和管理 Pipeline、Stage、Step、Service、Environment、Connector 和 Secret，并生成 OPA Policy。
- MCP 在该页列出 11 个 Tool 和 139 个 Resource Type；其开源仓库更新更快，数量会发生漂移。
- DevOps Agent、Support Agent、OPA Agent 和 Error Analyzer 使用 Harness 管理的模型；Worker Agent 的模型连接方式需另查产品文档。

## CI/CD 相关性

- 涉及阶段：八阶段全覆盖。
- 工具类别：Agent 控制面、Pipeline、MCP、CLI、专业 Agent 与治理。
- 自主等级：产品组合覆盖 L0—L3；GA 不等于关键生产 L4。
- 涉及角色：开发者、平台工程、QA、安全、SRE、发布和工程管理者。

## 对洞察的价值

该总览最适合作为 Harness 公司产品地图和状态索引，证明 Harness 的竞争单位已从单一 CI/CD 模块扩大为跨软件交付生命周期的平台组合。

## 限制与待验证项

- 总览中的 GA 是 Capability Status，不等于任意账户、Region、SKU 无条件可用；AI Test、AI Scribe、Hosted MCP 和部分 Worker 权限仍可能需要销售、Support 或 Feature Flag。
- 同页模型表与 FAQ 存在版本不一致，说明快速迭代带来文档同步风险。
- 功能广度不能证明跨客户效果、单位成功成本或生产自治成熟度。

## 可引用判断

- 对 Harness 的评估必须把“官方功能状态、账户实际可获得性、客户生产就绪度”分成三栏，而不能用一个 GA 标签概括。

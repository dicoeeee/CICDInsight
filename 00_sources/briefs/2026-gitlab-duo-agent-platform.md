---
title: GitLab Duo Agent Platform
source_id: gitlab-duo-agent-platform-docs-2026
organization: GitLab
source_type: official-docs
published: null
verified: 2026-07-28
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
tool_categories:
  - source-control
  - ci-pipeline
  - security
  - agent-runtime-orchestration
  - governance
company_topics:
  - GitLab
autonomy_levels:
  - L0
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitLab Duo Agent Platform

## 来源

- 标题：GitLab Duo Agent Platform
- 组织或项目：GitLab
- 链接：[GitLab Docs](https://docs.gitlab.com/user/duo_agent_platform/)
- 来源类型：官方产品文档
- 能力状态：GitLab 18.8 起 GA；部分治理与自定义能力仍为 Beta 或 Experiment

## 一句话结论

GitLab 正把统一 DevSecOps 数据面升级为 Agent 编排与治理控制面，并用 Agent、Flow、MCP、目录、审批和审计覆盖从代码到安全修复的多种任务。

## 可核验事实

- Agent Platform 在 GitLab 18.8 GA，可用于 GitLab.com、Self-Managed 和 Dedicated。
- Foundational Flows 包括 Fix CI/CD Pipeline、Convert to GitLab CI/CD、Code Review、SAST Vulnerability Resolution 和 SAST False Positive Detection。
- Agent 可访问代码、Issue、MR、CI/CD Job 日志和漏洞信息。
- Custom Flows、工具级审批、AI 审计报告、外部 MCP Server 等能力按版本和套餐处于不同成熟状态。
- 支持自托管 Agent Platform 和自托管模型，服务受监管行业的数据与模型控制需求。
- Custom Flows 于 GitLab 19.2（2026-07-16）GA；Issue、MR、评论提及和 Pipeline Event 可成为 Flow 入口。
- Flow 使用触发人与服务账号的 Composite Identity，实际权限取两者交集，并保留发起人/服务账号归因。

## CI/CD 相关性

- 涉及阶段：代码评审、安全、门禁和 CI 故障处理。
- 工具类别：一体化 DevSecOps、Agent 编排、AI 资产目录和治理。
- 自主等级：L0—L3。
- 涉及角色：开发、平台、CI、安全、合规和 GitLab 管理员。

## 对洞察的价值

GitLab 是“全生命周期上下文就是 Agent 平台壁垒”的典型路线，也提供了自托管、BYOM、工具审批和统一审计等企业治理样本。

## 限制与待验证项

- GA 指平台总体可用，不代表所有列出的 Agent 与治理功能均已 GA。
- 需要按具体 GitLab 版本核验客户可用能力和 Credits 成本。
- AI Audit Event Report 等治理能力仍可能处于 Beta/Experiment，不能宣称 Prompt 与全部 Tool 往返已完整审计。

## 可引用判断

- 当代码、流水线、安全发现和协作数据已经集中在同一平台时，Agent 平台的竞争点会从模型能力转向上下文、治理和工作流闭环。

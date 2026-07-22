---
title: Harness Worker Agents
source_id: harness-worker-agents-2026-07-08
organization: Harness
source_type: official-docs
published: 2026-07-09
verified: 2026-07-22
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
  - deployment
  - release
tool_categories:
  - ci-pipeline
  - security
  - infrastructure-deployment
  - agent-runtime-orchestration
  - governance
company_topics:
  - Harness
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness Worker Agents

## 来源

- 标题：Worker Agents
- 组织或项目：Harness
- 发布或更新日期：2026-07-20；2026-07-22 复核
- 链接：[Harness Developer Hub](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)
- 来源类型：官方产品文档
- 能力状态：平台总览列为 GA；账户入口、细粒度权限和部分功能仍可能需要管理员、Support 或 Feature Flag

## 一句话结论

Harness 把 Agent 做成流水线中的可复用治理步骤，可插入 CI、CD、IaC、STO、SCS 或自定义阶段，而不是只作为流水线外部的聊天助手。

## 可核验事实

- Worker Agent 由 Instructions、Model Connector 和可选 MCP Servers 组成。
- 可作为步骤加入 CI、CD、IaCM、安全测试和软件供应链阶段。
- Agent 通过 Instructions、Harness Expressions、Inputs、Workspace/文件、环境变量和显式 MCP Connector 获取上下文；当前文档不证明任意 Custom Worker 自动装载全量 Knowledge Graph。
- 可使用 Harness Managed Anthropic/OpenAI Connector，也可自带 Anthropic、Bedrock Anthropic 或 OpenAI Connector。
- CI、STO、SCS、IaCM 可使用 Harness Cloud Runtime；CD/Custom Stage 需要 Containerized Step Group；也可通过 Delegate 在客户 Kubernetes 中运行。
- Marketplace 分为 Managed、Certified 和 Community；用途覆盖 Review、AutoFix、Coverage、Manifest、Flag Cleanup、Zero-day、IaCM 和 Library Upgrade。
- 7 月 20 日 Worker 页面称存在 Principal 时，Harness 资源权限是触发人 RBAC 与声明 Grant 的交集；7 月 15 日 Agent permissions 页面又称 Token 独立于 Pipeline Author，当前文档存在直接冲突。
- 未声明 `permissions` block 时会注入文档化默认只读权限；声明 block 后默认项不再合并，Managed LLM Connector 还需显式 `ai_llm_gateway: access`。
- Permission 位于 Stage 或 Containerized Step Group，Scoped Token 会注入组内每个 Step，而不只进入 Agent 内核。
- 第三方 Tool Intersection 与逐次 Attribution 主要由官方工程博客披露，产品文档缺少同粒度配置/导出说明。

## CI/CD 相关性

- 涉及阶段：几乎覆盖编码完成后的全部交付阶段。
- 工具类别：流水线、Agent 运行时、模型与工具连接、治理。
- 自主等级：L1—L3；实际级别取决于步骤权限和工具配置。
- 涉及角色：平台工程师、DevOps、安全与合规团队、流水线维护者。

## 对洞察的价值

它代表“Agent 成为流水线一等步骤”的平台路线，并把提示词、模型、工具和权限组合为可复用的企业资产。

## 限制与待验证项

- 文档没有提供跨客户的任务成功率或稳定性数据。
- 细粒度 Permission/Token Injection 仍需确认 `HARNESS_TOKEN_INJECT` 是否在目标账户开启。
- 7 月 15 日与 7 月 20 日权限文档在 Principal、默认权限和资源范围上冲突，必须在目标 Cluster 验证 Effective Permission、Token Subject 与 Audit Principal。
- Webhook、Schedule、Artifact、Manifest 等 Trigger 发起的 Run 当前不能继承某个触发人的 scoped token；事件驱动高风险动作必须单独评估身份与审批。
- Scoped Token 对 Stage/Step Group 内所有 Step 生效，混合脚本/插件会扩大 Blast Radius，应拆最小独立组。
- Managed LLM 在 2026-08 后将单独计费，试点成本不能按当前包含窗口外推。
- Marketplace 等级不等于客户自己的生产安全认证。

## 可引用判断

- Agent 平台化的一项标志，是能够像普通流水线步骤一样被复用、授权、审计和组合。
- 通用 Agent 的生产边界由 Pipeline、短期权限子集、Tool Allowlist 和外部 Oracle 共同决定，而不由 Prompt 本身决定。

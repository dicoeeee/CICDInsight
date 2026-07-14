---
title: Harness AI DevOps Agent
source_id: harness-ai-devops-agent-2026-06-02
organization: Harness
source_type: official-docs
published: 2026-06-02
verified: 2026-07-14
availability: ga
confidence: high
geography:
  - global
lifecycle_stages:
  - security-compliance
  - testing-gates
  - build
  - infrastructure-deployment
  - release
tool_categories:
  - ci-pipeline
  - infrastructure-deployment
  - policy-as-code
  - agent-runtime-orchestration
company_topics:
  - Harness
autonomy_levels:
  - L0
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Harness AI DevOps Agent

## 来源

- 标题：Harness AI DevOps Agent
- 组织或项目：Harness
- 更新日期：2026-06-02
- 链接：[Harness Developer Hub](https://developer.harness.io/docs/platform/harness-ai/devops-agent/)
- 来源类型：官方产品文档
- 能力状态：企业版包含；由 Harness 管理模型，不支持 BYOM

## 一句话结论

Harness DevOps Agent 让用户用自然语言创建和修改 Pipeline、IaC Pipeline 与 OPA Rego Policy，体现流水线配置从手工 UI/YAML 操作转向意图驱动生成。

## 可核验事实

- 可创建或修改 Pipeline 的 Step、Stage 和整体结构。
- 可创建 IaCM Pipeline，管理 Terraform 等基础设施工作流。
- 可生成并集成 OPA Rego Policy。
- 2026 年架构从五个 Subagent 合并为单个统一 DevOps Agent，官方称改善上下文保持和速度。
- 官方称已用 50 Stage Pipeline 验证复杂流水线生成能力。

## CI/CD 相关性

- 涉及阶段：流水线设计、门禁策略、基础设施和部署发布。
- 工具类别：Pipeline Copilot、IaC、Policy as Code。
- 自主等级：L0—L2。
- 涉及角色：DevOps、平台工程师、流水线设计者和合规人员。

## 对洞察的价值

它展示 Agent 对“构建交付流程本身”的影响，而不只是处理流程中的某一次任务。

## 限制与待验证项

- 50 Stage 验证为厂商自测，未披露复杂度和成功标准。
- 当前不支持 BYOM，模型治理选择受到平台限制。

## 可引用判断

- Agent 不只执行流水线，还开始生成和修改流水线及其 Policy as Code，这会改变平台工程师的工作重心。


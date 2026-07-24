---
title: Slide 02 - Harness CI Agent Context and Knowledge
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
  - company/harness
status: draft
slide: 2
content_status: single-slide-ready
visual_status: editable-pptx-ready
primary_deep_dive: "[[50_deepdives/harness-company/README]]"
validation: "[[50_deepdives/harness-company/architecture-validation-2026-07-23]]"
workflow_orchestration: "[[50_deepdives/harness-company/intelligent-workflow-orchestration-2026-07-24]]"
as_of: 2026-07-24
---

# Harness CI：将会推理、能行动、可治理的 Agent 嵌入交付 Pipeline

> [!abstract] 页面唯一主张
> Harness Agent 的差异化来自三个条件同时成立：DevOps Agent、Worker Agents 与专项 Agent 分工协作；Knowledge Graph、HQL、Pipeline Context 和 Tool 为判断与行动提供真实交付语境；身份、Agent Scope、Pipeline Gate 与 Runtime 边界逐层约束权限和执行。

## 单页定稿

![[80_presentations/agentic-cicd-company-workflows/assets/harness-agentic-context-slide.png|1200]]

### 可编辑成品

- [[outputs/harness-agentic-control-plane-editable.pptx|Harness Agentic Software Delivery 可编辑 PowerPoint]]

### 右侧技术说明

1. **三类智能体｜平台、Pipeline 与领域闭环。** 平台操作智能体将用户意图转成 Harness 平台配置；Worker 智能体作为 Agent Step 推理并行动；领域专用智能体嵌入 Release、Test、SRE 等作业闭环。
2. **两类上下文｜平台知识 + 本次执行。** Knowledge Graph 连接跨模块对象、关系、状态与历史，HQL 查询已建模的交付知识；Pipeline Context 注入本次运行的输入、变量、Step 输出与状态。
3. **工具行动｜把判断转成受控操作。** MCP、Connector 与 API 执行查询、修改和修复；凭据、RBAC 与 Scoped Token 限定可执行边界，工具可调用不等于获得授权。
4. **智能工作流编排｜让行动可控、可恢复。** DevOps Agent 生成或优化流程，Pipeline 保持用户可见，并用 DAG、Approval、OPA、Retry、Failure Strategy 与 Rollback 治理 Agent Step 和确定性 Step；Dynamic Pipeline / Stage 只是受控扩展，不等于总控 Agent。

### 与左图的映射

- 右侧 `01` 对应左图 `03`，解释三类 Agent 的职责边界；
- 右侧 `02 / 03` 对应左图 `04`，分别解释上下文输入和受控行动；
- 右侧 `04` 对应左图 `05`，解释智能生成如何进入确定性 Pipeline；
- 身份、凭据、OPA、审批与 Runtime 遥测分别落在其实际生效的层中，不另造横切层。

### 页面启示

> Harness 的关键不是在 Pipeline 中放一个 Agent，而是让多类 Agent 基于真实交付上下文协作，并由身份、工具、流程和 Runtime 边界共同约束行动。

### 页面边界

- 已建模的 Read/Query/Analyze 优先使用 Knowledge Graph + HQL；外部长尾系统和 Create/Update/Execute 行动主要通过 MCP、Connector 与 API 完成。
- Knowledge Graph 不是所有 Custom Worker 或专项 Agent 默认拥有的上下文总线；具体 Agent 仍需通过定义、输入和工具显式接入。
- Pipeline Context 是运行范围内的输入、变量、状态和输出集合，不等同于持久化组织知识。
- Scoped Token 只适用于 Worker Agent 路径；专项 Agent 的 Credential、模型、权限与 Runtime 需要按模块分别核验。
- 六层框架是基于 Harness 官方产品组合形成的分析视图，不是 Harness 官方命名的参考架构。
- Audit 表示可追责能力，不等于所有 Pipeline Execution Event 已默认开启。

### 主要来源

- [Deterministic by Design: How Harness Grounds AI Agents in Structured Data](https://www.harness.io/blog/why-harness-ai-uses-knowledge-graph)
- [Harness Query Language](https://developer.harness.io/3k-docs/platform/getting-started/dashboards/hql/)
- [Harness DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/)
- [Worker Agents in Pipelines](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/)
- [Harness Variables and Expressions](https://developer.harness.io/docs/platform/variables-and-expressions/harness-variables/)
- [RBAC in Harness](https://developer.harness.io/docs/platform/role-based-access-control/rbac-in-harness/)
- [Harness Governance Overview](https://developer.harness.io/docs/platform/governance/policy-as-code/harness-governance-overview/)
- [Harness Secrets Overview](https://developer.harness.io/3k-docs/platform/getting-started/secrets/)
- [[50_deepdives/harness-company/architecture-validation-2026-07-23|Harness 2026 产品组合与架构核验]]
- [[50_deepdives/harness-company/architecture-content-alignment-2026-07-23|Harness 架构图内容对齐核验]]
- [[50_deepdives/harness-company/intelligent-workflow-orchestration-2026-07-24|Harness Intelligent Workflow Orchestration 核验与洞察]]
- [[50_deepdives/harness-company/agentic-ci-title-thesis-2026-07-23|Harness Agentic CI 单页主张与标题核验]]
- [[50_deepdives/harness-company/90_report|Harness 公司深度洞察报告]]

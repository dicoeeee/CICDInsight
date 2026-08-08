---
title: Harness Inc. Worker Agents 产品功能详章
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Harness Inc. Worker Agents 产品功能详章

## 产品状态与入口

- Harness Inc. Worker Agent 是 Harness AI 在 Pipeline 中运行 Agent 的产品能力，入口包括 Account/Organization/Project Agent Catalog 与 Pipeline Worker Agent Step。[Worker Agent configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)
- 官方页面持续更新，但未为所有 Worker Agent 子能力提供统一 GA/Preview/Beta 标签。本章记录为“官方产品文档能力，单项生命周期未统一声明”。
- 使用前需要启用相应 Harness AI 能力、配置 Model Connector，并具有 Agent 与 Pipeline 的相应权限。

## Agent Definition 与配置对象

| 配置对象 | 官方公开字段 |
|---|---|
| Agent Definition | Name、Description、Instructions、Model Connector、MCP Servers、Inputs、Environment Variables、Maximum Reasoning Turns |
| Agent Version | Pipeline 通过 Agent 名称/版本引用目录中的定义 |
| Agent Settings | Pipeline Run 时覆盖或注入特定参数 |
| Typed Inputs | String、Number、Secret 或其他定义类型，供 Pipeline 传值 |
| Trigger | Webhook、Artifact、Manifest、Schedule 等事件入口 |
| Output Variables | Agent 产生的结构化值，供后续 Step、Condition、Approval 或 Notification 使用 |

来源：[Worker Agent configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)

## 输入与 Pipeline 上下文

- Agent Definition 可声明 Typed Inputs；Pipeline-specific 值通过 Inputs 或 Agent Settings 注入，不需要复制一份 Agent 定义。
- Pipeline 可传入 Harness Expression、前序 Step Output、Manifest/Artifact 信息、用户输入或 Secret Reference。
- Instructions 与最大 Reasoning Turns 控制 Agent 的任务说明和迭代上限；Model Connector 决定模型接入。
- Environment Variables 和 Secret 的实际可见性受 Harness Scope、Connector 和 Runtime 隔离控制。

## Tool、MCP 与执行

- Agent Definition 可声明 MCP Server 和允许的工具；Runtime 根据 Agent 配置与 Connector 允许范围决定可调用工具集。
- Worker Agent 作为 Pipeline Step 执行，可以与 Build、Security、Approval、Deployment 等确定性 Step 串接。
- 多个 Worker Agent 可按 Pipeline 顺序运行。官方示例展示先生成 Specification、再生成 Plan、再生成 Code 的三个 Agent Step；这是 Pipeline 编排，不是单一 Agent 内部的自动团队调度。[Example agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/)

## 触发与自动化

- Agent-enabled Pipeline 可由手动、Webhook、Schedule、Artifact 或 Manifest Trigger 启动。
- 触发方式决定可用 Principal Context。官方安全资料指出，事件 Trigger 并不总能继承一个人类触发者的 Scoped Token，因此事件任务需要单独配置身份和权限边界。[Identity and permissions](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness)
- Trigger 启动 Agent 并不表示 Agent 输出自动越过 Pipeline Approval 或 Deployment Gate。

## 输出与后续交接

- Agent Output Variable 可传给后续 Step、Condition、Approval 或 Notification，实现结构化交接。
- IaC Plan Safety 示例输出 `APPROVE`、`REVIEW` 或 `REJECT` 候选值，Pipeline 可据此选择路径。[Example agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/)
- 官方软件开发示例要求开发者在 Merge 前审查 Agent 生成的 Specification、Plan 和 Code。
- Output Variable 是 Agent 结果，不自动等于 Policy、Approval 或生产发布已经接受；后续 Step 的配置才决定行为。

## 身份、权限与运行隔离

- 官方安全设计把 Agent 视为潜在受攻陷进程，使用 Agent、Broker、Egress 等运行用户分离，并限制 Image、Process、Secret 和 Network Blast Radius。[Worker Agent security](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)
- 存在人类 Principal 时，运行 Token 的权限取父级 RBAC 与 Agent 声明 Grant 的交集；MCP Tool 还受 Connector Allowlist 与 Agent Allowlist 的交集限制。
- Secret 可通过 Host-bound Placeholder 在调用时注入，减少直接暴露给 Agent Process 的范围。
- Webhook、Schedule 等无用户 Principal 的 Trigger 必须使用另外配置的身份；不能描述为自动继承最后编辑者或批准者身份。

## 管理、版本和记录

- Agent Catalog 支持在 Account、Organization 或 Project Scope 管理定义，Pipeline 通过名称和版本消费。
- Version 固定 Pipeline 所引用的 Agent 行为；Pipeline Input 和 Agent Settings 提供运行时参数。
- Pipeline Execution、Step Log、Output Variable 和 Tool Attribution 提供运行记录。具体保留期与审计范围取决于 Harness 配置和计划。

## 端到端功能流程

`在 Agent Catalog 创建并版本化 Agent Definition → 选择 Model Connector、Instructions、MCP、Typed Inputs 和 Reasoning Turn 上限 → 在 Pipeline 加入 Worker Agent Step 并绑定版本 → 由手动或事件 Trigger 提供 Pipeline Context → Agent 在受限 Runtime 调用工具 → 写出结构化 Output Variable → 后续 Condition/Approval/Notification/Deployment Step 消费结果 → Pipeline Gate 决定是否继续`

## 明确限制

- 官方示例与厂商安全说明不能外推为所有客户的默认配置或效果。
- 结构化输出不是独立 Policy Oracle；需要后续确定性 Step 验证。
- Event Trigger 的 Principal 与 Secret 路径必须单独设计。
- Harness Inc. 是公司名；本章不把其产品实现写成通用 Agent Harness 标准。

## 主要证据入口

- [[00_sources/briefs/2026-harness-worker-agents|Harness Worker Agents Source Brief]]
- [[00_sources/briefs/2026-harness-worker-agent-security|Worker Agent Security Source Brief]]
- [[50_deepdives/harness-company/README|Harness Inc. 公司专题]]

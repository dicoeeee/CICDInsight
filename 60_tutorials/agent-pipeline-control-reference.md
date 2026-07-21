---
title: Agent Pipeline 安全配置骨架
aliases:
  - Agent Pipeline Control Reference
tags:
  - research/agentic-cicd
  - tutorial
  - governance
status: reference
as_of: 2026-07-17
---

# Agent Pipeline 安全配置骨架

> [!warning] 不是可执行配置
> 下面是厂商无关的评审 Schema，用于发现配置缺口，不能直接交给 GitHub、Harness、GitLab 或其他平台运行。实际实现要映射到各平台字段。

## 参考骨架

```yaml
schema_version: 1

identity:
  mode: delegated
  principal_required: true
  lifetime: task

permissions:
  read: [repository, pipeline_runs, logs]
  write: []

tools:
  allow: [repository.read, pipeline.logs.read]
  deny: [secret.read, policy.update, deployment.approve]

runtime:
  sandbox: required
  network:
    default: deny
    allow: [source_control, model_provider]
  limits:
    timeout_minutes: 15
    max_turns: 30
    max_cost: 5

outputs:
  mode: staged
  allow: [diagnosis, draft_issue]
  max_items: 1

approval:
  required_for: [code_write, retry, deploy, rollback]
  approver_must_differ_from_agent_owner: true

verification:
  required: [schema_check, policy_check, tests]
  independent_from_generator: true

rollback:
  required_for_mutation: true
  strategy: platform_native

audit:
  record: [principal, prompt, tools, permissions, outputs, cost, decision]
  retention_days: 90
```

## 字段解释

| 字段路径 | 含义 | 评审重点 |
|---|---|---|
| `identity.mode` | Agent 以谁的身份行动 | 优先使用短时委托身份，不用多人共享的长期 Bot Token。 |
| `principal_required` | 是否必须能追溯到触发主体 | Schedule/Webhook 没有人类主体时，要有专用工作负载身份和单独权限模型。 |
| `permissions.read/write` | 服务端最终资源权限 | 初始试点让 `write` 为空；需要写时按资源和动作逐项增加。 |
| `tools.allow/deny` | 模型可以看到和调用的接口 | Tool Allowlist 与权限是两层；`deny` 应覆盖 Secret、Policy、Approval 等高风险控制面。 |
| `runtime.sandbox` | 是否要求隔离执行 | 还要验证文件、进程、网络、Metadata Service、凭据和容器逃逸边界。 |
| `runtime.network.default` | 默认网络策略 | 建议默认拒绝，再按模型、代码仓、制品仓等目标逐项允许。 |
| `runtime.limits` | 单次任务的时间、推理和成本上限 | 防止死循环、事件风暴和成本放大；三类限制不能互相替代。 |
| `outputs.mode` | 外部副作用如何产生 | `staged` 先预览；写入应通过类型化输出或确定性 Pipeline Step，不让 Agent 自由调用写 API。 |
| `approval.required_for` | 哪些动作必须人工批准 | 代码写入、重试、部署、批准和回滚应按风险分级，不能使用同一门槛。 |
| `verification` | Agent 结果由什么 Oracle 判断 | 测试、Policy 和环境验证应尽量独立于生成修复的同一个 Agent。 |
| `rollback` | 变更失败如何恢复 | 任何生产 Mutation 在执行前都应有可用、可验证的回退路径。 |
| `audit.record` | 需要留下哪些证据 | 至少能重建“谁触发、看了什么、调用什么、为什么写、花费多少”。 |

## 映射到现有平台

| 控制面 | GitHub Agentic Workflows | Harness Worker Agent / Pipeline |
|---|---|---|
| 身份与权限 | Trigger Actor、`permissions`、独立 Safe Output Token | Trigger Principal RBAC、Agent Grant、Service Account/Token |
| 工具 | `tools`、GitHub Toolsets、MCP | MCP Connector、Connector/Agent Tool Allowlist |
| 隔离与网络 | `sandbox`、`network`、Runner | Worker Container、Delegate/Cloud Runtime、Egress Policy |
| 成本与时间 | `max-ai-credits`、`max-daily-ai-credits`、`timeout-minutes` | `PLUGIN_MAX_TURNS`、Pipeline Timeout、模型配额 |
| 输出与审批 | `safe-outputs.staged`、Safe Output 类型、Environment | Pipeline Step、Approval/Barrier、Policy、Service Verification |
| 验证与回退 | CI Checks、独立 Workflow、Branch Protection | Verification Step、Rollback Strategy、Policy as Code |
| 审计 | `.lock.yml`、Artifacts、`gh aw audit` | Execution Log、Audit Trail、Connector/Agent 调用记录 |

## 使用方式

在产品选型、架构评审或试点验收时逐项回答：对应厂商字段是什么、默认值是什么、谁能修改、运行时是否真的生效、失败如何证明。任何无法映射或只能靠 Prompt 约束的项目，都应记录为控制缺口。

## 下钻阅读

- [[60_tutorials/github-agentic-workflows-config|GitHub Agentic Workflows 配置速查]]
- [[60_tutorials/harness-worker-agent-config|Harness Worker Agent YAML 配置速查]]
- [[50_deepdives/cicd-self-healing/90_report|CI/CD 问题自愈深度报告]]

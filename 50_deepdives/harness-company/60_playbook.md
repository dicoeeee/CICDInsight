---
title: Harness Agentic Software Delivery 企业落地 Playbook
tags:
  - research/agentic-cicd
  - research/playbook
  - company/harness
status: complete
as_of: 2026-07-16
---

# Harness Agentic Software Delivery 企业落地 Playbook

## 1. 先选择入口，不要一次采购全部 Agent

| 需求 | 首选入口 | 原因 | 暂不开放 |
|---|---|---|---|
| 加速 Pipeline/IaC/Policy 设计 | DevOps Agent | 低执行风险，能复用 Harness Template/资源 | 自动保存并执行生产 Pipeline |
| 在 IDE 查询交付状态 | MCP Server / Skills / CLI | 不搬迁开发界面，先验证只读上下文 | Create/Delete/Execute 全开 |
| CI 失败闭环 | Worker Agent + Draft PR | 可用原 CI 复验，收益可量化 | Merge/Main 写入 |
| 测试维护 | AI Test Automation | 已有较具体案例和确定性浏览器输出 | 仅靠自愈结果作为关键业务 Gate |
| Incident 文档/RCA | AI SRE Scribe + RCA | 首期只读/建议，风险较低 | Agent 自选生产修复动作 |
| 受限恢复 | AI SRE Runbook / Pipeline | 动作预定义、可批准、可验证 | 开放 Shell 和通用生产 Token |

## 2. PoC 前完成状态清单

每个能力记录：

- SKU、许可证和 Consumption Entitlement；
- Capability 状态；
- 账户是否需 Support/Sales/Feature Flag；
- SaaS Cluster、地区、数据驻留和 SMP 支持；
- Managed/BYOM 模型、版本、Retain/Training 条款；
- 运行位置：Harness Cloud 或客户 Kubernetes/Delegate；
- Manual/API 与 Webhook/Schedule/Artifact/Manifest Trigger 的 Principal、Token 和审计差异；
- 2026-08 后 LLM、Runner、模块调用和存储计费；
- SLA、Support 和回滚方式。

## 3. 建立生产参考架构

~~~mermaid
flowchart TB
  U["用户 / API<br/>存在 Principal"] --> ID["委托身份 + Scoped Token"]
  ID --> P["Harness Pipeline / AI UI"]
  EV["Webhook / Schedule / Artifact / Manifest"] --> EID["事件 Trigger 身份与审批<br/>当前需单独设计"]
  EID --> P
  P --> A["Worker / DevOps / 专项 Agent"]
  KG["Knowledge Graph + HQL"] --> A
  A --> MG["LLM Gateway / Model Connector"]
  A --> GW["MCP Gateway"]
  GW --> HT["Harness Resources"]
  GW --> TT["Third-party Tools"]
  A --> OUT["Patch / Report / PR / Runbook Input"]
  OUT --> OR["Test / Scan / Policy / Signature / SLO"]
  OR --> AP["Approval / Promotion / Rollback"]
  ID --> AUD["Agent + Run + Principal Audit"]
  GW --> AUD
  AP --> AUD
~~~

### 不可省略的边界

1. Agent 不直接持有长期生产 Secret；
2. Harness 资源权限和第三方 MCP Tool 权限分别最小化；
3. 只读调查与写动作使用不同 Connector/Agent；
4. Rules 负责引导，OPA/Approval 负责拒绝；
5. Agent 不得修改证明自己成功的 Gate；
6. 关键写动作进入 PR、Plan、Runbook 或受控 Pipeline；
7. 每个动作必须有外部 Oracle 和失败回退。

## 4. 四阶段试点

### Phase 0：两周盘点

- 选择 2 个高频、可验证、可回滚任务；
- 固定模型、Agent Template、MCP Release 和 Toolset；
- 准备 50—100 个历史任务评测集；
- 记录人工 Baseline、失败分类和现有 Gate；
- 完成数据、合规、Support 开通和计费确认。

**退出条件：** 能明确任务 Owner、成功 Oracle、权限和回退。

### Phase 1：只读/建议

- DevOps Agent 只生成 YAML/Policy 建议；
- MCP 仅开放 List/Get/Search/Diagnose；
- Worker Agent 输出报告或 Comment；
- AI SRE 只做 Scribe/RCA；
- 统计证据引用、误报、人工修订和成本。

**退出条件：** Diagnosis/Recommendation 在目标类别达到内部门槛，且审计完整。

### Phase 2：Draft PR 与非生产验证

- AutoFix 仅写临时 Branch；
- 原 CI、Scanner 和 Policy 全量复验；
- Manifest/IaC 先 Dry-run/Plan；
- AI Test 与独立 API/数据断言组合；
- 设置最大轮次、超时、Token/Runner 预算和 Kill Switch。

**退出条件：** Verified Fix Rate、回归率、人工介入和成本优于 Baseline。

### Phase 3：批准后动作

- 仅对稳定任务开放 Pipeline Execute、GitOps Sync 或 Runbook；
- 采用短期 Token、具体 Resource ID 和 Tool Allowlist；
- 高风险动作必须人类批准；
- 生产后观察 SLO，异常自动停止或回滚；
- Agent/模型升级自动触发离线回归。

**退出条件：** 经过至少两个版本周期和红队，没有未解释越权或质量退化。

## 5. OPA/权限策略最小集

- 只允许已批准 Model Connector；
- 禁止 Community Agent 直接进入生产；
- 限制 `max_turns`、运行时间和成本；
- 禁止 Agent 读取 Secret Value；
- 禁止直接 Push Main、Delete Artifact、Destroy IaC；
- 生产 Agent 必须绑定具体 Project/Resource ID；
- 写 Agent 必须有对应测试/扫描/Policy Step；
- Agent Template 和 MCP/Skill 必须固定版本；
- 关键 Pipeline 必须保留 Approval 和 Rollback；
- 未通过评测版本自动降级为只读。

## 6. 采购问题

### 产品和架构

- DevOps Agent、Worker Agent、Code Quality 和专项模块分别使用什么 Agent Runtime？
- Knowledge Graph 覆盖哪些外部工具、数据延迟和权限语义？
- Marketplace Agent 如何版本化、签名、扫描、回滚和证明测试？
- Managed Connector 2026-08 后的费率、Rate Limit 和区域是什么？
- Hosted MCP 与自托管 MCP 的能力、OAuth、日志和 SLA 是否一致？

### 安全

- 四层隔离是否覆盖 Harness Cloud 与客户 K8s 两种 Runtime？
- Broker 的 Secret 分类规则、Host Binding、Response Scrub 和告警如何验证？
- Scoped Token/Tool Intersection 是否已在目标 Cluster 开启？
- 没有触发 Principal 的事件 Run 使用什么身份，如何限制权限、重新授权和归因？
- 是否可导出每次 Tool Call 的结构化 Attribution Record？
- 是否有独立 Pen Test、SOC 报告或客户可执行的红队环境？

### 效果与运营

- 能否提供按 Failure Class 的 AutoFix 数据，而非总“成功率”？
- 是否支持 Shadow、A/B、回放和 Agent Version 对比？
- 如何追踪错误建议、人工拒绝、回归和缺陷逃逸？
- 模型或 SaaS 自动升级能否延迟、固定或回滚？
- Token、Runner、Module Consumption 和人类审查能否汇总为每成功任务成本？

## 7. 统一指标

| 维度 | 指标 |
|---|---|
| 能力 | Task Completion、Diagnosis Accuracy、Verified Fix Rate |
| 质量 | Regression、False Pass/Fail、Defect Escape、Gate Tampering |
| 人机 | Acceptance、Intervention、Time to Review、Escalation |
| 交付 | Lead Time、MTTR、Change Failure Rate、Release Frequency |
| 风险 | Denied Tool Calls、Secret Exposure、Blast Radius、Rollback Success |
| 运营 | Token/Runner Cost、每成功任务成本、Queue/Latency、Model Drift |
| 治理 | Attribution 完整率、Policy Coverage、版本可追溯率 |

## 8. 停止条件

- Agent 请求扩大自身权限或修改 Gate；
- 出现真实 Secret、跨 Host Placeholder 或非 Allowlist Egress；
- 无法关联触发人、Agent、Run 和 Tool Call；
- 连续两个评测周期 Verified Fix/Regression 低于门槛；
- 模型/Agent 升级后结果显著退化；
- 每成功任务成本超过人工/传统自动化且没有风险或时延优势；
- 产品状态、Support 或数据条款无法满足目标环境。

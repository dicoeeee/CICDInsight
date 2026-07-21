---
title: Harness AI SRE Agents 与 Runbooks
source_id: harness-ai-sre-2026-07-02
organization: Harness
source_type: official-docs
published: 2026-07-02
verified: 2026-07-16
availability: mixed
confidence: high
geography:
  - global
lifecycle_stages:
  - post-release
tool_categories:
  - sre-observability
  - incident-management
  - runbook-automation
  - agent-runtime-orchestration
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

# Harness AI SRE Agents 与 Runbooks

## 来源

- 标题：Harness AI SRE Overview、AI Scribe、RCA Change Agent、Runbooks
- 组织或项目：Harness
- 更新日期：2026-07-02
- 链接：[AI SRE Overview](https://developer.harness.io/3k-docs/ai-sre/get-started/overview/)、[AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)、[RCA Change Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/)、[Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/)
- 来源类型：官方产品文档
- 能力状态：混合；平台总览将 Scribe 列为 GA，细项仍有 Support、Feature Flag 与 EA 条件

## 一句话结论

Harness AI SRE 将事故沟通、变更事件和监控信号汇成时间线与根因理论，再把执行交给预定义 Runbook/Pipeline，形成“Agent 理解 + 确定性行动”的快慢双环。

## 可核验事实

- AI Scribe 监听 Slack、Zoom、Microsoft Teams，提取关键事件、行动项并生成 Postmortem。
- RCA Change Agent 读取 Scribe 时间线，关联近期 Deployment、PR、ServiceNow Change 等，输出持续更新的 Root Cause Theory 和 Confidence。
- Alert 可通过 Webhook 接入 Datadog、PagerDuty、New Relic 等并去重、关联；Incident 连接 Service、Runbook、Fire Drill 和 Change Event。
- Runbook 由预定义 Action 组成，可通知、创建事件、调用 Harness/Jenkins/GitHub Pipeline、Rollback 或操作 Feature Flag；外部动作经 Harness Delegate。
- On-call 和 Fire Drill 文档明确仍受 Feature Flag；Investigator Agent Pipelines 为 Early Access。

## CI/CD 相关性

- 涉及阶段：发布后验证、事故调查、回滚和恢复，并将运行反馈回流至交付。
- 工具类别：SRE Agent、Incident Timeline、Change Correlation、Runbook 和 Pipeline。
- 自主等级：分析 L1—L2；预定义且批准的恢复动作可到 L3。
- 涉及角色：SRE、Incident Commander、开发者、发布经理、平台团队。

## 对洞察的价值

该产品线展示了生产 Agent 更可行的边界：动态理解、假设与排序由 Agent 完成，真正的生产动作预先编码进 Runbook/Pipeline，并继续受人类、权限和 SLO 约束。

## 限制与待验证项

- 总览 GA 与细项 Support/Flag/EA 不一致，不能把整个套件写成无条件 GA。
- 缺少独立的 RCA Top-k、MTTR、误操作和 Runbook 长期成功率数据。
- Scribe 记录会接触敏感事故沟通，需验证保留、访问、区域和第三方会议数据边界。
- 自定义脚本类恢复应通过受控 Pipeline，不应给 Agent 开放通用生产 Shell。

## 可引用判断

- SRE Agent 的近期成熟路径是“整理证据和生成理论 → 人类确认 → 预定义 Runbook/Pipeline 执行”，而不是让模型临场发明生产修复动作。

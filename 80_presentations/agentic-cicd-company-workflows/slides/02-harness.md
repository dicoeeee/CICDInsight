---
title: Slide 02 - Harness Agentic Software Delivery Control Plane
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
as_of: 2026-07-23
---

# Harness 的竞争单位不是一个 Agent，而是受治理的软件交付控制面

> [!abstract] 页面唯一主张
> Harness 以多入口连接三类职责不同的 Agent，再用 Knowledge Graph/HQL、MCP/Connector、Pipeline/Runtime 和贯穿式治理与证明机制组成软件交付控制面；Agent 产生候选判断与动作，Pipeline 与 Oracle 决定它能否晋级。

## 单页定稿

![[80_presentations/agentic-cicd-company-workflows/assets/harness-agentic-control-plane-left-diagram.png|1200]]

### 可编辑成品

- [[outputs/harness-agentic-control-plane-editable.pptx|Harness Agentic Software Delivery 可编辑 PowerPoint]]

### 读图顺序

1. **三类 Agent：** DevOps Agent 设计与操作 Harness；Worker Agents 作为 Pipeline-native Agent Step；专项 Agent 分布在 Code、Test、Security、FME 与 AI SRE，Runtime 各不相同。
2. **上下文与行动分开：** Knowledge Graph/HQL 优先回答已建模的 Read/Query/Analyze；MCP、Connector 和 Module API 连接长尾系统并承担受治理动作。
3. **确定性控制仍在 Pipeline：** 顺序、并行、条件、Approval、Failure Strategy、Rollback、Max Turns、Timeout 与成本约束共同限制 Agent Loop。
4. **结果由外部证据证明：** Build/Test、Scanner、OPA、Signature、SLO 和 Human Review 决定候选输出是否足以晋级。

### 页面边界

- 六层是基于 Harness 官方产品组合形成的分析视图，不是 Harness 官方命名的参考架构。
- Knowledge Graph 不是所有 Custom Worker 或专项 Agent 默认拥有的上下文总线。
- Scoped Token 仅按 Worker 路径讨论，仍有 Feature Flag、Trigger Gap 和官方文档冲突。
- Audit 表示可追责能力，不等于所有 Pipeline Execution Event 已默认开启。

### 主要来源

- [[50_deepdives/harness-company/architecture-validation-2026-07-23|Harness 2026 产品组合与架构核验]]
- [[50_deepdives/harness-company/90_report|Harness 公司深度洞察报告]]

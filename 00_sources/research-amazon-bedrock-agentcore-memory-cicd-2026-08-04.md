---
title: Amazon Bedrock AgentCore Memory 机制与 CI/CD 启发
tags:
  - research/agentic-cicd
  - evidence/source
  - platform/aws
  - technology/agent-memory
status: complete
as_of: 2026-08-04
source_scope: aws-primary-only
confidence: high-for-mechanism-medium-high-for-cicd-inference
---

# Amazon Bedrock AgentCore Memory 机制与 CI/CD 启发

## 研究范围

本底稿只解释 AgentCore Memory 的当前机制及其对 Agentic CI/CD 的可迁移启发。AWS 文档事实与企业设计推断分开记录；Memory record、摘要或语义检索结果均不升级为业务事实或发布凭证。

AgentCore 于 2025-10-13 宣布 GA；当前 Memory 文档多数未显示单独发布日期。本轮访问日为 2026-08-04。

## 核心判断

**AgentCore Memory 不是模型参数，而是一条外部上下文管道：历史交互先写为短期 Event，再由 strategy 提炼和合并为长期 Record，随后按 namespace 与相关性检索，最后由 Agent 编排注入下一轮推理。**这条链能延续上下文，也会把错误提炼、过期记录和检索错配带入后续决策，因此在 CI/CD 中只能作为候选上下文，不能替代制品、测试、审批和运行状态的事实系统。

## Claim ledger

| ID | 类型 | 可核验事实 | 机制解释 | CI/CD 可见判断 | 边界 | 一手来源 |
|---|---|---|---|---|---|---|
| ACM-M01 | 事实 | Event 是带时间戳、不可变的短期记忆单元，按 actor 与 session 组织；当前只有 conversational payload 进入长期提取 | 原始交互、消息和工具调用先作为事件保留，长期记忆不是直接写入模型 | **先保留原始事件，再谈长期记忆** | Event 是记录，不自动等于可信测试或发布证据 | [Memory terminology](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-terminology.html)；[Create event](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-create-event.html) |
| ACM-M02 | 事实 | 启用的 memory strategy 从后续 raw conversation events 中执行 extraction / consolidation，形成 semantic、summary、user preference、episodic 或自定义长期 records | “记住什么”由策略、提示、模型与 schema 决定；未配置 strategy 时只保留短期事件 | **长期记忆是提炼结果，不是原始事实的复制** | 新 strategy 只处理其 ACTIVE 后的新事件；不同 strategy 的输出结构和适用范围不同 | [Memory strategies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-strategies.html)；[Enable long-term memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-enabling-long-term-memory.html) |
| ACM-M03 | 事实 | RetrieveMemoryRecords 在 namespace / namespacePath 内按 query、strategy、metadata 与 topK 检索长期 records；namespace 也可配合 IAM 限制访问 | 检索先限定可见范围，再选择相关记录；普通 Memory API 返回记录，应用决定如何放进模型上下文 | **检索范围决定 Agent 会看到哪段历史** | Harness 可自动检索并注入；不能把该行为外推给所有自带 Runtime / framework 的 Agent | [RetrieveMemoryRecords API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_RetrieveMemoryRecords.html)；[Namespaces](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/specify-long-term-memory-organization.html)；[Harness memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-memory.html) |
| ACM-M04 | 事实 | 删除 short-term Event 不会删除由它派生的 long-term Record；两者分别由 DeleteEvent 与 DeleteMemoryRecord 处理 | 原始层与派生层拥有独立生命周期；单点删除不能证明已经遗忘 | **遗忘必须同时覆盖 Event 与 Record** | 已经复制到日志、缓存或模型上下文的内容仍需由对应系统治理 | [Delete event](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-delete-event.html)；[Delete memory record](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-delete-memory-records.html) |
| ACM-M05 | 事实 | raw-event expiry 在事件写入时生效；修改 eventExpiryDuration 只影响之后新建事件，已过期事件不可恢复 | 保留期不是对全部历史数据的即时重写 | **保留期变更要按数据批次验证** | 修改配置不等于旧数据已同步延长、缩短或清除 | [Create memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-create-a-memory-store.html) |
| ACM-M06 | 分析 | ACM-M01—M05 与 AgentCore version / evaluation 能力共同表明，Agent 行为依赖事件、提炼策略、检索范围和注入结果 | 只锁定代码和 prompt 不能复现 Agent 当时看到的上下文 | **记忆策略与检索配置也要随 Agent 版本回归** | AWS 没有提供一个把 code、prompt、model、memory 与 evaluation 锁成原子 release bundle 的通用合同 | ACM-M01—M05；[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map|AC-C08、AC-C15、AC-C22]] |

## 给 CI/CD 的三条启发

### 1. 记住经验，但重新核对当前状态

Memory 可以保存诊断线索、历史轨迹和偏好；测试结果、制品摘要、审批状态、部署健康和 SLO 仍应从 pipeline、artifact registry、change record 与运行系统重新查询。

### 2. 把记忆配置和 Agent 版本绑在一起

抽取 strategy、提示、模型、record schema、namespace、检索 topK 等参数与保留期都会改变 Agent 看到的上下文。Harness 还允许设置 relevanceScore；不能把它泛化为普通 Memory API 的统一参数。对这些配置的变更应运行固定场景回归，并记录对应的 Agent version 与 evaluation set。

### 3. 专门测试串线、过期和删除

企业应按 repo / service、environment、tenant 或 run 设计 scope，并通过负向场景验证跨范围检索不会串线；删除验证需同时检查 raw Event 与 derived Record，不能只验证 API 调用成功。

以上三条均为基于 AWS 机制的企业设计建议，不是 AWS 已提供的 CI/CD 产品合同或客户效果证明。

## Presentation 语义输入

推荐右半页主张：

> **Memory 把历史交互提炼成下一轮可用的上下文；对 CI/CD 来说，关键不是让 Agent 记得更多，而是让记忆可隔离、可版本、可评测、可删除，并在执行前重新核对事实系统。**

推荐机制图：

`短期 Event → 策略提炼 / 合并 → 长期 Record → 分域检索 / 上下文注入 → 新一轮执行`

必须保留的边界：

- 派生 Record 与语义检索 top-K 都不是业务事实；
- Harness 自动注入不能外推为所有 Runtime / framework 的默认行为；
- 删除 Event 不会级联删除长期 Record；
- Memory 不拥有测试通过、制品可信、审批完成或发布放行权。

## 一手来源

- [AgentCore GA announcement](https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available/)
- [AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)
- [Memory terminology](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-terminology.html)
- [Memory strategies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-strategies.html)
- [RetrieveMemoryRecords API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_RetrieveMemoryRecords.html)
- [Namespaces](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/specify-long-term-memory-organization.html)
- [Harness memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-memory.html)
- [Delete event](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-delete-event.html)
- [Delete memory record](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-delete-memory-records.html)
- [Create memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-create-a-memory-store.html)

---
title: Amazon Bedrock AgentCore 逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - platform/aws
status: passed-with-gaps
as_of: 2026-08-04
confidence: high-for-mechanism-medium-for-outcomes
presentation_ready: true
---

# Amazon Bedrock AgentCore 逐主张事实审计

## 审计结果

- **核心 Claim：**27 项；**Memory 补充 Claim：**6 项；**Evaluations 补充 Claim：**13 项；均有直接一手证据或明确标为分析 / 证据缺口；
- **研究 Subagent：**先前四个 `gpt-5.6-terra` / `high` Subagent 分别核验架构、治理、Evaluations 机制和 CI/CD 门禁边界；本轮同规格 Subagent 继续核验 Memory 的事件、提炼、检索、scope、保留和删除语义。底稿落在 [[00_sources/research-amazon-bedrock-agentcore-architecture-2026-08-03|架构研究]]、[[00_sources/research-amazon-bedrock-agentcore-governance-2026-08-03|治理研究]]、[[00_sources/research-amazon-bedrock-agentcore-evaluations-mechanics-2026-08-03|Evaluations 机制研究]]、[[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|Evaluations CI/CD 研究]]和 [[00_sources/research-amazon-bedrock-agentcore-memory-cicd-2026-08-04|Memory 与 CI/CD 研究]]；
- **主 Agent 复核：**重新打开 AgentCore Overview、GA / Policy / Evaluations / Harness / Optimization / Registry 公告、Runtime Security、Gateway、Identity、Policy Core Concepts、Memory、Evaluation Types、Release Notes、Regions、Quotas、Pricing 与 Bedrock Agents Classic 文档；
- **状态口径：**平台 GA 与后来新增的 GA / Preview 子能力分开，未使用固定“八个服务”；
- **权限口径：**入站 IAM / JWT、Gateway Policy、Identity / downstream authorization 与 business gate 分层；
- **证据口径：**trace、LLM evaluator、programmatic trajectory / code evaluator、Policy / IAM 与外部 Oracle 分层；
- **效果口径：**没有独立客户 ROI、正确率、上线周期或大规模 TCO 数据，不外推 AWS 产品示例。
- **Evaluations 口径：**Online / on-demand / batch 按 trigger、source、Ground Truth 和 output 分开；LLM judge、programmatic trajectory、Lambda rule 与外部 Oracle 分层；Batch average 与最近 500 sessions 选择不外推为全量质量。
- **Memory 口径：**Event、strategy、Record、namespace / retrieval 与 context injection 分层；普通 Memory API 与 Harness 自动注入分开；原始层与派生层分别删除。

## 主张门禁核对

| 正式结论 | 直接证据 | 关键限制 | 结果 |
|---|---|---|---|
| AgentCore 是模块化 Agent 平台，不限单一 framework / model | Overview、Runtime 文档 | 接口开放不等于控制面无锁定 | passed |
| Bedrock Agents Classic 不再向新客户开放，新增设计指向 AgentCore | Bedrock Agents `agents-how` maintenance note | 现有客户仍可继续使用；不写停服或自动迁移 | passed |
| Harness 与 Runtime 是两种责任分配，且共享安全边界 | Harness vs Runtime、Runtime Security | Harness 不新增隔离层；导出只覆盖部分 loop | passed |
| Runtime 每 session microVM 隔离 | Runtime Security、Sessions | user-session mapping、execution role 与代码安全仍由客户负责 | passed |
| Gateway / Identity / Policy 形成受控工具行动面 | Gateway、Identity、Policy Core Concepts | 仅覆盖经 Gateway 的路径，listing 不等于 invocation allow | passed |
| Memory 是双层派生上下文 | Memory、DeleteEvent、DeleteMemoryRecord | Event 删除不级联；actorId 不是已验证企业身份 | passed |
| Memory 是 Event → strategy → Record → retrieval / injection 的外部管道 | Memory terminology、Memory strategies、RetrieveMemoryRecords、Harness memory | 普通 Memory API 不自动代表所有 Agent 编排均注入；检索结果不是完整历史或业务事实 | passed |
| Memory 配置与检索范围应随 Agent 版本回归 | Strategy / namespace / retrieval / retention 与 Version / Evaluations 的综合 | **分析。**AWS 未提供跨全部资源的原子 release bundle | passed-as-analysis |
| Observability / Evaluations / Optimization 形成质量闭环 | Observability、Evaluation Types、Optimization 公告 | trace / score / A-B 不是业务正确性或发布批准 | passed |
| On-demand / Batch 与 Online 是不同 Ground Truth 合同 | Evaluation Types、Evaluate API、Ground Truth | Online 对 live trace 抽样且无 Ground Truth；不能写成线上真值验证 | passed |
| expected response / assertions 不等于 deterministic assertion | Ground Truth Evaluations | Correctness 与 GoalSuccessRate 仍是 LLM judge；trajectory 才是程序化工具名匹配 | passed |
| Evaluations / Batch GA 与 Dataset Evaluation Preview 分开 | GA 公告、Release Notes、Dataset Evaluation | Dataset runner / schema API 可能变化；不能写成全部 GA | passed |
| Batch 结果不能只看平均分 | Batch Results、Get Batch Evaluation | 最多 500 sessions，超过取最近 500；逐 session / failure 必须保留 | passed |
| Evaluation 可以进入 CI/CD，但不持有 release authority | Evaluate / Batch API 输出合同、Pricing CI/CD 表述 | **分析。**threshold、must-pass、override、approval、rollback 在外部 pipeline | passed-as-analysis |
| Scenario Contract 才能把分数升级为可复现发布证据 | Ground Truth、Dataset Schema、Policy telemetry、Evaluate / Batch result | **分析。**AWS 没有跨 code / prompt / tool / policy / dataset / endpoint 的原子 bundle | passed-as-analysis |
| Agent release unit 应包含 code 之外的配置与证据 | Version / Endpoint、Policy、Memory、Evaluations 的综合 | **分析。**AWS 未公开统一原子 bundle | passed-as-analysis |
| AgentCore 与 CI/CD 应为双控制面 | Gateway / Evaluation / Step Functions / CI/CD 接点研究 | **分析。**CI/CD 持有制品、审批、部署和回滚权威 | passed-as-analysis |
| 已足以支持受控生产采用 | GA 基座、Security、Version、Policy、Evaluations | **分析。**按 Region / capability 核验；Preview 不做关键依赖 | passed-as-analysis |

## 证据冲突与处理

### 1. “AgentCore 已 GA”与新增能力 Preview

- 2025-10-13 公告证明当时平台与既有核心服务 GA；
- Policy、Evaluations、Harness 之后分别有独立 GA；
- Registry、Payments、Optimization Insights 截至观察日仍标 Preview；
- **处理：**所有正式表述按平台、能力、子能力和区域四层状态书写。

### 2. “八个服务”与当前能力域

- 2025-07 Preview 公告列七项初始服务；
- 2025-12—2026-07 又加入 Policy、Evaluations、Harness、Optimization、Registry、Payments、Web Search 等；
- **处理：**不用固定数量，以运行、行动、状态、质量、资产 / 工具能力域组织。

### 3. Harness 宣传语中的“每个行动都被治理和追踪”

- Harness GA 公告强调同一 Gateway、Identity、Memory 与 Observability 的平台集成；
- Runtime Security 同时明确：只有所有流量经过 Gateway 时，Policy / Guardrails / interceptor 才有效；直达 Runtime 会绕过；
- **处理：**写成“可组合为统一治理路径”，不写默认所有行动均不可绕过。

### 4. AgentCore 内容使用表述的范围差异

- AgentCore Overview 说明服务可能使用并保存内容来改善该客户自己的体验或性能，不用于其他客户；
- Gateway Data Protection 的表述范围更窄；
- **处理：**不把任一模块级说明泛化为覆盖 AgentCore 全部内容、日志、备份与训练条款；记录为采购 / 法务逐模块核验项。

### 5. Memory 删除语义

- DeleteEvent 不删除由该 Event 提炼的 long-term record；
- DeleteMemoryRecord 是单独 API；
- **处理：**正式材料明确“双层删除”，不写删除会话即完成遗忘。

### 6. Evaluation 的“验证改进”措辞

- AWS 公告使用 prove / validate 描述 batch / A-B；
- Evaluation Types 实际证明的是在选定 evaluator、dataset、traffic 与指标下的结果；
- **处理：**写“质量证据 / 指标改善”，不写业务安全、合规或发布正确已被证明。

### 7. “13 个 built-in evaluator”与当前文档 roster

- 2026-03 GA 公告、Release Notes、Pricing 与 Agentic AI Lens 使用 13 个；
- 当前 Prompt Templates 页面显示 14 个 LLM judge 模板；Ground Truth 页面另列三个 programmatic trajectory evaluator；
- AWS 未提供一份带发布日期、版本和全部 ID / judge / score mapping 的稳定静态清单；
- **处理：**回述 GA 时保留“当时 13 个”；截至日不写当前精确总数。必须写数量时在目标账户 / Region 调用 `ListEvaluators` 并保留带时间戳输出。

### 8. Batch Evaluation GA 与 Dataset Evaluation Preview

- Release Notes 明确 Batch Evaluation 于 2026-06 GA；
- 当前 Dataset Evaluation 页面明确标 Public Preview，包含 dataset management 与 on-demand / batch dataset runner；
- **处理：**把 Batch service job 与 Dataset orchestration 分开标状态；PPT 不用“一键版本化测试集已 GA”。

### 9. “带质量阈值的 CI/CD integration”与发布授权

- Pricing 页面称包含 configurable quality thresholds；
- `Evaluate` / Batch API 的正式结果是 score、label、explanation、job / session status 和 token usage，没有 artifact approval、promotion 或 deploy authorization 字段；
- **处理：**写成“为 pipeline 提供 threshold input”；block、override、approval 与 rollback 由外部 CI/CD 决定。

## 负面搜索边界

本轮在 AWS 官方文档、公告、博客、价格、配额、Release Notes 与官方仓库范围内，未识别到可独立复核的跨企业 benchmark、平台人力节省、Agent failure-rate 对照、regression escape rate、跨云迁移成本或完整 TCO。该结果只证明当前公开一手材料的证据缺口，不证明客户没有获得收益。

## Presentation-ready 决定

**结论：通过，但范围受限。**可使用以下单一主张：

> **Agent 平台的生产中心从编写 loop 转向治理行动与质量：AgentCore 把运行、工具授权和回归证据串成双闭环，但外部确定性 Gate 仍持有最终权威。**

Evaluations 专题也通过，可使用：

> **Agent 的测试对象正在从最终回答扩展为执行轨迹。AgentCore Evaluations 把 trace 变成可回归的行为证据，但真正的发布门禁必须组合确定性轨迹 / 代码断言与外部业务 Oracle。**

Memory 专题也通过，可使用：

> **Memory 把历史交互提炼成下一轮可用的上下文；对 CI/CD 来说，关键不是记得更多，而是让记忆可隔离、可版本、可评测、可删除，并在执行前重新核对事实系统。**

页面必须保留：

1. 平台 GA 与 Preview 子能力分开；
2. Policy 只覆盖经关联 Gateway 的调用，tool listing 不等于调用获准；
3. Runtime microVM 不绑定最终用户，也不证明输出安全；
4. Memory Event 与 long-term Record 分别删除；
5. Evaluation / A-B 是质量证据，不是业务正确性或发布批准；
6. 当前没有独立客户 ROI / benchmark。

Evaluations 专题页面还必须保留：Dataset Evaluation 为 Public Preview；Online 无 Ground Truth；Correctness / assertions 仍可由 LLM judge 评分；trajectory 只核工具名称和顺序；Batch average 与最近 500 session 选择不是全量质量；不使用未经实测的当前 built-in 精确总数。

## 不阻塞该页面的剩余缺口

统一内容处理与物理删除 SLA、Preview 能力 GA、独立客户结果、大规模故障耦合、跨云退出路径和完整 TCO 会影响采购与生产标准化，但不推翻“行动闭环 + 质量闭环 + 外部 Gate”的架构主张。

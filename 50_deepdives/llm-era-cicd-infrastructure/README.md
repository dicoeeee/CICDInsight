---
title: 大模型时代的 CI/CD 基础设施专题
aliases:
  - LLM-era CI/CD Infrastructure
  - Agentic CI/CD Infrastructure
tags:
  - research/agentic-cicd
  - research/deep-dive
  - technology/infrastructure
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
topic_type: technology
stages:
  - source-control
  - testing-gates
  - build
  - security-compliance
  - artifact-versioning
companies:
  - GitHub
  - GitLab
  - Buildkite
  - CircleCI
  - Nx
  - JFrog
  - Cloudsmith
  - Sonatype
confidence: high
presentation_ready: true
refresh_after: 2026-09-28
---

# 大模型时代的 CI/CD 基础设施专题

> [!abstract] 专题目标
> 判断大模型与软件工程 Agent 到来后，代码仓、流水线、构建系统和制品仓究竟发生了哪些结构性变化，哪些只是既有能力被重新重视，哪些仍停留在 Preview、厂商路线图或营销叙事，并给出企业基础设施演进顺序。

## 当前判断

大模型没有取消 CI/CD 的确定性基础设施，而是把它从“面向人的共享工具链”推向“同时面向人和 Agent 的上下文面、隔离执行面与治理控制面”。真正的变化集中在接口、负载形态、反馈闭环、身份授权、供应链证据和成本观测；测试、策略、签名、制品摘要和发布审批等外部 Oracle 反而需要更独立、更严格。

## 关键结论

1. 代码仓已从 Source of Truth 扩展为 Agent 的任务、Session、配置、PR/MR 与规则控制面。
2. CI 正分化为 Agent 的低延迟验证内环和决定接受的完整确定性外环。
3. 构建图、增量、远程缓存/执行和隔离大多是旧能力，但已升级为 Agent 的反馈与成本底座。
4. 制品仓正成为实时供应链事实、信任图和受限行动面，并开始管理 Skill/MCP 等 Agent 行为资产。
5. 静态 Bot Token 正让位于任务级委托、短期身份与 Tool/环境权限求交。
6. 行业已经出现多项 GA 能力，但 Agentic Workflow、制品高风险写入和 Agent 资产治理仍混合 Preview/Beta，不能写成普遍自治。

## 关键研究问题

1. 代码仓为什么开始成为 Agent 的任务入口、上下文系统和治理控制面？
2. 流水线为什么从一次性终点门禁，扩展为 Agent 可反复调用的验证基础设施？
3. 构建图、远程缓存、增量构建和隔离执行中，哪些是新能力，哪些只是因 Agent 负载而升级为关键能力？
4. 制品仓是否已从二进制存储扩展为 Agent 可查询、可操作的供应链信任与行动面？
5. 哪些身份、权限、证据、审计和成本控制必须下沉到模型之外？
6. 业界落地处于什么成熟度，哪些结论不能从厂商能力存在性外推？

## 交付状态

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map|证据矩阵]] |
| Findings | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/50_findings|分析发现]] |
| Report | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/90_report|完整报告]] |

## 可选交付物

| 交付物 | 是否需要 | 状态 | 入口 |
|---|---|---|---|
| Case Map | 需要，用于跨基础设施类型比较 | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/30_case-map|案例比较]] |
| Labs | 暂不需要；本专题回答行业变化而非验证单一产品 | 不适用 |  |
| Playbook | 需要，用于企业演进建议 | 完成 | [[50_deepdives/llm-era-cicd-infrastructure/60_playbook|企业演进手册]] |
| Tutorial | 暂不需要 | 不适用 |  |

## Presentation-ready 判断

- **当前值：** `true`
- **候选页面主张：** Agent 时代的 CI/CD 基础设施不是被模型替代，而是分化为“机器可调用的上下文与执行面”和“模型不可篡改的确定性控制面”。
- **通过理由：** 四类基础设施已有逐项状态核验；核心主张由跨厂商官方机制、SLSA 规范、DORA 与原始研究共同支撑；Preview/Beta、厂商自述、成效缺口和反例已显式保留。
- **页面边界：** `presentation_ready: true` 只表示研究可以支撑上述机制主张，不表示全链路 Agent 自治已经成熟。

## 研究底稿

- [[50_deepdives/llm-era-cicd-infrastructure/research-code-repository-2026-07-28|代码仓与托管平台]]
- [[50_deepdives/llm-era-cicd-infrastructure/research-pipeline-build-2026-07-28|流水线与构建系统]]
- [[50_deepdives/llm-era-cicd-infrastructure/research-artifact-supply-chain-2026-07-28|制品仓与供应链]]

## 上下游关系

- L0 证据：[[00_sources/README]]
- 已有关联专题：[[50_deepdives/github-agentic-workflows/README]]、[[50_deepdives/cicd-self-healing/README]]、[[50_deepdives/cli-agent-interface/README]]、[[50_deepdives/mcp-protocol/README]]
- 可能影响的批量观点：[[40_summaries/crosscutting/README]]、[[30_summaries/stages/README]]
- 可能进入的 Presentation：可围绕“概率性 Agent 面 + 确定性控制面”形成一页总览；如制作正式页面，应以本专题为主要 Source Map 入口。

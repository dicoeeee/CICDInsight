---
title: Amazon Bedrock AgentCore 深度洞察
aliases:
  - AWS AgentCore
  - Amazon Bedrock AgentCore
tags:
  - research/agentic-cicd
  - research/deep-dive
  - platform/aws
  - technology/agent-runtime
status: complete
as_of: 2026-08-04
topic_id: amazon-bedrock-agentcore
topic_type: technology
owners: []
stages:
  - agent-runtime
  - tool-access
  - governance
  - observability
  - evaluation
tools:
  - Amazon Bedrock AgentCore
companies:
  - AWS
confidence: high-for-mechanism-medium-for-outcomes
presentation_ready: true
refresh_after: 2026-11-03
---

# Amazon Bedrock AgentCore 深度洞察

> [!abstract] 专题目标
> 判断 AgentCore 是否、以及在什么边界内能成为企业 Agent 的生产控制面；解释其运行、工具行动、身份、策略、记忆、观测和质量回归机制，不把平台能力误写成业务授权或正确性证明。

## 当前判断

**AgentCore 的产品中心不是模型或 Agent framework，而是 Agent 的生产控制面。**它把 Agent 的运行、工具授权和质量回归组织成两个闭环：

- 行动闭环约束 Agent 在什么环境、代表谁、通过什么入口和规则调用工具；
- 质量闭环把 trace、evaluation、recommendation、experiment 与 immutable version / endpoint 连接起来。

这让 Agent 成为可版本、可观测、可授权、可回归的生产工作负载，但不会自动成为业务正确性的 Oracle。Policy allow、Identity token、microVM isolation、Evaluation pass 分别只能证明局部条件；外部 Test、Scan、Signature、SLO、事实系统与人工审批仍持有最终权威。

**Evaluations 补充判断：**Agent 的测试对象正在从最终回答扩展到 session、trace、tool call 与工具轨迹。On-demand / Batch 可用 Ground Truth 形成上线前回归证据，Online 用生产抽样发现退化；但 expected response 与 natural-language assertions 仍可能由 LLM judge 评分，程序化 trajectory 也只核工具名称和顺序。因此 Evaluations 最适合作为 CI quality signal，不应单独承担 release authority。详见 [[50_deepdives/amazon-bedrock-agentcore/55_evaluations-insight|Evaluations 补充洞察]]。

**Memory 补充判断：**Memory 是“短期 Event → strategy 提炼 / 合并 → 长期 Record → 分域检索 / 上下文注入”的外部管道。它适合复用历史诊断线索，但派生 Record 与语义检索结果不是当前发布事实；strategy、namespace、检索参数和保留期应随 Agent 版本回归，删除需覆盖 Event 与 Record 两层。详见 [[50_deepdives/amazon-bedrock-agentcore/56_memory-cicd-insight|Memory 机制与 CI/CD 启发]]。

## 三个核心结论

1. **从 loop 到控制面：**Harness 与 Runtime 解决不同责任分配，Gateway / Identity / Policy 约束行动，Observability / Evaluations / Optimization 约束质量演进；固定“八个服务”口径已经过时。
2. **安全是组合边界：**Runtime session 不自动绑定用户；Gateway Policy 只保护经过 Gateway 的调用；Memory 删除需覆盖 short-term Event 与 derived long-term Record；工具可发现不等于真实调用获准。
3. **与 CI/CD 是双控制面：**AgentCore 交付和治理 Agent，CI/CD 继续持有构建、制品、部署、审批和回滚权威；Agent evaluation 不能替代 release gate。

## 产品状态快照

| 能力 | 截至 2026-08-03 |
|---|---|
| AgentCore 平台 | 2025-10-13 GA |
| Policy | 2026-03-03 GA |
| Evaluations | 2026-03-31 GA |
| Harness | 2026-06-17 GA |
| Optimization | batch evaluations / recommendations / A-B tests GA；insights Preview |
| AWS Agent Registry | Preview |
| Payments | Preview |
| Bedrock Agents Classic | 2026-07-30 起不向新客户开放；现有客户可继续使用 |

平台级 GA 不能覆盖后来新增能力；部署必须按 capability × Region × quota 的共同交集重新核验。

## 交付状态

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | complete | [[50_deepdives/amazon-bedrock-agentcore/00_charter|00_charter]] |
| Question Tree | complete | [[50_deepdives/amazon-bedrock-agentcore/10_question-tree|10_question-tree]] |
| Evidence Map | complete | [[50_deepdives/amazon-bedrock-agentcore/20_evidence-map|20_evidence-map]] |
| Findings | complete | [[50_deepdives/amazon-bedrock-agentcore/50_findings|50_findings]] |
| Evaluations 补充洞察 | complete / presentation-ready | [[50_deepdives/amazon-bedrock-agentcore/55_evaluations-insight|55_evaluations-insight]] |
| Memory 补充洞察 | complete / presentation-ready | [[50_deepdives/amazon-bedrock-agentcore/56_memory-cicd-insight|56_memory-cicd-insight]] |
| Fact Audit | passed-with-gaps | [[50_deepdives/amazon-bedrock-agentcore/70_fact-audit|70_fact-audit]] |
| Report | complete | [[50_deepdives/amazon-bedrock-agentcore/90_report|90_report]] |

## Presentation-ready 判断

- **当前值：**`true`，仅支持受控的架构 / 平台判断页面；
- **候选页面主张：**“Agent 平台的生产中心从编写 loop 转向治理行动与质量：AgentCore 把运行、工具授权和回归证据串成双闭环，但外部确定性 Gate 仍持有最终权威。”
- **Evaluations 专题候选：**“Agent 的测试对象正在从最终回答扩展为执行轨迹。AgentCore Evaluations 把 trace 变成可回归的行为证据，但真正的发布门禁必须组合确定性轨迹 / 代码断言与外部业务 Oracle。”
- **Memory 专题候选：**“Memory 把历史交互提炼成下一轮可用的上下文；对 CI/CD 来说，关键不是记得更多，而是让记忆可隔离、可版本、可评测、可删除，并在执行前重新核对事实系统。”
- **必须保留：**平台 GA 与 Preview 子能力分开；Policy 只覆盖 Gateway；Evaluation 不是业务正确性；Memory 双层删除；无独立客户 ROI / benchmark；
- **不能支持：**全功能全球 GA、端到端自主发布、普遍成本节省、跨云无锁定、已证明的客户效果。

## 研究底稿

- [[00_sources/research-amazon-bedrock-agentcore-architecture-2026-08-03|产品边界、架构与运行约束]]
- [[00_sources/research-amazon-bedrock-agentcore-governance-2026-08-03|企业治理与落地边界]]
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-mechanics-2026-08-03|Evaluations 机制、数据合同与生产边界]]
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|Evaluations 与 CI/CD 发布门禁边界]]
- [[00_sources/research-amazon-bedrock-agentcore-memory-cicd-2026-08-04|Memory 机制与 CI/CD 启发]]
- [[00_sources/research-aws-llm-cicd-agent-platform-capabilities-2026-08-03|AgentCore 与 CI/CD Agent 平台能力]]
- [[00_sources/research-agentcore-transform-devops-agent-relationship-2026-08-03|AgentCore、AWS Transform 与 AWS DevOps Agent 层级关系]]

## 上下游关系

- L0 证据：[[00_sources/README]]
- 相邻专题：[[50_deepdives/aws-devops-agent/README|AWS DevOps Agent]]、[[50_deepdives/runtime-generated-verification-gates/README|Agent 生成验证的运行时 Gate]]、[[50_deepdives/llm-era-cicd-infrastructure/README|大模型时代的 CI/CD 基础设施]]
- 可能影响的批量观点：Agent 基础设施从模型调用扩展为运行、行动治理与质量回归控制面；
- 可能进入的 Presentation：Agentic CI/CD 平台基础设施页；在正式页面发现新增事实缺口时先回到本专题补证据。

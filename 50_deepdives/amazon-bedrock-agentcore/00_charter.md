---
title: Amazon Bedrock AgentCore Deep Dive Charter
tags:
  - research/agentic-cicd
  - research/deep-dive
  - platform/aws
  - technology/agent-runtime
status: complete
as_of: 2026-08-03
confidence: high
---

# Amazon Bedrock AgentCore Deep Dive Charter

## 研究决策

本专题支持三个决策：

1. Amazon Bedrock AgentCore 应被理解为模型服务、Agent 框架、运行时，还是企业 Agent 生产控制面；
2. 企业能否用它把 Agent 的执行、工具调用、身份、策略、观测和质量回归变成可治理的工程系统；
3. 在 CI/CD 与软件交付场景中，AgentCore 应持有什么权力，哪些正确性、审批和回滚权威必须留在外部确定性系统。

研究目标不是复述产品菜单，而是解释模块之间的机制链、控制边界、生命周期、采用代价和可证伪的企业价值。

## 观察窗口

- **起点：**2025-07-16，Amazon Bedrock AgentCore Public Preview；
- **截至：**2026-08-03；
- **刷新触发：**AgentCore 核心能力生命周期变化、Amazon Bedrock Agents Classic 迁移政策变化、AWS Agent Registry GA、Optimization Insights GA，或 Runtime / Gateway / Policy 的安全合同发生实质变化；
- **建议最晚复核：**2026-11-03。

## 核心研究问题

1. AgentCore 在 AWS Agent 产品栈中的层级是什么，与 Amazon Bedrock、Bedrock Agents Classic、Strands Agents、AWS DevOps Agent 有何区别？
2. Harness 与 Runtime 分别拥有哪一层责任，何时选择配置式 loop，何时保留自定义编排代码？
3. Gateway、Identity、Policy 和下游 IAM / OAuth 如何共同决定一次工具行动是否可执行？
4. Memory、Observability、Evaluations 与 Optimization 如何形成持续质量闭环，哪些输出仍只是证据而不是门禁？
5. 模型、框架、协议层的可移植性，是否等于生产控制面的可移植性？
6. Agent 定义应如何作为软件交付工件进入版本、测试、审批、发布和回滚链？
7. 消费计费、配额、区域差异与相邻 AWS 服务成本会如何改变采用判断？

## 范围

### 纳入

- AgentCore Harness、Runtime、Gateway、Identity、Memory、Policy、Observability、Evaluations、Optimization；
- Browser、Code Interpreter、Web Search、Registry 等与运行、工具或治理边界直接相关的能力；
- AgentCore CLI、CDK、版本与 Endpoint、Step Functions 集成；
- MCP、A2A、OpenAPI、Smithy、Lambda、HTTP/API Gateway 等连接面；
- Amazon Bedrock Agents Classic 与 AgentCore 的公开产品迁移边界；
- CI/CD Agent 的部署、回归评测、工具授权与确定性 Gate 组合。

### 排除

- 对所有 AWS AI 产品做横向采购比较；
- 重复 [[50_deepdives/aws-devops-agent/README|AWS DevOps Agent]] 的 Agent Space、Topology、Release Management 与 Production Operations 研究；
- 把 Strands、LangGraph、CrewAI 等框架本身做完整能力比较；
- 连接真实 AWS 账号、创建 AgentCore 资源、运行付费评测或调用生产工具；
- 用 AWS 厂商示例推导行业平均 ROI、准确率或 MTTR；
- 把“支持协议 / 可以调用”写成“已授权 / 已验证 / 可以自主发布”。

## 证据标准

| 等级 | 来源 | 可证明 | 不可外推 |
|---|---|---|---|
| A | AWS Developer Guide、API Reference、Pricing、Quotas、Release Notes、Security 文档 | 当前接口、状态、限制、安全责任和计费口径 | 客户效果、跨云可移植成本 |
| B | AWS What's New、官方工程博客、AWS 官方 GitHub / Samples | 发布日期、厂商实现路径、官方示例 | 独立生产效果、行业通用最佳实践 |
| 分析 | 多项 A/B 事实的机制综合 | 架构关系、采用顺序、治理含义 | AWS 已交付的端到端承诺 |
| 缺口 | 当前一手证据不足 | 明确保持阻塞的问题 | 不得补全为事实 |

所有关键事实必须回链到 AWS 一手来源或 `00_sources/` 研究底稿；产品级 GA 不覆盖后来新增能力，区域和状态按能力分别核对。

## 成功条件

- 建立 AgentCore 的产品层级、行动闭环和质量闭环；
- 区分 Harness、Runtime、Bedrock Agents Classic 与开源 Agent framework；
- 每项关键 Claim 都有证据、限制、置信度和审计状态；
- 把 Runtime 隔离、Gateway Policy、Identity、下游授权与业务 Gate 分层；
- 对 GA / Preview、区域、配额、计费与迁移状态做时间点校准；
- 给出不依赖厂商 ROI 的企业试点路径和退出条件；
- 完成逐主张事实审计后，再决定 `presentation_ready`。

## 停止条件

- 若主张必须依赖所有 AgentCore 能力均 GA 或全区域一致，则保持阻塞；
- 若无法证明某次工具调用必经 Gateway / Policy，不宣称它受统一策略保护；
- 若只能用 LLM-as-judge 分数证明高风险动作正确，不进入生产授权建议；
- 若必须把 Runtime microVM 隔离推导为 Agent 输出安全或业务结果正确，则停止；
- 若需要真实账户、生产数据、组织 IAM 或客户效果数据才能回答，则记录为 Lab / 采购阶段证据缺口，不做推测。

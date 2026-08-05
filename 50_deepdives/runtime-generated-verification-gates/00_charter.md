---
title: Agent 生成验证的运行时 Gate 研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - capability/testing-gates
status: complete
as_of: 2026-08-03
---

# Agent 生成验证的运行时 Gate 研究边界

## 决策目标

回答 CTO、研发效能负责人和平台工程负责人真正需要决定的四个问题：

1. Agent 生成的验证在流水线运行时究竟形成什么新对象；
2. 验证如何被规划、执行、判定并转换成 Gate 输入；
3. 哪些职责可交给 Agent，哪些必须留在 CI/CD 控制面、外部 Oracle 或人工审批；
4. AWS DevOps Agent Release Management 与 Meta JiTTesting 分别证明了哪一段机制，哪些能力仍未被公开证据证明。

## 纳入范围

- AWS DevOps Agent Release Management 的 release readiness review、release testing、PR/MR 与 CI/CD 集成、运行环境和 Preview 状态；
- Meta JiTTesting 的 Diff 上下文、意图/风险推断、临时验证生成、执行、assessor 与工程师反馈边界；
- 从 Agent 输出到流水线 Gate 的控制面接口：触发、作用域、环境、权限、证据、策略、失败模式与降级；
- 两个案例在产品化程度、运行方式和 Gate 强度上的差异。

## 非目标

- 不评价 mutant 设计、测试生成算法、用例质量或 UI/API 测试分类；
- 不把两家公司写成联合方案、直接竞品或同一成熟度；
- 不声称 Agent 取代固定测试、完整回归、Policy、签名、SLO 或人工审批；
- 不把普通 Pipeline 触发或 Check Run 写成默认阻断；AWS readiness review 可配置为 required status check / approval rule 的明确事实除外；
- 不使用 Meta 内部研究数字证明行业平均收益，也不使用 AWS Preview 证明生产效果。

## 核心术语

- **生成式验证：**Agent 根据当前变更和上下文产生的验证计划、验证作业或判定候选，而非仓库中长期固定的唯一测试资产。
- **验证计划：**本次变更需要验证什么、在哪里执行、需要哪些前置条件以及产生什么证据的结构化描述。
- **验证证据：**执行结果、日志、对照结果、assessor 输出和剩余风险等可被外部策略消费的记录。
- **Gate：**CI/CD 控制面根据预先授权的规则消费验证证据后给出的继续、阻断、降级或人工复核决定。
- **状态回写：**把验证结果关联到 revision，并写成 GitHub Check Run、Commit Status、GitLab status 或审查结论。
- **宿主 Gate：**由 GitHub branch protection、GitLab approval/status rule 等独立配置决定某个状态是否阻断 merge；不是 AWS/Meta 的通用组件名。
- **Oracle：**独立判定结果是否可接受的测试、规则、Policy、签名、SLO 或人工审批；不由生成验证的 Agent 自行改写。

## 证据口径

| 等级 | 来源 | 可支持 | 不可直接支持 |
|---|---|---|---|
| A | AWS 当前官方公告与文档、Meta 原始论文 | 产品状态、公开机制、运行边界、内部部署披露 | 第三方效果、普遍成熟度、未披露控制细节 |
| B | Meta Engineering、AWS News Blog | 工程流程与厂商解释 | 对外 SLA、全量覆盖、强制 Gate |
| 分析 | 对多个已核验事实的控制面推导 | CI/CD 架构主张、职责分离、企业设计建议 | 冒充来源原话或已部署事实 |

## 验收标准

- 必需交付物 README、Charter、Question Tree、Evidence Map、Findings、Report 完成；
- 每个页面关键句可回链到 Evidence Map；
- AWS 与 Meta 的产品/研究状态分开标注；
- 明确区分 Agent 生成验证、执行验证、判定证据和 Gate 执行；
- 对“自动发布”“所有 PR 同步阻断”“Agent 自行批准”保持否定或未证实边界；
- `presentation_ready` 前完成逐主张事实、时间、状态和引用边界审计。

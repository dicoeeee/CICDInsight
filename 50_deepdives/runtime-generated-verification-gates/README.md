---
title: Agent 生成验证的运行时 Gate 专题
aliases:
  - Runtime-generated Verification Gates Deep Dive
tags:
  - research/agentic-cicd
  - research/deep-dive
  - capability/testing-gates
status: complete
as_of: 2026-08-03
topic_id: runtime-generated-verification-gates
topic_type: capability
stages:
  - code-review
  - testing-gates
  - release
tools:
  - AWS DevOps Agent Release Management
  - Meta JiTTesting
companies:
  - AWS
  - Meta
confidence: high
presentation_ready: true
refresh_after: 2026-09-03
---

# Agent 生成验证的运行时 Gate 专题

> [!abstract] 当前判断
> Agent 带来的 CI/CD 变化，不只是“多生成一些测试”，而是让流水线在运行时新增一种按 revision 生成的验证作业。AWS DevOps Agent Release Management 已公开形成完整技术链：PR/MR 或 Pipeline 触发 → change-specific test plan → 受控环境执行 → readiness report / GitHub Check Run；其中 readiness 结果可配置为 GitHub required status check 或 GitLab approval rule。Meta JiTTesting 则公开披露 Diff-aware 验证生成、Diff/parent 对照、rule/LLM assessor 与工程师确认。两者共同证明“验证可以按变更生成”，但只有 AWS 公开了到宿主平台 Gate 的接点；两者都不证明 Agent 自主发布。

## 关键结论

1. **CI/CD 专家应关注验证作业的生命周期，而不是测试类型。** 页面核心对象应是 `变更上下文 → 验证计划 → 受控执行 → 证据判定 → Gate 映射`。
2. **Agent 负责生成候选验证，控制面负责把它制度化。** 触发范围、环境、权限、超时、副作用、证据格式、阈值、降级和人工复核必须由流水线定义。
3. **AWS 已公开到 merge gate 的技术接点。** Release testing 可回写 GitHub Check Run；release readiness 可配置成 GitHub required status check 或 GitLab MR approval rule。是否阻断由宿主平台规则决定，不是 Check Run 默认获得的权力。
4. **Meta 更接近内部验证证据工厂。** JiTTesting 生成临时验证并以规则与 LLM assessor 压缩候选信号，再反馈工程师；公开证据不足以证明它覆盖所有 PR 或作为同步强制 Required Check。
5. **Gate 是宿主平台对状态的约束。** GitHub required status check 与 GitLab approval/status rule 才执行 merge 阻断；AWS/Meta 的 Agent 负责生成、运行或评估验证，其中 Meta 的公开链路止于工程师确认。

## 研究快照

- **观察时间：**截至 2026-08-03；
- **证据底稿：**[[00_sources/research_ws-devops-agent-release-management_meta-jittesting|AWS 与 Meta 时间线核验]]；[[00_sources/research-agent-generated-verification-gate-technical-basis-2026-08-03|生成验证到 Gate 技术事实审计]]；
- **来源范围：**AWS 官方公告与 User Guide、Meta Engineering、Meta 作者公开论文；
- **状态口径：**AWS Release Management 为 Preview；Meta 为已披露内部生产工作流与公开研究报告，不映射为 GA/Preview 产品；
- **事实审计：**12 项页面相关 Claim 已完成来源、边界、置信度与状态核对；新增复核 AWS managed verification environment、Check Run、required status check、GitLab approval rule 与 GitHub branch protection。

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/runtime-generated-verification-gates/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/runtime-generated-verification-gates/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/runtime-generated-verification-gates/20_evidence-map|证据矩阵]] |
| Findings | 完成 | [[50_deepdives/runtime-generated-verification-gates/50_findings|分析发现]] |
| Report | 完成 | [[50_deepdives/runtime-generated-verification-gates/90_report|专题报告]] |

## Presentation-ready 判断

- **当前值：**`true`
- **可支撑主张：**Agent 可把 revision/Diff 转成运行时验证；验证结果只有被写成 SCM/CI 原生状态，并被宿主平台保护规则设为 required，才真正成为 merge gate。
- **事实审计：**AWS 的 Preview/区域边界、两类执行环境、真实请求、readiness report、GitHub Check Run、required status check 与 GitLab approval rule 已核对；Meta 的内部生产状态、Diff/parent 对照、assessor 与工程师确认边界已核对；跨案例链条明确标记为分析综合。
- **页面限制：**不得把 AWS Preview 写成 GA 或自动发布；不得把 Meta 内部工作流写成对外产品、全 PR 覆盖或强制阻断门禁；不得以 mutant、测试用例质量或 UI/API 分类作为页面主轴。

---
title: Human-AI Synergy in Agentic Code Review
source_id: academic-human-ai-agentic-code-review-2026-03-16
organization: Queen's University and collaborators
source_type: research-preprint
published: 2026-03-16
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
tool_categories:
  - code-review
  - human-agent-collaboration
  - measurement
company_topics: []
autonomy_levels:
  - L0
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Human-AI Synergy in Agentic Code Review

## 来源

- 标题：Human-AI Synergy in Agentic Code Review
- 作者：Suzhen Zhong、Shayan Noei、Ying Zou、Bram Adams
- 发布日期：2026-03-16
- 链接：[arXiv:2603.15911](https://arxiv.org/abs/2603.15911)
- 来源类型：原始实证研究预印本
- 能力状态：研究结论，未视为产品能力

## 一句话结论

大规模开源评审数据表明，Agent 可以扩大缺陷筛查能力，但其建议采纳率和上下文质量仍弱于人类，人工评审在理解、测试和知识传递方面保持关键作用。

## 可核验事实

- 研究分析 300 个开源 GitHub 项目的 278,790 个代码评审对话。
- 人类评审者比 Agent 提供更多关于理解、测试和知识传递的反馈。
- 人类评审 AI 生成代码时往返轮次增加 11.8%。
- Agent 建议的采纳率显著低于人类建议；未采纳建议中超过一半被判为错误或通过替代修复处理。
- 被采纳的 Agent 建议带来更大的代码体积与复杂度增加。

## CI/CD 相关性

- 涉及阶段：代码评审和合并决策。
- 工具类别：人机协作、评审质量和建议采纳效果。
- 自主等级：L0—L2。
- 涉及角色：开发者、代码评审者、工程管理者和 Agent 评测人员。

## 对洞察的价值

这是少数直接观察真实开源评审互动的实证材料，可用来平衡厂商关于自动评审的效果主张。

## 限制与待验证项

- 当前为预印本，同行评审状态需后续核验。
- 开源项目中的 Agent 使用方式不能完全代表大型企业受治理环境。

## 可引用判断

- 2026 年的证据更支持“Agent 扩展筛查、人类保留语境与责任判断”，而不是全面替代代码评审者。


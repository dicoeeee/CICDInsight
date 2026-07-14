---
title: SWE-chat 真实 Agent 开发会话研究
source_id: academic-swe-chat-2026-04-22
organization: SWE-chat research collaboration
source_type: research-preprint
published: 2026-04-22
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
tool_categories:
  - agent-evaluation
  - developer-workflow
company_topics: []
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# SWE-chat 真实 Agent 开发会话研究

## 来源

- 标题：SWE-chat: A Large-Scale Analysis of Real-World Software Engineering Agent Interactions
- 发布日期：2026-04-22
- 链接：[arXiv:2604.20779](https://arxiv.org/abs/2604.20779)
- 来源类型：观察性研究预印本、持续更新数据集
- 能力状态：研究

## 一句话结论

真实开源会话显示 Agent 已能承担大量代码生成，但纠正、中断和代码丢弃仍很常见，人类监督与后续质量门禁没有消失。

## 可核验事实

- 初始数据包含 6,000 个真实会话、63,000 多条用户提示和 355,000 次工具调用。
- 41% 会话中 Agent 几乎编写全部最终提交代码，23% 中代码完全由人类编写。
- 只有 44% 的 Agent 生成代码最终保留到提交；44% 的轮次出现纠正、失败反馈或中断。
- 论文报告样本中 Agent 代码引入的安全漏洞多于人类代码。

## CI/CD 相关性

- 涉及阶段：代码检查、安全扫描、测试门禁。
- 工具类别：开发 Agent、人机交互与效果评测。
- 自主等级：L1—L2。
- 涉及角色：开发者、代码评审者、质量与安全工程师。

## 对洞察的价值

它提示企业同时度量代码保留率、人工纠正率和下游缺陷，而非只统计 Agent 生成量。

## 限制与待验证项

- 公开会话存在自选择偏差，不能代表大型企业。
- 研究主要止于提交，未追踪构建、发布和生产结果。

## 可引用判断

- Agent 产出量不是交付效果；可纠正性、代码保留率和下游风险更接近真实工程价值。

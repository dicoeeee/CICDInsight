---
title: Atlassian Rovo Dev Code Reviewer Study
source_id: atlassian-rovo-code-review-2026-04-07
organization: Atlassian
source_type: official-engineering-practice
published: 2026-04-07
verified: 2026-07-14
availability: ga
confidence: medium
geography:
  - global
lifecycle_stages:
  - code-review
tool_categories:
  - code-review
  - human-agent-collaboration
  - measurement
company_topics:
  - Atlassian
  - Rovo Dev
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Atlassian Rovo Dev Code Reviewer Study

## 来源

- 标题：30.8% Faster PRs: How AI-Driven Rovo Dev Code Reviewer Improved Developer Productivity at Atlassian
- 组织或项目：Atlassian
- 发布日期：2026-04-07
- 链接：[Atlassian Engineering Blog](https://www.atlassian.com/blog/artificial-intelligence/developer-productivity-improved-with-rovo-dev)
- 来源类型：官方内部实践与论文结果介绍
- 能力状态：Rovo Dev Code Reviewer 于 2025-10 GA

## 一句话结论

Atlassian 的大规模在线评估显示代码评审 Agent 可以缩短 PR 周期并减少人工评论量，但产品仍定位为 Human-in-the-loop 评审伙伴。

## 可核验事实

- 评估持续一年，覆盖 1,900 多个代码仓。
- 官方报告 PR Cycle Time 缩短 30.8%，人工评审评论减少 35.6%。
- Agent 关注质量、安全、性能和可维护性问题。
- Atlassian 明确说明该系统不是替代人类判断，而是自动化部分评审工作。
- 相关研究被 ICSE 2026 接收；博客未提供完整统计模型和分层结果。

## CI/CD 相关性

- 涉及阶段：代码评审和合并前质量控制。
- 工具类别：Agent Review、人机协作和工程效能度量。
- 自主等级：L1—L2。
- 涉及角色：开发者、评审者、研发效能团队和工程管理者。

## 对洞察的价值

这是少数提供大规模内部在线效果数据的企业案例，可用来讨论人员工作量变化和评审瓶颈，而不只比较产品功能。

## 限制与待验证项

- 效果数据由 Atlassian 自己发布，需要阅读正式论文核验样本选择、对照方法和副作用。
- 评论减少不自动等于缺陷发现能力提高。

## 可引用判断

- 代码评审 Agent 已出现大规模效果证据，但其成熟工作模式仍是 Agent 扩展筛查、人类保留最终判断和责任。


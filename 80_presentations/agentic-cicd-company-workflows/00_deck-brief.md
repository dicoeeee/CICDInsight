---
title: Agentic CI/CD 公司与能力作业流 - Deck Brief
aliases:
  - Agentic CI/CD Company Workflows Deck
tags:
  - research/agentic-cicd
  - deliverable/presentation
status: draft
---

# Agentic CI/CD 公司与能力作业流

> [!todo] 待共同确认
> 演示的具体受众、使用场合、页数和展示时长尚未定稿。

## 工作标题

Agent 如何重构 CI/CD：主要公司与能力作业流洞察

## 沟通任务

通过统一的作业流口径，帮助受众看清不同公司或能力块如何获取上下文、安排 Agent 决策、调用确定性系统、验证结果并控制行动权。

## 当前核心结论

2026 年 Agentic CI/CD 的竞争重点不是单个模型或 Copilot，而是能否将“上下文、Agent 规划、工具执行、外部验证与治理边界”组成可审计的作业流。

## 内容组织原则

- 全生命周期或控制面较完整的平台：一家公司一页。
- 专业厂商证据集中的领域：一个大能力块一页，页内放代表厂商。
- 每页只表达一个主张，不追求罗列该公司的全部产品。
- 每页必须区分 GA、Preview、Beta、EA 和研究或路线图能力。
- 自治等级按具体任务判定，不给公司或平台贴统一的自治标签。

## 页面必须回答的六个问题

1. 什么事件或人类意图触发作业？
2. Agent 获取什么上下文？
3. Agent 负责判断、规划或生成什么？
4. 哪个确定性系统真正执行？
5. 谁验证结果并决定能否继续？
6. 自治停在哪里，当前证据成熟到什么程度？

## 生成输入与页面内容稿

- [[80_presentations/agentic-cicd-company-workflows/20_slide-template|作业流单页模板]]：定义页面字段、主作业流、三条泳道、页底洞察和来源要求。
- [[80_presentations/agentic-cicd-company-workflows/30_insight-slide-generation-prompt|洞察页生成方法与提示词]]：总结内容方法，并提供后续公司页或能力页可直接复用的参数化提示。
- [[80_presentations/agentic-cicd-company-workflows/slides/01-github|GitHub Agentic Workflows 页面分析]]：首个完成内容归档、等待视觉设计的示例页面。
- [[80_presentations/agentic-cicd-company-workflows/slides/02-harness|Harness CI 页面分析]]：解释 Agent Step、确定性复验与运行时权限边界如何组合。

## 上游研究入口

- [[20_summaries/companies/README|公司维度总结]]
- [[30_summaries/stages/README|八阶段维度总结]]
- [[40_summaries/crosscutting/README|横向变化总结]]
- [[90_report/seven-dimension-analysis|七维分析汇总报告]]

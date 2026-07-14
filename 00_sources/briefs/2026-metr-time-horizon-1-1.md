---
title: METR Time Horizon 1.1
source_id: metr-time-horizon-2026-01-29
organization: METR
source_type: original-research
published: 2026-01-29
verified: 2026-07-14
availability: research
confidence: medium
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-evaluation
  - autonomy-governance
company_topics: []
autonomy_levels:
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# METR Time Horizon 1.1

## 来源

- 标题：Measuring AI Ability to Complete Long Tasks — Time Horizon 1.1
- 发布日期：2026-01-29
- 链接：[METR 官方研究](https://metr.org/blog/2026-1-29-time-horizon-1-1/)
- 来源类型：原始评测研究与公开分析数据
- 能力状态：研究

## 一句话结论

Agent 可独立完成的任务时长增长很快，但结果高度依赖任务集和脚手架，因此自治授权应绑定本企业任务评测，而不是绑定模型品牌。

## 可核验事实

- 研究扩展到 228 个任务、14 个模型，其中 31 个任务超过 8 小时。
- 对 2023 年后模型的 50% task-time-horizon 翻倍时间估计约为 131 天。
- 任务集和 Agent 脚手架变化会显著改变部分模型结果。

## CI/CD 相关性

- 涉及阶段：跨阶段自治授权与运行时治理。
- 工具类别：Agent 评测、风险分级。
- 自主等级：L2—L4。
- 涉及角色：平台工程、安全、风险治理和工程管理者。

## 对洞察的价值

可用于设计动态权限：只让 Agent 在已证明能稳定完成的任务长度、工具范围和风险等级内自治。

## 限制与待验证项

- 长任务中仅少数有人类实测时间，其余依赖估算。
- 任务以软件、机器学习和安全为主，不能直接外推生产发布。

## 可引用判断

- 自治等级应由任务级证据持续校准，而非一次性按模型或产品定级。

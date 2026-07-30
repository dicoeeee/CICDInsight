---
title: 大模型时代的 CI/CD 基础设施单页汇报
aliases:
  - LLM-era CI/CD Infrastructure One-page Deck
tags:
  - research/agentic-cicd
  - deliverable/presentation
  - technology/infrastructure
status: active
as_of: 2026-07-29
---

# 大模型时代的 CI/CD 基础设施单页汇报

> [!abstract] 沟通任务
> 让 CTO、研发效能负责人和平台工程负责人理解：大模型没有替代 CI/CD，而是让基础设施形成“Agent 可调用的上下文与执行面 + 模型不可篡改的确定性控制面”；企业应先建设验证、身份和证据底座，再扩大 Agent 的写权限。

## 页面范围

- **受众：** CTO、研发效能负责人、平台工程负责人。
- **页数：** 1 页。
- **页面类型：** 技术洞察与决策启示混合页。
- **主要专题：** [[50_deepdives/llm-era-cicd-infrastructure/README|大模型时代的 CI/CD 基础设施专题]]。
- **证据门禁：** 专题已完成逐主张事实审计，`presentation_ready: true`。
- **阻塞状态：** 无。
- **观察口径：** 截至 2026-07-28 的产品、规范与研究证据。

## 核心主张

大模型没有替代 CI/CD，而是把它推向“双层控制”：Agent 负责理解、尝试和生成候选，基础设施负责验证、授权和发布。

## 不支持的更强表述

- Agent 已经替代代码仓、流水线、构建系统或制品仓。
- MCP、Skill、CLI 或 Agent 可调用后端能力，就天然获得业务授权。
- 行业已普遍实现无人值守的 Merge、Promotion 或生产发布。

## 页面阅读逻辑

1. 用“双层控制”解释总体结构变化；
2. 按代码仓、流水线、构建系统、制品仓比较 Agent 面与确定性控制面的职责；
3. 用身份、证据和成本三个横切变化说明为什么治理必须同步升级；
4. 以“先补底座、再放权”给出企业演进顺序。

## 视觉与对象策略

- **视觉基线：** `/Users/zhujiayi/.codex/skills/build-insight-pptx/assets/insight-process-table-template.pptx`。
- **复用模式：** style reuse；只复用白底、红色结论标题、灰色原生表格和红色页底洞察的视觉语言。
- **主体：** 原生 PowerPoint 表格与 Shape，不将整页或主体栅格化。
- **字体：** Microsoft YaHei。
- **组合：** 按 [[80_presentations/PPTX-editability-standard|PPTX 原生可编辑性与交付验收规范]]，所有非占位符页面对象必须可选择、移动、调整大小、编辑并组合。

## 交付物

- [[80_presentations/llm-era-cicd-infrastructure/slides/01-dual-control-plane|页面契约与文案]]
- [[80_presentations/llm-era-cicd-infrastructure/source-map|页面来源映射]]
- `outputs/llm-era-cicd-infrastructure-dual-control-plane.pptx`

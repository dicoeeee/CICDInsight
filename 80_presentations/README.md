---
title: Agentic CI/CD 演示文稿
aliases:
  - Agentic CI/CD Presentations
tags:
  - research/agentic-cicd
  - deliverable/presentation
status: active
as_of: 2026-07-24
---

# Agentic CI/CD 演示文稿

> [!abstract] 目的
> 本目录基于已经完成且标记为 `presentation_ready: true` 的 Deep Dive，管理 PPT 的叙事结构、页面文案、视觉意图和来源映射。研究事实与详细论证由专题深研维护，分类总结和 Source Brief 只作为辅助证据。

## 内容边界

- 本目录存放：演示目标、受众、页序、单页主张、作业流、图示说明、成熟度标签与来源映射。
- `50_deepdives/` 存放：技术原理、证据矩阵、详细案例、风险和 Playbook。
- `90_report/` 存放：跨维度决策结论和完整报告。
- `outputs/` 存放：完成渲染的 PPTX、HTML、PDF 和预览图。

> [!important] 维护原则
> PPT 不建立第二套事实源。每个正式公司页或功能页必须至少有一个 Presentation-ready Deep Dive 作为主要分析来源；页面文案可以持续改写，但不能绕过专题直接从单条来源生成结论。

补充洞察应先沉淀到 [[50_deepdives/README|专题深研层]]。没有对应专题或专题尚未达到 Presentation-ready 时，页面只能保留中性选题、阻塞原因和研究缺口，并标记为 `blocked-by-deep-dive`；不得预写页面主张、作业流或正式论证。当专题更新改变现有页面的主张、作业流、产品状态、自治或成熟度、控制边界、企业启示或来源映射时，再同步修改相关 `slides/*.md` 与 Source Map。

## 状态字段

- Deep Dive README 的 `presentation_ready: true/false` 是唯一汇报就绪事实源。
- Slide 的 `content_status` 只描述页面内容阶段，使用 `blocked-by-deep-dive`、`deep-dive-ready` 或 `content-archived`。
- Slide 的 `visual_status` 只描述视觉阶段，使用 `blocked`、`pending` 或 `complete`。
- Slide 不重复保存 Deep Dive readiness；非阻塞页面的 `primary_deep_dive` 必须能回链到专题 README，阻塞页面将该字段留空。

## 当前演示项目

- [[80_presentations/agentic-cicd-company-workflows/00_deck-brief|Agentic CI/CD 公司与能力作业流]]

## 成品交付规范

- [[80_presentations/PPTX-editability-standard|PPTX 原生可编辑性与交付验收规范]]：所有后续 PowerPoint 成品必须保持原生文本与对象，支持修改字体颜色、自由组合和取消组合，并在交付前检查 `noGrp`、文本锁、图片化、越界与渲染结果。

## 建议工作流

1. 检查候选页面是否存在 `presentation_ready: true` 的 Deep Dive；缺少时保持阻塞。
2. 在 Deck Brief 中明确受众、沟通任务和核心结论。
3. 在 Slide Outline 中决定页序和每页唯一主张。
4. 按统一 Slide Template 从 Deep Dive 提炼页面内容。
5. 在 Source Map 中校验主要专题、辅助总结、产品状态、自治等级和证据口径。
6. 完成文案审核后，再进入视觉制作和渲染。
7. PPTX 成品按 [[80_presentations/PPTX-editability-standard|原生可编辑性规范]]完成自动检查与 PowerPoint 抽检，再归档到 `outputs/`。

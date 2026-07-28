---
title: Agentic CI/CD 作业流单页模板
tags:
  - research/agentic-cicd
  - deliverable/presentation
  - template
status: draft
---

# 作业流单页模板

## 1. 页面标识

- **主要 Deep Dive：** 必须指向一个 `presentation_ready: true` 的专题；缺少时页面状态为 `blocked-by-deep-dive`。
- **专题观察日：** 使用 Deep Dive 的 `as_of`，页面不得把旧证据写成当前状态。
- **横向维度：** 公司 / 生态 / 大能力块
- **一句话主张：** 不使用纯产品名标题，直接写这页想让受众记住的判断。
- **覆盖阶段：** CI/CD 1—8 阶段中的具体范围。
- **最高可信自治：** 按页面中的具体任务标记 L0—L4。
- **产品状态：** GA / Preview / Beta / EA / Research。
- **证据类型：** 文档、开源实现、签名案例、第一方研究或独立研究。

## 2. 主作业流

建议控制在 4—6 个横向步骤：

1. **触发：** 人类意图、PR、Pipeline 失败、安全发现、发布事件或生产告警。
2. **上下文：** 代码、工作项、构建日志、测试、制品、策略、拓扑或遥测。
3. **Agent 决策：** 调查、归因、计划、生成候选变更或选择工具。
4. **确定性执行：** Actions、Pipeline、CLI/API/MCP、Runner、Scanner、Test Engine 或 Runbook。
5. **外部验证：** Test、Scan、Policy、Signature、SLO、Approval 或人工复核。
6. **输出与回流：** Comment、Draft PR、Plan、Evidence Pack、Approved Action、Rollback 或停止升级。

## 3. 三条泳道

| 泳道 | 内容 |
|---|---|
| 任务泳道 | 事件、人类意图、中间交付物与最终输出 |
| Agent 与平台泳道 | 上下文、Agent、专业工具、Pipeline 和执行环境 |
| 治理泳道 | 身份、权限、沙箱、Policy、Oracle、Approval、预算和回退 |

## 4. 页底洞察

- **核心差异化：** 这家公司或这类能力真正不同的是什么？
- **企业启示：** 可复用的架构或流程原则是什么？
- **当前边界：** 哪些能力仍不能作为生产基线？

## 5. 来源要求

- 主要分析来源必须是 Presentation-ready Deep Dive；分类总结用于跨页比较，Source Brief 用于核对事实。
- 每个成熟度、自治等级和产品状态都必须有上游链接。
- 供应商量化数据必须标记为厂商自述或第一方研究。
- 页面只展示必要来源，完整映射统一维护在 [[80_presentations/agentic-cicd-company-workflows/source-map|Source Map]]。
- 页面制作中发现新事实或证据缺口时，先更新 Deep Dive，再回到页面；不得直接在 Slide 文件建立孤立结论。

## 6. Agent 生成输入

本模板只定义页面应该包含的字段。需要生成新的公司页或能力页时，使用 [[80_presentations/agentic-cicd-company-workflows/30_insight-slide-generation-prompt|洞察页生成方法与提示词]]，先完成主张、论证链、组件阶段映射和排除项，再将结果归档到对应的 `slides/<编号>-<对象>.md`。

## 7. PPTX 成品要求

视觉设计和 PowerPoint 生成完成后，必须按 [[80_presentations/PPTX-editability-standard|PPTX 原生可编辑性与交付验收规范]]验收。由我们生成的文本、框、表格、图表和连线必须保持原生对象；用户应能修改字体颜色、移动对象并自由组合或取消组合。带有 `noGrp`、`noTextEdit`、`noSelect` 锁，或把页面主体图片化的文件不能标记为 `visual_status: complete`。

---
title: Alibaba Cloud DevOps AI Code Review
source_id: alibaba-yunxiao-ai-code-review-2025-10-2026-05
organization: Alibaba Cloud
source_type: official-release-notes
published: 2025-10-01
verified: 2026-07-14
availability: ga
confidence: high
geography:
  - china
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
tool_categories:
  - source-control
  - code-review
  - governance
company_topics:
  - Alibaba Cloud
  - Yunxiao
autonomy_levels:
  - L0
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Alibaba Cloud DevOps AI Code Review

## 来源

- 标题：云效产品功能及文档的更新动态；代码评审 AI 助手
- 组织或项目：阿里云云效 Codeup
- 首次相关发布：2025-10；2026-03 和 2026-05 持续增强
- 链接：[云效月度更新](https://help.aliyun.com/zh/yunxiao/product-overview/monthly-product-update-overview)、[代码评审 AI 助手](https://help.aliyun.com/zh/yunxiao/user-guide/ai-intelligent-code-review)
- 来源类型：官方发布记录与产品文档
- 能力状态：高级版正式能力

## 一句话结论

云效把 AI 评审嵌入 MR 流程，并通过组织级规则、严重度、跨文件沙箱分析和发布流程卡点，把 AI 发现逐步转化为可治理的质量门禁输入。

## 可核验事实

- 2025-10，Codeup 合并请求流程加入 AI 智能评审。
- 2026-03，支持组织级全局代码评审规则。
- 2026-05，相关更新继续增强 AI 评审；应用交付流程可检查代码是否经过评审，未评审代码不允许发布。
- 仓库可用 `.aliyun/code/code_review.yaml` 配置忽略文件、语言、问题级别、评审模式和路径规则。
- 可选沙箱支持跨文件变更检测，识别返回值语义、异常处理和参数顺序等破坏性变化。
- 输出包括变更摘要、时序图、行级问题、严重度和修复建议。

## CI/CD 相关性

- 涉及阶段：代码评审、静态质量检查、发布前门禁。
- 工具类别：代码仓、AI Review、组织级规则和流程卡点。
- 自主等级：L0—L2。
- 涉及角色：开发、评审者、代码仓管理员、研发效能和发布治理人员。

## 对洞察的价值

这是中国一体化 DevOps 平台把 AI Review 从建议能力推进到组织规则和流程卡点的代表。

## 限制与待验证项

- 文档没有把该能力称为可独立规划和调用多工具的 Agent，部分能力更接近 L0—L1。
- 缺少客户采用率、误报率和对评审周期的量化数据。

## 可引用判断

- 中国平台当前较成熟的路径之一，是先把 AI 评审制度化为组织规则和交付卡点，再逐步增加跨文件推理与修复闭环。


---
title: 京东 JoyAgent 双 RAG 智能代码评审
source_id: jd-joyagent-dual-rag-2026-01-13
organization: JD
source_type: first-party-engineering-practice
published: 2026-01-13
verified: 2026-07-14
availability: internal-exploratory-practice
confidence: medium
geography:
  - china
lifecycle_stages:
  - stage-1
  - stage-2
tool_categories:
  - code-review
  - rag
  - organizational-knowledge
company_topics:
  - jd
  - joyagent
autonomy_levels:
  - L1
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# 京东 JoyAgent 双 RAG 智能代码评审

## 来源

- 标题：基于知识工程 & JoyAgent 双 RAG 的智能代码评审系统探索与实践
- 发布日期：2026-01-13
- 链接：[京东云开发者社区官方文章](https://developer.jdcloud.com/article/4424)
- 来源类型：中国大型企业第一方工程实践
- 能力状态：内部探索性实践

## 一句话结论

企业代码评审的核心难题不是 Prompt 长度，而是如何准确识别项目、检索业务语义和规则，并在送入模型前完成重排序。

## 可核验事实

- Webhook 解析 MR 和 Diff，最终通过 Coding 平台 API 发布行级评论。
- 系统结合代码知识工程 RAG 与 JoyAgent 知识库 RAG。
- 先识别项目类型，再按文件和代码结构语义分块，避免简单行数截断。
- 使用 BGE 重排序业务上下文与评审规则，减少不相关知识干扰。

## CI/CD 相关性

- 涉及阶段：代码评审、组织规则与质量检查。
- 工具类别：MR Review、RAG、业务知识、规则检索。
- 自主等级：L1。
- 涉及角色：开发者、Reviewer、业务架构与平台团队。

## 对洞察的价值

它具体展示了“上下文控制面”的中国企业实现：业务知识、项目身份和规则质量比简单扩大模型上下文更重要。

## 限制与待验证项

- 只有案例和架构，没有 Precision、Recall、误报率或缺陷逃逸率对照。
- 不应把该探索写成已证明自治评审效果。

## 可引用判断

- 企业 Agentic Review 的差异化来自组织知识检索与规则运营，而不是更长 Prompt。

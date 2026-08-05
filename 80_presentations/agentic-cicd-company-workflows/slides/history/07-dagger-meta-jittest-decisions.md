---
title: Slide 07 - Dagger and Meta JiTTesting Decision History
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide/history
status: active
as_of: 2026-08-03
---

# Slide 07 - Dagger and Meta JiTTesting Decision History

## 2026-08-03 · LLM 硬条件与新候选

- 用户指出 Google CI/CD Variants 相邻的 AI 是传统 ML，不是 LLM。
- 用户新增硬条件：“请继续洞察，需要有明确的llm参与或者适应于LLM的改变”。
- 新一轮候选研究覆盖 Meta JiTTesting/ACH/TestGen-LLM、Uber uReview、ByteDance LogSage、Alibaba AACR/SWE-CI、AWS Kiro + CodeBuild、Microsoft TestExplora，以及 Develocity、CloudBees、GitLab、Nx、CircleCI 等平台。
- O1 提案：保留 Dagger，以 Meta JiTTesting 替换 Develocity。理由是它明确由 LLM 生成与评估验证工件，并且“针对 Diff 临时生成测试”的机制不重复现有五页。
- Meta Deep Dive 尚未建立，页面保持 `blocked-by-deep-dive`；对象未获批准前不启动 Deep Dive，也不进入分析路径、语义、结构或视觉锁。

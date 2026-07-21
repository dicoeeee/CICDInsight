---
title: GitHub Agentic Workflows 研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - company/github
status: complete
as_of: 2026-07-15
---

# GitHub Agentic Workflows 研究边界

## 决策目标

回答平台团队是否应在 GitHub Actions 上引入 Agentic Workflows、如何安全试点，以及哪些复杂 CI/CD 场景值得采用。

## 研究范围

- 产品状态、安装条件、认证和日常使用；
- Markdown Workflow、Frontmatter、Compiler、Lock File 和 Actions Job 图；
- Engine、GitHub Tools、MCP、Sandbox、Network、Safe Outputs 与 Threat Detection；
- CI 故障、维护、安全、发布准备、多仓与编排场景；
- 版本、成本、审计、失败模式、组织共享和企业治理。

## 非目标

- 不把 GitHub Actions 的通用 YAML 语法重新写成教程；
- 不评测 Copilot、Claude、Codex、Gemini 的模型优劣；
- 不把 GitHub 官方示例当成独立生产效果证明；
- 不把生成 PR、触发 Worker 或 Threat Detection 等同于安全发布；
- 不实际安装扩展、创建 GitHub Secret 或运行付费 Agent。

## 观察口径

- 观察日：2026-07-15；
- Public Preview 能力单独标识，不外推为稳定 GA；
- 官方案例指标标记为 GitHub 自报；
- 实验计划与已执行结果严格分开。

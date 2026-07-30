---
title: GitHub Copilot Cloud Agent 将代码仓扩展为 Agent 控制面
source_id: github-cloud-agent-repository-control-plane-2026
organization: GitHub
source_type: official-docs-and-changelog
published: 2025-09-25
verified: 2026-07-28
availability: ga-with-preview-extensions
confidence: high
geography:
  - global
lifecycle_stages:
  - source-control
  - code-review
  - testing-gates
tool_categories:
  - source-control
  - coding-agent
  - ci-pipeline
  - agent-runtime-orchestration
  - governance
company_topics:
  - GitHub
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitHub Copilot Cloud Agent 将代码仓扩展为 Agent 控制面

## 来源

- [Copilot coding agent is now generally available](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)，GitHub Changelog，2025-09-25。
- [Using Copilot cloud agent on GitHub](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，GitHub Docs，无发布日期，访问于 2026-07-28。
- [Best practices for using GitHub Copilot to work on tasks](https://docs.github.com/en/copilot/tutorials/cloud-agent/get-the-best-results)，GitHub Docs，无发布日期，访问于 2026-07-28。
- [Start Copilot cloud agent tasks via the REST API](https://github.blog/changelog/2026-05-13-start-copilot-cloud-agent-tasks-via-the-rest-api/)，GitHub Changelog，2026-05-13。

## 一句话结论

GitHub 已把 Issue、Agent Session、临时 Actions 环境、Branch/PR、评论迭代和 Review 串成 Agent 工作闭环，使代码仓从 Source of Truth 扩展为 Agent 的任务、执行状态和治理控制面；但核心 Cloud Agent 已 GA，不代表 Agent Tasks API 等扩展也已 GA。

## 可核验事实

- Copilot coding agent 于 2025-09-25 对付费 Copilot 订阅正式可用；它接收任务后在自己的开发环境中工作，并通过 PR 请求人审。
- 任务可从 Issue、Agents 页面或 PR 评论进入；Agent 可在同一 PR Session 中根据评论继续迭代。
- Agent 使用 GitHub Actions 支撑的临时开发环境，可读取代码、修改文件、运行测试和 Linter。
- Agent 推送到 PR 后，GitHub Actions Workflow 默认不会自动运行；用户需要检查变更后批准运行，或显式配置无需人工批准。
- 2026-05-13 发布的 Agent Tasks REST API 为 Public Preview，可编程启动和跟踪任务，不应与核心 Cloud Agent 的 GA 状态混写。

## CI/CD 相关性

- **代码仓：** Issue/PR/评论成为任务队列、交付物和接管界面。
- **流水线：** Actions 同时承载 Agent 临时环境与正式 Workflow，但两者的触发和信任边界分开。
- **治理：** PR 是默认写回缓冲区；是否运行有 Secret 的 Workflow 由独立批准控制。
- **经济性：** Agent 工作会消耗 Actions 分钟与 AI Credits，代码仓平台开始承担运行成本归因。

## 对洞察的价值

该案例证明“代码仓成为 Agent 控制面”不是抽象趋势：任务、上下文、执行、审阅、审计与计费都已被收进仓库协作模型。它也证明 Agent 自我运行测试不等于正式 Required Checks 已经执行。

## 限制与待验证项

- GitHub 官方材料证明产品机制与状态，不证明跨组织的正确率或业务收益。
- 自动允许 Agent 推送后运行 Workflow 会扩大 Secret 和 Actions 权限风险，必须按仓库单独配置。
- 第三方 Coding Agents、Agent Tasks API 和部分自动化能力仍为 Preview，应逐项判断状态。

## 可引用判断

- 代码仓的结构性变化不是“多了一个聊天框”，而是增加了可调度的 Agent 身份、Session、隔离环境和受控写回路径。
- Agent 在临时环境中运行测试，只是候选变更的内部验证；正式 CI、Required Checks 和 Review 仍是独立 Oracle。

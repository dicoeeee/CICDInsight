---
title: GitHub Copilot Cloud Agent 问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/github
status: complete
as_of: 2026-08-07
---

# GitHub Copilot Cloud Agent 问题树

## Q1：产品是什么，处于什么状态

- "coding agent" 与 "cloud agent" 是什么关系，命名如何演进？
- 哪些套餐可用，Business/Enterprise 是否默认关闭？
- 哪些能力是 GA，哪些仍是 Public Preview（Agent Tasks API、第三方 agent、issue 指派等）？

## Q2：任务如何进入、如何执行

- 有哪些任务入口（Issue、Agents 页面、PR 评论、Chat、REST API、Automations）？
- Agent Session 的生命周期：启动、流式日志、steering、取消、超时？
- Agent 如何创建分支、提交、开 PR，如何在同一 PR session 内迭代？
- research→plan→iterate 与"直接开 PR"两种路径分别适用哪些入口？

## Q3：运行环境与工具机制

- 临时开发环境由什么支撑，如何定制（copilot-setup-steps.yml）？
- 模型选择与推理等级如何配置？
- MCP 工具与内置 GitHub/Playwright MCP 的默认配置与限制？
- 出网防火墙的作用域与绕过边界？

## Q4：权限与写回机制

- 哪些用户可以触发，触发后 Agent 以什么身份提交？
- 委托写回边界：单分支、不能 git push、不能 approve/merge 的含义？
- Agent push 后 Actions Workflow 默认是否运行，谁批准？
- 发起人为何不能 approve 自家 Agent 的 PR？

## Q5：与 CI/CD 门禁如何交互

- Required Checks、分支保护、ruleset 如何约束 Agent？
- "Fix with Copilot"、Dependabot alert → Agent 的闭环机制？
- Agent 在临时环境跑测试与正式 CI 是两个不同的 Oracle 吗？
- 生产发布场景的边界在哪里？

## Q6：安全和治理机制有哪些层

- 静态校验（CodeQL/Advisory/secret scanning）默认开启什么？
- Agent secrets 与 Actions/Codespaces secrets 如何隔离？
- 防火墙、Prompt injection 过滤各防什么、不防什么？
- org/企业策略、审计日志（actor_is_agent）、第三方 agent 策略如何配置？
- Automations 的置信度路由与审批面板机制？

## Q7：可编程控制面长什么样

- Agent Tasks REST API 的端点、状态机与认证要求？
- 管理 API 如何控制仓库 enablement？
- Automations 的触发类型与归属？
- session logs 与用量指标提供哪些可观测性？
---
title: ChatGPT Work、Codex、Projects 与 Workspace Agents 产品边界
source_id: openai-work-codex-workspace-agents-2026
organization: OpenAI
source_type: official-docs
published: 2026-07-09
verified: 2026-08-08
availability: mixed
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-runtime-orchestration
  - developer-workflow
  - governance
company_topics:
  - ChatGPT Work
  - Codex
  - Workspace Agents
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# ChatGPT Work、Codex、Projects 与 Workspace Agents 产品边界

## 一手来源

| 页面 | 页面日期/核验 | 直接支持的功能 |
|---|---|---|
| [Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work) | 页面未标日期；访问 2026-08-08 | Work 任务、Local/Cloud、文件/Plugin/批准工具、可审查结果 |
| [Long-running work](https://learn.chatgpt.com/docs/long-running-work) | 页面未标日期；访问 2026-08-08 | Goal、暂停/恢复/编辑、同一 Chat 上下文 |
| [Projects](https://learn.chatgpt.com/docs/projects) | 页面未标日期；访问 2026-08-08 | Chats、Files、Sources、Project Instructions |
| [Codex CLI](https://learn.chatgpt.com/docs/codex/cli) | 页面未标日期；访问 2026-08-08 | 在终端中读取、修改当前工程并运行命令 |
| [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | 页面未标日期；访问 2026-08-08 | Work/Codex 专业 Subagent、并行汇总、账户和冲突限制 |
| [Permission modes](https://learn.chatgpt.com/docs/permission-modes) | 页面未标日期；访问 2026-08-08 | 文件、命令和互联网权限模式 |
| [Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills) | 页面未标日期；访问 2026-08-08 | Workspace Skill、本地 Skill 与 Plugin 的分发边界 |
| [Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep) | 发布 2026-04-22；访问 2026-08-08 | Research Preview、App/Skill/计划/共享与管理员控制 |
| [Work admin FAQ](https://learn.chatgpt.com/docs/enterprise/work-admin-faq) | Work 发布 2026-07-09；访问 2026-08-08 | 计划、设置、RBAC、Surface 和来源权限 |
| [Compliance API](https://learn.chatgpt.com/docs/enterprise/compliance-api) | 页面未标日期；访问 2026-08-08 | 审计、调查、留存、Legal Hold 及非生产力分析边界 |

## 产品面事实

| 产品面 | 官方公开事实 | 状态/限制 |
|---|---|---|
| ChatGPT Work | 委派具有明确产物的多步骤任务；可使用文件、Plugins 和已批准工具；用户可查看进度、回答问题、改变方向并批准重要动作 | 已公开发布；具体能力受计划、设置与 Surface 影响 |
| Work Local/Cloud | Local 用于电脑文件/应用；Cloud 可在应用关闭或电脑关机后继续并从 Web/Mobile 恢复 | 两个执行面不能互相补全权限和资源 |
| Long-running Goal | 可设置目标、约束和完成定义，并暂停、恢复、编辑或清除 | Goal 不是 Pipeline 状态机 |
| ChatGPT Projects | 聚合 Chats、Files、Sources 和 Project Instructions；同一 Project 可含 Chat 与 Work | 不直接取得本地 Codex 目录 |
| Codex CLI | 在终端中读取、修改当前工程并运行命令；可调用 Codex Subagent | 当前工程上下文与 ChatGPT Project 分开；不把其他 Codex Surface 的能力补入 CLI |
| Subagents | Work 与 Codex 可并行运行专业 Subagent 并汇总 | Work 限 eligible accounts；消耗更多 Token；并行写入可冲突 |
| Skills/Plugins | Workspace Skills、本地 Skills 和 Plugins 有不同所有权、分享、安装与 Connector 授权 | 移动 Skill 不迁移完整治理状态 |
| Workspace Agents | 可配置 Apps、Skills、Instructions、计划任务和分享，管理员控制启用/构建/发布 | Research Preview；不回填为 Work/Codex GA 能力 |
| 管理与审计 | Workspace 身份/RBAC 控制访问；Compliance API 提供合规记录 | Compliance API 不是生产力 Dashboard，也不证明任务质量 |

## 不可外推边界

- Work、Projects、Codex、Subagents 和 Workspace Agents 是不同产品面。
- Workspace Agents 为 Research Preview。
- Subagent 结果、文件或代码差异不等于测试、合并或发布已经通过。
- 官方文档没有直接证明 ChatGPT Work 是 CI/CD 原生 Pipeline 或发布控制面。

## 专题入口

[[50_deepdives/agent-workbench/41_openai-product-boundaries|OpenAI 产品边界详章]]

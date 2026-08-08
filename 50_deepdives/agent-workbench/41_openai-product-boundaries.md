---
title: ChatGPT Work、Codex、Projects 与 Workspace Agents 产品边界
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# ChatGPT Work、Codex、Projects 与 Workspace Agents 产品边界

## 产品面与状态

| 产品面 | 官方定义或用途 | 状态边界 |
|---|---|---|
| ChatGPT Work | 委派有明确结果的较长任务并获得可审查结果 | 已公开发布；账户、Cloud、工具和管理员能力取决于计划与配置 |
| ChatGPT Projects | 组织 chats、files、sources 与 project instructions | ChatGPT 上下文容器，不等同本地代码目录 |
| Codex CLI | 在终端中读取、修改当前工程并运行命令 | 当前工程目录与 ChatGPT Project 是不同上下文 |
| Subagents | Work 与 Codex 可并行调用专业 Subagent 并汇总结果 | Work 仅适用于 eligible accounts；Codex 当前默认启用；并行写入存在冲突风险 |
| Workspace Agents | 可连接 Apps、Skills，支持计划运行、共享和管理员配置的 Workspace Agent | 官方 Cookbook 标为 Research Preview；不能回填为 Work 或 Codex 的 GA 功能 |

来源：[Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work)、[Projects](https://learn.chatgpt.com/docs/projects)、[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)

## ChatGPT Work：入口、任务与产物

- Work 用于 brief、deck、analysis、recurring update、workflow 或 file 等具有明确结果的任务。它可以使用文件、Plugins 和已批准工具，用户可查看进度、回答问题、改变方向并批准重要动作。[Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work)
- 桌面端可选择 Work locally 或 Cloud。Cloud 可在应用关闭或电脑关机后继续，并支持从 Web/移动端继续；Local 用于需要电脑文件或应用的任务。
- 长任务可用 `/goal` 设置、编辑、暂停、恢复或清除目标；同一 Chat 保留任务上下文，互相独立的工作应使用不同 Chat。[Long-running work](https://learn.chatgpt.com/docs/long-running-work)
- Work 的交付物是可审查文件、分析或工作流结果。官方明确设计了用户审查和重要动作批准，因此产物不能写成自动业务接受。

## ChatGPT Projects：项目上下文

- Project 将 chats、files、sources 与 project instructions 放在同一上下文中；Chat 与 Work 可以位于同一个 ChatGPT Project。[Projects](https://learn.chatgpt.com/docs/projects)
- Project 内的文件和指令用于该项目的会话。ChatGPT Project 不直接取得本地 Codex CLI 的当前工程目录。
- 不同 Project 之间的上下文和分享规则按 ChatGPT 产品配置执行，不能把 Codex CLI 的当前工程文件或命令执行写成 Project 功能。

## Codex：工程上下文与执行面

- Codex CLI 在终端中读取、修改当前工程并运行命令；该工程目录不是 ChatGPT Project 的上下文。[Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- Codex 可调用专业 Subagent；本地文件系统 Skill 与 ChatGPT Workspace Skill 属于不同分发面。[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)

## Subagents：并行协作

- ChatGPT Work 与 Codex 可启动专门 Subagent，并在主任务中汇总结果；界面显示 Subagent 活动。[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- Codex 支持为自定义 Agent 配置模型和 Instructions。Work 的 Subagent 可用性受 eligible account 限制。
- 官方提示并行 Agent 消耗更多 Token，且多个 Agent 同时写相同文件可能产生冲突。该机制不能被表述为自动提高质量或成功率。

## Skills、Plugins 与 Workspace Agents

- OpenAI 文档将 ChatGPT Workspace Skills、Local filesystem Skills 和 Plugins 作为不同分发/管理面；移动 Skill 不会自动迁移所有权、分享、角色、Plugin 安装或 Connector 授权。[Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)
- Workspace Agents 可组合 Instructions、Apps 与 Skills，支持计划运行、共享和 Builder Preview；Workspace Admin 可控制 Agent 创建、分享、发布和 App/动作范围。[Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)
- Workspace Agents 使用最终用户对连接服务的个人认证；管理员可以把连接器限制为只读或只允许特定动作。Research Preview 状态必须随主张保留。

## 权限、管理员与审计

- Work 权限模式包括 Ask for approval、Approve for me、Full access 和 Custom；它们控制文件编辑、命令和互联网等操作边界，组织管理员可禁用部分模式。[Permission modes](https://learn.chatgpt.com/docs/permission-modes)
- Enterprise 管理员通过身份、角色和 Workspace 设置控制 Work 访问；可用 Apps、网络和操作范围取决于计划、设置、来源权限和运行 Surface。[Work admin FAQ](https://learn.chatgpt.com/docs/enterprise/work-admin-faq)
- Compliance API 提供用于审计、调查、留存和 Legal Hold 的合规记录；官方明确它不是生产力分析 Dashboard，使用分析由 Workspace Analytics 等独立功能承担。[Compliance API](https://learn.chatgpt.com/docs/enterprise/compliance-api)

## 端到端功能流程

### ChatGPT Work

`选择 Local/Cloud → 在 Chat 或 Project 中提交结果、约束与材料 → 设置长任务 Goal → Work 使用文件、Plugins 和已批准工具 → 启动 Subagent（若账户可用）→ 用户查看进度、补充信息或改变方向 → 审查文件/分析/工作流结果并批准重要动作`

### Codex

`在终端打开工程目录 → Codex CLI 读取工程文件并执行代码/命令任务 → 可调用自定义 Subagent 或本地 Skill → 查看差异和命令结果 → 用户决定是否接受代码变更`

## 明确限制

- Work、Codex、Projects 和 Workspace Agents 不共享完全相同的上下文、可用账户、工具和管理员能力。
- Workspace Agents 是 Research Preview，不能作为 GA 治理功能写入其他产品面。
- Subagent 的并行结果不是代码合并、测试通过或生产发布授权。
- 官方产品文档没有直接证明 ChatGPT Work 是 CI/CD 原生 Pipeline 或发布控制面。

## 主要证据入口

- [[00_sources/briefs/2026-openai-chatgpt-work-codex-workspace-agents|OpenAI Source Brief]]
- 本章只使用 [OpenAI 官方文档](https://learn.chatgpt.com/docs/get-started-with-work) 域内来源。

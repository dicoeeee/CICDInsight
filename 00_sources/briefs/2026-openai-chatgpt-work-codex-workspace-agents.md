---
title: ChatGPT Work、Codex 与 Workspace Agents 的工作台和治理边界
source_id: openai-work-codex-workspace-agents-2026
organization: OpenAI
source_type: official-docs-and-release-notes
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

# ChatGPT Work、Codex 与 Workspace Agents 的工作台和治理边界

## 来源

- 标题：ChatGPT Release Notes、Subagents、Projects and chats、Building workspace agents
- 组织或项目：OpenAI
- 发布或更新日期：ChatGPT Work 于 2026-07-09 发布；Workspace Agents Cookbook 于 2026-04-22 发布；2026-08-08 复核
- 链接：[ChatGPT Work 发布说明](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)、[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Projects and chats](https://learn.chatgpt.com/docs/projects)、[Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)
- 来源类型：官方产品文档、帮助中心与官方 Cookbook
- 能力状态：ChatGPT Work 已正式发布但按计划与账户分批开放；Codex 当前版本默认支持 Subagent workflow；Workspace Agents 为 Research Preview，面向获得访问资格的 Business、Enterprise、Edu 工作区

## 一句话结论

OpenAI 的当前产品线分别证明了长任务工作面、项目上下文、专业 Subagent 协作，以及由管理员控制构建、分享、发布、App 和角色权限的组织级供给面；这些能力必须按 ChatGPT Work、Codex、Workspace Agents 三条产品边界解释。

## 可核验事实

- ChatGPT Work 面向更长、更复杂的任务，可跨已连接的 App 和文件工作，创建文档、表格、演示、报告和 Sites；用户可观察进度、回答问题、改变方向并批准重要动作。
- ChatGPT Work 和 Codex 可运行 Subagent workflow：生成专业 Agent 并行处理独立工作，再由主会话汇总；ChatGPT Work 只向合资格账户暴露该能力，Codex 当前版本默认启用。
- 本地 Codex 可为不同任务定义具有不同模型配置和指令的自定义 Agent；产品界面可展示各 Subagent 线程及返回摘要。
- ChatGPT Project 将聊天、文件、项目指令和连接来源放在共享上下文中；同一项目可包含 Chat 和 ChatGPT Work 会话。Codex 则以目录或 IDE Workspace 作为本地项目上下文。
- Workspace Agents 是独立的 Research Preview 能力，可配置 Apps、Skills、指令和计划任务，并在组织工作区中分享。
- Workspace owner/admin 可启用 Agent，用 RBAC 控制谁能构建、分享和发布，并启用所需 Apps；Builder 可以生成 Skill、预览测试并迭代配置。
- Subagent 会增加模型和工具使用量；官方建议对并行写任务更加谨慎，因为它可能造成冲突和协调成本。

## CI/CD 相关性

- ChatGPT Work 证明“目标—上下文—过程观察—产物审查”的通用工作台入口；Codex 证明同一交互模式可深入代码库，并由专业 Subagent 分工。
- Workspace Agents 证明组织可以把 Apps、Skills、指令、测试预览、分享与发布权限沉淀为后台供给和治理能力。
- 三者都不直接证明 CI/CD 最终门禁已经被 Agent 取代；生产发布仍需要仓库、流水线、身份、策略、签名、审批、SLO 和回滚系统的外部接受。
- 涉及角色：开发者/知识工作者、Agent Builder、Workspace Admin、应用与权限管理员。

## 对洞察的价值

这组产品机制提供了第二个独立证据族：使用者可以在一个工作面提交复杂目标并观察并行工作，而管理员/Builder 在另一层配置上下文、Skill、Agent、App 和发布权限。它支撑“消费入口与能力供给相分离”的结构判断，但不证明企业岗位已经完成迁移。

## 限制与待验证项

- ChatGPT Work 的 Subagent workflow、桌面能力和各端开放范围受账户、计划、工作区策略和分批发布影响，不能写成所有用户默认可用。
- Workspace Agents 仍为 Research Preview，不能写成 GA 企业控制面。
- ChatGPT Work、Codex 与 Workspace Agents 是不同产品面；不能把 Workspace Agents 的 RBAC 和发布治理自动归到每一个 Work 会话。
- 官方材料未给出 CI/CD 发布成功率、跨企业采用率、岗位比例或人员结构变化数据。

## 可引用判断

- 工作台可以把复杂 Agent 任务前台化给最终用户，同时把 Agent、Skill、上下文和发布权限留给后台 Builder/Admin 管理。
- Subagent 协作能够扩大任务形状，也会增加成本与协调风险；专家团必须由评测和任务边界驱动。
- OpenAI 产品机制能够支撑目标 operating model，不能单独证明发布与运维岗位已普遍转型。

---
title: Agent 工作台产品功能事实摘要
tags:
  - research/agentic-cicd
  - research/findings
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台产品功能事实摘要

## 1. 三个通用工作台公开了不同的任务与项目模型

- WorkBuddy 以 Task 和 Project 组织工作，公开任务状态、项目指令、资料、专家、Skill、连接器和项目资产库。[WorkBuddy 项目](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project)
- ChatGPT Work 以 Chat、Project 和 Goal 组织长任务，并区分 Local 与 Cloud；Codex CLI 在终端中读取、修改当前工程并运行命令。[ChatGPT Work](https://learn.chatgpt.com/docs/get-started-with-work)、[Projects](https://learn.chatgpt.com/docs/projects)、[Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- Claude Cowork Project 使用本地文件、Instructions、Context 和 Project-scoped Memory；当前不支持 Cloud Sync、Team/Enterprise Project Sharing 或 Claude Code 使用。[Cowork Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)

这些对象名称相似但数据范围、执行位置和分享能力不同。

## 2. 专业 Agent 协作至少存在四种公开形态

| 产品 | 公开协作形态 | 已知限制 |
|---|---|---|
| WorkBuddy | 团长拆解，多个专家并行执行并整合 | 未公开依赖图、失败恢复和隔离细节 |
| ChatGPT Work / Codex | 主任务启动专业 Subagent 并汇总 | Work 限 eligible accounts；并行写入可能冲突 |
| Claude Cowork | Plugin 可打包并调用只在 Cowork 运行的 Subagents | 官方资料未公开通用任务图或 CI/CD 协作模型 |
| GitLab Duo Agent Platform | Custom Flow 组合多个 Agent 完成开发任务 | 不同 Flow 与治理功能的生命周期不同 |

来源：[WorkBuddy 专家](https://cloud.tencent.com/document/product/1831/134393)、[OpenAI Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Cowork Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)、[GitLab Platform](https://docs.gitlab.com/user/duo_agent_platform/)

## 3. Skill、Plugin、Connector 和 Agent Catalog 是不同配置对象

- WorkBuddy Skill 封装脚本和工作流，Connector 封装认证、MCP/CLI 与工具过滤，Expert 描述角色和方法。[WorkBuddy 技能](https://cloud.tencent.com/document/product/1831/134432)
- OpenAI 将 ChatGPT Workspace Skills、本地文件系统 Skills 和 Plugins 分开管理；所有权、分享、安装和 Connector 授权不会随文件移动自动迁移。[OpenAI Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)
- Claude Plugin 将 Skills、Connectors、Hooks 和 Subagents 打包；组织可通过 Marketplace 分发。[Claude Plugins](https://support.claude.com/en/articles/13837433-manage-plugins-for-your-organization)
- GitLab 支持 `AGENTS.md`、Agent Skills、Custom Flow 和 MCP 配置；Harness Inc. 使用版本化 Agent Definition 与 Pipeline Inputs。[GitLab Customize](https://docs.gitlab.com/user/duo_agent_platform/customize/)、[Harness Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)

配置对象可安装、可引用或可调用，不代表它已获得外部系统权限或通过质量验证。

## 4. 产物从文件延伸到仓库对象和 Pipeline 变量

| 产物类型 | 产品事实 |
|---|---|
| 文件、变更和预览 | WorkBuddy 右侧栏分开展示产物、Workspace File、Change 和 Preview |
| 文件、分析、工作流结果 | ChatGPT Work 返回可供用户审查和使用的结果 |
| 文件、报告和定时任务历史 | Claude Cowork 按 Session 保存任务结果 |
| Commit、MR、Review、CI 修复 | GitLab Agent/Flow 在项目中产生开发对象 |
| Output Variable | Harness Inc. Worker Agent 把结构化值交给后续 Condition、Approval 或 Notification |
| Issue、Comment、PR | GitHub Agentic Workflow 通过 Safe Output 的隔离 Job 写入仓库 |

产物类型说明交接接口，不说明内容正确或已经通过后续 Gate。

## 5. CI/CD 原生接入集中在四个产品面

- GitLab UI Flow 通过 CI/CD Runner 执行，Fix CI/CD Pipeline Flow 和 Convert to GitLab CI/CD Flow 直接面向 Pipeline。[GitLab Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)
- Harness Inc. Worker Agent 是 Pipeline Step，输入来自 Pipeline Context，输出可传给后续 Step。[Harness Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)
- GitHub Agentic Workflows 将 Markdown 编译为 Actions `.lock.yml`，用声明式 Safe Output 提议 Issue、Comment 或 PR。[GitHub About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)
- Octopus Claude Agent Step 在 Deployment Process 或 Runbook 中运行 Claude Code，并保存 Task Log、Token、Cost 和 Transcript。[Octopus Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)

WorkBuddy、ChatGPT Work 和 Claude Cowork 的官方产品文档没有直接证明它们是 CI/CD 原生发布控制面。

## 6. 权限、身份和审计能力按执行面分开

- WorkBuddy 使用用户授权、权限模式、Connector 工具过滤和企业专家分发控制。
- ChatGPT Work 使用权限模式与 Workspace 角色；Compliance API 和 Workspace Analytics 是不同产品接口。
- Claude Cowork 区分 Local/Remote Session、文件夹授权、远程网络策略和 OpenTelemetry；Cowork Activity 当前不进入 Compliance API/Audit Logs/Data Export。
- GitLab 区分远程 Flow Composite Identity 与 IDE/CLI 用户身份，并按执行面提供 Sandbox、Network 和 Human-in-the-loop 控制。
- Harness Inc. 通过 Parent RBAC、Declared Grant、Connector/Agent Tool Allowlist 和 Runtime 隔离缩小权限。
- GitHub 将高权限 Secret 和写操作放在 Agent Runtime 外的隔离 Actions Job 中，仓库 Ruleset 继续独立执行。

## 7. 生命周期不能按平台整体继承

| 产品 | 必须保留的状态边界 |
|---|---|
| WorkBuddy | 产品正式发布；专家团、Skill、连接器等单项能力阶段未声明 |
| OpenAI | Workspace Agents 为 Research Preview；Work/Codex/Projects 分产品面记录 |
| Claude Cowork | Remote Beta；Computer Use Research Preview |
| GitLab | Platform 18.8 GA；部分 Tool Governance、Audit、CI Expert 等能力为 Beta/Experiment |
| Harness Inc. | 文档可用；子能力没有统一生命周期标签 |
| GitHub Agentic Workflows | Public Preview |
| Octopus Claude Agent Step | Alpha |

## 事实结论

截至 2026-08-08，可由官方资料直接确认的产品功能包括：长任务和项目上下文、专家/Subagent/Flow 协作、Skill/Plugin/Connector 扩展、文件与仓库产物、定时或事件触发、权限和管理员控制，以及在 GitLab、Harness Inc.、GitHub 和 Octopus 中出现的 CI/CD/部署原生 Agent 入口。

官方资料尚不能提供统一的跨产品质量、效率、成本或人员配置影响结论。

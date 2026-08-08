---
title: Agent 工作台产品功能与控制边界事实报告
tags:
  - research/agentic-cicd
  - research/report
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台产品功能与控制边界事实报告

## 执行摘要

截至 2026-08-08，官方产品资料可以直接确认三类产品形态：

1. **终端 Agent 工作台：** WorkBuddy、ChatGPT Work 和 Claude Cowork 提供长任务或项目入口，并可使用文件、项目指令、Skill/Plugin、连接器和专业 Agent 协作生成可审查产物。
2. **Agent 构建与治理平台：** Microsoft Copilot Studio、Google Gemini Enterprise Agent Platform、Atlassian Rovo 和 Amazon Bedrock AgentCore 提供不同范围的 Agent 定义、目录、工具、身份、运行、评测或管理员控制。
3. **CI/CD、仓库与部署原生 Agent：** GitLab Duo Agent Platform、Harness Inc. Worker Agents、GitHub Agentic Workflows 和 Octopus Claude Agent Step 把 Agent 直接放入开发 Flow、Pipeline、Actions Workflow 或 Deployment/Runbook Step。

这些产品使用不同的配置对象、运行环境、身份和生命周期。功能名称相似不表示实现或授权边界相同。

## 一、统一功能字段

本报告对每个产品记录：状态、入口、配置对象、输入与上下文、任务流程、Agent 协作、Skill/工具/连接器、触发、产物、权限、管理审计和限制。完整原子证据见 [[50_deepdives/agent-workbench/20_evidence-map|Evidence Map]]，十一产品横向对照见 [[50_deepdives/agent-workbench/30_case-map|Case Map]]。

## 二、WorkBuddy

### 任务与项目

WorkBuddy 的新任务入口可设置工作目录、模型、默认/Plan/Ask 模式、Skill、连接器和权限。任务管理公开进行中、已完成、失败、待处理、规划中和已归档等状态；任务对话支持补充需求、上传文件、查看中间步骤和停止/继续。[新建任务](https://cloud.tencent.com/document/product/1831/134391)、[任务管理](https://www.workbuddy.cn/docs/workbuddy/Task-Management)

Project 把指令、资料、连接器、专家和 Skill 组合为项目配置。任务产物可以保存到项目资料库，并作为后续任务的检索上下文。[Project](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project)

### 专家、Skill 与连接器

Expert 描述人设、方法论和工具链；Expert Team 由团长与多个专家组成，团长拆解任务、协调并行执行并整合结果。Skill 是可执行脚本/工作流包；Connector 可使用 OAuth/API Key 和 MCP/CLI，并配置工具过滤与超时。[专家](https://cloud.tencent.com/document/product/1831/134393)、[技能](https://cloud.tencent.com/document/product/1831/134432)、[连接器](https://cloud.tencent.com/document/product/1831/134525)

### 产物、权限与管理

任务详情把产物、工作区文件、变更和预览分开展示。默认权限模式会对敏感路径、删除、脚本/命令和网络等操作请求确认；Full Access 是独立模式。企业管理员可管理专家 ID、版本、启用状态、可见范围和白/黑名单策略。[右侧栏](https://cloud.tencent.com/document/product/1831/134400)、[权限模式](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Permission-Modes)、[企业专家](https://cloud.tencent.com/document/product/1831/134421)

### 状态和限制

WorkBuddy 4.5.0 已正式发布；专家团、Skill、连接器等页面没有单项生命周期标签。官方未公开专家团依赖图、失败补偿、隔离模型和成功率，也没有证明 WorkBuddy 是 CI/CD 原生发布控制面。详见 [[50_deepdives/agent-workbench/40_workbuddy|WorkBuddy 功能详章]]。

## 三、ChatGPT Work、Codex、Projects 与 Workspace Agents

### Work

ChatGPT Work 面向具有明确结果的任务，例如 brief、deck、analysis、recurring update、workflow 或 file。它可使用文件、Plugins 和已批准工具；用户可以查看进度、回答问题、改变方向并批准重要动作。[Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work)

Desktop 区分 Local 与 Cloud：Cloud 可在应用关闭或电脑关机后继续；Local 用于需要本地文件或应用的任务。长任务可用 `/goal` 管理目标。[Long-running work](https://learn.chatgpt.com/docs/long-running-work)

### Projects 与 Codex

ChatGPT Project 组织 chats、files、sources 和 project instructions。Codex CLI 在终端中读取、修改当前工程并运行命令；ChatGPT Project 不直接取得该工程目录。[Projects](https://learn.chatgpt.com/docs/projects)、[Codex CLI](https://learn.chatgpt.com/docs/codex/cli)

Codex 可调用专业 Subagent；本地文件系统 Skill 与 ChatGPT Workspace Skill 属于不同分发面。这些能力不能写成 Work、Projects 或 Workspace Agents 的默认配置。[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)

### Subagents、Skills 与 Workspace Agents

Work 与 Codex 可启动专业 Subagent 并汇总结果。Work 的可用性受 eligible account 限制；并行 Agent 消耗更多 Token，多个 Agent 同时修改同一文件可能冲突。[Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)

Workspace Agents 可组合 Apps、Skills、Instructions、计划运行和共享，当前官方 Cookbook 标为 Research Preview。ChatGPT Workspace Skill、本地文件系统 Skill 和 Plugin 的所有权、分享、安装和 Connector 授权是分开的管理对象。[Workspace Agents](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)、[Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)

### 权限与记录

Work 权限模式控制文件、命令和互联网等动作；管理员可禁用部分模式。Compliance API 用于审计、调查、留存和 Legal Hold，不是生产力分析接口。[Permission modes](https://learn.chatgpt.com/docs/permission-modes)、[Compliance API](https://learn.chatgpt.com/docs/enterprise/compliance-api)

官方资料没有直接证明 Work 是 CI/CD Pipeline 或发布控制面。详见 [[50_deepdives/agent-workbench/41_openai-product-boundaries|OpenAI 产品边界详章]]。

## 四、Claude Cowork

### Local、Remote 与 Project

Cowork Remote Session 在 Anthropic 服务端隔离环境运行，当前为 Beta；既有 Local Session 在设备上运行 Agent Loop，并把代码放在本地隔离 VM 中执行。[Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)

Cowork Project 由本地文件、Instructions、Context 和 Project-scoped Memory 组成。当前 Project 不支持 Cloud Sync、Team/Enterprise Project Sharing 或 Claude Code 使用。[Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)

### Plugin、Subagent 与定时任务

Plugin 可以打包 Skills、Connectors、Hooks 和 Subagents；Hooks/Subagents 只在 Cowork 运行。组织可通过 Marketplace 分发 Plugin。[Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)

Scheduled Task 可按周期或按需运行，每次形成独立 Cowork Session，并使用已配置的 Connector、Skill 和 Plugin。Remote Scheduled Task 不依赖电脑保持唤醒，但不能绑定本地文件夹。[Scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork)

### 权限、隔离和记录

Remote Session 访问设备文件需要 Desktop 在线、文件夹已连接且本地工具权限允许。管理员可关闭 Remote Session、限制网络、要求每次工具调用重新批准并要求可信设备。Cowork Activity 当前不进入 Audit Logs、Compliance API 或 Data Export；Owner 可通过 OpenTelemetry 输出事件。[Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)

Computer Use 是独立的 Research Preview。官方资料没有直接证明 Cowork 原生运行 CI/CD Check 或批准发布。详见 [[50_deepdives/agent-workbench/42_claude-cowork|Claude Cowork 功能详章]]。

## 五、GitLab Duo Agent Platform

GitLab Duo Agent Platform 自 GitLab 18.8 GA。GA 功能表包含 Custom Agents、Custom Flows、Developer Flow、Code Review Flow、Fix CI/CD Pipeline Flow、Convert to GitLab CI/CD Flow、Software Development Flow 和 MCP Clients；Tool Governance、AI Audit Event Report、CI Expert Agent 等能力仍按 Beta/Experiment 记录。[Platform overview](https://docs.gitlab.com/user/duo_agent_platform/)

UI 启动的 Flow 使用 CI/CD Runner，IDE 启动的 Flow 本地运行。远程 Runner 下载 Duo CLI，经 WebSocket 连接 Workflow Service，并执行文件和 Git 工具。[Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)

项目通过默认分支上的 `.gitlab/duo/agent-config.yml` 配置 Image、Setup Script 和 Network Policy，并可使用 `AGENTS.md`、Agent Skill、Custom Flow 和 MCP 配置。[Agent config](https://docs.gitlab.com/user/duo_agent_platform/flows/agent_config_yml/)、[Customize](https://docs.gitlab.com/user/duo_agent_platform/customize/)

远程 Flow 使用服务账号和触发用户组成的 Composite Identity，并提供 Sandbox 与可配置 Network Egress；IDE/CLI 本地执行使用不同边界。Flow 生成的 Commit、MR、Review 或 CI 修复仍由项目 Approval、Pipeline 和 Deployment Rule 决定是否接受。详见 [[50_deepdives/agent-workbench/43_gitlab-duo-agent-platform|GitLab 功能详章]]。

## 六、Harness Inc. Worker Agents

Worker Agent Definition 可设置 Name、Description、Instructions、Model Connector、MCP、Typed Inputs、Environment Variables 和 Maximum Reasoning Turns。Pipeline 按名称/版本引用 Agent，并通过 Inputs 或 Agent Settings 注入运行参数。[Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)

Agent-enabled Pipeline 可由 Webhook、Artifact、Manifest 或 Schedule 触发。Agent Output Variable 可传给后续 Condition、Approval、Notification 或 Step。官方示例展示 Specification、Plan 和 Code 三个 Agent 按 Pipeline 顺序串接，也展示 IaC Plan Safety 输出 `APPROVE/REVIEW/REJECT` 候选值。[Examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/)

运行时使用 Parent RBAC、Declared Grant、Connector/Agent Tool Allowlist、进程隔离、Secret Placeholder 和 Network Control 缩小权限。事件 Trigger 不总有可继承的人类 Principal。Output Variable 与示例分类只提供 Pipeline 输入，后续 Gate 的配置决定是否继续。详见 [[50_deepdives/agent-workbench/44_harness-worker-agents|Harness Inc. 功能详章]]。

## 七、GitHub Agentic Workflows

GitHub Agentic Workflows 当前为 Public Preview。作者用 YAML Frontmatter 和 Markdown Instructions 编写 `.md`，再由 `gh aw compile` 生成 Hardened `.lock.yml` GitHub Actions Workflow。[About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)

Frontmatter 可配置 Trigger、Permissions、Network、Tools、Safe Outputs 和 Engine。Agent Job 默认只读；写操作由 Agent 生成 Safe Output Proposal，再在隔离下游 Job 中验证并创建 Issue、Comment 或 PR。Secret 保留在 Agent Runtime 外。[Architecture](https://github.github.com/gh-aw/introduction/architecture/)

仓库 Ruleset 可以独立要求 PR、Review、Required Status Check、Deployment Success 或指定 App 来源的 Check。Safe Output 不替代这些规则；PR 创建后的下游 CI 是否触发取决于 Token、Event 和仓库配置。[Ruleset rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)

详见 [[50_deepdives/agent-workbench/45_github-agentic-workflows|GitHub 功能详章]]。

## 八、五个补充产品

| 产品 | 本专题记录的公开功能 | 状态与边界 |
|---|---|---|
| Microsoft Copilot Studio | Agent、Topic、Action、Flow、Connector、Agent Library、Agent Inventory、Test Set、ALM、DLP、RBAC 和发布控制 | 具体能力逐项核验；不是 WorkBuddy 专家团或 CI/CD Gate；[官方治理说明](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience) |
| Google Gemini Enterprise Agent Platform | Managed Agent、ADK Multi-agent、Runtime、Memory、Identity、Tool Registry、Gateway、Evaluation 和 Observability | Runtime/Observability 等与 Preview 评测/优化能力分开；[Agents overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/agents) |
| Atlassian Rovo | Agent、Subagent、Knowledge、Tool、Automation、Rovo Dev Skill 和 Agent 创建治理 | 保留原数据权限；页面未给统一生命周期；[Rovo Agents](https://support.atlassian.com/rovo/docs/agents/) |
| Amazon Bedrock AgentCore | Harness、Runtime、Memory、Gateway、Identity、Policy、Evaluations、Observability、Browser、Code Interpreter | 通用 Agent 运行底座，不是终端工作台或 CI/CD Gate；[[50_deepdives/amazon-bedrock-agentcore/README|既有专题]] |
| Octopus Claude Agent Step | 在 Deployment Process/Runbook 中运行 Claude Code，注入 Release/Environment/Variable/前序 Log，记录 Token、Cost 和 Transcript | Alpha；无中途批准，官方不建议关键无人值守自动化；[官方文档](https://octopus.com/docs/octopus-ai/claude-agent-step) |

## 九、控制边界

| 产品类别 | Agent 可直接产生 | 后续接受由什么决定 |
|---|---|---|
| 通用工作台 | 文件、分析、变更、报告、项目资产 | 用户审查、业务流程和外部系统权限 |
| GitLab Flow | Commit、MR、Review、CI 修复 | Project Approval、Pipeline、Branch/Deployment Rule |
| Harness Inc. Pipeline Step | Output Variable、生成文件或分析结果 | Condition、Approval、Policy、Deployment Step |
| GitHub Agentic Workflow | Issue、Comment、PR Safe Output | Ruleset、Required Checks、Review、Environment Rule |
| Octopus Agent Step | 命令/文件结果、Task Log、Transcript | Deployment/Runbook 后续 Step 与人工流程 |

## 十、状态与证据缺口

- WorkBuddy 细分功能、Harness Inc. 子能力没有统一生命周期标签。
- Workspace Agents 为 Research Preview；Claude Remote 为 Beta、Computer Use 为 Research Preview；GitHub Agentic Workflows 为 Public Preview；Octopus Step 为 Alpha。
- GitLab Platform GA 与单项功能状态必须分别记录。
- 六个核心产品没有统一、可复验的跨产品成功率、成本或质量指标。
- 通用工作台的官方资料没有直接证明其获得 CI/CD 合并或生产发布权。

## 主要证据入口

- [[00_sources/research-agent-workbench-expert-team-2026-08-08|十一产品一手证据日志]]
- [[50_deepdives/agent-workbench/20_evidence-map|功能 Evidence Map]]
- [[50_deepdives/agent-workbench/30_case-map|产品功能矩阵]]
- [[50_deepdives/agent-workbench/70_fact-audit|逐主张事实审计]]

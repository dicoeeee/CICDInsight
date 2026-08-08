---
title: Agent 工作台与 CI/CD Agent 产品功能矩阵
tags:
  - research/agentic-cicd
  - research/case-map
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台与 CI/CD Agent 产品功能矩阵

## 比较规则

- 三张表按产品公开入口分组，不代表成熟度排序。
- “有”只表示官方文档存在对应功能，不表示不同产品实现等价。
- 每行保留产品或单项能力状态；无标签时写“未声明”。
- 详细原子证据见 [[50_deepdives/agent-workbench/20_evidence-map|Evidence Map]]。

## A. 终端 Agent 工作台

| 产品 | 入口与配置对象 | 上下文和任务 | 协作与扩展 | 产物 | 权限/管理 | 状态与边界 |
|---|---|---|---|---|---|---|
| WorkBuddy | Desktop/Web；Task、Project、Expert、Skill、Connector、Automation | 工作目录、文件、项目指令、资料库；公开任务状态和停止/继续 | Expert Team 团长拆解；Skill、MCP/CLI Connector | 文件、变更、预览、PPT/PDF/文档 | 权限模式；企业专家版本、可见范围、白/黑名单 | 产品已正式发布；细分能力阶段未声明；非 CI/CD 原生控制面 |
| ChatGPT Work | Desktop/Web/Mobile；Chat、Goal | 用户文件、批准的工具和同一 Chat 的任务上下文；Local/Cloud 分开 | eligible account 可调用 Subagent；可使用 Plugin | 文件、分析或工作流结果 | Work 权限模式；Workspace Admin 可限制访问和模式 | 已公开发布；不是 CI/CD 原生控制面 |
| ChatGPT Projects | ChatGPT Project；Project Instructions、Files、Sources | Project 内的 chats、files、sources 与 instructions | 可容纳 Chat 与 Work，但不是 Agent 编排对象 | 项目内会话和材料 | 继承 ChatGPT Workspace 的分享与访问设置 | 是 ChatGPT 上下文容器；不直接取得本地 Codex 目录 |
| Codex CLI | 终端；当前工程目录 | 工程文件和命令执行上下文 | 可调用自定义 Subagent 和本地 Skill | 代码差异与命令结果 | 文件、命令和批准边界按 CLI 配置执行 | 工程执行面；不等同 ChatGPT Project 或 Workspace Agent |
| Workspace Agents | ChatGPT Workspace；Agent Instructions、Apps、Skills | 连接服务的数据与 Workspace 配置 | Apps、Skills、计划运行与共享 | Agent 运行结果 | Admin 控制创建、分享、发布和动作范围 | Research Preview；不能回填为 Work/Codex GA 功能 |
| Claude Cowork | Desktop/Web/Mobile；Session、Project、Plugin、Scheduled Task | 文件、URL、Connector、Project Instructions 与 Memory | Plugin 打包 Skills、Connectors、Hooks、Subagents | 文件、报告、任务历史 | Local/Remote 隔离、应用/文件许可、组织网络与 Plugin 控制、OTel | Remote Beta；Computer Use Research Preview；当前无 Compliance API 覆盖 |

## B. Agent 构建、运行与治理平台

| 产品 | 构建/目录对象 | 协作与运行 | 工具和上下文 | 治理/评测 | 状态 | 与核心产品的边界 |
|---|---|---|---|---|---|---|
| Microsoft Copilot Studio | Agent、Topic、Action、Flow、Connector、Agent Library | Generative Orchestration、Multi-step Logic、Branch/Approval、外部发布 | Knowledge Source、Prebuilt/Custom Connector | Agent Inventory、Compliance Hub、Test Set/Rubric、ALM、DLP、RBAC、审计 | 具体能力逐项核验；部分 2026 能力 Preview | 是 Maker/Developer 构建治理平台，不等同 WorkBuddy 专家团或 CI/CD Gate；[官方对比](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience) |
| Google Gemini Enterprise Agent Platform | Managed Agent、Agent Studio/Garden、Skill、Tool Registry、Gateway | ADK 多 Agent、Managed Runtime、Memory | API、MCP/A2A、Artifact/Sandbox、企业数据 | Unique Identity、Gateway、Observability、Evaluation/Optimization | Runtime/Observability 等逐项 GA；部分评测/优化 Preview | 是 Agent Build/Scale/Govern 平台，不是终端 CI/CD 控制面；[Agents overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/agents) |
| Atlassian Rovo | Rovo Agent、Subagent、Tool、Knowledge、Automation、Rovo Dev Skill | Agent 根据 Subagent 条件委派；Chat、Jira/Confluence、Automation、CLI | Jira/Confluence/Drive 知识；Tools；`SKILL.md` | Agent 创建权限、Owner/Admin 编辑、原数据权限继承 | 页面未统一标 GA/Preview | 面向 Atlassian 工作面与开发 CLI；不证明长任务或发布控制面；[Rovo Agents](https://support.atlassian.com/rovo/docs/agents/) |
| Amazon Bedrock AgentCore | Harness、Runtime、Memory、Gateway、Identity、Policy、Evaluation | Managed Agent Loop 或自带 Framework；支持 Multi-agent Runtime | MCP/API Gateway、Browser、Code Interpreter、Memory | Cedar Policy、IAM、Trace/Logs、Evaluations、Observability | Runtime/Gateway/Identity/Policy/Observability 与 Evaluations 等逐项 GA | 是通用 Agent 运行底座，不是终端工作台或 CI/CD Gate；[[50_deepdives/amazon-bedrock-agentcore/README|既有专题]] |

## C. CI/CD、仓库与部署原生 Agent

| 产品 | 原生入口 | 配置对象和触发 | Agent 执行/协作 | 输出与后续 | 控制边界 | 状态 |
|---|---|---|---|---|---|---|
| GitLab Duo Agent Platform | GitLab UI、IDE、CLI、API、CI/CD Runner | Agent、Flow、Skill、`AGENTS.md`、`agent-config.yml`；Issue/Project/API | Custom Flow 组合 Agent；UI Flow 远程 CI/CD，IDE 本地 | Commit、MR、Review、CI/CD 修复、Session/Job Log | Composite Identity、Sandbox、Network Policy、Project Approval/Pipeline Rule | Platform 18.8 GA；部分 Agent/Flow/Governance 为 Beta/Experiment |
| Harness Inc. Worker Agents | Harness Pipeline Agent Step | Versioned Agent Definition、Typed Inputs、Trigger、MCP；Webhook/Artifact/Manifest/Schedule | 单 Agent Step 或多个 Agent 按 Pipeline 顺序串接 | Output Variable 供 Condition、Approval、Notification、后续 Step | Parent RBAC 与 Grant/Allowlist 交集；独立 Pipeline Gate | 官方文档可用；细分能力状态未统一声明 |
| GitHub Agentic Workflows | Repository + GitHub Actions | Markdown/frontmatter 编译为 `.lock.yml`；Repo Event/Schedule/Web/CLI | 只读 Agent Job；Engine 与 Tool 可配置 | Safe Output 创建 Issue、Comment、PR | 隔离写 Job、Secret 外置、Ruleset/Required Checks 独立 | Public Preview |
| Octopus Claude Agent Step | Deployment Process 或 Runbook Step | Project/Environment/Release/Variable/前序 Log；Worker/Target | Claude Code 只读调查或执行 Target Command | Task Log、Token、Cost、完整 Transcript、文件/命令结果 | Sandbox 可选；未授权 Tool 失败；无中途批准 | Alpha；官方不建议关键无人值守自动化；[官方文档](https://octopus.com/docs/octopus-ai/claude-agent-step) |

## 事实边界汇总

| 问题 | 已有产品事实 | 尚不能写成事实 |
|---|---|---|
| 是否有长任务/项目工作面 | WorkBuddy、ChatGPT Work、Claude Cowork 公开了任务、项目或长任务能力 | 所有工作台共享同一任务状态或上下文模型 |
| 是否有专业 Agent 协作 | WorkBuddy Expert Team、OpenAI Subagents、Cowork Plugin Subagents、GitLab Custom Flow 均有公开机制 | 多 Agent 必然优于单 Agent |
| 是否能沉淀可复用能力 | 多产品公开 Skill、Plugin、Agent Catalog、Expert 或 Flow | Skill/Agent 包自动安全、正确或具备平台工程质量 |
| 是否原生接入 CI/CD | GitLab Flow、Harness Pipeline Step、GitHub Actions Workflow、Octopus Deployment Step 有直接产品入口 | 通用工作台自动具备发布权限 |
| 是否有治理与审计 | 产品分别公开权限、目录、身份、日志、Trace 或合规接口 | 任一产品已经覆盖企业全部审计与职责分离要求 |

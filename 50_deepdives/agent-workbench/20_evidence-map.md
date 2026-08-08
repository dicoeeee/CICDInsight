---
title: Agent 工作台产品功能 Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台产品功能 Claim—Evidence—Gap Matrix

## 证据标记

- `direct-fact`：官方文档直接描述的功能或限制。
- `status-fact`：官方发布说明或文档直接标注的生命周期。
- `vendor-example`：厂商示例，只证明配置或流程可表达。
- `not-stated`：官方未公开实现、状态或效果，不能补全。

## WorkBuddy

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| WB-01 | WorkBuddy 4.5.0 于 2026-03-04 正式发布。 | [Changelog](https://www.workbuddy.cn/docs/workbuddy/Changelog) | 发布 2026-03-04；访问 2026-08-08；`status-fact` | 不表示后续所有细分功能均 GA |
| WB-02 | 新任务入口可选择默认、Plan、Ask 模式，并配置工作目录、模型、Skill、连接器和权限。 | [新建任务](https://cloud.tencent.com/document/product/1831/134391) | 更新 2026-07-20；访问 2026-08-08；单项阶段未声明 | 不证明这些配置适用于 CodeBuddy |
| WB-03 | 任务管理公开进行中、已完成、失败、待处理、规划中和已归档等状态。 | [任务管理](https://www.workbuddy.cn/docs/workbuddy/Task-Management) | 页面未标日期；访问 2026-08-08；阶段未声明 | 不是 Pipeline 或 Release 状态 |
| WB-04 | Project 可统一配置指令、资料、连接器、专家和 Skill，并把产物保存到资料库供后续任务使用。 | [项目](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project) | 页面未标日期；访问 2026-08-08；阶段未声明 | 不证明 RAG 结果准确或满足审计要求 |
| WB-05 | 专家团由团长拆解任务、协调专家并行执行并整合交付。 | [专家](https://cloud.tencent.com/document/product/1831/134393) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | 调度、失败恢复和质量基准未公开 |
| WB-06 | Skill 是可执行脚本/工作流包，支持导入、查找、创建和启停。 | [技能](https://cloud.tencent.com/document/product/1831/134432) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | Skill 可用不等于获得系统授权 |
| WB-07 | Connector 可封装 OAuth/API Key、MCP Server、工具过滤和超时。 | [连接器](https://cloud.tencent.com/document/product/1831/134525)、[企业连接器](https://cloud.tencent.com/document/product/1831/134453) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | 工具可见不等于后端业务动作获批 |
| WB-08 | 任务详情把产物、工作空间文件、文件变更和预览分开呈现。 | [右侧边栏](https://cloud.tencent.com/document/product/1831/134400) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | 产物存在不等于质量或发布通过 |
| WB-09 | 企业管理员可按专家 ID、版本、启用状态、可见范围和白/黑名单管理专家包。 | [企业专家管理](https://cloud.tencent.com/document/product/1831/134421) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | 包校验不证明内容安全或正确 |
| WB-10 | 自动化可按日程运行 Prompt 任务，使用当前身份、模型与 Skill，并记录运行状态。 | [自动化](https://cloud.tencent.com/document/product/1831/134399) | 更新 2026-07-20；访问 2026-08-08；阶段未声明 | 当前文档是个人定时任务，不是 CI/CD Trigger |

## ChatGPT Work、Codex 与 Workspace Agents

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| OA-01 | ChatGPT Work 用于有明确结果的较长任务，并返回可审查的文件、分析或工作流结果。 | [Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work) | 页面未标日期；访问 2026-08-08；公开产品 | 不证明结果已由业务或 CI 接受 |
| OA-02 | Work Desktop 可选择 Local 或 Cloud；Cloud 可在应用关闭或电脑关机后继续。 | [Get started with Work](https://learn.chatgpt.com/docs/get-started-with-work) | 页面未标日期；访问 2026-08-08 | Local 与 Cloud 的文件/应用能力不同 |
| OA-03 | `/goal` 支持设置、编辑、暂停、恢复和清除长任务目标。 | [Long-running work](https://learn.chatgpt.com/docs/long-running-work) | 页面未标日期；访问 2026-08-08 | Goal 不是 Pipeline 状态机 |
| OA-04 | ChatGPT Project 组织 chats、files、sources 和 project instructions。 | [Projects](https://learn.chatgpt.com/docs/projects) | 页面未标日期；访问 2026-08-08 | Project 不直接取得本地 Codex 目录 |
| OA-05 | Work 与 Codex 可运行专业 Subagent 并由主任务汇总结果。 | [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | 页面未标日期；访问 2026-08-08；Work 限 eligible accounts | 并行消耗更多 Token，写密集任务可冲突 |
| OA-06 | ChatGPT Workspace Skills、本地文件系统 Skills 和 Plugins 有不同分发与管理边界。 | [Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills) | 页面未标日期；访问 2026-08-08 | 移动 Skill 不迁移所有权、分享或 Connector 授权 |
| OA-07 | Workspace Agents 可组合 Apps、Skills、Instructions、计划运行与共享。 | [Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep) | 发布 2026-04-22；访问 2026-08-08；Research Preview | 不能回填为 Work/Codex GA 功能 |
| OA-08 | Work 权限模式控制文件编辑、命令和互联网等动作，组织可禁用部分模式。 | [Permission modes](https://learn.chatgpt.com/docs/permission-modes) | 页面未标日期；访问 2026-08-08 | 权限模式不等于生产发布审批 |
| OA-09 | Compliance API 用于审计、调查、留存和 Legal Hold，不是生产力分析 Dashboard。 | [Compliance API](https://learn.chatgpt.com/docs/enterprise/compliance-api) | 页面未标日期；访问 2026-08-08 | 不表示所有 Surface 的每种活动均进入相同记录 |
| OA-10 | Codex CLI 可在终端读取、修改当前工程并运行命令。 | [Codex CLI](https://learn.chatgpt.com/docs/codex/cli) | 页面未标日期；访问 2026-08-08 | 不把其他 Codex Surface 的能力补入 CLI |

## Claude Cowork

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| CC-01 | Cowork Remote Session 在 Anthropic 服务端隔离环境运行，当前为 Beta。 | [Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview) | 页面未标日期；访问 2026-08-08；Beta | Remote 与既有 Local Session 不同 |
| CC-02 | Cowork Project 组织本地文件、Instructions、Context 和项目 Memory。 | [Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork) | 发布 2026-04-09；访问 2026-08-08 | 当前不支持 Cloud Sync 或 Team/Enterprise Project Sharing |
| CC-03 | Project Memory 限于本 Project，不跨 Project 继承。 | [Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork) | 发布 2026-04-09；访问 2026-08-08 | Claude Code 当前不支持 Cowork Project |
| CC-04 | Cowork Plugin 可打包 Skills、Connectors、Hooks 和 Subagents；Hooks/Subagents 只在 Cowork 运行。 | [Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude) | 发布 2026-05-29；访问 2026-08-08 | Chat 与 Cowork 的 Plugin 能力不同 |
| CC-05 | Scheduled Task 每次运行形成独立 Cowork Session，并可使用已配置的 Connector、Skill 和 Plugin。 | [Scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork) | 页面未标日期；访问 2026-08-08 | 远程任务不能绑定电脑本地文件夹 |
| CC-06 | Remote Session 访问设备文件需要 Desktop 在线、文件夹已连接且本地工具调用通过权限检查。 | [Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview) | 页面未标日期；访问 2026-08-08；Beta | 不是对整个设备或企业内网的默认访问 |
| CC-07 | Computer Use 在访问每个应用前请求许可，能力状态为 Research Preview。 | [Computer use](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork) | 发布 2026-04-24；访问 2026-08-08；Research Preview | 仍存在屏幕数据与误操作风险 |
| CC-08 | Cowork 活动当前不进入 Audit Logs、Compliance API 或 Data Export；Owner 可通过 OpenTelemetry 输出事件。 | [Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview) | 页面未标日期；访问 2026-08-08 | OTel 监控不等于 Compliance API 覆盖 |

## GitLab Duo Agent Platform

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| GL-01 | GitLab Duo Agent Platform 自 GitLab 18.8 GA。 | [Platform overview](https://docs.gitlab.com/user/duo_agent_platform/) | 页面版本历史；访问 2026-08-08；GA | 各 Agent/Flow/治理功能仍有独立状态 |
| GL-02 | GA 功能表包含 Custom Agents、Custom Flows、Developer/Code Review/Fix CI/CD 等 Flow。 | [Platform overview](https://docs.gitlab.com/user/duo_agent_platform/) | 页面未标日期；访问 2026-08-08 | Beta/Experiment 表中的功能不能继承 GA |
| GL-03 | UI 启动的 Flow 通过 CI/CD 执行，IDE 启动的 Flow 在本地执行。 | [Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/) | GitLab 18.3 引入；访问 2026-08-08 | 两种执行面的隔离和身份不同 |
| GL-04 | `.gitlab/duo/agent-config.yml` 从默认分支加载，可配置 Image、Setup Script 和 Network Policy。 | [Agent config](https://docs.gitlab.com/user/duo_agent_platform/flows/agent_config_yml/) | 页面未标日期；访问 2026-08-08 | 其他分支中的配置被忽略 |
| GL-05 | GitLab 支持 `AGENTS.md`、Agent Skills、Custom Flow 和 MCP 配置。 | [Customize](https://docs.gitlab.com/user/duo_agent_platform/customize/) | 页面未标日期；访问 2026-08-08 | MCP Client GA 不等于 External MCP Server GA |
| GL-06 | 远程 Flow 使用服务账号与触发用户组成的 Composite Identity。 | [Flow variables](https://docs.gitlab.com/user/duo_agent_platform/flows/execution_variables/) | 页面未标日期；访问 2026-08-08 | Flow 不直接获得所有自定义 CI/CD Variables |
| GL-07 | 远程 Flow 支持 Sandbox 和可配置 Network Egress；IDE/CLI 本地执行没有同一 Sandbox。 | [Security threats](https://docs.gitlab.com/user/duo_agent_platform/security_threats/) | 页面未标日期；访问 2026-08-08 | 不同 Surface 不能共享安全结论 |
| GL-08 | Flow 可生成 Commit/MR/评审或 CI 修复，但后续仍受 Project Approval、Pipeline 和 Deployment Rule 控制。 | [Platform overview](https://docs.gitlab.com/user/duo_agent_platform/) | 页面未标日期；访问 2026-08-08 | 生成 MR 不等于合并或部署通过 |

## Harness Inc. Worker Agents

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| HA-01 | Worker Agent Definition 可配置 Instructions、Model Connector、MCP、Typed Inputs、Environment Variables 和最大 Reasoning Turns。 | [Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) | 更新 2026-07-22；访问 2026-08-08；单项状态未统一声明 | 启用和权限依赖 Harness 账户配置 |
| HA-02 | Pipeline 通过 Agent 名称/版本引用目录定义，并可用 Inputs/Agent Settings 注入 Pipeline-specific 参数。 | [Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) | 更新 2026-07-22；访问 2026-08-08 | 运行参数不自动修改 Agent Definition |
| HA-03 | Agent-enabled Pipeline 可由 Webhook、Artifact、Manifest 和 Schedule 等 Trigger 启动。 | [Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) | 更新 2026-07-22；访问 2026-08-08 | 事件 Trigger 不总有可继承的人类 Principal |
| HA-04 | Agent Output Variable 可供下游 Condition、Approval 或 Notification 使用。 | [Configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/) | 更新 2026-07-22；访问 2026-08-08 | 输出变量不等于 Gate 已通过 |
| HA-05 | 官方示例可把 Specification、Plan、Code 三个 Agent 按 Pipeline 顺序串接。 | [Example agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/) | 更新 2026-07-23；访问 2026-08-08；`vendor-example` | 是厂商示例，不是客户效果数据 |
| HA-06 | IaC Plan Safety 示例产生 `APPROVE/REVIEW/REJECT` 候选值。 | [Example agents](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/) | 更新 2026-07-23；访问 2026-08-08；`vendor-example` | 候选值必须由 Pipeline 配置解释和执行 |
| HA-07 | Runtime 通过进程、Secret、Network 与 Principal/Grant 交集限制 Agent。 | [运行隔离](https://www.harness.io/blog/how-we-secured-ai-worker-agents-in-harness)、[身份与权限](https://www.harness.io/blog/identity-and-permissions-for-ai-worker-agents-in-harness) | 发布 2026-07-13/16；访问 2026-08-08；厂商安全说明 | 具体 Feature Flag 与账户权限需实测 |

## GitHub Agentic Workflows

| ID | 原子功能主张 | 官方证据 | 页面日期 / 状态 | 不可外推边界 |
|---|---|---|---|---|
| GH-01 | GitHub Agentic Workflows 当前为 Public Preview。 | [About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows) | 页面未标日期；访问 2026-08-08；Public Preview | 不表示全部仓库默认启用 |
| GH-02 | Source Workflow 使用 Markdown Instructions 和 YAML Frontmatter。 | [About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows) | 页面未标日期；访问 2026-08-08；Public Preview | Source 文件本身不是可执行 Actions Workflow |
| GH-03 | `gh aw compile` 把 `.md` 编译为 Hardened `.lock.yml` Actions Workflow。 | [gh-aw docs](https://github.github.com/gh-aw/) | 页面未标日期；访问 2026-08-08 | Source 与 Compiled 文件都需要审查 |
| GH-04 | Frontmatter 可配置 Trigger、Permissions、Network、Tools、Safe Outputs 和 Engine。 | [About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows) | 页面未标日期；访问 2026-08-08；Public Preview | 非默认 Engine 可能需要 Secret |
| GH-05 | Agent Job 默认只读；写操作通过声明的 Safe Output 在隔离下游 Job 执行。 | [Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08 | Safe Output 不证明内容正确 |
| GH-06 | Safe Output 可创建 Issue、Comment 或 PR，Secret 保留在 Agent Runtime 之外。 | [Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08 | 只允许声明的写出口 |
| GH-07 | Ruleset 可独立要求 PR、Review、Required Check 和成功部署。 | [Ruleset rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) | 页面未标日期；访问 2026-08-08 | Agentic Workflow 不替代这些规则 |
| GH-08 | Safe Output 创建 PR 后的下游 CI 触发取决于 Token、Event 和仓库配置。 | [Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08；条件化事实 | 必须在目标仓库实测 |
| GH-09 | 已引用文档描述单个 Agent Job 的 Engine 与 Tool 配置，未描述 Expert Team、Subagent 或自定义多 Agent 调度。 | [About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08；`not-stated` | 不据此断言产品未来不支持多 Agent |
| GH-10 | 已引用文档未描述独立于仓库权限、Workflow 文件和 Actions 运行面的 Agent 专用合规审计接口。 | [About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 页面未标日期；访问 2026-08-08；`not-stated` | 不将普通 Actions 记录补写为专用 Agent 审计产品 |

## 关键证据缺口

- 六个核心产品均未提供可跨产品比较的任务成功率、错误率、成本或多 Agent 质量基准。
- WorkBuddy、ChatGPT Work 和 Claude Cowork 没有官方资料证明其为原生 CI/CD 发布控制面。
- GitLab、Harness Inc. 与 GitHub 的 Agent 产物能进入仓库或 Pipeline，但最终接受依赖目标项目的 Rule、Check、Approval 和 Environment 配置。
- 产品状态变化频繁；任何 Preview/Beta/Alpha 变化都需要同步 Source Brief、详章、Case Map 和候选页。

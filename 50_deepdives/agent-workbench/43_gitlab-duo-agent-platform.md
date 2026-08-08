---
title: GitLab Duo Agent Platform 产品功能详章
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# GitLab Duo Agent Platform 产品功能详章

## 产品状态与入口

- GitLab Duo Agent Platform 在 GitLab 18.2 以 Beta 引入，并于 18.8 GA；Self-Managed 形态曾由 Feature Flag 控制，后续版本移除该 Flag。[平台概览](https://docs.gitlab.com/user/duo_agent_platform/)
- 平台整体 GA 不代表所有能力 GA。官方概览将功能分为 Generally available 与 Beta/Experiment；Agent Tool Governance、AI Audit Event Report、CI Expert Agent、Improve CI/CD Setup 等需要保留各自状态。
- 用户可从 GitLab UI、IDE Extension、GitLab Duo CLI 或 API 使用 Agents/Flows。Flow 从 GitLab UI 启动时通过 CI/CD 执行，在 IDE 中启动时本地执行。[Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)

## 配置对象

| 对象 | 官方公开的配置或行为 |
|---|---|
| Agent | 内置或 Custom Agent；可配置 Instructions、Tools、Context 与权限 |
| Flow | 多个 Agent 的组合，用于开发、评审、CI/CD 修复或自定义任务 |
| AI Catalog item | Agent/Flow 的目录项与版本；Consumer 决定项目启用的目录项 |
| `AGENTS.md` / Rules | 用户级或项目级指令，用于 Chat、Agent 和部分 Flow |
| Agent Skill | `SKILL.md` 及可选脚本/参考资料，为 Agent/Flow 提供可复用能力 |
| `agent-config.yml` | CI/CD 运行的 Image、Setup Script、Network Policy 等执行配置 |
| Runner/Sandbox | 远程 Flow 的执行器、容器镜像、网络与文件隔离 |

来源：[Customize Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/customize/)、[Agent configuration syntax](https://docs.gitlab.com/user/duo_agent_platform/flows/agent_config_yml/)

## 输入、上下文和 Flow 状态

- Flow API 支持 `goal`、`additional_context`、`agent_privileges`、Issue、Project、Environment、Container Image 与目录项版本等输入。[Flows API](https://docs.gitlab.com/api/duo_agent_platform_flows/)
- `allow_agent_to_request_user` 决定 Flow 是否可暂停并向用户提问；关闭后 Flow 运行到结束而不等待用户输入。
- 项目上下文可由 Repository、Issue、MR、`AGENTS.md`、Custom Rules、MR Review Instructions、Skill 和 MCP 配置提供。
- Flow Session 可在 AI > Sessions 中查看，并链接到 CI/CD Job Logs；Flow 状态与 Pipeline Job 记录相关，但不等同 MR 已合并或部署已成功。

## Agents、Flows 与 CI/CD 功能

- GA 功能表列出 Custom Agents、Custom Flows、Developer Flow、Code Review Flow、Convert to GitLab CI/CD Flow、Fix CI/CD Pipeline Flow、Software Development Flow 和 MCP Clients。[平台概览](https://docs.gitlab.com/user/duo_agent_platform/)
- Flow 是多个 AI Agent 的组合，可用于修复 Bug、写代码或处理漏洞。Custom Flow 允许定义多个 Agent 的协作，而不是把所有任务固定给一个 Agent。
- Fix CI/CD Pipeline Flow 直接面向失败 Pipeline；Convert to GitLab CI/CD Flow 直接面向 CI 配置转换。这些是 GitLab 产品内的 CI/CD 原生功能。
- CI Expert Agent、Improve CI/CD Setup 等列在 Beta/Experiment 表中，不能继承 Platform GA 状态。

## Skill、MCP 与自定义指令

- GitLab 支持 Custom Rules、`AGENTS.md`、MR Review Instructions、Agent Skills、Custom Flow Definition 和 MCP Server 配置。[Customize](https://docs.gitlab.com/user/duo_agent_platform/customize/)
- Agent Skill 使用 `SKILL.md`，可附带脚本、参考资料和其他资源；支持用户级与项目级位置。
- MCP Clients 是 GA 功能；External MCP Servers 位于 Beta/Experiment 表。两者状态和数据边界必须分开。

## 远程执行、Runner 与网络

- UI Flow 通过 Runner 执行：Runner 下载 `@gitlab/duo-cli`，Duo CLI 经 WebSocket 连接 Workflow Service，并按模型指令执行文件和 Git 工具。[Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)
- `.gitlab/duo/agent-config.yml` 从默认分支只读加载；其他分支中的该文件被忽略。它可设置 Container Image、Setup Script、Network Allow/Deny List 等。
- 自有 Runner 需要 `gitlab--duo` Tag，并使用支持 Docker Image 的 Executor；Shell Executor 不受支持。
- 远程 Sandbox 提供应用级网络和文件系统隔离；自定义 Image 是否支持 Sandbox 取决于是否包含所需运行组件。[Environment sandbox](https://docs.gitlab.com/user/duo_agent_platform/environment_sandbox/)

## 身份、权限与人类批准

- 远程 Flow 使用服务账号与触发用户组成的 Composite Identity；Git Commit 显示触发用户为 Committer、服务账号为 Author。[Flow variables](https://docs.gitlab.com/user/duo_agent_platform/flows/execution_variables/)
- CI Job Token 与 GitLab Token 的可用范围由 Flow 环境限定；普通自定义 CI/CD Variables 不直接提供给 Flow，配置应来自 `agent-config.yml` 或允许的环境变量。
- Remote Flow、UI Chat Agent 与 IDE/CLI 的安全控制不同：远程 Flow 有 Sandbox 和 Network Egress Control，IDE/CLI 在本地执行并依赖用户批准与本机边界。[Security threats](https://docs.gitlab.com/user/duo_agent_platform/security_threats/)
- Agent Tool Governance 用于对敏感 Tool Action 配置人类批准策略，但该功能位于 Beta/Experiment 状态表。

## 产物、日志与审计

- Developer/Software Development Flow 可创建分支、Commit 和 Merge Request；Code Review Flow 产生评审结果；Fix CI/CD Flow 产生 Pipeline 修复变更。
- Flow Session、CI/CD Job Log 与 Git 历史构成可查看记录。AI Audit Event Report 是独立的 Beta/Experiment 功能，不能写成所有部署均已具备统一审计报告。
- Agent 生成的 Merge Request 仍由项目 Branch Rule、Approval、Pipeline 和 Deployment Rule 决定是否接受。

## 端到端功能流程

`在项目启用 Agent/Flow → 由 UI、IDE、CLI 或 API 提供 Goal 与项目上下文 → 读取默认分支的 Agent 配置和项目指令 → UI Flow 创建 CI/CD 执行任务或 IDE 本地执行 → Agent/Flow 调用文件、Git、Skill 或 MCP 工具 → 在 Session 与 Job Log 查看过程 → 生成 Commit/MR/评审或 CI 修复 → 由现有 GitLab 审批和 Pipeline 规则决定后续接受`

## 明确限制

- Platform GA 与单项功能状态必须分离。
- UI 远程 Flow 与 IDE/CLI 本地 Agent 的隔离、网络、身份和人类批准机制不同。
- Flow 创建 MR 或修复 Pipeline 不等于 MR、Required Job 或部署已经通过。
- Self-Managed、Dedicated、GitLab.com 和 Self-Hosted Model 的前置条件不同。

## 主要证据入口

- [[00_sources/briefs/2026-gitlab-duo-agent-platform|GitLab Duo Agent Platform Source Brief]]
- [[00_sources/research-agent-workbench-expert-team-2026-08-08|产品景观证据日志]]

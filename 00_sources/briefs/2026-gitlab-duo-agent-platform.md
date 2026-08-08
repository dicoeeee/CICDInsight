---
title: GitLab Duo Agent Platform 功能、执行与状态边界
source_id: gitlab-duo-agent-platform-docs-2026
organization: GitLab
source_type: official-docs
published: null
verified: 2026-08-08
availability: ga-platform-with-mixed-features
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - security-compliance
  - testing-gates
  - build
tool_categories:
  - source-control
  - ci-pipeline
  - security
  - agent-runtime-orchestration
  - governance
company_topics:
  - GitLab
autonomy_levels:
  - L0
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitLab Duo Agent Platform 功能、执行与状态边界

## 一手来源

| 页面 | 日期/状态 | 直接支持的功能 |
|---|---|---|
| [Platform overview](https://docs.gitlab.com/user/duo_agent_platform/) | 18.2 Beta、18.8 GA；访问 2026-08-08 | GA/Beta/Experiment 功能表、Agents 与 Flows |
| [Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/) | GitLab 18.3 引入；访问 2026-08-08 | UI/CI 与 IDE/Local 执行、Runner、Duo CLI、WebSocket、Sandbox |
| [Agent config](https://docs.gitlab.com/user/duo_agent_platform/flows/agent_config_yml/) | 页面未标日期；访问 2026-08-08 | Image、Setup Script、Network Policy、默认分支读取 |
| [Customize](https://docs.gitlab.com/user/duo_agent_platform/customize/) | 页面未标日期；访问 2026-08-08 | Rules、`AGENTS.md`、MR Instructions、Agent Skills、Custom Flow、MCP |
| [Flows API](https://docs.gitlab.com/api/duo_agent_platform_flows/) | 页面未标日期；访问 2026-08-08；Create Flow 为 Experiment | Goal、Context、Privileges、Environment、Catalog Version、问询开关 |
| [Execution variables](https://docs.gitlab.com/user/duo_agent_platform/flows/execution_variables/) | 页面未标日期；访问 2026-08-08 | Composite Identity、Token 与变量范围 |
| [Security threats](https://docs.gitlab.com/user/duo_agent_platform/security_threats/) | 页面未标日期；访问 2026-08-08 | Surface-specific Sandbox、Network、Identity、人类批准与 Sanitization |

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 状态 | Platform 在 18.8 GA；Platform Page 仍把 Tool Governance、AI Audit、CI Expert 等列为 Beta/Experiment |
| 入口 | GitLab UI、IDE Extension、Duo CLI 与 API；UI Flow 使用 CI/CD，IDE Flow 本地执行 |
| 配置 | Agent、Flow、AI Catalog Item、`AGENTS.md`、Agent Skill、Custom Flow、MCP、`agent-config.yml` |
| 上下文 | Repository、Issue、MR、Goal、Additional Context、Project Rule、Skill 与 Review Instruction |
| 协作 | Custom Flow 组合多个 Agent；GA 表包含 Developer、Code Review、Fix CI/CD 等 Flow |
| CI/CD | UI Flow 使用 Runner；Fix CI/CD Pipeline Flow 和 Convert to GitLab CI/CD Flow 直接面向 Pipeline |
| 执行 | Runner 下载 Duo CLI，经 WebSocket 连接 Workflow Service 并执行文件/Git Tool |
| 隔离 | Remote Flow 可用 Sandbox 和 Network Policy；IDE/CLI Local 使用不同边界 |
| 身份 | Remote Flow 使用 Service Account 与 Triggering User 的 Composite Identity |
| 产物 | Commit、MR、Review、CI Fix、Session 和 Job Log |

## 状态与接受边界

- Platform GA 不覆盖所有 Agent、Flow、Tool Governance 或 Audit 能力。
- `agent-config.yml` 只从默认分支读取；其他分支中的文件被忽略。
- Remote Flow 与 IDE/CLI Local Agent 的权限、网络和隔离不能互相补全。
- Agent 生成 Commit/MR/CI Fix 不等于 Approval、Pipeline、Merge 或 Deployment Rule 已通过。

## 专题入口

[[50_deepdives/agent-workbench/43_gitlab-duo-agent-platform|GitLab Duo Agent Platform 功能详章]]

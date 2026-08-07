---
title: GitHub Copilot Cloud Agent Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/github
status: complete
as_of: 2026-08-07
---

# GitHub Copilot Cloud Agent Claim—Evidence—Gap Matrix

> 全部证据来自 GitHub 一手来源，访问日期 2026-08-07。完整事实清单见 [[00_sources/research-github-cloud-agent-capabilities-2026-08-07|一手证据底稿]]。

## 产品身份与状态

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C01 | Copilot coding agent 已于 2025-09-25 GA，异步自治、经 Actions 后台工作、产出 draft PR | [GA Changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/) | 官方效果数据为厂商自述 | high |
| CCA-C02 | 现文档统一命名为 Copilot cloud agent，旧路径重定向 | [About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent) | 改名专项 changelog 未找到 | high for naming, medium for rename date |
| CCA-C03 | Business/Enterprise 默认禁用、需管理员启用；Pro/Pro+/Max 默认启用；Free 不含 | [Plans](https://docs.github.com/en/copilot/get-started/plans)、[Access](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/access-management) | 组合策略（org/repo 级）可再收窄 | high |
| CCA-C04 | Agent Tasks REST API 为 Public Preview，仅 Business/Enterprise，user-to-server token | [REST Agent Tasks](https://docs.github.com/en/enterprise-cloud@latest/rest/agent-tasks/agent-tasks)、[Changelog](https://github.blog/changelog/2026-05-13-start-copilot-cloud-agent-tasks-via-the-rest-api/) | 不得与核心 Cloud Agent 的 GA 混写 | high |
| CCA-C05 | 第三方 coding agent（Claude/Codex）为 Public Preview，安全校验已 GA | [Third-party agents](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-third-party-coding-agents)、[Changelog](https://github.blog/changelog/2026-06-09-security-validation-for-third-party-coding-agents/) | 策略独立于第一方 cloud agent | high |

## 执行机制

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C06 | 任务可来自 Issue、Agents 页面、PR 评论、Chat、REST API、Automations 等入口 | [Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)、[Start sessions](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions) | 各入口状态不一（issue 指派为 Preview） | high |
| CCA-C07 | 每个任务一个 Agent Session，日志流式展示推理/工具调用/用量，可监控、可归档 | [Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents) | 归档不可删除，属数据生命周期限制 | high |
| CCA-C08 | Agent 自动建分支、自动 commit、自动 push，完成后开 PR 并把发起人加为 reviewer | [Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github) | 单任务单仓库单分支单 PR | high |
| CCA-C09 | `@copilot` PR 评论触发新 session，同 PR 记忆上下文，默认 push 到 PR 分支 | [Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github) | 仅写权限用户评论被处理 | high |
| CCA-C10 | 单 session 硬上限 59 分钟，可 Stop 保留已 push commit | [About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)、[Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents) | 不可延长 | high |
| CCA-C11 | 临时环境由 GitHub Actions 支撑，可跑测试/linter；支持 larger runner 与自定义镜像 | [Customize environment](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment) | 仅 Ubuntu x64/Windows x64，无 macOS | high |
| CCA-C12 | 默认上下文只读当前任务仓库；内置 GitHub MCP 默认只读当前仓库 | [About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)、[Configure MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers) | 扩大访问需自供 PAT/仓库配置 | high |
| CCA-C13 | 支持多模型 Auto 选择与可配置推理等级 | [Changing model](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model)、[Changelog](https://github.blog/changelog/2026-08-03-customize-the-reasoning-level-for-copilot-cloud-agent) | REST API 允许的 model 值范围更窄 | high |
| CCA-C14 | 内置防火墙限制出网，但只作用于 Bash 启动进程、只覆盖 Actions appliance、不覆盖 MCP/setup steps | [Customize firewall](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall) | 自托管 runner 需禁用内置防火墙 | high for scope |

## 权限与写回机制

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C15 | 仅仓库写权限用户可触发，无写权限评论不传给 Agent | [Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations) | — | high |
| CCA-C16 | 提交以 Copilot 身份 author、发起人 co-author，签名 Verified，commit 含 session logs 链接 | [Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[Changelog](https://github.blog/changelog/2026-04-03-copilot-cloud-agent-signs-its-commits) | 可触发 Require signed commits 规则 | high |
| CCA-C17 | 委托写回为单分支、不能 git push、不能标 ready/approve/merge 自己的 PR | [Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations) | 受分支保护/ruleset 约束，不兼容规则直接阻断 | high |
| CCA-C18 | Agent 被授予自有 token；内置 GitHub MCP 用专门 scoped 只读 token | [Configure MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers) | 完整 scope 明细未公开 | high for mechanism, low for detail |
| CCA-C19 | Agent push 后 Workflow 默认不自动运行，需写权限用户批准；可配置跳过人工审批 | [Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)、[Changelog](https://github.blog/changelog/2026-03-13-optionally-skip-approval-for-copilot-coding-agent-actions-workflows) | 跳过审批会放大写权限与 secrets 暴露面 | high |
| CCA-C20 | 发起人不能 approve 自家 Agent 的 PR，保留 Required approvals | [Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations) | — | high |

## CI/CD 集成机制

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C21 | Agent 受分支保护与 Required Checks 约束，不兼容 ruleset 阻断 | [About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent) | 可设 bypass actor | high |
| CCA-C22 | Dependabot alerts 可指派给 Agent 修复并开 draft PR（GA） | [Changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation) | 需 Code Security + 含 coding agent 的套餐；仅 github.com | high |
| CCA-C23 | 失败 Actions run 与 merge conflict 可 "Fix with Copilot" | [Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github) | 修复仍需人工批准 Workflow | high |
| CCA-C24 | Agent 在临时环境自跑测试只是候选变更的内部验证，不等于正式 CI/Required Checks 已通过 | [Customize environment](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)、[Risks](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations) | 需人工批准才运行正式 Workflow | high for mechanism |
| CCA-C25 | 生产发布无 Copilot 专属文档，依赖通用 environment protection / pending deployments | [REST workflow runs](https://docs.github.com/en/rest/actions/workflow-runs) | 非针对 cloud agent 的专门机制 | medium |

## 安全与治理机制

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C26 | 静态校验默认开启：CodeQL、Advisory Database、secret scanning，无需 GHAS 许可 | [Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[Changelog](https://github.blog/changelog/2025-10-28-copilot-coding-agent-now-automatically-validates-code-security-and-quality/) | 个开关可禁用；不构成通过证明 | high |
| CCA-C27 | Agent secrets 独立，不能访问 Actions/Codespaces/Dependabot secrets | [Configure secrets](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables) | COPILOT_MCP_ 前缀仅传给 MCP | high |
| CCA-C28 | 防火墙作用域有限：仅 Bash 进程、仅 Actions appliance、不覆盖 MCP/setup steps | [Customize firewall](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall) | 不能视为完整出网隔离 | high |
| CCA-C29 | Prompt injection 缓解：过滤隐藏字符，automation 忽略无写权限用户事件 | [Risks](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[About automations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-automations) | 缓解不是免疫 | high |
| CCA-C30 | org/企业策略、audit log（actor_is_agent）、第三方 agent 独立策略构成治理面 | [Access management](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/access-management)、[Agentic audit log events](https://docs.github.com/en/copilot/reference/enterprise-administrators/agentic-audit-log-events) | 流式审计日志为 preview | high |
| CCA-C31 | Automation 用置信度路由分级（Full control/Cautious/Balanced/Full automation），低置信度走审批面板 | [Manage approvals](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/manage-rationale-confidence-approvals) | 逐步灰度，未全量 | medium |

## 可编程控制面

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C32 | Agent Tasks REST API 提供任务启动/列表/状态，状态机含 queued→in_progress→completed/failed 等 | [REST Agent Tasks](https://docs.github.com/en/enterprise-cloud@latest/rest/agent-tasks/agent-tasks) | Public Preview；仅 Business/Enterprise | high |
| CCA-C33 | 管理 API 可控制企业级策略与 org/repo 级 enablement | [REST management](https://docs.github.com/en/rest/copilot/copilot-coding-agent-management) | org 级仓库权限为 preview | high |
| CCA-C34 | Automations 支持定时/事件/评论触发，仅 private/internal 仓库，归属创建者 | [Create automations](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations)、[Changelog](https://github.blog/changelog/2026-08-03-trigger-copilot-automations-with-comments) | 创建者不能 approve 自己 automation 的 PR | high |
| CCA-C35 | session logs + 用量指标（含仓库级）提供可观测与成本归因 | [Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents)、[Changelog](https://github.blog/changelog/2026-07-17-repository-level-github-copilot-usage-metrics-generally-available) | 指标为 GitHub 自报口径 | high |

## 与 Agentic Workflows 的边界

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| CCA-C36 | Cloud agent 是「任务型、单仓、PR 写回」的托管 Agent 服务；Agentic Workflows 是「把 Agent 推理编译进 Actions Workflow」的开源框架 | [About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)、[gh-aw](https://github.com/github/gh-aw) | 两者可组合（Workflow 内也可调用 coding agent） | high |

## 关键证据缺口

- Agent token 完整 scope 明细、golden image 内容、runner 规格、仓库/Monorepo 规模上限未公开；
- Merge Queue 与 cloud agent 的专门配合机制无文档；
- 生产发布场景无 cloud agent 专属文档；
- `approve-following-pr-comment` 术语未在当前官方文档命中（等价机制为 "Approve and run workflows" + "Require approval" 设置）；
- 改名（coding agent → cloud agent）时间点无专项 changelog；
- 无独立第三方针对 cloud agent 安全边界的红队/基准结果。
---
title: GitHub Agentic Workflows 产品功能详章
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# GitHub Agentic Workflows 产品功能详章

## 产品状态与入口

- GitHub Agentic Workflows 是 Public Preview，允许用自然语言描述仓库自动化任务，并在 GitHub Actions 中执行。[About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)
- Workflow 可通过仓库事件、Schedule、Web UI 或 `gh aw` CLI 运行；需要 GitHub Actions、受支持的 AI Engine 与相应认证。
- Public Preview 状态必须与每一项总体能力主张一起保留，不能写成所有 GitHub 仓库默认可用。

## Authoring 与编译对象

| 对象 | 官方公开的配置或行为 |
|---|---|
| Source Workflow | `.md` 文件，由 YAML Frontmatter 与 Markdown Instructions 组成 |
| Compiled Workflow | Hardened `.lock.yml` GitHub Actions Workflow |
| Trigger | Issue、Pull Request、Schedule、Workflow Dispatch 等 Actions Event |
| Permissions | Workflow/Token 的 GitHub 权限声明 |
| Network | 允许访问的网络目标 |
| Tools | GitHub、MCP 或其他允许工具 |
| Safe Outputs | 允许 Agent 提议并由隔离 Job 执行的 Issue、Comment、PR 等写操作 |
| Engine | Copilot、Claude、Codex、Gemini 等受支持引擎配置 |

来源：[About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[gh-aw documentation](https://github.github.com/gh-aw/)

## 编译和运行流程

- 作者编辑 `.md` Source Workflow；`gh aw compile` 将其编译为 Hardened `.lock.yml` Actions Workflow。
- 编译后的 `.lock.yml` 提交到默认分支后，GitHub Actions 才按 Trigger 运行。Source Markdown 与 Compiled Workflow 是两个文件，不应只审查其中一个。
- Workflow 可由 Actions Event、Web 页面或 CLI 触发；实际 Event、Branch 与权限由 Frontmatter 和仓库设置决定。
- 非默认 Engine 可能需要 Repository Secret 或 Provider Credential；Engine 可选不等于组织已授权其使用。

## 输入、上下文与工具

- Agent Job 读取触发事件、仓库内容和明确允许的工具结果。默认设计为只读，并通过最小化 Permission、Network 与 Tool Scope 限制输入和行动面。
- Frontmatter 可声明 Trigger、Permissions、Network、Tools、Safe Outputs 和 Engine；Markdown Instructions 描述任务目标和约束。
- Tool Output 可能包含不可信文本，运行时包含 Threat Detection 与输出处理，以降低 Prompt Injection 风险。[Security architecture](https://github.github.com/gh-aw/introduction/architecture/)

## Agent 协作

- 已引用的 About 与 Architecture 文档描述一个 Agent Job 使用所选 Engine 和允许工具运行任务，没有公开 Expert Team、Subagent 或自定义多 Agent 调度对象；本字段记录为 `not-stated`。
- 多个 Actions Workflow 或 Job 可以由仓库事件衔接，但官方 Agentic Workflows 文档没有把这种 Actions 编排定义为多 Agent 协作机制。[About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[Security architecture](https://github.github.com/gh-aw/introduction/architecture/)

## Safe Outputs 与产物

- Agent Runtime 不直接使用高权限 Token 完成仓库写操作。Agent 生成 Safe Output Proposal，由隔离的下游 Job 解析、验证并执行声明的写操作。
- Safe Outputs 可创建或更新 Issue、Comment、Pull Request 等产品对象；只有 Workflow 明确声明的类型可被执行。
- Secret 保留在 Agent Runtime 之外，并在受控的下游 Actions Job 中使用。
- Agent 生成 PR 是候选变更。是否触发其他 Workflow、满足 Required Check、获得 Approval、合并或部署，取决于 Token、Event 和仓库配置。

## 权限、隔离与管理

- 默认只读、声明式 Safe Outputs、Network Allowlist、Threat Detection、Actions 隔离环境和仓库角色控制构成官方安全边界。[Security architecture](https://github.github.com/gh-aw/introduction/architecture/)
- Repository/Organization 权限决定谁可修改 Workflow、运行 Workflow 和管理 Secret；Source 与 Compiled Workflow 的变更均应受 Branch Protection 或 Ruleset 保护。
- Engine Credential、MCP Secret 和 GitHub Token 的 Scope 分开配置；可配置 Secret 不等于 Agent 默认可读取或写出 Secret。

## 管理与审计

- Source Workflow、Compiled Workflow、Repository/Organization 权限和 Actions 运行面是当前公开的管理对象。
- 已引用的 Agentic Workflows 文档没有说明独立于这些仓库与 Actions 对象的 Agent 专用 Compliance API 或统一审计界面；本字段记录为 `not-stated`，不把普通 Actions 记录补写为专用 Agent 审计产品。[About Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[Security architecture](https://github.github.com/gh-aw/introduction/architecture/)

## 与 Required Checks 的边界

- GitHub Ruleset 可要求 Pull Request、Review、Required Status Checks、Deployment Success 和指定 App 来源的 Check。[Ruleset rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)
- Agentic Workflow 的 Agent Job 或 Safe Output 不自动成为 Required Check，也不替代现有 Check、Review 或 Environment Protection。
- 如果 Safe Output 创建 PR，后续 CI 是否运行取决于创建 PR 使用的 Token 和仓库 Event 配置，需要在目标仓库验证。

## 端到端功能流程

`编写 Markdown Frontmatter 与 Instructions → 使用 gh-aw 编译为 .lock.yml → 将 Source 与 Compiled Workflow 提交到默认分支 → 由仓库事件、Schedule、Web 或 CLI 触发 → 只读 Agent Job 使用受限工具和网络生成结果 → Safe Output 下游 Job 验证并创建 Issue/Comment/PR → 独立 Ruleset、Required Checks、Review 和 Environment Rule 决定是否接受`

## 明确限制

- 产品仍为 Public Preview。
- Source Markdown、Compiled Workflow、Engine Secret 和仓库 Ruleset 必须分别审查。
- Safe Output 只约束声明的写出口，不证明输出内容正确。
- PR 创建后的 CI 触发、Required Check 和合并行为依赖具体仓库配置。

## 主要证据入口

- [[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows Source Brief]]
- [[50_deepdives/github-agentic-workflows/README|GitHub Agentic Workflows Deep Dive]]

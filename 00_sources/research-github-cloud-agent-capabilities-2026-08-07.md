---
title: GitHub Copilot Cloud Agent 功能与机制一手证据
tags:
  - research/agentic-cicd
  - evidence/research-note
  - company/github
status: complete
as_of: 2026-08-07
confidence: high
---

# GitHub Copilot Cloud Agent 功能与机制一手证据

> 研究范围：GitHub Copilot coding agent / cloud agent 的功能与机制。全部事实来自 GitHub 一手来源（docs.github.com、github.blog Changelog、GitHub 官方博客），访问日期 2026-08-07。厂商自述指标单独标注，不外推为行业均值。

## 产品身份与状态

- Copilot coding agent 于 2025-09-25 对付费 Copilot 订阅 GA；异步自治 agent，经 GitHub Actions 在后台工作、开 draft PR 并请求评审（[Changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)，2025-09-25，GA）。
- 现官方文档统一命名为 **Copilot cloud agent**；旧文档路径 `concepts/agents/coding-agent/about-coding-agent` 已重定向到 `concepts/agents/cloud-agent/about-cloud-agent`（[Docs](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。改名专项 changelog 未找到（证据缺口）。
- 套餐可用性：Copilot Free 不含；Student/Pro/Pro+/Max/Business/Enterprise 含。Business/Enterprise 默认禁用、需管理员在 Policies 启用；Pro/Pro+/Max 默认启用（[Docs Plans](https://docs.github.com/en/copilot/get-started/plans)、[Access](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/access-management)，访问 2026-08-07，GA）。
- Copilot Pro/Pro+ 模型选择器 2025-12-08 上线；Business/Enterprise 2026-02-19 开放（[Changelog](https://github.blog/changelog/2026-02-19-model-picker-for-copilot-coding-agent-for-copilot-business-and-enterprise-users/)，2026-02-19，GA）。
- 自动安全/质量校验（CodeQL、GitHub Advisory Database、secret scanning + 自审）：2025-10-28 GA，无需 GHAS 许可（[Changelog](https://github.blog/changelog/2025-10-28-copilot-coding-agent-now-automatically-validates-code-security-and-quality/)，2025-10-28，GA）。
- Agent Tasks REST API：2026-05-13 Public Preview，仅 Copilot Business/Enterprise；支持 PAT（classic/fine-grained）与 OAuth token；GitHub App installation token 与 Pro/Pro+ 支持 "coming soon"（[Changelog](https://github.blog/changelog/2026-05-13-start-copilot-cloud-agent-tasks-via-the-rest-api/)，2026-05-13，Public Preview）。
- 第三方 coding agent（Anthropic Claude、OpenAI Codex）：Public Preview；2026-06-09 起施加与第一方相同的自动安全校验并 GA（[Changelog](https://github.blog/changelog/2026-06-09-security-validation-for-third-party-coding-agents/)，2026-06-09，GA；[Docs](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-third-party-coding-agents)，Public Preview）。
- 2026 年内能力增量（均为一手 changelog）：语义代码搜索 03-17；可配置验证工具 03-18；会话日志可见性 03-19；提交可回溯到 session logs 03-20；管理 REST API（repo 级 enablement）03-24 Public Preview；Jira 集成 03-05 Public Preview；auto model selection 05-14；一键修复失败 Actions run 05-18；低成本小模型用于简单任务 05-18；REST API 审计 repo 配置 05-18；推理等级可配置 08-03；Automations 支持 comment 触发 08-03。

## 任务入口与执行流程

- 入口清单：Agents tab/panel、dashboard、Copilot Chat `/task`、Issue 分配、新仓库 seeding、失败 Actions run 的 "Fix with Copilot"、PR 评论 `@copilot`；另有 GitHub Mobile、VS Code/JetBrains/Eclipse/Visual Studio 2026、gh CLI、REST API、GitHub MCP Server、Jira、Slack、Teams、Azure Boards、Linear、Raycast（[Start sessions](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions)、[Use cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，GA）。
- Agents 页面：`https://github.com/copilot/agents`；仓库内 "New agent task" 表单支持选择仓库、prompt（可粘图片，最大 3 MiB）、base branch、custom agent、模型与推理等级（[Docs](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，GA）。
- Issue 分配：在 Assignees 中选择 Copilot 即触发；可选 target repository、base branch、custom agent、附加说明；Copilot 收到 issue 标题、描述、现有评论与附加说明，但**不再响应发送后新增的 issue 评论**（Public Preview，同上）。
- Agent Session：每个任务一个 session；session 日志流式展示内部推理、工具调用、token 用量与时长；可从 agents panel 实时监控；session 默认共享（仓库内 "All sessions" 可见），可归档不可删除（[Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents)，访问 2026-08-07，GA）。
- 分支/提交/PR 流程：agent 自动创建分支（基于 base branch 或默认分支）、自动写 commit message、自动 push；可提示 "Open a pull request"；新仓库 seeding 或 Chat 场景立即开 draft PR；完成后把发起人加为 reviewer 并通知（[Docs](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，GA）。
- PR 评论迭代：`@copilot` 提及触发新 session；**只有对仓库有写权限的用户评论会被处理**；Copilot 记忆同一 PR 先前 session 上下文，默认直接 push 到 PR 分支；合并冲突可用 "Fix with Copilot" 或 `@copilot` 解决（同上；[Troubleshoot](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/troubleshoot-cloud-agent)，访问 2026-08-07，GA）。
- 取消/暂停：Stop session 按钮结束 Actions run 并保留已 push 的 commit；steering 消息可中途重定向（每条消耗 AI credits，第三方 agent 不支持）；session 超时上限 59 分钟（硬限制）（[Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents)、[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- 深度研究流程：GitHub.com 上支持 research→plan→iterate 后再建 PR；集成入口（Jira/Azure Boards/Linear/Slack/Teams）只支持直接开 PR（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。

## 运行架构

- 临时开发环境**由 GitHub Actions 支撑**：可探索代码、修改文件、运行测试与 linter；默认标准 GitHub-hosted runner，可升级 larger runners（Ubuntu x64 与 Windows 64-bit 兼容，macOS 不支持），也可用 self-hosted runner（推荐 ARC 临时单用 runner）（[Customize environment](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)，访问 2026-08-07，GA）。
- 环境定制：`.github/workflows/copilot-setup-steps.yml`（job 必须叫 `copilot-setup-steps`，仅默认分支生效）可预装依赖、改 `runs-on`、`services`、`snapshot`、`timeout-minutes`（最大 59）（同上）。
- 可访问上下文：默认只能访问**当前任务指定仓库**；内置 Copilot MCP server 默认配置为只读当前仓库（issues、历史 PR 等）；可通过仓库 MCP 设置扩大访问（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- 模型：支持 Auto + 多模型，docs 现行列表含 Claude Sonnet 4.5/Opus 4.7/Opus 5/Haiku 4.5、Gemini 3.1 Pro/3.5 Flash/3.6 Flash、GPT-5.4 mini/5.6 Luna/5.6 Sol/5.6 Terra、Grok 4.5、MAI-Code-1-Flash；支持可配置推理等级（[Changing model](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/changing-the-ai-model)，访问 2026-08-07，GA）。REST API 现行允许的 model 值范围更窄（claude-sonnet-4.6、claude-opus-4.6、gpt-5.2-codex、gpt-5.3-codex、gpt-5.4、claude-sonnet-4.5、claude-opus-4.5）（[REST Agent Tasks](https://docs.github.com/en/enterprise-cloud@latest/rest/agent-tasks/agent-tasks)，Public Preview）。
- MCP/工具调用：支持 MCP **tools**（不支持 resources/prompts；不支持启用 OAuth 的远程 MCP）；GitHub MCP server 与 Playwright MCP server 默认启用；MCP 配置存于仓库设置 JSON，secret 需 `COPILOT_MCP_` 前缀（[Configure MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)，访问 2026-08-07，GA）。
- 网络限制：默认启用内置 firewall 限制出网；推荐 allowlist（OS 包仓库、通用容器注册表、主流语言包仓库、CA、Playwright 浏览器下载源）；org 与 repo 级可配自定义域名 allowlist；**firewall 只作用于 Agent 经 Bash tool 启动的进程，不覆盖 MCP server 与 setup steps，且只作用于 GitHub-hosted Actions appliance 内**；self-hosted runner 需禁用内置 firewall（[Customize firewall](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，访问 2026-08-07，GA）。
- 时长/规模限制：单 session 最大 59 分钟（硬性，不可延长）；单仓库、单分支、一个任务对应一个 PR；只能在 GitHub 托管仓库工作；图片附件上限 3 MiB（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)、[Troubleshoot](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/troubleshoot-cloud-agent)，访问 2026-08-07，GA）。仓库大小/git 镜像规格官方未给数字（证据缺口）。

## 认证与权限模型

- 触发限制：只有仓库**写权限**用户可触发；无写权限用户的评论不会传给 agent（[Risks and mitigations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)，访问 2026-08-07，GA）。
- 提交身份：agent 提交以 Copilot 身份 author、发起任务的人为 co-author；commits 签名显示 Verified；每条 commit message 含 session logs 链接；管理者可查 session logs 与审计日志（[Risks](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents)，访问 2026-08-07，GA）。
- 委托权限边界：agent 只能 push 到**单个分支**（已有 PR 分支或新建 `copilot/` 分支）；**不能直接运行 `git push` 或其他 git 命令**；不能把 PR 标为 ready、不能 approve 或 merge PR；受分支保护与 ruleset 约束（不兼容的规则会阻止 agent，可把 Copilot 加为 bypass actor）（[Risks](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[About](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- Token 机制：agent 被授予自有 token（setup steps 文档明示 "Copilot will be given its own token"）；内置 GitHub MCP server 使用专门 scoped、只读当前仓库的 token，扩大访问需自供 PAT（[Configure MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/configure-mcp-servers)，访问 2026-08-07，GA）。官方未公开 agent token 完整 scope 明细（证据缺口）。
- Actions workflow 门禁：agent push 后 workflow **默认不自动运行**，需写权限用户点击 "Approve and run workflows"；可配置跳过人工审批（[Changelog](https://github.blog/changelog/2026-03-13-optionally-skip-approval-for-copilot-coding-agent-actions-workflows)，2026-03-13，GA）。
- 时序门禁：发起 agent PR 的人不能 approve 该 PR，保留 Required approvals（[Risks](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/risks-and-mitigations)，访问 2026-08-07，GA）。
- 第三方 agent 身份：启用 Claude/Codex 时安装对应 GitHub App（`anthropic code agent` / `openai code agent`），其动作进审计日志但不出现在安装列表中（[Third-party agents](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-third-party-coding-agents)，Public Preview）。
- API 认证：Agent Tasks API 仅支持 user-to-server token（PAT、OAuth app token、GitHub App user-to-server token）；不支持 installation token；fine-grained PAT 需 "Agent tasks" 仓库权限（读，或读+写）（[Via the API](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-via-the-api)，Public Preview）。

## CI/CD 集成机制

- cloud agent 运行时由 GitHub Actions 驱动临时环境，可执行自动化测试、Linter、编译与静态检查（[Customize environment](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)，访问 2026-08-07，GA）。
- **默认 Agent push 后 Actions workflow 不自动运行**，需写权限用户点 "Approve and run workflows"（[Use cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)、[Review output](https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/review-copilot-output)，访问 2026-08-07，GA）。
- 管理员可在关闭 "Require approval for workflow runs" 后允许 Agent push 自动触发 workflow；官方警告该设置会放大 Agent 代码的写权限与 secrets 暴露面（[Configuring agent settings](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/configuring-agent-settings)，访问 2026-08-07，GA）。
- Agent 受分支保护与 required checks 约束；不兼容 ruleset（如限定 commit author）会阻断，可设 Copilot 为 bypass actor（[About cloud agent](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- Dependabot alerts 可指派给 AI coding agent 修复（GA 2026-04-07）：alert 详情页选 "Assign to Agent"，可指派 Copilot/Claude/Codex；Agent 分析 → 开 draft PR → 修测试失败；需 Code Security + 含 coding agent 的 Copilot 套餐，仅 github.com（[Changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation)，2026-04-07，GA）。
- 失败 Actions run 可直接 "Fix with Copilot" 调查并推送修复；merge conflict 也可 "Fix with Copilot"（[Use cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，GA）。
- Copilot code review 自 2026-06-01 起在私有仓库消耗 GitHub Actions 分钟（[Changelog](https://github.blog/changelog/2026-04-27-github-copilot-code-review-will-start-consuming-github-actions-minutes-on-june-1-2026)，2026-04-27，2026-06-01 生效）；code review 的 Agent skills 与 MCP 已于 2026-07-29 GA（[Changelog](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available)，2026-07-29，GA）。
- GitHub Mobile 已支持用 cloud agent 修复失败 Actions checks、PR 评论、merge conflicts（[Changelog](https://github.blog/changelog/2026-07-23-github-mobile-fix-failing-actions-checks-with-copilot-cloud-agent)，2026-07-23，GA）。
- Merge Queue 与 Copilot 无专门配合文档，仅有「受分支保护与 required checks 约束」的笼统表述（证据缺口）。
- 生产发布场景无 Copilot 专属文档，依赖通用 environment protection / pending deployments 审批（证据缺口）。

## 安全与治理机制

- 触发权限：仅仓库写权限用户可触发 Agent；无写权限用户的评论不传给 Agent（[Risks](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations)，访问 2026-08-07，GA）。
- 静态校验默认开启：CodeQL、Advisory Database（恶意包 + CVSS High/Critical）、secret scanning，**不要求 GHAS/Secret Protection 许可证**；可单开关禁用（[Risks](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations)，访问 2026-08-07，GA）。
- secrets：Agent 用独立 Agents secrets/variables，**不能访问 Actions/Codespaces/Dependabot secrets**；`COPILOT_MCP_` 前缀仅传给 MCP（[Configure secrets](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables)，访问 2026-08-07，GA）。
- 网络防火墙默认限制外网，可 org/repo 级配置 allowlist；局限：仅作用于 Agent Bash 启动进程、仅 GitHub-hosted Actions appliance 内、可被绕过（[Customize firewall](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-firewall)，访问 2026-08-07，GA；org 级 [Changelog](https://github.blog/changelog/2026-04-03-organization-firewall-settings-for-copilot-cloud-agent)，2026-04-03，GA）。
- Prompt injection 缓解：过滤隐藏字符（HTML 注释不传入）；automation 默认忽略无写权限用户触发的事件（[Risks](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/risks-and-mitigations)、[About automations](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations)，访问 2026-08-07，GA）。
- 组织策略：Business/Enterprise 下 cloud agent **默认禁用**需管理员启用，Pro/Pro+/Max 默认启用；可逐仓库拉黑；企业级四种策略状态，可 REST API 管理（[Access management](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/access-management)、[Add cloud agent](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent)，访问 2026-08-07，GA；[Changelog custom properties](https://github.blog/changelog/2026-04-15-enable-copilot-cloud-agent-via-custom-properties)，2026-04-15，GA）。
- Automations 可独立于 cloud agent 策略开关 "Allow automations"（[Add cloud agent](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-for-organization/add-copilot-cloud-agent)，访问 2026-08-07，GA）。
- 审计：企业审计日志支持 `actor:Copilot` 过滤，保留 180 天，字段含 `actor_is_agent`/`agent_session_id`/`user`；流式审计日志 public preview（EMU+数据驻留企业）（[Agentic audit log events](https://docs.github.com/en/copilot/reference/enterprise-administrators/agentic-audit-log-events)，访问 2026-08-07，GA/预览）。
- 第三方 coding agent（Claude/Codex）为 public preview，策略独立于 CCA；启用时安装对应 GitHub App，动作进审计日志但 App 不显示在安装列表（[About third-party](https://docs.github.com/en/copilot/concepts/agents/about-third-party-coding-agents)，Public Preview）。
- 企业 managed settings 自 2026-07-27 起覆盖 Copilot app 与 cloud agent（[Changelog](https://github.blog/changelog/2026-07-27-enterprise-managed-settings-now-apply-to-the-github-copilot-app)，2026-07-27，GA）；Copilot app 访问可用专属策略管理（[Changelog](https://github.blog/changelog/2026-07-27-manage-github-copilot-app-access-with-a-dedicated-policy)，2026-07-27，GA）。
- org 级可设默认 runner 并锁定（仓库不可覆盖），用于强制 guardrail（[Changelog](https://github.blog/changelog/2026-04-03-organization-runner-controls-for-copilot-cloud-agent)，2026-04-03，GA）。
- AI Controls 可看近 24h agent sessions、企业级自定义 agent、MCP 注册表/白名单（[Enterprise management](https://docs.github.com/en/copilot/concepts/agents/enterprise-management)，访问 2026-08-07，GA）。
- 2026-07-23 Issues 中 agent automation controls 为 public preview（[Changelog](https://github.blog/changelog/2026-07-23-agent-automation-controls-in-github-issues-in-public-preview)，2026-07-23，Public Preview）。

## 工作流触发与批准机制

- `@copilot` 评论可触发/迭代；仅回应写权限用户；👀 reaction + timeline "Copilot started work"；同 PR 记忆 session 上下文；默认 push 到 PR 分支，也可要求另开 PR（[Use cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，GA）。
- 2026-08-03 起 automation 可被 issue/PR 评论触发（指定关键词），用于生成文档、调查错误、创建任务（[Changelog](https://github.blog/changelog/2026-08-03-trigger-copilot-automations-with-comments)，2026-08-03，GA）。
- Automation 触发器：定时（hourly/daily/weekly）、issue 创建、PR 创建、PR 同步（[Create automations](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations)，访问 2026-08-07，GA）。
- 谁能批准 workflow：仓库写权限用户点 "Approve and run workflows"；是否需要人在场由管理员配置（[Configuring agent settings](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/configuring-agent-settings)，访问 2026-08-07，GA）。
- Automation 的 PR/代码改动归属创建 automation 的用户，该用户不能 approve 这些 PR（[About automations](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations)，访问 2026-08-07，GA）。
- Issue 指派给 Copilot 为 public preview（只读到指派瞬间题面+评论，后续评论不感知）（[Use cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)，访问 2026-08-07，Public Preview）。
- Automation level（Full control / Cautious[默认] / Balanced / Full automation）用置信度路由，低置信度挂起为建议、审批面板 Accept/Decline（[Manage approvals](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/manage-rationale-confidence-approvals)，访问 2026-08-07，逐步灰度）。
- 2026-08-03 起可定制 reasoning level，更高推理消耗更多 credits（[Changelog](https://github.blog/changelog/2026-08-03-customize-the-reasoning-level-for-copilot-cloud-agent)，2026-08-03，GA）。
- 生产发布场景边界：cloud agent 不能 approve/merge、workflow 需人工批准，但无生产部署专门文档；生产发布仍依赖通用 Actions environment protection rules / pending deployments 审批（[REST workflow runs](https://docs.github.com/en/rest/actions/workflow-runs)，访问 2026-08-07）。证据缺口。

## 可编程控制面与可观测机制

- UI：Agents 页面（github.com/copilot/agents）、仓库内 Agents tab、dashboard、"New agent task" 表单；Automations 管理在 Agents tab 的 Automations pane（[Use cloud agent](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-on-github)、[Create automations](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations)，访问 2026-08-07，GA）。
- Agent Tasks REST API（Public Preview，2026-05-13）：`POST/GET /agents/repos/{owner}/{repo}/tasks`（Start / List for repo）、`GET /agents/tasks`（List mine）、`GET /agents/tasks/{task_id}`（含 sessions）；body 参数 `prompt`(必需)、`model`、`custom_agent`、`create_pull_request`、`base_ref`、`head_ref`；状态枚举 `queued/in_progress/completed/failed/idle/waiting_for_user/timed_out/cancelled`；`X-GitHub-Api-Version: 2026-03-10`（[REST Agent Tasks](https://docs.github.com/en/enterprise-cloud@latest/rest/agent-tasks/agent-tasks)，访问 2026-08-07，Public Preview）。
- Issues API 分配：GraphQL mutation（`updateIssue`/`createIssue`/`addAssigneesToAssignable`/`replaceActorsForAssignable`，需 `GraphQL-Features` 头）或 REST，附 `agent_assignment` 对象（target_repo、base_branch、custom_instructions、custom_agent、model）；Bot 身份 `copilot-swe-agent`；Public Preview（[Via the API](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/use-cloud-agent-via-the-api)，访问 2026-08-07，Public Preview）。
- 管理 API：org 级仓库 enablement 管理 REST API 2026-03-24 Public Preview；企业级 `PUT /enterprises/{enterprise}/copilot/policies/coding_agent`（enabled_for_all_orgs / disabled_for_all_orgs / enabled_for_selected_orgs / configured_by_org_admins）；组织级仓库权限（all/selected/none）为 public preview（[Changelog](https://github.blog/changelog/2026-03-24-manage-copilot-coding-agent-repository-access-via-the-api/)，2026-03-24，Public Preview；[REST management](https://docs.github.com/en/rest/copilot/copilot-coding-agent-management)，Public Preview）。
- 调度/定时：Automations 支持 hourly/daily/weekly 计划、issue 创建、PR 打开、PR 同步触发；2026-08-03 起新增 comment 触发；仅 private/internal 仓库；覆盖 Pro/Pro+/Max/Business/Enterprise；automation 私有于创建者，但启动的 session 对仓库访问者可见（[About automations](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-automations)、[Create automations](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations)，访问 2026-08-07，GA；[Changelog](https://github.blog/changelog/2026-08-03-trigger-copilot-automations-with-comments/)，2026-08-03，GA）。
- 状态跟踪：session logs 实时流；API task/session `state`；Copilot usage metrics 提供 PR 创建/合并数与 median time to merge（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)、[Manage and track](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/use-copilot-agents/manage-and-track-agents)，访问 2026-08-07，GA）。
- 成本归因机制（机制层）：cloud agent 同时消耗 Actions 分钟与 AI credits；usage metrics 新增 `used_copilot_cloud_agent` 字段（旧 `used_copilot_coding_agent` 保留至 2026-08-01 后失效）（[Changelog](https://github.blog/changelog/2026-04-23-copilot-cloud-agent-fields-added-to-usage-metrics)，2026-04-23，GA）；仓库级用量指标 GA：`GET /enterprises/{enterprise}/copilot/metrics/reports/repos-1-day`、`GET /orgs/{org}/copilot/metrics/reports/repos-1-day`，按日+按仓库返回 coding agent 创建/合并的 PR 与 code review 等（[Changelog](https://github.blog/changelog/2026-07-17-repository-level-github-copilot-usage-metrics-generally-available)，2026-07-17，GA）。

## 限制与边界

- 单 session 硬上限 59 分钟不可延长（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- 单任务单仓库单分支、最多开一个 PR；仅 GitHub 托管仓库（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- 仅 Ubuntu x64 Linux 与 Windows 64-bit，不支持 macOS；自托管 runner 需自行网络控制并禁用内置防火墙（[Customize environment](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)，访问 2026-08-07，GA）。
- 附加截图 ≤3 MiB（[Troubleshoot](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/troubleshoot-cloud-agent)，访问 2026-08-07，GA）。
- 不兼容 ruleset 会完全阻断；需 bypass actor（[About cloud agent](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/cloud-agent/about-cloud-agent)，访问 2026-08-07，GA）。
- EMU 账户无法在个人仓库用 cloud agent（需 GitHub-hosted runners）（[Troubleshoot](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/troubleshoot-cloud-agent)，访问 2026-08-07，GA）。
- Automations 仅限 private/internal 仓库（[Create automations](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/cloud-agent/create-automations)，访问 2026-08-07，GA）。
- Preview 清单：第三方 coding agent、issue 指派、agent session streaming（[Changelog](https://github.blog/changelog/2026-07-02-copilot-agent-session-streaming-is-now-in-public-preview)，2026-07-02）、agentic autofix for code scanning（[Changelog](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview)，2026-07-10）、Issues agent automation controls、流式审计日志、Copilot Memory（Pro/Pro+/Max）。

## 厂商自述指标（不外推为行业均值）

- "Copilot coding agent now starts work 50% faster"（[Changelog](https://github.blog/changelog/2026-03-19-copilot-coding-agent-now-starts-work-50-faster/)，2026-03-19）。
- "自 2025-10 起已主动阻止 hundreds of potential security leaks and vulnerabilities"（[Changelog](https://github.blog/changelog/2026-06-09-security-validation-for-third-party-coding-agents/)，2026-06-09）。
- custom images 启动快 20%（[Changelog](https://github.blog/changelog/2026-04-27-copilot-cloud-agent-starts-20-faster-with-actions-custom-images)，2026-04-27）；validation tools 快 20%（[Changelog](https://github.blog/changelog/2026-04-10-copilot-cloud-agents-validation-tools-are-now-20-faster，2026-04-10）。
- Heap 采用数据（1.8 亿开发者、每月约 120 万 PR 等）见 Agent HQ 公告（[Welcome home agents](https://github.blog/news-insights/company-news/welcome-home-agents/)，2025-10-28），标注为厂商自述。

## 证据缺口

1. "coding agent" → "cloud agent" 改名专项 changelog 未找到，仅能确认 docs 现行名称与旧 URL 重定向。
2. Technical Preview 起始日期（约 2025-07）未在本轮核验。
3. 运行镜像细节：golden image 内容、runner CPU/内存规格、仓库大小/Monorepo 规模上限未公开。
4. Agent token 完整 scope 清单未公布，官方仅给出行为边界。
5. 出站 IP 段/完整防火墙 allowlist 明细未逐项核验。
6. `approve-following-pr-comment` 术语未在当前官方文档/Changelog 命中；官方等价机制为 "Approve and run workflows" 按钮 + "Require approval for workflow runs" 设置。
7. Merge Queue 与 Copilot 无专门配合文档。
8. 生产发布场景无 Copilot 专属文档，仅能引用通用 environment protection / pending deployments。
9. Copilot Free 与 GA 公告表述（"all paid subscribers"）的关系未完全核验。
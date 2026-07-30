---
title: 大模型时代代码仓与托管平台的 Agent 控制面变化核验（2026-07-28）
tags:
  - research/agentic-cicd
  - research/evidence
  - company/github
  - company/gitlab
  - topic/repository-control-plane
status: complete
as_of: 2026-07-28
accessed_at: 2026-07-28
source_policy: primary-only
confidence: high
---

# 大模型时代代码仓与托管平台的 Agent 控制面变化核验（2026-07-28）

> [!abstract] 可被一手资料支持的结论
> **GitHub 与 GitLab 已把“仓内工作项 → Agent 会话/受限执行 → Issue 或 PR/MR 候选变更 → 既有合并、验证和审计门”的链路做成原生平台能力。** 因而，Repository 不再只是 Git 对象和人工协作页面的存放处：它正在成为 Agent 任务的上下文源、事件入口、配置载体、执行权限边界和结果接受控制面。
>
> 这是对两家平台公开机制的**跨来源推断**，不是“所有仓库都已启用 Agent”的采用率结论，也不意味着 Agent 绕过 CI、分支规则或人工批准。GitHub Agentic Workflows、GitHub 第三方 Coding Agents，以及 GitLab 若干治理能力仍分别处于 Public Preview、Preview 或 Beta/Experimental，不能把它们统称为已 GA 的行业标准。

## 一、研究问题与检索范围

### 研究问题

1. 大模型/编码 Agent 到来后，代码仓和代码托管平台是否已出现可证实的基础设施变化，而不只是 IDE 内的代码补全？
2. Repository 在 Agent 生命周期中承担哪些新的控制面职责：任务入口、上下文和配置、运行时、写入出口、验证/审批、身份权限与审计？
3. 哪些机制已 GA，哪些仍在 Preview/Beta/Experimental；哪些说法不能从现有一手资料推出？

### 范围与方法

- **平台范围：** GitHub 与 GitLab 的官方文档、官方 Changelog/博客和官方 API 文档；不使用媒体报道、市场份额或厂商外部案例来证明产品事实。
- **技术范围：** Issue/PR/MR 驱动任务、云端或 CI 运行环境、MCP/API/CLI 接口、代码评审与验证、令牌/服务账号/身份、分支保护/规则集、日志与审计。
- **时间口径：** 所有活文档均在 **2026-07-28** 访问；页面没有显示发布日期时写作 `n/a`，不从页面爬取时间反推发布日期。
- **证据口径：** 单一产品能力只证明该厂商的实现；只有 GitHub 与 GitLab 均有同构、且各自一手资料直接支持时，才写作跨平台变化。厂商对“生产力”“治理”的宣传不作为本研究结论。

## 二、主张—证据表

| ID | 类型与受限主张 | 一手证据（直接事实） | 发布日期；状态（截至访问日） | 结论边界 |
|---|---|---|---|---|
| F1 | **事实：GitHub 已将 Issue、PR 评论和 Agents UI 设为异步 Coding Agent 的任务入口。** | GitHub 文档列出三类入口：在 **Issue** 指派 Agent、在 **PR** 评论中 `@AGENT_NAME`、在 Agents UI 发起任务；Agent 完成后创建 PR 并请求人工 review。 | [GitHub Docs: third-party coding agents](https://docs.github.com/en/copilot/concepts/agents/about-third-party-coding-agents)，日期 n/a；**Public Preview**（该页明确标注）。 | 这是 GitHub 对 Copilot 与第三方 Coding Agent 的托管入口，不证明每个 GitHub App 或本地 Agent 都遵循该流。 |
| F2 | **事实：GitHub Copilot Coding Agent 已 GA，并把任务结果交付为 Draft PR 和 review 请求。** | GitHub Changelog 称：委派任务后，Copilot 在独立开发环境后台工作、打开 Draft PR；可通过 Issue 指派、Agents panel 或 VS Code 的 Delegate 按钮启动；完成后请求 review，PR 评论可要求迭代。 | [GitHub Changelog: Copilot coding agent is now GA](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)，2025-09-25；**GA**。 | 是 GitHub 厂商自述的产品行为，不是对修复正确率、合并率或企业采用率的证明。 |
| F3 | **事实：GitLab Custom Flow 可由仓内工作项、MR、流水线与评论提及触发，且把相应对象 ID/事件 payload 传给 Flow。** | GitLab 的 Custom Flow schema 说明：服务账号被指派给 Issue/MR 或成为 reviewer 时，该资源 IID 成为 goal；Pipeline event 的完整 webhook payload 成为 goal。Triggers 文档还列出 Issue/MR 评论中提及服务账号的事件。 | [GitLab Docs: Custom flow YAML schema](https://docs.gitlab.com/user/duo_agent_platform/flows/custom_flows_schema/)；[Triggers](https://docs.gitlab.com/user/duo_agent_platform/triggers/)，日期 n/a；Custom Flows 在 [GitLab 19.2](https://about.gitlab.com/blog/multi-step-software-delivery-with-agentic-flows/) **GA**（2026-07-16）。 | 证明 GitLab 原生事件/工作项可成为 Agent 输入；不证明所有 trigger 类型在所有部署和版本均已稳定可用。 |
| F4 | **事实：两平台都让 Agent 在平台托管的 CI 类运行时中工作，而不是只在用户 IDE 内生成文本。** | GitHub 说明 Copilot cloud agent 在由 **GitHub Actions** 驱动的 ephemeral development environment 中探索代码、改文件、运行测试和 linter。GitLab Flow 文档说明 Flow 可在 runner 上执行；其 session 详情能链接到 CI/CD job logs。 | [GitHub Docs: Configure the development environment](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)；[GitLab Docs: Composite identity](https://docs.gitlab.com/user/duo_agent_platform/composite_identity/)；[GitLab Docs: Troubleshooting](https://docs.gitlab.com/user/duo_agent_platform/troubleshooting/)，日期均 n/a；GitHub Coding Agent **GA**，GitLab Duo Agent Platform 自 18.8 **GA**。 | “CI 类运行时”是对 GitHub Actions / GitLab runner 的机制性概括；不代表 Agent 获得了 production deploy 权限。 |
| F5 | **事实：GitHub 把 Agent 工作环境与普通 Actions/Codespaces/Dependabot secrets 隔离，并为 Agent 与 MCP 单设 secret/variable 通道。** | Copilot cloud agent 只能取得配置为 **Agents secrets and variables** 的值；默认不能取得 GitHub Actions、Codespaces 或 Dependabot secrets。供 MCP 使用的 Agent secret/variable 必须以 `COPILOT_MCP_` 开头；值以环境变量提供且在 session log 中掩码。 | [GitHub Docs: Configure secrets and variables for Copilot cloud agent](https://docs.github.com/en/enterprise-cloud%40latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables)，日期 n/a；页面未对该配置单独标阶段。 | “掩码”不等于绝对防泄漏；此页也不证明 MCP server 本身安全，仍须审查其网络与工具权限。 |
| F6 | **事实：GitLab Flow 用人—服务账号的复合身份限制越权，并保留归因。** | GitLab 18.8 将 composite identity 设为 GA：一枚 token 组合触发者与 service account；实际访问取二者都能访问的项目和较低角色。其 OAuth scope 限于 `ai_workflows` 和 `mcp`，CI job token 还受 job-token 权限约束；MR 归因给触发者以维持职责分离。 | [GitLab Docs: Composite identity](https://docs.gitlab.com/user/duo_agent_platform/composite_identity/)，日期 n/a；**GA**（18.8）。 | 这是 GitLab Duo Agent Platform 的身份模型，不能外推为 GitHub 或任意 MCP Agent 的通用模型。 |
| F7 | **事实：Repository 内的规则/说明文件已成为 Agent 的可版本化行为配置。** | GitHub 可用 `copilot-setup-steps.yml` 定制 cloud agent runner、依赖和环境；GitHub 建议以 Rulesets 与 CODEOWNERS 控制 Copilot/MCP 配置文件修改。GitLab 支持 `AGENTS.md`、`.gitlab/duo/` custom flow/MCP config、MR review instructions；Fix CI/CD Pipeline Flow 会读取 `AGENTS.md`。 | [GitHub Docs: Configure environment](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment)；[GitHub Docs: Give access to resources](https://docs.github.com/en/copilot/tutorials/cloud-agent/give-access-to-resources)；[GitLab Docs: Customize Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/customize/)；[GitLab Docs: Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)，日期均 n/a；各项状态依产品页，见来源清单。 | `AGENTS.md`/review instructions 是给 Agent 的上下文或指导，不是 Policy Engine，也不能替代测试、审批或分支门禁。 |
| F8 | **事实：MCP 已被两平台接入为受控的外部工具/上下文接口，但授权仍留在平台控制面。** | GitHub repository/custom-agent MCP config 可引用专用 Agents secrets。GitLab MCP client 在 18.8 GA；首次调用外部 MCP tool 需用户审批（或批准到 session），且文档明确不能将 terminal/CLI commands 作同样的 session approval。 | [GitHub Docs: Custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration)；[GitLab Docs: MCP clients](https://docs.gitlab.com/user/gitlab_duo/model_context_protocol/mcp_clients/)，日期 n/a；GitLab MCP clients **GA**（18.8），GitHub 单项页未标状态。 | MCP 是连接/工具调用层，不自动授予仓库、云资源或生产环境权限；它也不替代 GitHub/GitLab API/CLI。 |
| F9 | **事实：Agent 输出仍被送回 PR/MR 与独立验证/人类 review 的现有工作流。** | GitHub Coding Agent 提交 Draft PR 并请求 review；GitHub 可对 PR 自动触发 Copilot code review。GitLab Fix Pipeline Flow 对 MR 内失败优先提出 inline code suggestions；若需改动 diff 外文件则新建 MR，无法可靠判断或安全敏感时可只评论而不改动。 | [GitHub Changelog: Coding Agent GA](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)；[GitHub Docs: Copilot code review](https://docs.github.com/en/copilot/concepts/agents/code-review)；[GitLab Docs: Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)，日期 n/a，除 GitHub 公告外；GitLab Flow **GA**（18.8，MR suggestions 路径 19.2 GA）。 | 这是“候选变更进入 review/validation 环”的证据，不表示 AI review 等同 required human approval 或可证明语义正确性。 |
| F10 | **事实：原有仓库分支规则仍可要求 PR review、status check、签名和部署成功，且对 App/可推送主体生效。** | GitHub protected branch 可要求 PR reviews、status checks、conversation resolution、signed commits、merge queue 与 deployment success；有推送权的 users/teams/**apps** 仍会在 required checks 失败时被阻止合并，PR 被要求时仍须走 PR。Rulesets 可以叠加并采用更严格版本。 | [GitHub Docs: About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)；[About rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)；[Available rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)，日期 n/a；平台既有功能，页面未给单独阶段。 | 管理员或被授权 bypass 主体的例外仍可能存在；因此不能笼统声称“任何 Agent 都不能越过分支规则”。 |
| F11 | **事实：平台开始为 Agent 行为提供专门的审计字段与会话可见性，但覆盖边界不同。** | GitHub Enterprise audit log 可用 `actor:Copilot` 查近 180 天 agent activity，agent event 含 `actor_is_agent`、`agent_session_id`、发起用户与 action；streaming 为 Public Preview 且有企业条件。GitLab Flow session 可从 UI 进入，session details 链至 CI job logs；GitLab 的 AI audit event report 仍列在 Beta/experiment。 | [GitHub Docs: Audit log events for agents](https://docs.github.com/en/copilot/reference/agentic-audit-log-events)；[GitLab Docs: Troubleshooting](https://docs.gitlab.com/user/duo_agent_platform/troubleshooting/)；[GitLab Docs: Duo Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/)，日期 n/a；GitHub streaming **Public Preview**，GitLab AI audit report **Beta/experiment**。 | GitHub audit log 不含本地 client prompts；GitLab 的 AI audit report 尚非 GA。因此不能声称现有审计已完整保留所有 prompt、tool input/output。 |
| F12 | **事实：GitHub 还在把自然语言 Repository automation 编译成受限 Actions workflow，但该产品仍是 Preview。** | Agentic Workflows 以 Markdown 定义、编译为 hardened `.lock.yml` Actions workflow；默认 read-only，写入只能经 frontmatter 声明的 safe outputs，且在 firewalled Actions environment 中执行。 | [GitHub Docs: About GitHub Agentic Workflows](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows)；[Creating GitHub Agentic Workflows](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows)，日期 n/a；**Public Preview**。 | 不能把 Agentic Workflows 的默认只读/safe-output 安全架构泛化到 GitHub Actions 的全部 workflow，或泛化到 GitLab。 |

## 三、变化前后对照（架构比较，不是“所有企业历史”的实证统计）

| 控制面对象 | 传统代码托管/CI 的主语 | Agent 到来后已可核验的新增或强化 | 仍不变的接受边界 |
|---|---|---|---|
| **任务入口** | 人从 Issue 读需求、建分支、开 PR/MR；CI 多由代码或手工触发。 | GitHub 支持将 Issue 直接指派给 Agent、在 PR 评论提及 Agent；GitLab Flow 接收 Issue/MR/service-account mention 和 pipeline event。Repository 工作项成为可机器路由的任务载体。 | 工作项文本与评论是非可信输入，可能包含 prompt injection；任务被接收不等于有权写入或合并。 |
| **上下文与指令** | README、代码、CI YAML、CODEOWNERS 主要服务于人和确定性工具。 | `AGENTS.md`、Copilot setup steps、GitLab MR review instructions、Duo/MCP config 使仓内文件还定义 Agent 任务上下文、运行环境和 review 关注点。 | 指令是行为引导，不是强制政策；它们自身应由 CODEOWNERS/Ruleset/MR 审核保护。 |
| **执行面** | Runner 执行固定 steps；IDE 是开发者的主要生成/编辑环境。 | GitHub cloud agent 在 Actions-backed ephemeral environment 执行测试/lint；GitLab flows 在 runner 上执行并把 session 对接 CI job logs。 | 运行时执行成功、命令退出码或模型“完成”不构成质量、合规或上线的独立证明。 |
| **变更出口** | 人工分支和 PR/MR 是主要变更通道。 | Agent 的候选结果被产品化为 Draft PR、PR comment iteration、MR code suggestion 或新 MR；Agent 与 reviewer 可以形成异步闭环。 | 仍需 branch protection/ruleset、required checks、code review、部署环境审批和组织职责分离。 |
| **扩展接口** | Git provider API、webhook、CLI、App/token 主要由脚本和集成调用。 | MCP 使 Agent Host 能从 repository configuration 接入外部 tools/context；GitHub 单设 MCP secrets，GitLab 在首次外部 tool 调用时要求审批。 | MCP 工具发现/调用不等于被授予后端资源权限；每个 MCP server 的身份、网络和数据边界仍须单独审查。 |
| **身份与审计** | 用户、deploy key、PAT、GitHub App/CI token 是主要主体，审计以用户/API 操作为中心。 | GitLab composite identity 把人和 service account 的最小交集写入 Flow token；GitHub audit 记录 agent actor、session ID 与 initiator。 | 覆盖不完整：GitHub 不记录本地 client prompt；GitLab AI audit report 仍是 Beta/experiment。 |

## 四、跨来源判断、反例与证据边界

### J1：Repository 正成为 Agent 的“任务与控制面”，但不是唯一的推理运行时

**来源事实。** GitHub 把 Issue、PR 评论和 Agents UI 接入 Coding Agent；GitLab 把 Issue/MR、service-account mention 与 pipeline event 接入 Flow（F1–F3）。两家都允许仓内配置改变 Agent 的上下文、环境或工具连接（F7–F8）。

**跨来源推断（高置信）。** Repository 的角色已从“保存代码与人工协作记录”扩展为：任务路由对象、受版本控制的 Agent 行为配置、可授予最小权限的资源边界、候选写入与验证的归宿。称其为 **Agent control plane** 是对这一组可观察机制的架构概括。

**反例/限制。** 模型推理可能发生在云端、IDE、CLI 或第三方 Agent SaaS；Repository 不是模型本体，也不拥有所有外部系统的授权。GitHub 第三方 agents、GitHub Agentic Workflows 与 GitLab 治理功能的状态并不相同。

### J2：Agent 执行已嵌入现有 CI 基础设施，而不是取代 CI/CD

**来源事实。** GitHub 使用 GitHub Actions 驱动的 ephemeral environment；GitLab flows 在 runner 上执行并保留 CI job logs（F4）。GitHub Agentic Workflows 更直接地编译为 hardened Actions workflow（F12）。

**跨来源推断（高置信）。** 托管平台正把 Agent 作为受 CI/runner、token、日志和网络边界约束的工作负载，而非将“聊天生成的代码”直接视为可发布物。对 CI/CD 架构而言，合理分层是：Agent 负责理解、尝试和生成候选；确定性 CI、策略与人工/环境审批负责验证与接受。

**反例/限制。** 这不说明每一种 Agent 必须在 CI runner 执行，或所有 CI job 都具备 Agent 的 sandbox/secret 隔离。尤其 GitHub Agentic Workflows 的安全模型仍处 Preview。

### J3：平台正在把 Agent 身份、凭据与可审计行动显式化，但审计并非完整语义记录

**来源事实。** GitLab 以 composite identity 防止 service account 或发起者单方越权，并对 MR 保留发起者归因（F6）；GitHub audit event 有 agent 标志、session ID、agent action 和 initiator（F11）。

**跨来源推断（中高置信）。** 在 Agent 时代，“谁发起、哪个代理执行、以何种受限 token 访问、生成了哪项仓库动作”正成为代码托管平台的一级可治理对象；这比仅把 Agent 当作共享 PAT 的脚本更接近可追责的控制平面。

**反例/限制。** GitHub 明确不记录本地客户端 prompt；GitLab AI audit event report 尚为 Beta/experiment。审计记录不能自动证明 prompt、工具响应、代码语义或批准过程完整无误。

### J4：PR/MR 仍是候选变更的受控接收器，不是可被 Agent 绕过的旧式流程

**来源事实。** GitHub Coding Agent 交付 Draft PR 并请求 review；GitLab 对失败流水线优先提交 MR suggestions 或新 MR，并在安全敏感/上下文不足时只评论（F9）。GitHub rulesets/branch protection 继续可要求 review、checks、签名和部署成功，且对可推送 App 一样生效（F10）。

**跨来源推断（高置信）。** Agent 扩大了候选变更的产生速度，但没有取消独立接受门。更准确的管理命题是：**把 Agent 输出定义为“可验证的候选”，让 PR/MR、policy、scan/test、approval 和 deploy gate 决定是否接受。**

**反例/限制。** 具有 bypass 权限的管理员/App 可能例外；不同 GitLab 项目的 MR approval/push rule 配置另行决定。不能用 GitHub 规则文档证明 GitLab 的具体 branch policy 行为。

### 尚待验证的假设（不进入正式结论）

1. “Repository control plane”是否可显著降低 Agent 变更的逃逸率、MTTR 或合规成本，当前没有本次范围内可复现实验或跨厂商对照数据。
2. GitLab 的 next-generation source-code management、Orbit context graph、agents governance 等近期宣布能力的生产覆盖率与长期稳定性仍需逐功能核验，不能以路线图/营销页替代现状证据。
3. MCP 的引入是否提升端到端交付可靠性尚无本研究范围内的一手对照；它目前只能证明“可连接、可审批/配置”，不能证明“更安全”或“更有效”。

## 五、对 CI/CD 设计的可操作含义（建议，不是来源事实）

1. **把 Issue/PR/MR 当作 Agent 的有状态任务协议。** 明确输入、目标、风险标签、验收条件和可回放链接；不把自由文本 prompt 当作唯一任务记录。
2. **将 Agent configuration 视为高风险代码。** 对 `AGENTS.md`、Copilot/MCP/Flow 配置、setup steps 使用 CODEOWNERS、Ruleset、PR review 与变更审计；配置能扩大 Agent 的上下文、工具或运行时权限。
3. **将写权限拆开。** Agent Runtime 默认只读或最小写入；PR/MR、评论、issue、部署等高影响输出通过显式 safe output、窄 token、服务账号和环境批准分别放行。
4. **保留独立 Oracle。** build、test、SAST/SCA、签名、policy、required check、环境保护和人工 reviewer 的判定必须独立于 Agent 自述和 CLI exit code。
5. **审计关联至少覆盖四元组。** `initiating human`、`agent/session`、`runtime/token`、`repository action/PR/MR`；并承认平台日志可能不含本地 prompt 或全部 MCP 往返。

## 六、来源清单（全部一手资料）

| # | 来源 | 发布日期 | 状态/用途 | 访问 |
|---|---|---|---|---|
| 1 | GitHub Changelog, [Copilot coding agent is now generally available](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/) | 2025-09-25 | **GA**；Issue/Agents panel/PR 交付 | 2026-07-28 |
| 2 | GitHub Docs, [About third-party coding agents](https://docs.github.com/en/copilot/concepts/agents/about-third-party-coding-agents) | n/a | **Public Preview**；Issue、PR comment、Agents UI、security validation | 2026-07-28 |
| 3 | GitHub Docs, [Configure the development environment](https://docs.github.com/en/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment) | n/a | Cloud agent ephemeral Actions environment | 2026-07-28 |
| 4 | GitHub Docs, [Configure secrets and variables for Copilot cloud agent](https://docs.github.com/en/enterprise-cloud%40latest/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/configure-secrets-and-variables) | n/a | Agent-specific secret/variable and MCP boundary | 2026-07-28 |
| 5 | GitHub Docs, [Custom agents configuration](https://docs.github.com/en/copilot/reference/custom-agents-configuration) | n/a | Repository/custom Agent MCP configuration | 2026-07-28 |
| 6 | GitHub Docs, [About GitHub Agentic Workflows](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows)；[Creating GitHub Agentic Workflows](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows) | n/a | **Public Preview**；Markdown → hardened Actions、permissions/safe outputs | 2026-07-28 |
| 7 | GitHub Docs, [About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)；[About rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)；[Available rules](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) | n/a | PR/review/check/signature/deploy gate | 2026-07-28 |
| 8 | GitHub Docs, [Audit log events for agents](https://docs.github.com/en/copilot/reference/agentic-audit-log-events)；[Reviewing audit logs for Copilot](https://docs.github.com/en/enterprise-cloud%40latest/copilot/how-tos/administer-copilot/manage-for-enterprise/review-audit-logs) | n/a | Agent actor/session audit；streaming **Public Preview** | 2026-07-28 |
| 9 | GitLab Docs, [GitLab Duo Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/)；GitLab, [GA announcement](https://about.gitlab.com/press/releases/2026-01-15-gitlab-announces-duo-agent-platform-general-availability/) | 2026-01-15（公告）；文档 n/a | Agent Platform **GA**（18.8）；功能状态矩阵 | 2026-07-28 |
| 10 | GitLab Docs, [Custom flows](https://docs.gitlab.com/user/duo_agent_platform/flows/custom/)；[Custom flow YAML schema](https://docs.gitlab.com/user/duo_agent_platform/flows/custom_flows_schema/)；[Triggers](https://docs.gitlab.com/user/duo_agent_platform/triggers/)；GitLab Blog, [Custom Flows GA](https://about.gitlab.com/blog/multi-step-software-delivery-with-agentic-flows/) | 2026-07-16（GA blog）；文档 n/a | Custom Flows **GA**（19.2）；事件/工作项任务协议 | 2026-07-28 |
| 11 | GitLab Docs, [Composite identity](https://docs.gitlab.com/user/duo_agent_platform/composite_identity/)；[Flow execution variables](https://docs.gitlab.com/user/duo_agent_platform/flows/execution_variables/) | n/a | Composite identity **GA**（18.8）；token/服务账号/发起人归因 | 2026-07-28 |
| 12 | GitLab Docs, [Customize Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/customize/)；[MCP clients](https://docs.gitlab.com/user/gitlab_duo/model_context_protocol/mcp_clients/)；[Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/) | n/a | MCP clients **GA**（18.8）；Fix Pipeline Flow **GA**（18.8，相关 MR suggestion 19.2 GA） | 2026-07-28 |
| 13 | GitLab Docs, [Customize review instructions](https://docs.gitlab.com/user/duo_agent_platform/customize/review_instructions/)；[Security threats in agentic systems](https://docs.gitlab.com/user/duo_agent_platform/security_threats/)；[Troubleshooting](https://docs.gitlab.com/user/duo_agent_platform/troubleshooting/) | n/a | review instructions 是 guidance 非 enforced policy；Flow session/log 与安全边界 | 2026-07-28 |

---

**事实审计说明：** 本文的 F1–F12 仅复述或紧贴上述官方资料可核验的产品机制；J1–J4 明确为跨来源架构推断；“建议”和“尚待验证假设”不应被回写成产品事实或市场结论。

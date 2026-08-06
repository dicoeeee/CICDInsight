---
title: Microsoft 智能化 CI/CD 扩展能力候选池（GitHub / Azure DevOps / Azure）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/microsoft
  - company/github
  - company/azure-devops
status: complete
as_of: 2026-08-06
accessed: 2026-08-06
confidence: high-for-mechanism-and-status; medium-for-Azure-SRE-Agent-lifecycle-label-not-explicitly-stated
---

# Microsoft 智能化 CI/CD：从“代码审查 Agent”扩展到“自主修复—门禁—运维 Agent—MCP 接入”

## 提纲

1. 范围与产品状态边界
2. 候选能力池（一张表）
3. 逐项能力介绍
4. 相对原范围的保留／拆分／替换建议
5. 明确排除项
6. 主要一手来源
7. 证据缺口与复核记录

## 结论先行

截至 2026-08-06，Microsoft 侧最有区分度的能力已从“Copilot coding agent / code review”扩展为五条线：**跨阶段自主修复（Agentic autofix、Dependabot→AI agent、Fix with Copilot）**、**代码质量门禁（GitHub Code Quality）**、**Azure DevOps 上的 Copilot 审查与 Autofix（limited public preview）**、**运维阶段 AGENT（Azure SRE Agent，Review/Autonomous run modes）**、以及 **MCP 接入层（Remote Azure DevOps MCP Server / Azure MCP / Azure Skills）**。

生命周期必须拆开写：Copilot coding agent 与 agentic code review 已 **GA**；GitHub 侧 Agentic autofix、Agentic Workflows、Code Quality 状态各异（autofix 为 public preview，Code Quality 为 GA）；Azure DevOps 侧 Copilot code reviews 与 Autofix 仍为 **limited public preview**（路线图称 2026 Q3 转 public preview，截至访问日未落地）；Azure SRE Agent 文档未显式标注 Preview 或 GA，保持 unverified。

## 范围与判断口径

- 只使用 GitHub Changelog、GitHub 官方仓库、Microsoft Learn 官方文档与 Azure DevOps release notes／roadmap；全部于 2026-08-06 访问（部分条目在 2026-08-05 访问并复核）。
- “能力”指用户可配置、调用或消费的可见产品功能；不把通用 MCP 协议、GitHub Actions 运行时本身单独写成智能 CI/CD 功能。
- 自动生成的修复仍是候选代码，合并、部署、恢复仍由 PR Gate、Ruleset、外部测试、人工审批与目标环境权限决定；MCP/CLI 可调用不等于获得授权。
- 任何数值、命中率与效果（如 GitHub Code Quality 的 67.3%）属厂商自述，不写成页面结论。
- Azure DevOps 路线图日期是“预期”而非承诺；截至访问日 Remote MCP GA、Copilot Code Reviews public preview、Autofix public preview 均标 2026 Q3 但未在 release notes 中出现。

## 候选能力池

| # | 候选能力 | 机制与 CI/CD 阶段 | 用户可见产物 | 状态、日期与授权／副作用边界 | 上页建议 |
|---|---|---|---|---|---|
| M1 | **Copilot coding agent（自主开发 Agent）** | 异步自治开发：由用户创建任务，Agent 在 GitHub Actions 自有开发环境运行，编辑代码、跑测试、交付 Draft PR；PR 评论可要求修改。阶段：编码、PR 前。 | Draft PR、任务评论、run 日志。 | **GA 2025-09-25**。所有付费 Copilot 计划；企业需管理员启用。Draft PR 是候选变更，合并仍由人决定。 | 保留为基础面，不单独做主卡。 |
| M2 | **agentic Copilot code review** | 基于 Agent 的 tool-calling 架构，主动取代码、跑分析、生成内联评论；支持 Agent skills 与 MCP。阶段：PR 审查。 | PR 内联评论、审查报告；分析深度可配置。 | **GA**（2026-03-05 agentic 架构 + GA）；2026-04-27 起消耗 Actions minutes；2026-07-29 skills/MCP GA。自托管 runner 需额外设置。 | 保留；注意 actions minutes 计费与自托管边界。 |
| M3 | **Dependabot 告警 → AI agent 修复** | 安全告警可指派给 coding agent，跨仓库生成 Draft PR 修复，并可迭代。阶段：依赖／安全告警 → 修复 PR。 | 指派后的修复 Draft PR。 | **GA 2026-04-07**（Improvement）。需 GitHub Code Security + 含 coding agent 的 Copilot 计划；仅 GitHub.com。 | 推荐，作为“告警→自主修复”闭环入口。 |
| M4 | **GitHub Code Quality** | CodeQL 确定性检测 + AI 辅助检测 + Copilot Autofix + 测试覆盖率 + Ruleset 门禁。阶段：PR 合并前质量门禁。 | Code Quality 报告、autofix 建议、覆盖率数据、ruleset。 | **GA 2026-07-20**。仅 Enterprise Cloud + Team；$10/active committer/月 + AI/计算用量；GHES 暂不支持。厂商自述 67.3% 修复于合并前解决。 | **强烈推荐，新增。** 是“AI 检测 + 自主修复 + 门禁”的合并前闭环。 |
| M5 | **Copilot code review for Azure Repos** | 将 agentic code review 带到 Azure Repos；PR 内分析与建议。阶段：PR 审查（Azure DevOps）。 | PR 审查评论；项目级成本报告（Sprint 276）。 | technical preview **2026-06-02**；Sprint 275 **2026-06-17 limited public preview**；路线图 2026 Q3 转 public preview（未落地）。无需 GitHub Copilot 订阅，按 GitHub AI credits 计费。 | 保留；标注状态为 limited public preview。 |
| M6 | **Copilot Autofix for code scanning（Azure DevOps）** | 对 GHAzDO code scanning 告警生成修复建议。阶段：扫描告警 → 修复 PR。 | Autofix 建议、重试／失败状态。 | Sprint 275 **limited public preview**（需 sign-up）；Sprint 276 增加 org/project/repo 级启用与失败重试；路线图 2026 Q3 public preview（未落地）。 | 保留；标注 limited public preview。 |
| M7 | **Azure SRE Agent（运维 Agent）** | 连接可观测性、事件平台与代码仓库，自动化事件分诊、根因调查与缓解建议；Review（默认）与 Autonomous 两种 run mode；权限门控下运行。阶段：发布后验证、事故调查与恢复准备。 | 调查摘要、根因假设、缓解方案、ticket；建议审批动作。 | 文档当前版本（overview ms.date 2026-07-30）。**文档未显式标 Preview 或 GA → unverified**。`sre.azure.com`；“No change deploys without human sign-off”与 Autonomous mode 并存于文档。按 AAU 计费（always-on 4 AAU/agent-hour + active flow）。 | 强烈推荐新增，但页面必须标注状态未核验。 |
| M8 | **GitHub Copilot for Azure / Azure MCP / Azure Skills** | IDE（VS Code／VS 2022/2026）内 Agent 模式操作 Azure 资源、生成 IaC、排查应用；底层 tools 经 Azure MCP Server；azure-prepare/validate/deploy 等 26+ skills 属 Azure Skills Plugin。阶段：编码、部署前 IaC 生成与验证。 | IDE 内工具列表、az/Azd 生成、Bicep 模板、诊断。 | docs get-started ms.date 2026-06-01；Azure MCP Server overview ms.date 2026-06-02（updated 2026-07-31）。文档未系统标注各入口 GA/Preview。 | 作为开发者入口与扩展面，不单独占主卡。 |
| M9 | **GitHub Agentic Workflows（自然语言 → Actions）** | 以 Markdown 自然语言定义工作流，系统转换为 Actions YAML；默认只读，sandbox 容器 + Agent Workflow Firewall、integrity filter 与威胁检测。阶段：持续集成定义与执行。 | Agentic workflow 配置、Actions run。 | technical preview 2026-02-13 → **public preview 2026-06-11**。只读默认；执行产物受 firewall／sandbox 约束。 | 推荐作为 CI 定义形态的差异化点。 |
| M10 | **Agentic autofix for code scanning（GitHub）** | 对 CodeQL 与第三方扫描告警，跨代码库探索、提议修复并重跑扫描以验证。阶段：扫描告警 → 修复 PR。 | 修复建议、验证后的 label/autorun。 | **public preview 2026-07-10**；取代经典 autofix。AI credits + Actions minutes；需 Code Security/Advanced Security + 含 cloud agent 的 Copilot。 | **强烈推荐，新增。** 是“修复并重跑验证”的关键闭环。 |
| M11 | **Fix with Copilot for failing Actions** | 对失败的 Actions job，Copilot 分析并将修复建议为 PR。阶段：CI 失败 → 修复。 | 修复 PR、一键修复入口。 | 2026-05-18 一键修复；2026-06-04 扩展至 Pro/Pro+/Max；2026-07-23 GitHub Mobile 支持。 | 推荐作为 CI 失败闭环，可与 M4 合并叙述。 |
| M12 | **Code-to-cloud risk visibility（Defender for Cloud）** | 将 GitHub 代码告警与云运行时上下文关联，运行时感知过滤与 campaign 定向。阶段：代码 → 运行风险关联。 | 告警关联视图、campaign。 | **GA 2026-05-05**。 | 可选；作为跨界（代码→云）风险视角。 |
| M13 | **Remote Azure DevOps MCP Server** | 以 remote MCP server 暴露 Azure DevOps（repos/wit/pipelines/wiki/testplan/advsec/elm toolsets），供 Copilot/VS/Foundry/Copilot Studio 等接入。阶段：跨工具接入层。 | MCP tools、streamable HTTP 端点、Entra ID 认证。 | Sprint 271 **public preview 2026-03-31**；路线图 2026 Q3 GA **未落地**（DevOps 索引页仍标 preview）；Sprint 276 增加 ELM tools（ELM 本身 private preview）。 | 作为接入层放在页脚，不占主卡。 |

## 逐项能力介绍

### M1 Copilot coding agent —— 已 GA 的异步自治开发 Agent

来源：[GitHub Changelog 2025-09-25](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available)。Agent 由用户在 GitHub 上创建任务，在 GitHub Actions 自带开发环境中异步运行，可编辑代码、运行测试并交付 Draft PR；用户在 PR 评论中可要求修改，Agent 迭代。所有付费 Copilot 计划可用；企业组织需管理员启用。机制上它把“开发”搬进平台内自治循环，但交付物仍是待合并的草案。

### M2 agentic Copilot code review —— Agent 架构 + Project 内联审查

来源：[2026-03-05 agentic 架构 + GA](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture)、[2026-07-29 skills/MCP GA](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available)。审查从静态规则升级为可调用外部工具（Team 自定义 Agent skills、MCP server）的 Agent。自托管 runner 需额外配置；2026-04-27 起运行消耗 Actions minutes（计费边界）。Review 评论是建议，不是合并门禁。

### M3 Dependabot 告警 → AI agent 修复

来源：[2026-04-07](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation)。Dependabot 告警可指派给 coding agent，agent 生成修复 Draft PR。前提：GitHub 组织的 Code Security 功能 + 含 coding agent 的 Copilot 计划；仅 GitHub.com。这是“安全告警 → 自主修复候选”的官方入口。

### M4 GitHub Code Quality —— 合并前的 AI 质量门禁

来源：[2026-07-20 GA](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available)。组合 CodeQL 确定性检测 + AI 辅助检测 + Copilot Autofix + 测试覆盖率 + Ruleset 强制执行。仅 Enterprise Cloud + Team；定价 $10/active committer/月 + AI/计算用量；GHES 暂不支持。厂商自述“67.3% 的修复在合并前被解决”属营销数据，不进入正式结论。其独特点是“AI 修复 + 覆盖率 + 门禁”打包为 PR 合并前的一站式质量面。

### M5 Copilot code review for Azure Repos —— limited public preview

来源：[GitHub changelog 2026-06-02](https://github.blog/changelog/2026-06-02-github-copilot-code-review-for-azure-repos-is-now-in-technical-preview)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update)、[路线图](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline)。把 agentic code review 带到 Azure Repos。无需 GitHub Copilot 订阅，按 GitHub AI credits 计费。Sprint 276 增加项目级成本报告标签。路线图将 public preview 定在 2026 Q3，截至 2026-08-05 更新未落地。

### M6 Copilot Autofix for code scanning（Azure DevOps）—— limited public preview

来源：[Autofix 文档（Preview）](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning-autofix?view=azure-devops)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update)、[Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update)、[路线图 ghazdo/copilot-autofix](https://learn.microsoft.com/en-us/azure/devops/release-notes/roadmap/2026/ghazdo/copilot-autofix)。对 GHAzDO code scanning 告警生成修复。Sprint 275 以 limited public preview 开放（需注册）；Sprint 276 增加 org/project/repo 三级启用、失败状态与重试。public preview 目标 2026 Q3，未落地。

### M7 Azure SRE Agent —— 运维阶段 Agent（状态标签未核验）

来源：[overview ms.date 2026-07-30](https://learn.microsoft.com/en-us/azure/sre-agent/overview)、[pricing-billing ms.date 2026-05-12](https://learn.microsoft.com/en-us/azure/sre-agent/pricing-billing)、[create-agent](https://learn.microsoft.com/en-us/azure/sre-agent/create-agent)、[run-modes ms.date 2026-06-02](https://learn.microsoft.com/en-us/azure/sre-agent/run-modes)。

- 连接可观测性（Azure Monitor、App Insights、Log Analytics、Grafana）、事件平台（Azure Monitor Alerts、PagerDuty、ServiceNow）与代码仓库（GitHub、Azure DevOps）三端，进行事件分诊、跨信号根因关联与缓解建议。
- **Run modes**：Review（默认）与 Autonomous，按 response plan 与 scheduled task 分别设置；SRE Agent Administrators 可审批。overview 的“No change deploys without human sign-off”与 Autonomous mode 并存，页面表述必须同时写两者。
- 扩展原语五种：skills、subagents（内建 6 个：Explore、Plan、CodeReview、Bash、Verification、GeneralPurpose）、Python tools、MCP servers（40+ 托管 connector）、agent hooks（command/prompt 两类）。前置 permission gate 与审计遥测。
- 计费：AAU（Azure Agent Unit）——always-on 4 AAU/agent-hour + active flow 按 token 类型（input/output/cache read/write）计；模型含 Claude Opus 4.6、GPT 5.3 Codex、GPT 5.2。
- 区域：Sweden Central、East US 2、Australia East；入口 `sre.azure.com`。
- **缺口**：overview、pricing、create-agent、run-modes 均未显式写 Preview 或 GA 标签，故产品生命周期状态记 `unverified`。

### M8 GitHub Copilot for Azure / Azure MCP / Azure Skills

来源：[get-started ms.date 2026-06-01](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/get-started)、[Azure MCP Server overview ms.date 2026-06-02 (updated 2026-07-31)](https://learn.microsoft.com/en-us/azure/developer/azure-mcp-server/overview)。VS Code/VS 2022/VS 2026 内以 Agent 模式查询 Azure 资源、生成 IaC（Bicep/ARM、az CLI）、排查应用。底层工具经 Azure MCP Server（GitHub Copilot agent mode、OpenAI Agents SDK、Semantic Kernel 均可作为 MCP client）。`azure-prepare`、`azure-validate`、`azure-deploy`、`azure-diagnostics`、`azure-cost` 等 26+ skills 属于 **Azure Skills Plugin**（github.com/microsoft/azure-skills），与 Azure MCP Server 及 Foundry MCP Server 配套，不是 GitHub Copilot for Azure 扩展内置。文档未系统标注各入口 GA/Preview。

### M9 GitHub Agentic Workflows —— 自然语言定义的 CI

来源：[2026-06-11 public preview](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview)、文档 [github.github.io/gh-aw/](https://github.github.io/gh-aw/)。以 Markdown 自然语言描述工作流，系统解析为 Actions YAML。默认只读；执行在 sandbox 容器内，受 Agent Workflow Firewall、integrity filter 与威胁检测约束。2026-06-11 起无需 PAT。它改变“CI 定义”形态，但不改变执行权限模型。

### M10 Agentic autofix for code scanning（GitHub）—— public preview

来源：[2026-07-10 public preview](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview)。对 CodeQL 与第三方 code scanning 告警，Agent 跨代码库探索、生成修复并重跑扫描验证，取代经典 autofix。计费 AI credits + Actions minutes；需 GitHub Code Security/Advanced Security + 含 cloud agent 的 Copilot 计划。这是“修复 + 验证”闭环，比仅生成 PR 更进一步。

### M11 Fix with Copilot for failing Actions

来源：[2026-05-18 一键修复](https://github.blog/changelog/2026-05-18-fix-failing-actions-with-copilot-cloud-agent)、[2026-06-04 Pro/Pro+/Max](https://github.blog/changelog/2026-06-04-fix-with-copilot-for-failing-actions-now-in-pro-pro-and-max)、2026-07-23 GitHub Mobile。对失败 Actions job 生成修复 PR。与 M10/M4 同属“CI/扫描失败 → Copilot 修复”方向，可合并叙述。

### M12 Code-to-cloud risk visibility（Defender for Cloud）—— GA

来源：[2026-05-05 GA](https://github.blog/changelog/2026-05-05-code-to-cloud-risk-visibility-with-microsoft-defender-for-cloud-is-now-generally-available)。把 GitHub 代码告警与 Defender for Cloud 运行时上下文关联，支持运行时感知过滤与 campaign 定向。属码→云跨界风险视图，非 CI/CD 门禁本身。

### M13 Remote Azure DevOps MCP Server —— public preview，GA 未落地

来源：[Remote MCP 文档 ms.date 2026-08-03](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server)、[Sprint 271](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-271-update)、[Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update)、[路线图](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline)。以 remote MCP server 暴露 Azure DevOps toolsets（repos/work items/pipelines/wiki/testplan/advsec/elm），streamable HTTP 传输 + Entra ID 认证，支持 VS、VS Code、Foundry、Copilot Studio、GitHub Copilot。Sprint 276 (2026-07-09) 增加 Enterprise Live Migration tools（ELM 本身 private preview）。路线图仍标 2026 Q3 GA、未落地；DevOps 索引页仍写 “(preview)”。

## 相对原范围的取舍

原范围（MS-1…MS-8 + Agentic Workflows + Remote MCP）全部保留，建议结构化为五条线：

```text
合并前闭环（GitHub）：
  M10 Agentic autofix（扫描告警→修复→重跑验证，PP）
  M3 Dependabot→AI agent（依赖告警→修复 PR，GA）
  M4 Code Quality（检测+修复+覆盖率+Ruleset 门禁，GA）
  M11 Fix with Copilot for failing Actions（CI 失败→修复）
Azure DevOps 侧（均 limited public preview）：
  M5 Copilot code review for Azure Repos
  M6 Copilot Autofix for code scanning
运维阶段：
  M7 Azure SRE Agent（Review/Autonomous，状态 unverified）
接入与定义层：
  M13 Remote Azure DevOps MCP Server（PP，GA 未落地）
  M8 GitHub Copilot for Azure / Azure MCP / Azure Skills
  M9 Agentic Workflows（自然语言定义 CI，PP）
跨云风险：
  M12 Code-to-cloud（Defender for Cloud，GA）
```

| 原项 | 建议 | 原因 |
|---|---|---|
| M1 coding agent / M2 code review | 保留为基础面 | 均已 GA，是其他能力的宿主与入口。 |
| M3 Dependabot→AI agent | 保留，归入“告警→修复”闭环 | 官方、GA、可回链。 |
| M4 GitHub Code Quality | 新增为主卡 | GA 且是“AI 检测+修复+门禁”打包，最具话题性；注意计费与 GHES 限制。 |
| M5/M6 Azure DevOps Copilot 审查与 Autofix | 保留，状态必须写 limited public preview | 与 GitHub 侧形成“Azure Repos 也有”的对照，但生命周期未成熟。 |
| M7 Azure SRE Agent | **新增为主卡候选** | 运维阶段的差异化；但文档未标生命周期，页面必须写 unverified。 |
| M8 Copilot for Azure | 列为开发者入口/扩展面 | 与 M13 共用 MCP 接入叙事。 |
| M9 Agentic Workflows | 保留 | 改变 CI 定义形态，public preview。 |
| M10 Agentic autofix | **新增为主卡候选** | 修复+重跑验证闭环，public preview。 |
| M13 Remote MCP | 保留，页脚接入层 | GA 未落地，不能写成 GA。 |

## 明确排除项

- **github.com/Azure/mcp（Azure MCP Server Registry）**：是基于 Azure API Center 的企业 MCP 注册表/基础设施，不是 CI/CD 智能功能。
- **经典 autofix**：已被 M10 Agentic autofix 取代，不再作为独立能力呈现。
- **GitHub Actions 运行时 / MCP 协议本身**：是执行与连接机制；可调用不等于授权，不写成端到端自治。
- **Azure DevOps 路线图中的未落地项**（Copilot Code Reviews public preview、Autofix public preview、Remote MCP GA、Code-to-cloud GHAzDO 集成）只作为 roadmap 存在，不写成已发布。
- **“自动部署/自动恢复”表述**：未找到官方材料证明 coding agent、SRE Agent 或 autofix 将合并、部署、恢复以一个端到端事务自动执行；SRE Agent 的 Autonomous mode 仍受权限门控与响应计划约束。

## 主要一手来源（均访问于 2026-08-06）

| 来源 | 发布/更新信息 | 本研究使用范围 |
|---|---|---|
| [Copilot coding agent GA](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available) | 2025-09-25 | M1 机制、计划覆盖、企业开关。 |
| [Copilot code review agentic architecture](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture) | 2026-03-05 | M2 GA、actions minutes、自托管边界。 |
| [Copilot code review skills/MCP GA](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available) | 2026-07-29 | M2 skills/MCP GA。 |
| [Dependabot alerts → AI agents](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation) | 2026-04-07 | M3 前提与范围。 |
| [GitHub Code Quality GA](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available) | 2026-07-20 | M4 GA、定价、GHES、厂商数据。 |
| [Copilot code review for Azure Repos technical preview](https://github.blog/changelog/2026-06-02-github-copilot-code-review-for-azure-repos-is-now-in-technical-preview) | 2026-06-02 | M5 起点。 |
| [Azure DevOps Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update) | 2026-06-17 | M5/M6 limited public preview。 |
| [Azure DevOps Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update) | 2026-07-09 | M5 成本报告、M6 多级启用/失败重试、CodeQL default setup GA、Remote MCP ELM tools。 |
| [Azure DevOps Roadmap](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline) | ms.date 2026-08-05 | M5/M6/M13 生命周期目标与未落地状态。 |
| [Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview) | ms.date 2026-07-30 | M7 机制、扩展原语、集成、审批表述。 |
| [Azure SRE Agent run modes](https://learn.microsoft.com/en-us/azure/sre-agent/run-modes) | ms.date 2026-06-02 | M7 Review/Autonomous 模式、permission 交互。 |
| [Azure SRE Agent pricing-billing](https://learn.microsoft.com/en-us/azure/sre-agent/pricing-billing) | ms.date 2026-05-12 | M7 AAU 计费、模型、限额。 |
| [Azure SRE Agent create-agent](https://learn.microsoft.com/en-us/azure/sre-agent/create-agent) | ms.date 2026-03-09 | M7 区域、权限级别、入口。 |
| [GitHub Copilot for Azure get-started](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/get-started) | ms.date 2026-06-01 | M8 安装、Agent 模式、工具。 |
| [Azure MCP Server overview](https://learn.microsoft.com/en-us/azure/developer/azure-mcp-server/overview) | ms.date 2026-06-02 (updated 2026-07-31) | M8 Azure Skills Plugin 26+ skills、客户端矩阵。 |
| [Agentic autofix public preview](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview) | 2026-07-10 | M10 机制、计费、取代经典 autofix。 |
| [GitHub Agentic Workflows public preview](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview) | 2026-06-11 | M9 机制、sandbox/firewall、无 PAT。 |
| [Code-to-cloud risk visibility GA](https://github.blog/changelog/2026-05-05-code-to-cloud-risk-visibility-with-microsoft-defender-for-cloud-is-now-generally-available) | 2026-05-05 | M12。 |
| [Fix with Copilot for failing Actions](https://github.blog/changelog/2026-06-04-fix-with-copilot-for-failing-actions-now-in-pro-pro-and-max) | 2026-06-04 | M11 可用范围。 |
| [Remote Azure DevOps MCP Server docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server) | ms.date 2026-08-03 | M13 传输、认证、toolsets、客户端。 |
| [Azure DevOps Autofix 文档](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning-autofix?view=azure-devops) | ms.date 2026-08-05 | M6 文档状态（Preview）。 |

## 证据缺口

1. **Azure SRE Agent 生命周期标签**：overview/pricing/create-agent/run-modes 均未显式写 Preview 或 GA，故 `unverified`；页面如需引用必须标注。
2. GitHub Copilot for Azure 各入口（skills、MCP tools）未被微软逐项标注 GA/Preview；只报告文档日期，不推断生命周期。
3. Azure DevOps M5/M6/M13 的 public preview/GA 均为路线图目标（2026 Q3），截至 2026-08-05 路线图更新未显示落地；须与 release notes 交叉后确认。
4. Code Quality 67.3% 及其他任何效果/命中率数据均为厂商自述，未找到独立复核来源。
5. 2026 Q3 多条 GitHub changelog 公告（2026-07-14 security reviews、2026-07-23 MCP spec、2026-07-23 Linear GA、2026-07-23 Issues automation controls、2026-08-03 automations with comments 等）已记录但未逐一展开细读，未纳入上表主卡。

## 复核记录

- 2026-08-06 复核：Sprint 276（2026-07-09）确认 M5 项目级成本报告、M6 多级启用与失败重试、CodeQL default setup GA、Remote MCP ELM tools；路线图（2026-08-05）确认 M5/M6/M13 未转正。
- 2026-08-06 复核：Azure MCP Server overview 确认 26+ skills（含 azure-prepare/validate/deploy）属 Azure Skills Plugin（github.com/microsoft/azure-skills），修正此前“skills 属 GitHub Copilot for Azure 扩展”的表述。
- 2026-08-06 复核：SRE Agent run-modes 文档确认 Review/Autonomous 表述与权限/审批交互；overview “No change deploys without human sign-off”为默认模式表述而非禁止 Autonomous。
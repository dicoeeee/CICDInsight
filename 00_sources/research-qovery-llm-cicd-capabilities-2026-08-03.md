---
title: Qovery LLM 与 Agentic CI/CD 能力一手资料研究
date: 2026-08-03
as_of: 2026-08-03
status: research-complete
confidence: medium-high
scope: Qovery 在 CI/CD、部署、Preview Environment、故障诊断与 DevOps 工作流中直接使用 LLM/AI 的能力；观察窗口 2024-01-01—2026-08-03，必要时追溯更早的平台基线。
source_policy: 仅 Qovery 官方文档、Changelog、产品页、官方博客、官方 GitHub；页面均于 2026-08-03 访问。未以搜索摘要或模型记忆替代原始证据。
---

# Qovery LLM 与 Agentic CI/CD 能力

## 提纲

1. 结论与产品状态时间线
2. 当前能力、入口、上下文、输出和执行边界
3. CI/CD 作业流映射及传统平台基线
4. Claim—Evidence—Limit 矩阵与交叉核验
5. 客户结果、来源冲突和证据缺口

## 结论先行

1. **事实：Qovery 已将 LLM 入口产品化为三条不同路径，但不能把它们混为“一个已 GA 的自治 Copilot”。** 可核验的路径是：`Qovery AI Copilot`（2025-05 Alpha，2025-11 Closed Beta，当前官方文档仍标 **Beta**）、`Qovery MCP Server`（2026-02-11 宣布 live；其 `devops_copilot` 工具仍受 Copilot 开关和写权限约束）、以及 `Qovery Agent Skill`（2026-04-21 发布，当前未见 GA/Beta 标签）。[Copilot Alpha](https://www.qovery.com/changelog/2025-05-21)、[Copilot Closed Beta](https://www.qovery.com/changelog/2025-11-05)、[当前 Copilot 文档](https://www.qovery.com/docs/copilot/overview)、[MCP 发布](https://www.qovery.com/changelog/2026-02-11)、[Skill 发布](https://www.qovery.com/changelog/2026-04-22)。
2. **事实：目前最明确的“LLM + 失败诊断”交付是 Beta 的 AI Copilot deployment troubleshooting。** 2026-03-25 的 Changelog 说它读取 deployment logs、application logs 和 deployment history，识别根因并建议解决方案；同一套能力页把“查失败部署/分析部署日志”标为 full support，却把“修复 deployment problem / identify root cause”标为 partial support，且其验证日期停在 2025-01-15。因此它可表述为“诊断和修复建议”，不可表述为已证实的自动修复或可靠的根因 Oracle。[Changelog](https://www.qovery.com/changelog/2026-03-25)、[Troubleshooting capability](https://www.qovery.com/docs/copilot/capabilities/troubleshooting)。
3. **事实：真正可产生写动作的不是“模型有工具”本身，而是经 Qovery API/MCP/Skill 的单独授权路径。** Copilot/MCP 的每个新会话默认 read-only；read-write 需要组织管理员启用、会话切换，MCP 还要显式 `read_write=true`；2026-06-17 起任何 Copilot 写操作需显式确认。所有动作仍受角色或专用 token 的 Qovery 权限、组织边界及审计约束。[Console Copilot](https://www.qovery.com/docs/copilot/console)、[MCP Server](https://www.qovery.com/docs/copilot/mcp-server)、[写操作确认发布](https://www.qovery.com/changelog/2026-06-17)。这证明**授权和审计边界**，不证明某次模型建议、测试通过或 tool call 的业务正确性。
4. **事实：2026-04 的 Qovery Agent Skill 是从代码到部署的新入口。** 它安装在 Claude Code、Cursor、Codex 等兼容 Agent 中；`qovery-deploy` 文档声称会分析代码库、生成缺失 Dockerfile、询问环境/数据库/CLI 或 Terraform 选择、创建资源并监视 rollout。官方还声称其可处理 build error、port mismatch、health-check failure，并“在触碰用户代码前询问”。这是一项厂商功能声明，未公开其模型、失败分类准确率、重试上限、生成补丁审阅模型或生产成功率。[AI Agent Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)、[AI Skill 产品页](https://www.qovery.com/interfaces/ai-skill)。
5. **事实：Qovery 在 2026-07 将 Kubernetes cluster-state 工具接入 `qovery-troubleshoot` skill，使 Agent 能优先读取结构化对象状态，而不只拼接 logs/`kubectl`；这改善诊断输入，不等于 Kubernetes 自治修复。** 同次发布还将 KRR 的历史 CPU/内存建议暴露给 CLI/API，供 Agent 获取后自行纳入 right-sizing workflow；KRR 推荐本身不是 LLM 功能。[2026-07-15 Changelog](https://www.qovery.com/changelog/2026-07-15)。
6. **分析推断：Qovery 的变化不是用 LLM 替换 CI，而是把已存在的环境级部署控制面变为 Agent 的受限行动面。** 平台仍把 containers、databases、Terraform modules、Helm charts 和 preview 作为同一 environment 图编排；它宣称可与 GitHub Actions、GitLab CI、Jenkins、CircleCI 和 Argo CD 并行。LLM 层负责意图理解、计划、上下文读取和受控调用，Qovery control plane/engine 仍负责实际期望状态和 Kubernetes 执行。[How Qovery Works](https://www.qovery.com/docs/getting-started/how-it-works)、[Deploy 产品页](https://www.qovery.com/product/deploy)、[Argo CD integration](https://www.qovery.com/changelog/2026-06-03)。

## 1. 名称、时间线与状态

| 时间 | 功能/名称 | 官方状态 | 可以确认的变化 | 不能由此确认 |
|---|---|---|---|---|
| 2024-06-30 | *Insights AI*、*AI Troubleshooting Assistant*、*Dockerfile Generation*、*Automatic Deployment Remediation (ADR) AI Agent*、*Migration Helper AI Agent* | **路线图/计划，非交付状态** | 博客把前两类分别排到 Q3/Q4 2024，ADR 排到 Q1 2025；还明确说第一阶段 AI 不直接执行动作。 | 它们均已按期上线、ADR 已可自动重部署、生成 Dockerfile 已被客户采用。未见针对这些名称的后续 release note 交叉核验。[原始路线图](https://www.qovery.com/blog/how-qovery-uses-ai-to-empower-developers) |
| 2025-05-20 | **Qovery DevOps Copilot** | **Alpha**，需联系 Qovery 早期访问 | 宣布自然语言→工具调用、计划、恢复、会话记忆的设计；默认 read-only、权限可定制。 | Alpha 的所有示例均可稳定执行，也不能用其声称的“Claude Sonnet 3.7”推断 2026 当前模型。[Alpha release](https://www.qovery.com/changelog/2025-05-21)、[技术博客](https://www.qovery.com/blog/how-we-built-an-agentic-devops-copilot-to-automate-infrastructure-tasks-and-beyond) |
| 2025-11-04 | **Qovery DevOps AI Copilot** | **Closed Beta** | 五个 specialized agents、自然语言基础设施任务、role-based approval 是厂商发布表述；访问仍需 CSM。 | “five agents”各自的运行边界、模型、工具或量化效果；更不能把 marketing 口号当作当前 GA。[Closed Beta release](https://www.qovery.com/changelog/2025-11-05) |
| 2025-11-18 | **DevOps AI Copilot updates** | early access / Closed Beta 延续 | 应用 log 可按 `deployment_id` 过滤；发布称改善 read-only blocked 提示、对话上下文和「auto remediation」处理更多**内部技术问题**。 | 自动修复的对象、触发、审批和成功率均未定义；不能以此证明 production deployment 已自动修复。[update](https://www.qovery.com/changelog/2025-11-19) |
| 2026-01-27 | **Qovery AI Copilot self-service activation** | **Beta**；当时仅 read-only | 组织可在 Settings 自助启用；可查看 Copilot 正在运行/暂停的 scheduled tasks。 | 当时 read-write 已可用；后来能力应以新的 2026-06 文档/发布为准。[2026-01-28 Changelog](https://www.qovery.com/changelog/2026-01-28) |
| 2026-02-11 | **Qovery MCP Server** | Changelog 称 “officially live”；未见 GA 标签 | MCP client 可通过 AI-powered workflows 触及 cluster provisioning 到 application deployments。 | MCP protocol 入口即自动拥有写权限，或 Copilot 已 GA。[MCP launch](https://www.qovery.com/changelog/2026-02-11)、[current MCP docs](https://www.qovery.com/docs/copilot/mcp-server) |
| 2026-03-25 | **AI Copilot deployment troubleshooting** | **Beta**，Copilot enabled 用户免费探索期 | 分析 deployment/application logs 与 deployment history，给出 root-cause 判断和 resolution suggestion。 | 对所有失败的完整根因、自动变更配置、自动恢复生产服务。[Troubleshooting release](https://www.qovery.com/changelog/2026-03-25)、[capability matrix](https://www.qovery.com/docs/copilot/capabilities/troubleshooting) |
| 2026-04-21 | **Qovery Skill / Qovery Agent Skills** | 已发布；**未标 GA/Preview/Beta** | 通过 CLI/API/MCP 连接 coding agent 和 Qovery 部署基础设施；覆盖 provisioning、environment config、service wiring。 | 每个兼容 Agent 的功能对等、生产环境可无人审批、自动修复准确率。[Skill launch](https://www.qovery.com/changelog/2026-04-22)、[Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent) |
| 2026-06-03 / 06-17 | **Copilot role restriction / write action confirmation** | Copilot role **Beta**；写确认已发布 | 可从继承登录用户权限改为指定 Copilot role；所有 Copilot 写操作要求显式确认。 | 角色或确认能够替代 IAM/RBAC、变更审批、测试或回滚验证。[role restriction](https://www.qovery.com/changelog/2026-06-03)、[confirmation](https://www.qovery.com/changelog/2026-06-17) |
| 2026-07-01 / 07-15 | **Skill audit logging / MCP cluster state / `qovery-troubleshoot`** | 已发布；未标 GA/Preview/Beta | Skill invocation 写入 audit logs；MCP 可返回 full/selective Kubernetes state，`qovery-troubleshoot` 接入该结构化输入。 | Agent 可安全修复所有 Kubernetes 问题，或该输入能证明 incident 根因。[audit log update](https://www.qovery.com/changelog/2026-07-01)、[cluster-state update](https://www.qovery.com/changelog/2026-07-15) |

**截至观察日的产品状态：**唯一明示的当前总状态是 `Qovery AI Copilot: Beta`；MCP Server 有“officially live”发布用语但没有 GA label；Agent Skill 当前文档和发布均未给成熟度标签。因此正式材料应写“Copilot Beta；MCP/Skill 已发布、状态未标”，不要泛称“Qovery AI 已 GA”。

## 2. 当前能力与操作边界

### 2.1 Qovery AI Copilot：运行态、计划、诊断和受控执行

| 维度 | 可被一手资料支持的事实 | 边界/限制 |
|---|---|---|
| 入口 | Console 内的 AI Copilot panel（按钮或 `⌘ I`/`Ctrl I`）和 MCP-compatible client；组织管理员在 Settings 启用。 | 当前文档同时称 Console/MCP 为 Beta；不能由“可见于 Console”推断所有套餐/地区/组织已启用。[overview](https://www.qovery.com/docs/copilot/overview)、[console](https://www.qovery.com/docs/copilot/console) |
| 模型与规划 | 当前 overview 称使用 **Claude Sonnet 4.5**，用自然语言将请求映射到 versioned/stateless tools，动态规划多步、校验中间态、re-plan/retry，并维持会话上下文。 | 2026-07 changelog 却称 Copilot 已运行在 **Sonnet 5**；两者冲突，模型版本须标 `unverified/conflicting`。未公开 prompt、tool schema、retry budget 或 evaluation result。[overview](https://www.qovery.com/docs/copilot/overview)、[2026-07-15 update](https://www.qovery.com/changelog/2026-07-15) |
| 读取上下文 | 官方列出 Qovery documentation、customer infrastructure historical data/operations（“Data Warehouse”）；Console 另明确当前 page、environment、services、deployment status、recent errors，且不读取 app code、application-log **内容**、secret values 或 API token。 | 数据仓字段、保留期、每次 task 实际读取范围未公开；Console 的“不读 application logs content”与故障诊断页对“analyze logs”的广义产品说法不能拼成“Console 必然直接读取全部日志”。[overview](https://www.qovery.com/docs/copilot/overview)、[console privacy](https://www.qovery.com/docs/copilot/console) |
| 诊断输出 | 可检查 deployment/application/build logs、deployment failure、health checks、connectivity/resource/database/performance；输出 guidance、remediation steps 或 suggestion。 | Capability matrix 显示 fix deployment problem、root cause 是 partial；无公开 precision/recall、日志采样、secret redaction、自动补丁或 root-cause evidence chain。[troubleshooting](https://www.qovery.com/docs/copilot/capabilities/troubleshooting) |
| 写动作 | 可以部署、创建/修改/删除环境与资源、变量、scale/restart/stop，前提是 read-write 已启用。 | 新 thread 默认 read-only；组织管理员要先开 write；2026-06 起每次 write 显式确认。LLM 生成 action 不构成授权；实际权限来自 Qovery role/token。[console mode](https://www.qovery.com/docs/copilot/console)、[write confirmation](https://www.qovery.com/changelog/2026-06-17) |
| 定时/恢复 | 文档示例含部署、关停、删除 feature environment、业务时间扩缩；2026-01 release 称可在设置中看 Copilot 后台 scheduled tasks。 | 没有公开 scheduler语义、失败重试、幂等性、审批/变更窗口或 rollback proof，不能把“scheduled task”表述为经过外部 Gate 的自动生产发布。[overview](https://www.qovery.com/docs/copilot/overview)、[2026-01-28 release](https://www.qovery.com/changelog/2026-01-28) |

### 2.2 MCP Server：给外部 LLM Agent 的 Qovery 行动面

**事实：**Qovery MCP endpoint 是 `https://mcp.qovery.com/mcp`，支持 OAuth 或 Qovery API token。默认 read-only，只能 query/list environments、services、deployments 等；要允许 deployments、configuration updates 和其他 writes，需 `read_write=true`。若使用 `devops_copilot` tool，除 URL 参数外还需在 Console 启用 Copilot 和 write access；token 权限仍与选择的 Qovery role 相同，操作会被审计。[MCP 文档](https://www.qovery.com/docs/copilot/mcp-server)。

**结论边界：**MCP 使 Claude Code、Codex、ChatGPT 等 client 能把自然语言转换为 Qovery API 调用；不是 Qovery 自己对每个 client 行为作出的正确性承诺。它也不是“AI 自动部署”唯一入口：官方文档清楚区分 MCP 用于管理**既有**基础设施，Agent Skill 用于从 source code 部署新应用。[MCP 文档](https://www.qovery.com/docs/copilot/mcp-server)。

**Skill telemetry 边界：**截至 2026-08-03 的官方 `qovery-skills` 各 `SKILL.md` 还要求其运行时立即向 Qovery 的 `/skill-tracking` endpoint 以 POST 记录使用情况。这是独立于部署/诊断动作的隐式外部写与遥测；安装或调用 Skill 不能被表述为纯本地、无网络副作用的 prompt 模板。公开 release 只证明 2026-07 起 **skill invocation** 会记录到 Qovery audit log，并未公开 `/skill-tracking` 的 payload、保留期、可选择退出或与 audit log 的精确关系，因此这些数据处理属性保持 `unverified`。[官方 skills repository](https://github.com/Qovery/qovery-skills)、[skill audit update](https://www.qovery.com/changelog/2026-07-01)。

### 2.3 Qovery Agent Skill：从代码和 prompt 到部署/预览

**准确名称与入口：**安装 `https://skill.qovery.com/install.sh` 后，兼容 tools 发现多个 SKILL.md；Quickstart 显示至少 `qovery-deploy`、`qovery-preview`、`qovery-optimize`、`qovery-troubleshoot` 等 8 个 skills，适用于 Claude Code、Cursor、OpenCode、Codex 等。官方 GitHub 源仓为 [Qovery/qovery-skills](https://github.com/Qovery/qovery-skills)。发布日可由 2026-04-21 Changelog 交叉核验。[Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)、[release](https://www.qovery.com/changelog/2026-04-22)。

| 作业步骤 | 谁触发/读什么 | 输出和动作 | 授权/限制 |
|---|---|---|---|
| 配置生成 | 开发者向 coding agent 发 prompt；`qovery-deploy` 读取代码库，探测 language/framework/ports/database needs/env vars。 | 若无 Dockerfile，生成 multi-stage Dockerfile；询问 dev/production、DB、CLI+API 或 Terraform 路径。 | 官方说“在触碰用户代码前询问”；未公开所读文件白名单、代码外传规则、生成配置的校验/测试合同。Skill 的 Phase 3B 初次部署有显式确认门禁，但后续自动修复规则另有例外，见下表与冲突表。[Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)、[official skills repository](https://github.com/Qovery/qovery-skills) |
| Provision/部署 | Agent 在已有 Qovery account、Git repository（GitHub/GitLab/Bitbucket）条件下调用 Qovery。 | 按文档创建 cluster（如需要）、database、env vars，部署并 watch rollout。 | 具体写操作仍依 OAuth/API token/RBAC；“agent 能调用 CLI/API/MCP”不等于得到组织或生产写权限。[Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)、[MCP docs](https://www.qovery.com/docs/copilot/mcp-server) |
| 验证/故障处理 | 读取 rollout 结果；2026-07 起 troubleshooting skill 可优先取 MCP cluster state。 | 官方声称 auto-fix build errors、port mismatch、health-check failures；公开 skill README 还列 8-layer diagnosis、20+ error patterns、10 playbooks、runbook 生成，并称可自动更改 Qovery 配置（ports/health checks/memory/CPU/env vars/deployment stages），改用户代码前询问。`qovery-deploy` Skill 文本另规定：首次部署 Phase 3B 先显式确认；Skill 创建的 Dockerfile 与 Qovery config 的修复可在最多 3 次 retry 内自动进行。 | 这是一份**本地 Agent Skill 指令**的行为规则，不能覆盖 OAuth/API token、RBAC、生产审批，也不能泛化为托管 Copilot 服务行为；更与平台级“Copilot every write confirmation”口径不完全一致。未公开环境隔离、真实自动变更审计样本或成功率；不能把它写成 ADR 已 GA。[Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)、[official skills repository](https://github.com/Qovery/qovery-skills)、[cluster-state update](https://www.qovery.com/changelog/2026-07-15) |
| Preview | 用户的 PR/branch prompt 可触发 `qovery-preview`；平台基线为 per-PR full-stack environment。 | 官方产品页称 PR preview 包含 app、database、seed data，merge 后删除；Skill 页面说明 prototype/demo 可 clone environment、override branch、返回 URL。 | 没有找到 `qovery-preview` 的独立 release date、PR event schema、默认数据脱敏或 production clone 安全规则；不可把“full-stack preview”说成自动批准的 production path。[Deploy](https://www.qovery.com/product/deploy)、[AI Skill](https://www.qovery.com/interfaces/ai-skill) |
| 回滚/恢复 | Agent 可发起 Qovery 动作，平台已提供 environment-level rollback。 | 产品页称 one-click rollback 反转整个 environment；Copilot 文档把 recovery/rollback 写为能力。 | 未公开 LLM 自动选择 rollback、对数据库破坏性迁移的补偿或自动恢复 SLO。回滚动作仍须读写授权和确认。[Deploy](https://www.qovery.com/product/deploy)、[Copilot overview](https://www.qovery.com/docs/copilot/overview) |

### 2.4 Kubernetes 故障诊断与优化输入

- **结构化诊断输入（直接 LLM 相关）：**2026-07-15 之后 MCP 返回完整或按 pods/networking/certificates/nodes 筛选的 Kubernetes objects state；Qovery 说已接到 `qovery-troubleshoot` skill，目的是让 Agent 不用只靠 logs 和 `kubectl` 拼图。[release](https://www.qovery.com/changelog/2026-07-15)。
- **非 LLM、但可成为 Agent 上下文的输入：**KRR 根据可配置历史窗口的使用量生成 CPU/memory request/limit recommendation，并通过 CLI/API 暴露。它是 Kubernetes Resource Recommender 集成，不能写成“LLM right-sizing”。[release](https://www.qovery.com/changelog/2026-07-15)。
- **修复候选而非执行证明：**Copilot Troubleshooting 可建议 remediation；Agent Skill 页面声称 auto-fix 某些 deployment failures；两处都没有公开描述“无需人确认即修改生产”的机制。因此对外结论只能是“候选修复/受控执行面”，不是 self-healing CI/CD 的已验证事实。

## 3. 与传统 Qovery CI/CD 基线的关系

Qovery 的非 LLM 基线是一个把应用与基础设施作为 **environment** 编排的 Kubernetes control plane：开发者可使用 Console、CLI、Terraform、API（后来再加 MCP/Skill）声明期望状态，Qovery engine 执行使 cluster/application 达到该状态的任务。GitHub/GitLab/Bitbucket 可连接 Git；Git push 可触发 build/deploy，传统 CI 也可以调用 Qovery API/CLI。该层本来就有 Preview Environment、build/deploy history、logs、variables、RBAC、audit 和 environment-level rollback；LLM 功能并没有消除这些确定性控制面。[How Qovery Works](https://www.qovery.com/docs/getting-started/how-it-works)、[Deploy](https://www.qovery.com/product/deploy)。

| 基线 | 与 LLM 层的关系 | 应避免的误读 |
|---|---|---|
| GitHub/GitLab/Bitbucket 与 push/PR | Git/PR 给 preview 与传统 deployment 提供 source/event；Skill 还可由当前 source/branch 创建部署。 | prompt-to-deploy 不能证明跳过 Git 的实验路径等同于生产的 PR-reviewed path。 |
| Kubernetes / Qovery engine | MCP/Skill/Copilot 把自然语言编译为对 control plane 的受限请求；engine 才执行 cluster、resource、rollout 工作。 | LLM 不直接取代 Kubernetes reconciliation，也不是外部真实健康/业务正确性 Oracle。 |
| Terraform/API/CLI | Agent Skill 使用 CLI/API/MCP；MCP 让 model client 发现和调用 Qovery 操作。Terraform 仍是 IaC 和审查可见的路径。 | Tool/API 能调用不等于 Agent 已获 token、RBAC、组织管理员的 write switch 或每次写确认。 |
| Argo CD / GitOps | 2026-06 Qovery 宣布可连接现有 Argo CD 并与 Terraform、lifecycle jobs、Qovery-native service 同一 control plane 管理；产品页明说 “alongside, not replace”。 | 不能把 Qovery Agentic Layer 写成已替代 Argo CD/GitOps，或写成所有 GitOps 流程均由 LLM 调度。 |
| Preview / rollback | Qovery 本就宣称 per-PR full-stack preview、merge 后删除和 environment-level rollback；Skill/MCP 给 Agent 提供同一行动面。 | 预览通过、Agent test pass、LLM conclusion 都不是 production promotion authority；相关 gate/approval contract 未公开。 |

## 4. Claim—Evidence—Limit 矩阵

> 访问日期均为 **2026-08-03**。状态栏优先保留官方原词；“未标”不被解释为 GA。`厂商自述` 指 Qovery 自己的产品页/博客说法，尚无独立的客户或测量材料交叉证明。

| # | 核心 Claim | 一手证据（标题、URL、发布/更新） | 状态 | 证据支持 | 证据不支持/限制 |
|---|---|---|---|---|---|
| C01 | 2024 的 Insights、Troubleshooting Assistant、Dockerfile Gen、ADR 是路线图而非发布事实 | [How Qovery Uses AI To Empower Developers](https://www.qovery.com/blog/how-qovery-uses-ai-to-empower-developers)，2024-06-30 | roadmap | Q3/Q4 2024 与 Q1 2025 的计划、第一阶段不直接 action | 如期上线或今天仍以这些名称可用 |
| C02 | DevOps Copilot 在 2025-05-20 进入 Alpha | [Qovery DevOps Copilot in Alpha](https://www.qovery.com/changelog/2025-05-21)，2025-05-20；[技术博客](https://www.qovery.com/blog/how-we-built-an-agentic-devops-copilot-to-automate-infrastructure-tasks-and-beyond)，2025-05-20 | Alpha | natural-language + tools、read-only default、early access | GA、全客户可用、示例成功率 |
| C03 | Copilot 在 2025-11 是 Closed Beta | [DevOps AI Copilot Beta](https://www.qovery.com/changelog/2025-11-05)，2025-11-04 | closed Beta | 五个 specialized agents（厂商自述）和 CSM access | 各 agent 的独立产品/执行策略；量化业务收益 |
| C04 | 2026-01 Copilot 可以自助开通但仅 read-only | [Copilot accessible to anyone](https://www.qovery.com/changelog/2026-01-28)，2026-01-27 | Beta | org settings activation、scheduled task visibility、当时 read-only | 后续已具备的 read-write 能力 |
| C05 | 截至观察日 Copilot 仍为 Beta | [Qovery AI Copilot overview](https://www.qovery.com/docs/copilot/overview)，当前文档、访问 2026-08-03 | Beta | “Beta Status”明示 | 独立 sub-capability 的可用性/region/SLA |
| C06 | Copilot 故障诊断读取 deployment/app logs 和 history、给出建议 | [2026-03-25 Troubleshoot deployments](https://www.qovery.com/changelog/2026-03-25)，2026-03-25；[Troubleshooting capability](https://www.qovery.com/docs/copilot/capabilities/troubleshooting)，当前、访问 2026-08-03 | Beta | 发布、logs/history 输入、suggest resolution；查失败/日志为 full support | 自动/正确 root cause、自动变更或恢复；fix/root cause 在 capability matrix 为 partial |
| C07 | Console Copilot 有 page/environment/service/deployment status 上下文 | [Console AI Copilot](https://www.qovery.com/docs/copilot/console)，当前、访问 2026-08-03 | Copilot Beta | page、environment、services、recent errors/context switch | app code、secret/API token、application log **内容**；完整 Data Warehouse schema |
| C08 | Copilot/MCP 新会话默认 read-only；write 必须额外启用 | [Console AI Copilot](https://www.qovery.com/docs/copilot/console)，当前；[MCP Server](https://www.qovery.com/docs/copilot/mcp-server)，当前 | Copilot Beta / MCP 未标 | RO禁止 deploy/restart/config/variable write；MCP `read_write=true` | 提示词或 tool call 自动取得 write 权限 |
| C09 | Copilot 写动作现在需显式确认，且可用专用 token 缩权 | [Copilot write action confirmation](https://www.qovery.com/changelog/2026-06-17)，2026-06-17；[MCP Server](https://www.qovery.com/docs/copilot/mcp-server)，当前 | 已发布；Copilot Beta | every write confirmation、token/role/RBAC/audit | confirmation 替代 change approval、test、policy 或正确性验证 |
| C10 | MCP Server 2026-02 live，供 MCP client 以自然语言访问 Qovery | [Qovery MCP server is now live](https://www.qovery.com/changelog/2026-02-11)，2026-02-11；[MCP docs](https://www.qovery.com/docs/copilot/mcp-server) | live（非 GA 标签） | OAuth/token、RO/RW、existing-infra management | MCP client 相同能力、模型正确性、默认生产写权限 |
| C11 | Qovery Agent Skill 在 2026-04 发布 | [Demo Day, Qovery Skills](https://www.qovery.com/changelog/2026-04-22)，2026-04-21；[AI Agent Quickstart](https://www.qovery.com/docs/getting-started/quickstart/ai-agent) | 已发布，未标 | coding agent→CLI/API/MCP；provision/config/service wiring | GA/Preview status、所有 compatible agent 的功能对等 |
| C12 | `qovery-deploy` 可读代码、生成 Dockerfile、创建资源并部署/监视 | [Deploy with AI Agent](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)，当前、访问 2026-08-03；[AI Skill](https://www.qovery.com/interfaces/ai-skill)，当前 | 未标 | language/framework/port/DB/env inference，Dockerfile，provision，rollout watch | 生成正确性、安全扫描、生产审批、环境数据最小化 |
| C13 | `qovery-troubleshoot` 以 MCP/CLI/API 做 8-layer diagnosis，并声称可自动修改部分 Qovery 配置 | [Qovery Skills README](https://github.com/Qovery/qovery-skills)，当前、访问 2026-08-03；[Deploy with AI Agent](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)，当前 | 未标，厂商自述 | status/logs/health/env/network/resource/cluster 诊断、20+ patterns/10 playbooks、Qovery config auto-fix、code-change confirmation；`qovery-deploy` 首 deploy 确认与最多 3 次自动修复规则 | token/RBAC 与 production change approval；具体修复审计、成功率；Skill 文本「without permission」与 Copilot write confirmation 不可合并为全产品无确认写入 |
| C13a | Skill 运行有独立的 `/skill-tracking` POST telemetry | [Qovery Skills repository](https://github.com/Qovery/qovery-skills)，当前 Skill text、访问 2026-08-03；[skill audit update](https://www.qovery.com/changelog/2026-07-01) | 当前 Skill 实现；未标产品状态 | 即时外部 telemetry POST 与 skill invocation audit 的存在 | endpoint payload、data retention、opt-out、与用户的 Qovery action audit 的对应关系 |
| C14 | `qovery-preview`/平台可做 PR full-stack preview | [Deploy with AI Agent](https://www.qovery.com/docs/getting-started/quickstart/ai-agent)，当前；[Deploy 产品页](https://www.qovery.com/product/deploy)，当前 | Skill 未标 | 列有 `qovery-preview`；PR app/db/seed/full-stack、merge cleanup 是产品声明 | 每个 preview 默认隔离/脱敏、PR 触发授权、生产 promotion gate |
| C15 | 2026-07 MCP cluster-state 进入 troubleshooting skill | [Agentic Platform, MCP cluster state](https://www.qovery.com/changelog/2026-07-15)，2026-07-15 | 已发布，未标 | full/selective K8s state；接入 `qovery-troubleshoot` | automatic K8s remediation 或根因证明 |
| C16 | Skill 调用会进入审计日志 | [RDE improvements, Ephemeral Shell, GCP WIF](https://www.qovery.com/changelog/2026-07-01)，2026-07-01 | 已发布，未标 | invoked skills/when 的 audit visibility | 全部 prompt、model reasoning、tool arguments/outputs 都被保存 |
| C17 | Qovery 不是取代传统 CI/GitOps，而是在其旁提供 environment control plane | [How Qovery Works](https://www.qovery.com/docs/getting-started/how-it-works)，当前；[Deploy](https://www.qovery.com/product/deploy)，当前；[ArgoCD integration](https://www.qovery.com/changelog/2026-06-03)，2026-06-03 | 平台基线 | Git/CI triggers、Terraform/API/CLI、Argo alongside | LLM 已取代 Git review、CI checks、Argo reconciliation |
| C18 | 当前模型版本没有可消解的一手证据 | [Copilot overview](https://www.qovery.com/docs/copilot/overview)，当前，称 Sonnet 4.5；[2026-07-15 Changelog](https://www.qovery.com/changelog/2026-07-15)，称 Sonnet 5 | **冲突** | 两个官方页面均称具体模型 | 哪一个在 2026-08-03 实际服务，或版本切换日期/范围 |

## 5. 客户案例与量化结果

**结论：未找到能把量化结果归因于 Qovery LLM/Copilot/MCP/Skill 的官方客户案例。** 官方 [Syment case study](https://www.qovery.com/case-studies/kubernetes-management-syment) 记录的是传统 Qovery/Kubernetes 自动化成果（如维护时间、零停机、每月 5+ ephemeral environments），同时在后来追加“AI-agent ready / MCP Server and AI Skill”段落；页面发布日期是 2025-06-04，早于 MCP 2026-02 与 Skill 2026-04 发布，不能把这些数值回填为 AI 功能收益。它应只作为**传统平台基线的厂商案例**。

Qovery 产品页中的 “55 → 8 minutes”、`<30s` preview 和 AI-agent 文案也都是厂商自述，没有可审计的测试方法、样本、时间窗口或客户可复核的 LLM 对照组；不外推为行业平均或 Copilot/Skill ROI。[Deploy 产品页](https://www.qovery.com/product/deploy)。

## 6. 冲突、待验证项与不应写入正式结论的说法

| 项目 | 冲突/缺口 | 当前处理 |
|---|---|---|
| Copilot 读写接口 | 旧 Getting Started 页面仍说 Console 永久 read-only、Slack/MCP 可 read-write；当前 Console 文档（后有 2026-06 release 交叉支持）写 Console/MCP 支持 read-write，Slack read-only。 | 以 2026-06-17 release + 当前 Console/MCP docs 为准：Console/MCP 写入需要管理员开关、每-thread toggle、确认；Slack 仅可安全说当前文档为 read-only。旧页标为陈旧冲突，不做机制证据。 |
| 模型版本 | 当前 overview 写 Claude Sonnet 4.5，2026-07-15 changelog 写 Sonnet 5；2025 Alpha blog 又是 Sonnet 3.7。 | 不在正式结论中指定当前模型；记录为同厂商材料冲突。 |
| “AI troubleshooting”可读日志的范围 | 故障诊断发布/能力页声称分析 app logs；Console privacy 明说 Console Copilot 不读 application logs content。 | 不把所有界面混写。可称产品面提供 log analysis；Console panel 的公开输入边界是 status/history/page context，实际 log-content route 未公开。 |
| ADR / Migration Helper / Insights AI | 2024 博客有详细路线图，但未找到对应 release、current docs 或 GitHub release 的二次证明。 | 只保留为历史预告/研究方向；不计入当前功能清单。 |
| Agentic Workflow + Linear/Jira | 2026-06/07 changelog称 “coming soon”/closed set customers，描述从 ticket 建 sandbox clone、code/test、PR 的流程。 | roadmap / closed-customer 预告，不称通用当前产品，不作为 LLM capability 的正式事实。 |
| AI 修复与生产恢复 | 有“auto-fix”“self-remediation improved”厂商语言，但无操作 schema、审批例外、成功率或客户量化。 | 只称修复候选和受控执行面；不能说 self-healing 已 GA，不能说无需人工。 |
| Skill 的 Qovery config auto-fix | 当前官方 GitHub README 说 `qovery-troubleshoot` 对 ports、health checks、memory、CPU、env vars、deployment stages “without permission” auto-fix，改用户代码才询问；平台 Copilot 的 2026-06 release 则说每次 Copilot write 均需 confirmation。 | 将前者限缩为 Skill workflow 的厂商实现声明；不推广为 Console/MCP Copilot 可无确认写入，也不取消 token/RBAC/组织控制面。需要端到端实测或接口合同才能消解。 |
| Skill 首次部署/自动修复规则 | `qovery-deploy` 文本一面为 Phase 3B 初次部署设显式确认，另一面允许 Skill 创建的 Dockerfile 与 Qovery config 自动修复，最多 3 次 retry。 | 首次部署确认不等于每次后续修复确认；但也绝不可将 Skill 的局部自动修复写成托管 Copilot 的服务承诺或生产无授权写入。需要执行 trace/权限合同才可确定其实际边界。 |
| Skill telemetry | SKILL.md 要求运行前向 `/skill-tracking` POST 使用情况；而 2026-07 release 只说 Skill invocation 被记录在 audit log。 | 将前者标为隐式外部写/遥测，不把 Skill 当作无副作用的本地模板；payload、保留期与 opt-out 都是证据缺口。 |
| Capabilities matrix 日期 | 当前 Troubleshooting 页所有/多项能力显示 “Last Validated 2025-01-15”，早于 2025-05 Alpha 公告。 | 该矩阵只能作为当前页面的功能声明，不能倒推为 2025-01 已发布，更不能作为 GA 时间证明。 |
| 模型/数据治理 | 2024 blog 承诺第三方模型不训练客户数据、暂存限制/去标识；当前 Copilot docs 没有可审计的 provider contract、retention、region 或 prompt/log schema。 | 不将 2024 博客承诺提升为 2026 当前数据处理保证；需要 DPA/安全文档/控制面实测才可进入合规结论。 |

## 7. 可用于后续 Deep Dive 的最小判断

**事实层：**Qovery 已从 2025 Alpha/Closed Beta 发展到当前 Beta Copilot，并在 2026 补上 MCP、Agent Skill、部署故障分析、cluster-state 检索、role/token 限权、写确认与 skill audit。其显著新能力是让外部 coding agent 或 Qovery Copilot 在已有 Qovery environment control plane 上进行受限的自然语言计划、信息检索和操作。

**分析层：**对 CI/CD 平台团队，Qovery 的架构价值在于把 Agent 的上下文（environment graph、status/history、Kubernetes state、部分 logs/metrics）和行动（deploy/configure/scale/rollback）收束在同一个 RBAC/audit/confirmation boundary，而非把 LLM 直接授予 `kubectl` 或云账号。要获得可靠的“AI-assisted deployment”效果，仍需把 PR review、policy、deterministic tests、deployment health/SLO oracle、secret boundary 与 production approval 作为 LLM 之外的 gate。

**不进入正式结论：**Qovery 已 GA 的自治 DevOps、AI 已替代 Argo CD/GitOps/CI、ADR 已自动修复部署、Copilot 对所有界面读取全部日志、Agent 自动拥有生产授权、客户量化收益来自 LLM。

## 来源清单

- [How Qovery Uses AI To Empower Developers](https://www.qovery.com/blog/how-qovery-uses-ai-to-empower-developers) — 2024-06-30，官方博客。
- [Qovery DevOps Copilot in Alpha](https://www.qovery.com/changelog/2025-05-21) — 2025-05-20，官方 Changelog。
- [How We Built an Agentic DevOps Copilot](https://www.qovery.com/blog/how-we-built-an-agentic-devops-copilot-to-automate-infrastructure-tasks-and-beyond) — 2025-05-20，官方技术博客。
- [DevOps AI Copilot Beta](https://www.qovery.com/changelog/2025-11-05) — 2025-11-04，官方 Changelog。
- [DevOps AI Copilot updates](https://www.qovery.com/changelog/2025-11-19) — 2025-11-18，官方 Changelog。
- [Copilot self-service activation](https://www.qovery.com/changelog/2026-01-28) — 2026-01-27，官方 Changelog。
- [Qovery MCP Server is now live](https://www.qovery.com/changelog/2026-02-11) — 2026-02-11，官方 Changelog。
- [AI Copilot deployment troubleshooting](https://www.qovery.com/changelog/2026-03-25) — 2026-03-25，官方 Changelog。
- [Qovery Skills launch](https://www.qovery.com/changelog/2026-04-22) — 2026-04-21，官方 Changelog。
- [Copilot role restriction](https://www.qovery.com/changelog/2026-06-03) — 2026-06-03，官方 Changelog。
- [Copilot write confirmation](https://www.qovery.com/changelog/2026-06-17) — 2026-06-17，官方 Changelog。
- [Skill audit logging](https://www.qovery.com/changelog/2026-07-01) — 2026-07-01，官方 Changelog。
- [MCP cluster state and troubleshooting skill](https://www.qovery.com/changelog/2026-07-15) — 2026-07-15，官方 Changelog。
- [Qovery AI Copilot overview](https://www.qovery.com/docs/copilot/overview) — 当前文档，2026-08-03 访问。
- [Console AI Copilot](https://www.qovery.com/docs/copilot/console) — 当前文档，2026-08-03 访问。
- [MCP Server](https://www.qovery.com/docs/copilot/mcp-server) — 当前文档，2026-08-03 访问。
- [Troubleshooting capability](https://www.qovery.com/docs/copilot/capabilities/troubleshooting) — 当前文档，2026-08-03 访问。
- [Deploy with AI Agent](https://www.qovery.com/docs/getting-started/quickstart/ai-agent) — 当前文档，2026-08-03 访问。
- [Qovery AI Skill](https://www.qovery.com/interfaces/ai-skill) — 当前产品页，2026-08-03 访问。
- [How Qovery Works](https://www.qovery.com/docs/getting-started/how-it-works) — 当前文档，2026-08-03 访问。
- [Deploy](https://www.qovery.com/product/deploy) — 当前产品页，2026-08-03 访问。
- [Qovery/qovery-skills](https://github.com/Qovery/qovery-skills) — 官方 GitHub，2026-08-03 访问。
- [Syment case study](https://www.qovery.com/case-studies/kubernetes-management-syment) — 2025-06-04，官方客户案例；仅作传统平台基线，不作 AI 效果证据。

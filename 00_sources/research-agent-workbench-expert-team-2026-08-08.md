---
title: Agent 工作台与 CI/CD Agent 产品功能一手证据日志
tags:
  - research/agentic-cicd
  - research/source
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: medium-high
---

# Agent 工作台与 CI/CD Agent 产品功能一手证据日志

## 记录范围与口径

- 访问日期：2026-08-08。仅记录产品官方文档、官方帮助中心、官方开发者文档、官方更新日志或官方 GitHub Pages。
- 核心详写对象：WorkBuddy、ChatGPT Work／Codex／Projects／Workspace Agents、Claude Cowork、GitLab Duo Agent Platform、Harness Inc. Worker Agents、GitHub Agentic Workflows。
- 对照对象：Microsoft Copilot Studio、Google Gemini Enterprise Agent Platform、Atlassian Rovo、Amazon Bedrock AgentCore、Octopus Claude Agent Step。
- 每个对象按同一字段记录：状态、入口、配置对象、上下文、任务执行、协作、工具扩展、触发、产物、身份权限、管理审计和官方未说明事项。
- “未声明”只表示所列官方页面没有标出 GA、Beta、Preview、Alpha 或 Experimental；不据此判断能力可用范围。页面日期采用来源页面所列的发布时间或最近更新时间；没有标注时写“页面未标日期”。

## 产品状态总表

| 产品或能力 | 官方状态与时间点 | 状态边界 | 主要官方来源 |
|---|---|---|---|
| WorkBuddy | WorkBuddy 4.5.0 于 2026-03-04 正式发布；专家、Skill、连接器、自动化等页面未逐项标注生命周期 | 产品版本正式发布不等于每项后续能力均为 GA | [更新日志](https://www.workbuddy.cn/docs/workbuddy/Changelog)、[产品目录](https://cloud.tencent.com/document/product/1831) |
| ChatGPT Work | OpenAI 于 2026-07-09 发布 Work；不同计划、权限模式和执行位置决定可用范围 | Work 的发布状态不能外推给 Workspace Agents | [管理员 FAQ](https://learn.chatgpt.com/docs/enterprise/work-admin-faq)、[入门](https://learn.chatgpt.com/docs/get-started-with-work) |
| Codex、Projects、Subagents | 所列功能页面未使用统一 GA/Beta/Preview 标签 | 不把 Codex、Projects 或 Subagents 自动视为 ChatGPT Work 的同一产品能力 | [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)、[Projects](https://learn.chatgpt.com/docs/projects) |
| Workspace Agents | Research Preview；面向符合条件的 Business、Enterprise、Edu 工作区 | 不能以 Work 的发布状态替代 Workspace Agents 的状态 | [Workspace Agents cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep) |
| Claude Cowork | Remote execution 为 Beta，渐进开放；Computer use 为 Research Preview | Remote execution 与 computer use 的状态独立 | [架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)、[Computer use](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork) |
| GitLab Duo Agent Platform | GitLab 18.2 Beta；GitLab 18.8 GA | 平台 GA 不代表表中单项 Flow、治理或模型能力均 GA | [平台概览](https://docs.gitlab.com/user/duo_agent_platform/) |
| Harness Inc. Worker Agents | 页面列为 Harness AI 能力；页面未声明 Worker Agents 单项 GA/Beta/Preview | 账户启用、RBAC 和功能开关仍影响可见或使用范围 | [Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/) |
| GitHub Agentic Workflows | 当前官方文档为 Public Preview；2026-02-13 发布公告曾标为 Technical Preview | Preview 状态及其 Safe Output 行为不等于 GitHub Actions、Rulesets 或 Required Checks 的状态 | [官方概览](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)、[发布公告](https://github.blog/changelog/2026-02-13-github-agentic-workflows-are-now-in-technical-preview/) |
| Microsoft Copilot Studio | 所列页面未标 Copilot Studio 整体生命周期 | Copilot Agent Kit 是供 Copilot Studio 使用的官方工具包，不是 Copilot Studio 核心服务状态声明 | [产品对比](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience)、[Agent Kit](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/kit-overview) |
| Google Gemini Enterprise Agent Platform | Agent Gateway、Agent Observability、Agent Registry 于 2026-06-18 GA；其他功能逐项核验 | 不将上述三个 GA 项外推至 Agent Platform 全部服务 | [更新日志](https://docs.cloud.google.com/gemini-enterprise-agent-platform/release-notes?hl=en) |
| Atlassian Rovo | 所列页面未统一标注生命周期 | Agent/Studio 入口与单个 Agent 的可见、编辑权限分开配置 | [Rovo Agents](https://support.atlassian.com/rovo/docs/agents/)、[治理](https://support.atlassian.com/rovo/docs/rovo-agent-permissions-and-governance/) |
| Amazon Bedrock AgentCore | 服务于 2025-10 GA；2026 年的 Harness、Evaluations、CLI 等能力另有各自发布记录 | 服务 GA 不等于所有近期能力均为 GA | [更新日志](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html) |
| Octopus Claude Agent Step | Alpha；页面更新于 2026-07-07 | 官方明确不建议用于关键、无人值守自动化 | [Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step) |

## 核心产品事实

### 1. Tencent WorkBuddy

页面日期：专家页发布于 2026-07-20；其余引用页部分未标日期。产品版本状态见上表。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 使用入口 | WorkBuddy Enterprise 文档列出新建任务栏、助手、专家、技能、灵感、资料库、自动化和右侧边栏等入口。([产品目录](https://cloud.tencent.com/document/product/1831)) | 产品目录不说明所有入口在各部署、版本或套餐中的默认可用性。 |
| 配置对象 | 新建任务可选择工作目录、模型、Skill、连接器和权限模式。([新建任务栏](https://cloud.tencent.com/document/product/1831/134391)) | 页面未把这些选项声明为 CI/CD 流水线配置。 |
| 输入与上下文 | 新建任务支持工作目录；多个独立任务各有隔离的工作区和上下文。([新建任务栏](https://cloud.tencent.com/document/product/1831/134391)) | 文档未说明跨任务自动共享上下文或依赖图。 |
| 任务流程与状态 | 同一用户可并行发起多个独立任务，运行任务在侧边栏显示。([新建任务栏](https://cloud.tencent.com/document/product/1831/134391)) | 页面没有公开任务调度算法、失败补偿或服务级别目标。 |
| 专家协作 | 专家由“人设 + 方法论 + 工具链”构成；专家团由团长拆解任务、并行执行并整合交付。([专家](https://cloud.tencent.com/document/product/1831/134393)) | 不将“团长”写成已公开的通用编排引擎，或外推为 CI/CD 放行机制。 |
| Skill | Skill 可封装领域知识、工作流、工具指令和脚本资源；支持上传、查找、创建、启用和停用。([技能](https://cloud.tencent.com/document/product/1831/134432)) | Skill 不等于账户、文件或外部系统授权。 |
| 连接器与工具 | 连接器可通过 MCP+CLI 或 Skill+CLI 连接外部服务，并使用 OAuth 或 API Key；连接器可被禁用或断开。([连接器](https://cloud.tencent.com/document/product/1831/134525)) | 连接器调用只在既有授权内进行，页面不证明外部服务的业务审批已完成。 |
| 自动化 | 自动化任务可设置名称、提示词、周期、工作目录、模型和 Skill，结果可保存并通知；页面列出频率、时长和并发限制及日志。([自动化](https://cloud.tencent.com/document/product/1831/134399)) | 不将定时任务视为生产变更审批或发布编排。 |
| 产物与交接 | 右侧边栏可展示对话生成的文件、变更、预览等产物；官方列举 PPT、PDF、文档和网页预览。([右侧边栏](https://cloud.tencent.com/document/product/1831/134400)) | 产物存在不证明内容质量、测试结果或业务验收。 |
| 身份与权限 | 专家本身不主动取得系统权限；配置 Skill 或 MCP 后，相关能力在用户授权下访问文件或外部服务。([专家](https://cloud.tencent.com/document/product/1831/134393)) | 用户授权并不替代外部系统的 RBAC、审批或审计要求。 |
| 管理与审计 | 企业管理员可管理专家 ID、版本、发布状态、可见范围、启用状态，以及成员或部门的白名单、黑名单和分发策略。([企业专家管理](https://cloud.tencent.com/document/product/1831/134421)) | 页面未公开专家定义的回归测试、依赖锁定或撤回 SLA。 |

### 2. OpenAI：ChatGPT Work、Codex、Projects、Subagents 与 Workspace Agents

页面日期：Workspace Agents 管理员 FAQ 说明 Work 于 2026-07-09 发布；其他页面未统一标日期。以下按产品面分开记录。

| 产品面与字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| ChatGPT Work：入口与任务 | Work 可委派需要多步骤执行的任务；任务可使用文件、Apps、Plugins 和已批准的工具，用户可查看进度、回答问题、调整方向并批准重要动作。([Work 入门](https://learn.chatgpt.com/docs/get-started-with-work)) | 文档未声明每一个工具动作均需相同等级的人工批准。 |
| ChatGPT Work：本地与云端 | 桌面端可使用本地文件、应用和浏览器；云端任务可在计算机关闭后继续，并可从 Web 或移动端继续。([Work 入门](https://learn.chatgpt.com/docs/get-started-with-work)) | 本地或云端可用性受计划、设置和执行位置影响。 |
| ChatGPT Work：长任务状态 | 任务可设置目标、约束和完成定义，并支持暂停、恢复、编辑或清除；同一聊天维持任务上下文。([Long-running work](https://learn.chatgpt.com/docs/long-running-work)) | 文档未声明跨聊天自动合并上下文。 |
| Projects：上下文 | Project 可聚合聊天、文件、指令和来源；同一 Project 中可包含 Chat 与 Work。([Projects](https://learn.chatgpt.com/docs/projects)) | Project 是上下文组织能力，不证明其具备生产流水线或发布控制能力。 |
| Codex 与 Subagents：协作 | Work 和 Codex 可并行运行专业 Subagent 并收集结果；本地 Codex 支持自定义 Agent。([Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)) | 并行 Subagent 不等于自动获得写入、合并或生产部署授权。 |
| Skills 与 Plugins：扩展 | 企业文档区分 ChatGPT workspace Skills、本地文件系统 Skills 和 Plugins 的分发边界；移动 Skill 不会转移所有权、共享角色、Plugin 安装或连接器授权。([Enterprise Skills](https://learn.chatgpt.com/docs/enterprise/skills)) | 共享 Skill 不自动复制外部连接器凭据或权限。 |
| 权限模式 | Ask for approval、Approve for me、Full access 与 Custom 模式为文件编辑、命令和互联网访问设置不同边界；组织可禁用某些模式。([Permission modes](https://learn.chatgpt.com/docs/permission-modes)) | 此页面不把权限模式等同于外部系统的生产审批。 |
| Workspace Agents：配置和治理 | Workspace Agents 可配置 Apps、Skills、指令和计划任务，并支持共享；管理员可通过 RBAC 管理启用、构建、共享、发布及 Apps。([Workspace Agents cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)) | Workspace Agents 为 Research Preview，不能当作 Work 的 GA 管理面。 |
| 管理与合规 | Work 的访问、上下文、动作、网络与额度受计划、工作区设置、来源权限和执行面影响；管理员通过身份与 RBAC 管理访问。([Work 管理员 FAQ](https://learn.chatgpt.com/docs/enterprise/work-admin-faq)) | 管理员控制不保证每个 App 或外部系统的单独授权配置。 |
| 审计 | Compliance API 为审计、调查、保留和 legal hold 提供合规记录；页面明确其不是生产力仪表盘。([Compliance API](https://learn.chatgpt.com/docs/enterprise/compliance-api)) | 该记录能力不证明任务结果已通过质量或 CI/CD 门禁。 |

### 3. Anthropic Claude Cowork

页面日期：Computer use 页面为 2026-04-24；Claude Agent Step 外的 Claude Cowork 页未统一标日期。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 执行位置 | Cowork 有本地和云端 session；远程执行为 Beta 并逐步向不同计划开放。云端每个 session 使用临时隔离 sandbox；本地 session 在成员桌面设备运行。([架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)) | Beta 不能外推为所有计划、地区或任务类型都可用；远程会话的端点 EDR 可见性与成员设备不同。 |
| 项目与上下文 | Cowork Projects 可将任务组织到 Project 中；项目记忆用于保存该 Project 的上下文。([Cowork Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)) | 页面未将 Projects 描述为 CI/CD 项目配置或发布环境。 |
| 本地任务 | 本地 session 在成员设备上运行；文档说明包括会话处理、连接文件夹读写、网页获取和本地 Plugin MCP server。([架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)) | 设备上可访问的资源仍受本地设置和系统权限约束。 |
| 远程任务 | 云端 session 使用与本地 Cowork/chat 相同的网络访问设置；组织可开关 Cowork/远程会话、设网络策略、关闭持续授权或要求逐次审批。([架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)) | 隔离和管理设置不等于每个外部服务均已获业务许可；远程会话仅可在桌面 App 在线时访问已连接的本地文件或浏览器。 |
| 多端、后台与计划任务 | Chat 输入框可切换到 Cowork；云端会话可后台继续，支持定时任务和多端恢复，并可使用 Connector、Skill、Plugin、Projects 和文件预览。([多端使用](https://support.claude.com/en/articles/15520349-use-claude-cowork-on-web-desktop-and-mobile)) | Web/Mobile 访问本地文件、浏览器或本地 Connector 仍依赖桌面 App；Live artifacts 仅限桌面。 |
| Plugin 扩展 | Cowork Plugins 可以包含 Skills、Connectors 和 Subagents。([Cowork Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)) | Plugin 不等于默认拥有全部连接器或机器权限。 |
| Computer use | Cowork 与 Claude Code 的 desktop computer use 能在无连接器或工具时操作屏幕、浏览器、文件和开发工具；该能力为 Pro/Max 的 Research Preview。([Computer use](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork)) | Research Preview 页面明确提示风险；不能将其写成默认自动化或生产执行控制面。 |
| 官方未说明事项 | 所查页面未给出 Cowork 多 Agent 的公开调度协议、项目级可审计的审批流程或 CI/CD Required Check 集成。 | 缺少公开说明不等于产品不存在该能力。 |

### 4. GitLab Duo Agent Platform

页面日期：平台页未列更新日期；Flow execution 页面说明功能于 GitLab 18.3 引入。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 使用入口 | 平台把多个 Agent 放入软件开发生命周期，支持异步委派；官方列出 Developer Flow、Code Review Flow、Convert to GitLab CI/CD Flow、Fix CI/CD Pipeline Flow、Custom flows 和 MCP clients。([平台概览](https://docs.gitlab.com/user/duo_agent_platform/)) | 平台 GA 不使平台页列出的所有 Beta/Experiment 功能自动成为 GA。 |
| Agent 类型 | Foundational、Custom 与 External Agents 在 GitLab 18.8 GA；Custom Agent 以 system prompt 定义行为并选择工具，可按 group/project 启用；External Agent 可由 discussion、issue 或 merge request 触发。([Agents](https://docs.gitlab.com/user/duo_agent_platform/agents/)) | 不同 Agent 类型、工具与触发入口不共享同一执行或权限范围。 |
| 协作与配置对象 | Custom flows 可组合多个 Agent；项目可使用 custom rules、`AGENTS.md`、MR review instructions、Agent Skills 和 custom flow definitions。([自定义](https://docs.gitlab.com/user/duo_agent_platform/customize/)) | 自定义文件结构不证明各类配置具有相同作用范围。 |
| CI/CD 执行 | 从 GitLab UI 运行的 Flow 使用 CI/CD；在 IDE 运行的 Flow 在本地执行。([Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)) | UI 执行使用 CI/CD 不等于 Flow 结果已自动通过合并或部署门禁。 |
| 执行环境 | `.gitlab/duo/agent-config.yml` 可配置镜像、setup script、网络规则、Runner 和变量；仅从默认分支读取。([agent-config.yml 语法](https://docs.gitlab.com/user/duo_agent_platform/flows/agent_config_yml/)) | setup script 在 SRT 保护应用前运行，不能写成默认完全沙箱化。 |
| Runner 与工具 | CI/CD Flow 的 Runner 下载 Duo CLI，通过 WebSocket 连接 Workflow Service，并按模型指令执行文件操作和 Git 命令。([Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)) | Runner 执行能力不等于可绕过 GitLab 项目与组级授权。 |
| 沙箱与网络 | 执行环境沙箱可提供网络和文件系统隔离；需使用指定镜像或含 SRT 的自定义镜像，并配置相应 Runner。([Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)) | 沙箱并非任意 Runner 或任意镜像的默认状态。 |
| 身份 | Flow job 使用代表触发用户和 service account 的复合身份 token；Git 提交的 committer 为触发用户，author 为 service account。([Flow execution variables](https://docs.gitlab.com/user/duo_agent_platform/flows/execution_variables/)) | 文档未将复合身份描述为人工审批。 |
| 工具治理与审计 | Agent tool governance 和 AI audit event report 在平台页的 Beta/experiment 表中；前者支持敏感工具动作在执行时由人工批准。([平台概览](https://docs.gitlab.com/user/duo_agent_platform/)) | Beta/experiment 功能不可作为默认或稳定控制机制。 |

### 5. Harness Inc. Worker Agents

页面日期：Harness Agents 页面更新于 2026-07-22；Reference 页面更新于 2026-07-15。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 使用入口 | Worker Agent Catalog 可浏览现有 Agent；可通过 Harness UI、Harness AI Chat 或 IDE 经 MCP 创建自定义 Worker Agent。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | 页面未声明所有账户默认启用；前置条件包括 AI Agents enabled。 |
| 配置对象 | Worker Agent 是加入 CI、CD、IaCM、STO、SCS 或 Custom stage 的可复用步骤，包含 Instructions、Model Connector、可选 MCP Servers 和可配置输入。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | Agent 配置不等于 Pipeline 的最终策略、审批或环境授权。 |
| 定义、版本与复用 | Catalog 保存容器镜像、Instructions、typed inputs、环境变量和 outputs；Pipeline 以 `agentName: name@version` 引用并传入运行时输入。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | 版本引用只说明定义复用机制，不等于版本已经通过回归评测。 |
| 输入与上下文 | Instructions 支持 Harness 变量表达式做运行时动态上下文注入。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | 文档未说明每种阶段对哪些变量默认可见。 |
| 模型与工具 | 定义中的 Model Connector 选择 LLM provider 和默认模型；MCP Servers 为可选配置。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | MCP Server 连接不自动授予对应数据源操作权。 |
| 触发 | 官方示例可通过 Pipeline trigger 在 PR 增加指定 label 时触发 Agent。([Worker Agent examples](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/example-agents/)) | 示例不是平台默认触发策略，也不说明所有 PR 事件均可触发。 |
| 产物与交接 | Agent 可向 `$HARNESS_OUTPUT` 写入键值，以供 Pipeline 后续步骤使用。([Worker Agent configuration](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/configuration/)) | 输出变量只是 Pipeline 数据接口，不证明内容已经过测试或人工验收。 |
| 执行环境 | CD/Custom stage 中的 Worker Agent 需位于 Containerized Step Group；Agent 在 Docker 容器中运行，可使用 Harness Cloud 或自管 Kubernetes 的 isolated VM。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | 容器隔离不替代 Connector、Secret、Pipeline 或环境级权限控制。 |
| 权限与治理 | 官方标签页说明 Worker Agent 权限可在 Pipeline 层和 RBAC 中配置；创建和使用前需具有相应 Pipeline、Connector、Secret 权限。([Harness Agents 标签](https://developer.harness.io/docs/tags/agents/)、[Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | 所查页面未列出每个 Agent 的完整默认权限矩阵；应按目标账户和 Pipeline 逐项核验。 |

### 6. GitHub Agentic Workflows（gh-aw）

页面日期：所查产品首页未标页面日期；产品处于 Public Preview。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 配置对象 | gh-aw 用 Markdown 文件描述工作流；`gh aw` CLI 将其编译为 hardened GitHub Actions `.lock.yml`，两者需推送到默认分支后按触发、UI 或 CLI 运行。([官方概览](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows)) | Markdown 源文件不是直接执行的 GitHub Actions YAML。 |
| 触发与执行 | 工作流可由事件、计划或手动方式运行；可在 GitHub Actions 中使用 Copilot、Claude Code、Codex、Gemini 或自定义 AI processor。([产品首页](https://github.github.com/gh-aw/)) | 可选 AI engine 不代表每种 engine 在每个账户均可用。 |
| 默认权限 | Agentic 部分默认只读；`permissions:` 控制工作流的 GitHub API 范围。([Permissions](https://github.github.com/gh-aw/reference/permissions/)) | 只读默认不替代仓库 Ruleset 或分支保护。 |
| 运行隔离 | 产品页说明 Agent 在容器化 sandbox 中运行，并受网络 firewall 限制；敏感凭据保留在下游隔离 job。([产品首页](https://github.github.com/gh-aw/)) | 所查页面不承诺 Agent 结果的功能正确性。 |
| Safe Outputs | `safe-outputs:` 以结构化输出请求后续写操作；独立的 permission-controlled job 执行创建 issue、comment、PR 等动作。([Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs/)) | Safe Output 是写入控制，不等于 PR 已被审查、合并或部署。 |
| 预演 | Staged mode 展示将要创建的 Safe Outputs，但跳过所有写操作。([Staged mode](https://github.github.com/gh-aw/reference/staged-mode/)) | 预演不等于外部审查或生产批准。 |
| PR 接受边界 | `merge-pull-request` Safe Output 为 experimental；其门控包括状态检查、review decision、未解决 review thread、label、branch 约束和 GitHub mergeability，且默认分支合并始终拒绝。([PR Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs-pull-requests/)) | 单项 merge Safe Output 的 experimental 状态不改变产品总体 Public Preview 状态，也不能替代仓库其他策略。 |
| 与确定性 CI/CD 的关系 | 官方 FAQ 说明 Agentic Workflows 是增量层，既有确定性 build、test、release pipeline 保持不变。([FAQ](https://github.github.com/gh-aw/reference/faq/)) | 不能把 Agent 输出写成测试通过、Required Check 通过或生产发布事实。 |

## 对照产品事实

### 7. Microsoft Copilot Studio

页面日期：产品对比页更新于 2026-07-02；Compliance Hub 页更新于 2026-04-16；Agent Kit 页发布于 2026-07。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 入口与构建 | Copilot Studio standalone portal 面向 maker/developer；支持多步骤逻辑、approvals、分支 workflow、预置和自定义连接器及外部发布。([产品对比](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience)) | 该页面不把 Copilot Studio 描述为多 Agent 团长或 CI/CD 发布产品。 |
| 生命周期与环境 | 产品对比页列出 ALM、版本和 dev/test/prod environment。([产品对比](https://learn.microsoft.com/en-us/microsoft-365/copilot/extensibility/copilot-studio-experience)) | 环境管理不等于 Agent 产物已经过独立测试、签名或发布审批。 |
| 数据与连接器治理 | 管理员可使用数据策略管理 Copilot Studio 功能和 Agent 能力，并可使用 DLP、环境路由等治理控制。([安全与治理](https://learn.microsoft.com/en-gb/microsoft-copilot-studio/security-and-governance)) | 所查页未证明所有连接器默认允许。 |
| 测试、合规与指标 | Copilot Agent Kit 支持 tests/test sets、批量测试及延迟、响应、pass/fail 结果；Compliance Hub 基于 Agent Inventory 持续评估配置。([Agent Kit](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/kit-overview)) | Agent Kit 的测试输出不等于业务流程或 CI/CD 放行结果。 |
| 连接器审批 | Power Shield 为数据策略提供 maker 请求、admin 审查/批准和可追溯变更流程。([Power Shield](https://learn.microsoft.com/en-us/microsoft-copilot-studio/guidance/kit-power-shield)) | 该工具不自动证明所有外部资源的业务权限正确。 |

### 8. Google Gemini Enterprise Agent Platform

页面日期：Agents overview 与 ADK 页面更新于 2026-07-21；更新日志记录 2026-06-18 的 GA 项。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 管理对象 | Managed Agents API 管理 Agent 配置，并可挂载 Skill/Artifact sandbox；Interactions API 调用已部署 Agent。([Agents overview](https://docs.cloud.google.com/gemini-enterprise-agent-platform/agents)) | API 管理能力不等于最终用户工作台或 CI/CD Gate。 |
| Agent 编排 | ADK 支持复杂多 Agent 编排、delegation、tools 与环境模拟。([ADK](https://docs.cloud.google.com/gemini-enterprise-agent-platform/build/adk?hl=en)) | 文档不把 ADK 的 Agent 协作写成项目发布审批机制。 |
| 网关、观测与目录 | Agent Gateway、Agent Observability 与 Agent Registry 于 2026-06-18 GA；观测页描述 agent/MCP server 性能、行为和健康可见性及 trace DAG。([更新日志](https://docs.cloud.google.com/gemini-enterprise-agent-platform/release-notes?hl=en)) | 仅这三项在该日期被标为 GA，不能外推其他构建、评测或优化能力。 |
| 官方未说明事项 | 所查页面未给出 Agent Platform 对 GitHub/GitLab Required Checks、生产环境批准或软件发布签名的原生声明。 | 缺少公开说明不等于产品不存在集成。 |

### 9. Atlassian Rovo

页面日期：所查页面未标统一发布日期。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 使用入口 | Rovo Agents 可在 Chat、Automation rules、Confluence/Jira 编辑场景和 Studio 中访问。([Rovo Agents](https://support.atlassian.com/rovo/docs/agents/)) | 页面未声明这些入口都提供长任务或 CI/CD 执行。 |
| Agent 配置 | Studio Agent 可由自然语言描述或手工配置；组成部分包括 identity、behavior、subagents、knowledge 与 skills。([What are agents](https://support.atlassian.com/studio/docs/what-are-agents/)) | Subagents 字段不证明公开的并行调度、隔离或质量指标。 |
| 权限模型 | Agent 以交互用户身份行动，只能查看、编辑或删除该用户已有权限范围内的信息；也可通过指令或工具配置限制动作。([权限与治理](https://support.atlassian.com/rovo/docs/rovo-agent-permissions-and-governance/)) | 用户权限继承不等于全局管理员或生产批准。 |
| 管理 | Studio admin 可限制谁创建 Agent；Agent owner 可设置 editor/manager；页面说明当前不支持用 group/team 限制 Agent 的可见与使用。([权限与治理](https://support.atlassian.com/rovo/docs/rovo-agent-permissions-and-governance/)) | 不将“默认所有人可见”写成所有组织或所有 Agent 的固定政策。 |

### 10. Amazon Bedrock AgentCore

页面日期：服务于 2025-10 GA；2026-03、05、06 的能力状态见更新日志。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 平台能力 | AgentCore 提供 Runtime、Memory、Gateway、Identity、Browser、Code Interpreter、Policy、Observability 与 Evaluations 等能力；服务于 2025-10 在九个区域 GA。([更新日志](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html)) | 平台是 Agent 构建、运行和治理服务，不是终端用户工作台或 CI/CD Gate 的直接声明。 |
| Harness 与评测 | Managed AgentCore Harness、Recommendations、Batch Evaluations 与 A/B Testing 于 2026-06 标为 GA；Failure Insights 为 Public Preview。([更新日志](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html)) | 评测或优化功能不证明 Agent 对任一业务任务或生产变更正确。 |
| 策略与工具边界 | Policy 通过 Gateway 拦截 Agent 请求，在允许工具访问前评估 Policy；支持基于用户身份和工具输入参数的细粒度控制，决策可记录。([Policy](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html)) | 只有经 Gateway 路径的操作适用这项 Policy 描述；不外推至其他运行路径。 |
| 身份与审计 | Identity 提供 Agent/自动化负载的认证、授权和凭据管理，可代表用户访问 AWS 与第三方服务，并维护审计轨迹。([Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html)) | 身份和凭据服务不等于业务系统中的动作已经被人工批准。 |
| 可观测性 | Runtime、Memory、Gateway、Built-in Tools 和 Identity 可提供 CloudWatch 指标与配置日志目的地。([Observability](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-configure.html)) | 日志与指标不自动形成业务验收结论。 |

### 11. Octopus Claude Agent Step

页面日期：Claude Agent Step 页面更新于 2026-07-07，状态 Alpha。

| 字段 | 官方功能事实 | 官方未说明或不可外推边界 |
|---|---|---|
| 运行位置与上下文 | Claude Agent Step 在 deployment process 或 runbook 的 worker/deployment target 上运行 Claude Code，并接收项目、环境、release、变量、前序 Step 日志和输出。([Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)) | 运行于部署流程中不等于可绕过既有环境或项目权限。 |
| 权限与隔离 | 可配置为只读调查或在 target 上执行命令，并可沙箱化；可用工具之外的调用会被拒绝且导致 Step 失败。([Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)) | 非交互式执行没有中途审批提示；权限需在运行前配置。 |
| 产物与审计 | 每次调用将输出流写入 Task Log，记录 token、成本并保存完整 transcript。([Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)) | Task Log 与 transcript 不判断 Agent 是否真正完成目标。 |
| 工具扩展 | Step 可使用内置 Octopus MCP server、额外 MCP servers 和 Skills；默认读写文件、运行命令和 Web 访问仍受权限与沙箱控制。([扩展 Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step/tools)) | 工具连接不替代外部系统授权。 |
| 接受边界 | 页面明确：仅在非零退出码、终态非成功、工具调用被拒绝或 `octopus-fail-deployment` skill 显式失败时失败；“放弃”但正常退出可能显示成功，重要结果应有确定性检查。([Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)) | Alpha 产品不能作为关键、无人值守自动化的充分控制。 |

## CI/CD 接入与接受边界：具名产品事实

| 产品 | 官方记录的 CI/CD 或部署接入 | 官方记录的结果边界 |
|---|---|---|
| GitLab Duo Agent Platform | UI Flow 使用 CI/CD；包含 Convert to GitLab CI/CD Flow 和 Fix CI/CD Pipeline Flow。([平台概览](https://docs.gitlab.com/user/duo_agent_platform/)、[Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/)) | 平台页的 Agent tool governance 仍标 Beta/experiment；Flow 执行不等于合并或部署已获批准。 |
| Harness Inc. Worker Agents | Worker Agent 作为 CI、CD、IaCM、STO、SCS 或 Custom stage 的 Pipeline step。([Harness Agents](https://developer.harness.io/3k-docs/ai/harness-agents/)) | Agent 输出变量供后续步骤消费，不等于测试、策略或审批通过。 |
| GitHub Agentic Workflows | 在 GitHub Actions 中运行，作为既有确定性 CI/CD 的增量层。([产品首页](https://github.github.com/gh-aw/)、[FAQ](https://github.github.com/gh-aw/reference/faq/)) | Agent 默认只读；写操作经 Safe Output；merge Safe Output 为 experimental，且有独立 policy gate。 |
| Octopus Claude Agent Step | 在 deployment process 或 runbook 中运行。([Claude Agent Step](https://octopus.com/docs/octopus-ai/claude-agent-step)) | Alpha；Agent 正常退出不保证完成目标；官方建议关键结果使用确定性检查。 |
| WorkBuddy、ChatGPT Work、Claude Cowork | 所查页面记录通用任务、项目、Agent/专家、Skill、工具或本地/远程执行能力。 | 所查页面未给出原生 Required Check、发布签名、生产批准或 CI/CD 流水线接受门禁的明确声明。 |

## 公开证据缺口

- WorkBuddy 的专家团页面没有公开团长的任务依赖模型、失败补偿、跨专家隔离、任务成功率或成本基准。
- ChatGPT Work、Codex、Projects 和 Workspace Agents 的文档分别描述不同产品面；没有一份所查页面把它们声明为一个统一的 CI/CD 产品。
- Claude Cowork 所查页面没有公开多 Agent 调度协议、CI/CD Required Check 集成或项目级生产审批模型。
- Harness Inc. Worker Agents 所查页面没有给出所有账户、阶段和角色的默认权限矩阵；需按目标账户与 Pipeline 配置核验。
- GitHub Agentic Workflows 仍为 Public Preview；Safe Output 后是否触发下游 CI、是否满足仓库 Rule 和审批要求取决于具体仓库配置。
- Copilot Studio、Gemini Enterprise Agent Platform、Rovo 和 AgentCore 的所查页面证明 Agent 构建、连接、治理或运行能力；没有据此记录它们已具备原生软件发布控制面。
- Octopus Claude Agent Step 为 Alpha，官方明确提示不要用于关键无人值守自动化。

## 相关 Source Brief 与专题

- [[00_sources/briefs/2026-tencent-workbuddy-agent-workbench|WorkBuddy Source Brief]]
- [[00_sources/briefs/2026-openai-chatgpt-work-codex-workspace-agents|OpenAI Work/Codex/Workspace Agents Source Brief]]
- [[00_sources/briefs/2026-harness-worker-agents|Harness Inc. Worker Agents Source Brief]]
- [[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows Source Brief]]
- [[50_deepdives/aws-devops-agent/README|AWS DevOps Agent / AgentCore Deep Dive]]

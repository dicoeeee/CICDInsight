---
title: CLI / MCP 决策洞察单页证据刷新
aliases:
  - CLI MCP slide evidence refresh
tags:
  - research/agentic-cicd
  - research/evidence
  - tool/cli
  - protocol/mcp
status: complete
as_of: 2026-07-28
accessed_at: 2026-07-28
topic_id: cli-agent-interface
source_policy: primary-only
confidence: high
---

# CLI / MCP 决策洞察单页证据刷新（截至 2026-07-28）

> [!abstract] 已审计结论
> **CLI 与 MCP 是组合选择，而不是已被证实的替换关系：主流 Agent CLI 同时保留终端／Shell 的直接执行面并支持配置 MCP；MCP 则标准化 Host—Server 的上下文、工具发现与调用。**
>
> 对 CI/CD 的决策含义是：单 Harness、已有成熟命令、需要 Runner 内重放和调试时，直接 CLI 是合适执行面；多个 Agent 客户端需要复用同一远程能力、目录与认证接入时，MCP 是合适互操作层。二者都**不**单独授予后端权限，也不构成 Test、Policy、Signature、SLO 或人工审批等外部接受门。

## 1. 研究口径与写作提纲

### 研究口径

- **任务边界：** 对现有 CLI Deep Dive 与 `industry-mainstream-evidence-2026-07-28.md` 做当前性、逐主张审计；仅补齐“主流机制收敛”和单页案例选择。未扩展新的产品、市场或采用率研究。
- **来源：** 仅使用官方文档、官方发布说明、官方仓库、MCP 官方规范和 CLI-Anything 原始论文。所有网页访问日均为 **2026-07-28**；活文档未显示发布日期时如实标为“未显示”。
- **“主流机制”含义：** 至少两个相互独立组织的公开、当前产品/规范有同构机制；它不是市场份额、企业采用率或 CI/CD 成功率结论。
- **术语：** “直接 CLI/Shell”指 Harness 可从终端运行、或能直接调用 Shell/本地命令；“MCP”指 Host/Client/Server 的协议式工具与上下文接入。两者位于不同抽象层，不能仅因两者都可触发后端动作就当作可替代物。

### 写作提纲

1. 审计“组合而非替代”是否可写；
2. 将 CLI、MCP 各自的价值、代价和适用面限定为证据支持的范围；
3. 核验指定对象的状态、版本和可用主张；
4. 只挑两项主案例；以规范作为可选第三项机制证据；
5. 给出可直接放入 Speaker Notes 的 Source Map 和标题判定。

## 2. 页面主张的逐项事实审计

| ID | 候选页面主张 | 一手证据与直接事实 | 审计结论 | 不可外推边界 |
|---|---|---|---|---|
| C1 | **“主流 Agent CLI 同时支持直接 CLI/Shell 与 MCP；二者组合而非替代。”** | GitHub Copilot CLI 是 GA 的终端 Agent；官方文档同时说明 `!` 可直接运行 Shell、内建 GitHub MCP server 且可添加其他 MCP server。Anthropic Claude Code 的官方 MCP 文档以 `claude mcp add` 配置 HTTP/stdio server，并明确 stdio server 是本地进程；OpenAI Codex 文档说明 Codex CLI 支持 MCP，且用 `codex mcp add ... -- <stdio command>` 添加 server。 | **支持，跨三家厂商交叉验证；高置信。** “组合”是基于这些产品结构的分析推断，而不是协议的强制规定。 | 不能写“所有 Agent CLI”或“MCP 必然调用 CLI”。MCP server 可以直连 API/库；直接 Shell 也可以完全不经 MCP。 |
| C2 | **“CLI 的核心价值是 Runner 内显式、可组合的进程契约。”** | Terraform CLI 以 `init`、`validate`、`plan`、`apply` 等显式子命令工作；`plan` 只生成执行计划，`show -json` 可输出机器可读计划/状态。`kubectl` 的参考和约定文档持续维护机器输出与脚本使用约束。CLI-Anything 原始论文主张以结构化命令、显式状态和机器可读协议改造 GUI 交互。 | **支持；高置信，但“核心价值”是综合分析。** 能证明显式命令、机器输出、Plan/Apply 分离与脚本组合存在，不能量化其收益。 | 不写“确定性执行”：远端状态、网络、并发、Provider 与业务系统仍会漂移。CLI 返回 0 也不等于业务成功。 |
| C3 | **“直接 CLI 的优势是单 Harness/Runner 的低适配与可调试组合；代价是调用契约、状态和权限需要产品/平台自己治理。”** | Codex、Claude Code、Copilot CLI 都提供终端/非交互或 Shell 路径；Terraform 直接 CLI 有可检查 `plan`、`apply` 和 JSON；Kubernetes 授权由 API server 在 CLI 之外决定。 | **部分事实、部分分析；中高置信。** “少一层适配、便于调试/重放”是进程模型的合理推断，不是厂商的比较测量。 | 不能声称直接 CLI 必然低成本、低延迟或更安全；命令仍可能隐含工作目录、账户、集群、Region、版本和凭据。 |
| C4 | **“MCP 的核心价值是跨 Host 的标准化上下文/工具连接、能力协商和可组合工作流。”** | 当前 `latest` 入口仍指向 MCP 2025-11-25 规范：它将 MCP 定义为 LLM 应用与外部数据源/工具的开放协议；规定 Host、Client、Server，使用 JSON-RPC 2.0 与 capability negotiation，Server 可提供 Resources、Prompts、Tools。 | **支持；高置信。** | 协议定义的是互操作语义，不能证明某 server 的业务语义、可靠性、权限强度或治理成熟度。 |
| C5 | **“MCP 的代价是新增 server、传输、认证与信任边界；适合多客户端复用的远程能力，不替代业务授权。”** | MCP 规范指出任意数据访问与代码执行路径带来安全/信任问题，并要求用户明确同意数据访问与操作。Codex 与 Claude Code 都需配置本地 stdio 或远程 HTTP server、环境变量/Token 或 OAuth；Terraform MCP server 可暴露工作区管理操作。 | **支持；高置信。** 认证/配置复杂度是从这些必要组件作出的架构推断。 | 不能把“支持 OAuth / allowlist”说成“动作已获业务授权”。Host、任务身份、后端 API/RBAC、Policy 与审批仍须单独验证。 |
| C6 | **“CLI-Anything 证明接口工厂路线存在。”** | 论文 v1（2026-06-02）提出将现有软件改造成以结构化命令、显式状态与机器可读协议为中心的 CLI harness；官方仓库同时将其 Codex 集成标记为 `Experimental` / `Community`。 | **支持为研究/开源参考；中高置信。** | 不应把它作为“行业主流产品化”或“生产授权/可靠性已验证”的证据，也不应进入本页两个主案例。 |

## 3. 指定对象的当前性核验

| 对象 | 截至 2026-07-28 的已核验事实 | 发布日期 / 访问日期 | 状态 / 版本 | 可精确支持的主张 | 不可外推 |
|---|---|---|---|---|---|
| **OpenAI Codex CLI** | Codex CLI 支持 MCP；`codex mcp add` 可启动 stdio server，亦支持 Streamable HTTP、Bearer/OAuth；MCP 配置可被 ChatGPT desktop、Codex CLI、IDE extension 共享。既有专题已核验 `codex exec` 用于非交互脚本/CI。 | MCP 文档未显示日期；访问 2026-07-28。 | 页面未对 Codex CLI/MCP 单独标 GA/Preview；产品状态按**官方活文档，生命周期未显示**记录。 | “Codex CLI 可把 MCP server 作为 CLI 配置的扩展能力，而非只能二选一。” | 不从这一项推出 Codex 的所有 MCP 功能均 GA，也不推出 MCP server 底层使用 CLI。 |
| **GitHub Copilot CLI** | 2026-02-25 发布说明宣布 Copilot CLI GA；GA 文档称它为 terminal-native coding agent，`!` 可直接运行 Shell；默认含 GitHub MCP server，且可加自定义 MCP server；官方 command reference 同时列出 `--prompt`、`--available-tools`/`--allow-tool` 和 MCP 配置/禁用开关。 | 2026-02-25（GA 公告）；文档页未显示日期；访问 2026-07-28。 | **GA**。 | “单一 GA Agent CLI 同时具有终端/Shell 直接执行与内建/可配 MCP。” | 这是 GitHub 产品事实；不能代表所有 Agent CLI 的默认权限、安全性或企业部署。 |
| **Anthropic Claude Code** | 官方文档说明 Claude Code 通过 MCP 连接外部工具/数据；`claude mcp add` 支持 remote HTTP 与 local stdio，后者作为本地进程运行；官方 CLI 文档提供 `claude -p` 等命令行使用方式。 | Claude 4 公告 2025-05-22 宣布 Claude Code GA；活文档未显示日期；访问 2026-07-28。 | **GA**（Claude Code；单独 flag 按文档状态另计）。 | “第二个独立 GA Agent CLI 也把本地命令行与 MCP 配置放在同一产品中。” | 不把某个 Claude Code transport 或 flag 的行为外推为 MCP 规范。 |
| **MCP 官方规范** | 当前 `latest` 入口仍指向 2025-11-25 规范：MCP 是 LLM 应用连接外部数据/工具的开放协议，采用 JSON-RPC 2.0、stateful connection、client/server capability negotiation；Server 可提供 Resources、Prompts、Tools。规范还要求用户同意数据访问/操作。 | 2025-11-25（latest 当前指向）；访问 2026-07-28。另有 2026-07-28 release candidate 公告，未据此假定最终规范已切换。 | 已发布规范版本；2026-07-28 为 RC/待最终化状态；不适用 GA/Preview 产品口径。 | “MCP 是 Host—Server 互操作、能力暴露和发现层。” | 不写成 Shell 执行协议或后端授权协议。 |
| **Kubernetes `kubectl`** | Kubernetes 当前参考页继续维护 `kubectl` 命令参考；Kubernetes 授权文档说明 API server 在认证后按请求属性与策略作允许/拒绝裁决，默认拒绝；`kubectl auth can-i` 可查询身份权限。 | 活文档未显示日期；访问 2026-07-28。 | Kubernetes v1.36 文档站；未对 `kubectl` 另标生命周期。 | “成熟 CLI 的机器调用不等于得到后端权限；真实授权在 API/身份控制面。” | 不把 Kubernetes 的 RBAC/授权模型视为所有 CLI 的统一模型。 |
| **HashiCorp Terraform** | Terraform CLI v1.15.x 文档把 CLI 定义为 `terraform` 命令及 `init`/`validate`/`plan`/`apply` 等子命令；Terraform MCP Server v1.0.x 文档说明其为 AI 提供实时 Registry 文档、module/policy 与 HCP/Enterprise workspace 操作。 | 两页均为活文档、未显示发布日期；访问 2026-07-28。 | Terraform CLI **v1.15.x latest**；Terraform MCP Server **v1.0.x latest**；文档未对两者标 GA/Preview。 | “同一成熟基础设施领域并行维护直接 CLI 和独立 MCP server：前者是显式计划/执行契约，后者是给 AI Host 的发现/上下文/动作接入。” | 不声称 MCP server 是 Terraform CLI 的封装，或所有 Terraform MCP 操作都等价于本地 CLI 命令。 |
| **CLI-Anything 论文 / 官方仓库** | arXiv `2606.03854v1` 于 2026-06-02 提交；论文主张 structured commands、explicit state 与 machine-readable protocol。仓库将 Codex 集成标为 Experimental/Community。 | 论文 v1：2026-06-02；仓库活文档访问 2026-07-28。 | **研究论文 / 开源参考实现**；Codex 集成 Experimental/Community。 | “接口工厂可以把长尾软件转向 Agent-ready CLI。” | 不作为主流、GA 或生产可靠性证据。 |

## 4. 决策框架：各自的优势、代价与适用场景

| 选择 | 已有证据支持的核心优势 | 代价 / 控制要求 | 最适用的场景 | 不适用的简化说法 |
|---|---|---|---|---|
| **直接 CLI / Shell** | 显式命令、参数、进程输出与退出状态；可在本地或 Runner 执行；成熟工程 CLI 可提供 JSON、Plan 与可检查执行阶段。 | 必须显式治理目标、工作目录、账户、环境、凭据、版本、幂等与 JSON/错误契约；Shell 本身可能扩大当前 OS 身份的爆炸半径。 | 单一 Agent Harness；已有成熟能力 CLI；本地/CI Runner 内的脚本组合、排错、Plan/Verify/Apply；需贴近既有 API/RBAC 的执行。 | “CLI 天然确定、安全或自动可审计。” |
| **MCP** | 跨 Host/Client 的标准 Resources、Prompts、Tools 与能力协商；适合把远程服务、文档或业务工具供多个 Agent 接入。 | 增加 server 生命周期、传输、认证、输出限制、调用超时和信任/提示注入面；仍须有 Host policy、任务身份与后端权限。 | 多个 Agent 客户端要复用同一远程能力；需要标准工具发现/目录；连接外部文档、SaaS 或集中治理服务。 | “MCP 自动把工具变安全，或已经取代底层 CLI/API。” |
| **组合：MCP + CLI/API** | Harness 经 MCP 获得工具/上下文/远程服务，同时直接命令或服务后端保留清晰的执行契约与既有权限面；GitHub、Anthropic、OpenAI 的 Agent CLI 都公开展示这种共存。 | 需要明确“谁负责发现、谁负责执行、谁负责授权、谁负责验收”，避免同一操作经两条路径得到不同审计或权限。 | 既要跨客户端发现/复用，又要在 Runner 中保留成熟 CLI、Plan、日志与调试路径。 | “MCP 必然包裹 CLI”或“CLI 与 MCP 只能择一”。 |

## 5. 推荐上页的案例组合

### 主案例：只放两个

| 推荐对象 | 一句话机制 | 为何适合本页 | 页面上的受限写法 |
|---|---|---|---|
| **GitHub Copilot CLI（GA）** | 一个终端原生 Agent：用户可在 CLI 中直接运行 Shell（`!`），产品同时内建 GitHub MCP server 并允许添加自定义 MCP server。 | 最直接地在**同一 GA Agent CLI**中同时展示“直接执行面 + MCP 互操作层”；官方资料还明确了工具许可与 sandbox，适合 CI/CD 受控执行叙事。 | “GitHub 的 GA Agent CLI 同时保留 Shell 执行与 MCP 扩展，而非二选一。” |
| **HashiCorp Terraform（CLI + MCP Server）** | Terraform CLI 提供 `plan`/`apply` 与 JSON 化检查路径；官方 Terraform MCP Server 向 AI Host 暴露当前文档、模块、策略和工作区操作。 | 补上不同机制：不是另一个 coding harness，而是**成熟基础设施能力同时维护执行 CLI 与 AI 接入 server**。能把“组合”从 Agent 前端扩展到工程系统边界。 | “Terraform 以 CLI 保留显式 Plan/Apply，同时用 MCP 向 AI 提供当前知识与受控工作区操作。” |

### 可选第三项：仅作脚注/架构层，不与两项主案例抢空间

| 对象 | 一句话机制 | 使用条件 |
|---|---|---|
| **MCP Specification 2025-11-25** | 定义 Host/Client/Server、JSON-RPC、能力协商和 Resources/Prompts/Tools，因此解释 MCP 是互操作层而不是 Shell 或业务授权层。 | 当图中需要标出协议层时加入；它增加的是**标准机制**，不是第三个产品标志。2026-07-28 新规范当日仍应以 RC/发布切换待核实标注。 |

**不推荐上页的已核验对象及原因：**

- **OpenAI Codex CLI、Anthropic Claude Code：** 是 C1 的有力交叉验证，应放入 Speaker Notes / Source Map；若再在页面点名，会与 GitHub 的“Agent CLI + MCP 共存”机制重复。
- **Kubernetes `kubectl`：** 最适合支持“CLI 调用不等于权限”这一控制边界，适合作页脚或讲稿中的反例，不适合作为本页主案例。
- **CLI-Anything：** 是研究/开源接口工厂，不应与 GA 产品或已发布规范并列为“主流”佐证。

## 6. Source Map 候选（供单页脚注或 Speaker Notes）

| 页面元素 | 推荐主入口 | 精确支撑 | 时间 / 状态 |
|---|---|---|---|
| “Agent CLI 与 MCP 共存” | GitHub, [Copilot CLI GA](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/)；[Using Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/overview) | GA、terminal-native agent、直接 Shell、内建/自定义 MCP。 | 2026-02-25；GA；访问 2026-07-28。 |
| “CLI 的 Plan/Apply 执行契约” | HashiCorp, [Terraform CLI overview](https://developer.hashicorp.com/terraform/cli/commands) | `terraform` 子命令，`plan`、`apply` 与 CLI 语义。 | v1.15.x latest；活文档；访问 2026-07-28。 |
| “MCP 的远程 AI 接入用途” | HashiCorp, [Terraform MCP server overview](https://developer.hashicorp.com/terraform/mcp-server) | 当前 Registry 文档、模块/策略与 HCP/Enterprise 工作区操作。 | v1.0.x latest；活文档；访问 2026-07-28。 |
| “MCP 的协议边界” | [MCP Specification latest](https://modelcontextprotocol.io/specification/latest)（当前指向 2025-11-25） | Host/Client/Server、JSON-RPC、capability negotiation、Resources/Prompts/Tools 与同意要求。 | 2025-11-25 为当前 latest；2026-07-28 版本为 RC/最终切换待核实；访问 2026-07-28。 |
| “两家独立 Harness 的交叉验证” | OpenAI, [MCP for Codex](https://developers.openai.com/codex/mcp/)；Anthropic, [Claude Code MCP](https://code.claude.com/docs/en/mcp) | 两个终端 Harness 皆能用 CLI 配置 local stdio / remote MCP。 | 活文档；Claude Code GA 公告 2025-05-22；访问 2026-07-28。 |
| “Shell 与权限不等价” | Kubernetes, [Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)；GitHub, [Copilot CLI command reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference) | 后端 API 授权与 Harness 工具许可是独立层。 | 活文档；访问 2026-07-28。 |

## 7. 推荐标题与证据支持判定

### 推荐标题

> **CLI 保留受控执行，MCP 统一跨 Agent 接入：二者组合，而非替代**

**判定：支持，但须保留两个限定词。**

- “CLI 保留受控执行”：Terraform 的 Plan/Apply 契约、GitHub Copilot CLI 的 Shell/工具权限和 Kubernetes 的后端授权层支持“执行必须受控”的页面机制；这不等于所有 CLI 默认受控。
- “MCP 统一跨 Agent 接入”：MCP 规范支持标准化连接、能力协商与 Tools/Resources/Prompts；“统一”应理解为**协议语义**，不是所有产品已互通或所有工具均有 MCP server。
- “组合，而非替代”：GitHub、Anthropic、OpenAI 三个独立 Agent CLI 的共存设计，以及 Terraform CLI 与官方 MCP server 的并行产品面共同支持；它是基于多个一手产品事实的架构归纳。

### 不建议使用的标题

- “MCP 将取代 CLI”：无一手证据支持，且被上述共存设计反证。
- “所有主流 Agent 都以 MCP 调 CLI”：无证据；MCP server 可直连 API/SDK。
- “CLI 是确定性、安全的 Agent 执行层”：过度概括；确定性、安全性与成功证据仍取决于目标显式化、任务身份、后端 Policy、隔离和外部 Oracle。

## 8. 证据冲突与保留的缺口

1. **不存在可用的一手横向统计。** 本次资料不能证明直接 CLI 与 MCP 在成本、成功率、延迟、事故率或企业采用率上谁“更优”；页面只能做机制和适用边界判断。
2. **“主流”是机制收敛，不是市场结论。** 三个 Agent CLI 的产品事实和 Terraform 的并行接口足以说明共存路线真实存在；不足以证明它已是所有企业的默认架构。
3. **MCP 版本存在演进与当日切换缺口。** `latest` 在访问时仍指向 2025-11-25；官方 2026-05-21 博文将 2026-07-28 版本称为 release candidate，并计划在 2026-07-28 发布最终版。故正式页面应标“2025-11-25（current latest at access）”，而不能把当日 RC 当作已最终切换的规范。
4. **功能生命周期粒度不一致。** GitHub Copilot CLI 和 Claude Code 有 GA 公告；Codex CLI/MCP、Terraform CLI/MCP 文档页未给出可直接对应的单项 GA/Preview 标签，故不能在图上把所有组件画成同一成熟度。
5. **授权和接受门不由接口替代。** MCP 规范自身要求用户同意；Kubernetes 与 GitHub 文档亦显示后端/API 与 Harness 权限另行控制。任何涉及 CI/CD 写操作的页面仍须明确身份、策略、批准和独立验证。

## 9. 访问来源索引

1. OpenAI, [Model Context Protocol for Codex](https://developers.openai.com/codex/mcp/), 页面未显示日期，访问 2026-07-28。
2. GitHub, [Copilot CLI is now generally available](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/), 2026-02-25，访问 2026-07-28。
3. GitHub Docs, [Using GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/overview)；[Copilot CLI command reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference)，页面未显示日期，访问 2026-07-28。
4. Anthropic, [Introducing Claude 4](https://www.anthropic.com/news/claude-4), 2025-05-22；[Connect Claude Code to tools via MCP](https://code.claude.com/docs/en/mcp)；[Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)，活文档访问 2026-07-28。
5. Model Context Protocol, [Specification latest](https://modelcontextprotocol.io/specification/latest)（访问时指向 2025-11-25）；[2026-07-28 release candidate](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/)，2026-05-21，访问 2026-07-28。
6. Kubernetes, [The kubectl command-line tool](https://kubernetes.io/docs/concepts/overview/kubectl/)；[Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)，活文档访问 2026-07-28。
7. HashiCorp, [Terraform CLI overview](https://developer.hashicorp.com/terraform/cli/commands)（v1.15.x latest）；[Terraform MCP server overview](https://developer.hashicorp.com/terraform/mcp-server)（v1.0.x latest），活文档访问 2026-07-28。
8. Yang, Fan, Huang, [CLI-Anything: Towards Agent-Native Computer Use](https://arxiv.org/abs/2606.03854), arXiv v1 2026-06-02；[CLI-Anything official repository](https://github.com/HKUDS/CLI-Anything)，活仓库访问 2026-07-28。

---
title: "CLI 与 MCP：协议与架构视角的业界主流理解"
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

# CLI 与 MCP：协议与架构视角的业界主流理解

> [!abstract] 可用于页面的核心判断
> **CLI 是 Agent 可直接调用的进程执行契约；MCP 是让 Agent Host 以统一 Schema 发现、连接和调用外部能力的协议。二者可以共享 stdio，却不处于同一抽象层，因此不是替代关系。**
>
> 业界产品的共同设计不是“用 MCP 淘汰 CLI”，而是让 Agent CLI 同时拥有 Shell/CLI 执行面和 MCP 扩展面：OpenAI Codex、Anthropic Claude Code、GitHub Copilot CLI 都以 CLI 配置本地 stdio 或远程 MCP server；成熟工程系统如 Terraform 同时维护 CLI 与独立 MCP Server。这个判断描述公开产品与规范的**架构收敛**，不代表市场份额或所有企业的默认实现。

## 1. 研究问题、口径与结论强度

### 研究问题

1. CLI 与 MCP 是否互相替代？
2. “命令/进程契约”与 “Tool Schema/协议契约”本质上有什么差异？
3. stdio MCP 是否就是 CLI，或是否意味着 MCP 必然调用 CLI？
4. 当 Agent 参与软件交付作业时，如何在能力发现、权限、状态、故障和部署上选择两者？

### 证据口径

- 仅采用 MCP 官方规范与架构文档、OpenAI / Anthropic / GitHub 官方产品文档、HashiCorp / Kubernetes 官方文档。
- 所有活文档均于 **2026-07-28** 访问；网页未显示发布日期时记为“未显示”。
- “主流”仅指至少三家独立 Agent CLI 厂商公开支持“CLI/Shell 与 MCP 共存”的产品结构，**不是**市场采用率、成本或可靠性排名。
- “更适合”是针对架构条件的判断；没有可用的一手横向实验来证明 CLI 或 MCP 在成功率、时延或安全性上普遍更优。

## 2. 先澄清：不是“人用 CLI，Agent 用 MCP”

### J1：CLI 是 Agent 的一等执行接口

**事实。** OpenAI 将 `codex exec` 明确用于脚本和 CI，支持管道、预设 sandbox / approval 以及 JSON Lines 输出；GitHub Copilot CLI 将 Shell 与 MCP server tool 都纳入同一套工具许可规则；Terraform 的 `plan`、`apply`、`show -json` 则展示了成熟工程 CLI 的自动化与机器输出路径。

**推断（高置信）。** CLI 并不天然“面向人”。当 Agent 能运行命令、拥有受控工作目录与任务身份时，CLI 直接提供本地/Runner 内执行、调试、日志、文件和管道组合的路径；它通常也是产品能力最完整的既有表面。

**边界。** 命令返回 0 不等于变更被业务接受；执行是否成功仍可能受远端状态、网络、凭据、并发、后端 Policy 和独立质量门影响。

### J2：MCP 不是“比 CLI 更会执行”，而是协议式接入与发现层

**事实。** MCP 2025-11-25 规范定义 Host、Client、Server 之间基于 JSON-RPC 2.0 的有状态连接、能力协商，以及 Resources、Prompts、Tools 等原语；其架构文档将 MCP 的范围限定为“上下文交换协议”，不规定 Host 如何使用 LLM 或管理这些上下文。Claude Code 将 MCP 用于连接外部工具、数据库和 API；Codex 用 MCP 将第三方工具和上下文接入本地客户端。

**推断（高置信）。** MCP 的新增价值是让不同 Host 以相对统一的工具描述、调用和上下文语义接入能力；它解决的是互操作与能力目录问题，而不是取代业务系统的执行引擎、CLI 或 API。

**边界。** MCP 没有要求 Server 必须通过 CLI 实现。一个 MCP Server 可以封装 CLI、调用 SDK/API、查询数据库，或直接提供文档；具体实现不能从协议名称推断。

## 3. 本质差异：两种不同的“契约”

| 维度 | 直接 CLI / Shell：进程契约 | MCP：Tool Schema + 协议契约 | 对 Agent 的含义 |
|---|---|---|---|
| 调用单位 | 可执行程序及其子命令，如 `terraform plan` | Server 暴露的 Tool / Resource / Prompt，如 `tools/list`、Tool invocation | CLI 先选命令；MCP 可先枚举和理解能力 |
| 输入契约 | `argv`、stdin、环境变量、当前目录、配置文件 | JSON-RPC 方法、JSON Schema 参数、协商后的能力与会话 | CLI 需要 Agent 学会各产品语法；MCP 统一了调用包装，但不统一业务语义 |
| 输出契约 | stdout / stderr、退出码、文件、日志与副作用；是否 JSON 取决于产品 | JSON-RPC result / error、通知、进度；Tool/Resource 的结构由 Server 声明 | 好 CLI 可以 `--json`；MCP 不保证业务返回一定简洁、正确或无提示注入 |
| 生命周期 | 通常是一次进程：启动、执行、退出；也可为 daemon | 初始化、版本/能力协商、持续连接、取消、通知、终止 | MCP 引入连接与 session 管理；CLI 更接近一次性 Runner job |
| 组合方式 | 管道、重定向、脚本、Make/CI 编排、共享文件系统 | Host 对多个 Server 的工具/资源聚合；Server 可提供 prompts、resources、notifications | 前者擅长操作流组合；后者擅长跨产品工具目录与上下文组合 |
| 授权位置 | OS 用户 / 工作负载身份、CLI 凭据、后端 API/RBAC/Policy | Host 的工具许可 + Server 身份/凭据 + 后端 API/RBAC/Policy | 两者都不自动授权；MCP Schema 也不是权限证明 |

**事实。** Terraform `plan` 只生成拟议变更，不会执行；可保存 Plan 再交给 `apply`，其 `-json` 与 `show -json` 均可输出机器可读数据。Kubernetes `kubectl` 也提供 `-o json`，而真实 API 请求仍由 API server 根据身份和策略允许或拒绝，默认拒绝。

**推断（高置信）。** 适合 Agent 的 CLI 不是“能在终端显示文字”即可，而是应尽量具备显式命令、稳定参数、可检查状态/Plan、结构化输出、退出码和可审计副作用。MCP 则把“可调用工具的元数据与交互方式”提升为跨 Host 协议对象，但无法自动补齐底层能力的幂等性、事务语义或业务 Oracle。

## 4. stdio MCP 与 CLI 的关系：共用通道，不是同一种接口

### J3：`stdio` 是 MCP 的传输方式，不是把 MCP 降格为普通 CLI

**事实。** MCP stdio transport 要求 **Client 启动 MCP Server 子进程**；Server 从 stdin 读取 JSON-RPC，从 stdout 输出 JSON-RPC，stdout 不能混入任何非 MCP 消息。MCP 同时支持 Streamable HTTP；后者的 Server 是可服务多个 Client 的独立进程，并引入 HTTP、认证、可选 SSE 和 session 管理。

**事实。** Codex 的官方配置形式是 `codex mcp add <name> -- <stdio server-command>`；Claude Code 的 `claude mcp add ... -- <command> [args...]` 会启动 local stdio server，并说明本地 stdio server 是本机进程。GitHub Copilot CLI 的 `copilot mcp add -- <command> [args...]` 也将该命令定义为本地 stdio server 的启动命令。

**推断（高置信）。** stdio MCP 的关系可画成：

```text
Agent Host / Agent CLI
  └─ 启动子进程（用一条本地命令）
       └─ MCP Server
            stdin/stdout：只传 JSON-RPC
            └─ 可选：内部再调用 CLI / SDK / API / DB
```

因此，`snyk mcp -t stdio`、`circleci mcp start` 之类的命令，是“用 CLI **启动** MCP Server”，不是让 Agent 以人类命令格式调用 Snyk / CircleCI 的业务能力。反过来，`snyk test --json`、`terraform plan -json` 才是 Agent **直接调用 CLI** 的例子。

**边界。** stdio 没有网络传输开销，但仍有子进程启动、协议初始化、工具枚举和 Server 实现开销；直接 CLI 也可能调用远程 API。没有一手基准测试时，不能宣称任何一方“必然更低延迟”。

## 5. 业界产品信号：共存不是偶然

| 组织 / 资料 | 已核验的产品事实 | 可支持的架构判断 | 页面使用边界 |
|---|---|---|---|
| OpenAI / Codex | `codex exec` 用于 scripts / CI、管道与 JSONL；`codex mcp add ... -- <stdio server-command>` 配置 local MCP，也支持 Streamable HTTP。 | 一个 Agent CLI 同时是非交互执行 Harness 和 MCP Host。 | 文档未单列 MCP GA/Preview；不能说 MCP Server 底层必用 CLI。 |
| Anthropic / Claude Code | `claude mcp add` 同时支持 remote HTTP 与 local stdio；官方将 local stdio 用于需直接系统访问或自定义脚本的工具；MCP 可暴露 resources、prompts，并提供 tool search。 | MCP 适合把外部服务/上下文加入 Agent CLI；本地进程仍可作为 Server。 | “tool search 降低上下文”是 Claude Code 的实现特性，不能外推成 MCP 协议保证。 |
| GitHub / Copilot CLI | GitHub MCP Server 默认已配置；CLI 也能通过 hooks 运行 Shell；工具许可可分别约束 Shell 与指定 MCP Server Tool，sandbox 同时限制 Shell、MCP/LSP server 和内建工具。 | 同一 Agent CLI 把直接命令与 MCP 当作并列工具面，并在 Host 层做差异化许可。 | GitHub 的具体许可 flag / 行为不是 MCP 标准。 |
| HashiCorp / Terraform | CLI 将 `plan`、审查与 `apply` 分开，并维护 JSON 输出；官方还维护 Terraform MCP Server。 | 成熟工程能力可并行保留“执行 CLI”与“面向 AI 的协议接入”。 | 不可据此认定 Terraform MCP Server 是 CLI 的薄封装。 |

**跨来源推断（高置信）。** OpenAI、Anthropic、GitHub 三家独立的 Agent CLI 都公开将 MCP 配置置于 CLI 产品中，并同时保留 Shell / 非交互执行路径；HashiCorp 的并行产品面进一步说明，CLI 与 MCP 已形成互补接口组合，而非替换迁移。

## 6. 选择框架：按任务边界选择，而不是按“是否 Agent”选择

| 作业条件 | 更优先的接口 | 原因（判断性质） | 必须保留的控制 |
|---|---|---|---|
| 单一 Agent Harness 在开发机或 CI Runner 内执行已有的 build、test、scan、plan、deploy 命令 | **直接 CLI** | 命令、工作目录、日志、文件、Plan、管道与已有身份可以在同一 Runner 内显式编排和排错。 | 最小权限身份、固定版本/输入、`--json` 或可解析输出、timeout、重试/幂等、Test/Policy/审批。 |
| 需要完整使用成熟工具的所有子命令；MCP 只暴露其中一部分 | **直接 CLI** | MCP Server 的 Tool 集是发布者选择的能力子集；CLI 可能是功能覆盖更完整的既有表面。 | 不把“功能更多”理解为“可自动授权更多”。 |
| 多个 Agent Host / IDE / CLI 都需要复用同一个 SaaS、知识库、工单、监控或集中平台能力 | **MCP（通常 Remote HTTP）** | 可将工具、资源、认证接入与上下文语义做成可被多个 Host 消费的统一集成点。 | Server allowlist、OAuth/Token scope、Host 工具许可、后端 RBAC、审计和速率/超时控制。 |
| Agent 需要先发现有哪些工具、理解参数 Schema、按目录选择跨产品能力，或读取可引用的外部上下文 | **MCP** | Tools / Resources / Prompts、能力协商和连接生命周期是 MCP 的原生协议对象。 | 不信任 Server 的描述或 annotation；防提示注入、数据外泄和过宽的工具发现。 |
| 同时需要跨 Host 的发现/复用，且关键动作仍应在受控 CI Runner 中以可复验 Plan/日志执行 | **组合：MCP + CLI/API** | MCP 负责接入、发现与上下文；CLI/API 负责明确执行。也可由 MCP Server 调用后端，而无需再经 CLI。 | 在架构中明确：谁负责发现、谁执行、用谁的身份、在哪个控制点批准、如何验收。 |

## 7. 架构代价与故障面：不要只看“调用是否方便”

| 维度 | 直接 CLI / Shell | Local stdio MCP | Remote MCP（Streamable HTTP） |
|---|---|---|---|
| 部署单元 | Agent / Runner 所在环境必须安装 CLI 与其依赖 | Host 还需启动、监督和升级 Server 子进程 | Server 作为独立服务部署，可由多 Client 复用 |
| 故障面 | 二进制、参数、cwd、环境变量、凭据、后端 API | 上述项（若 Server 依赖它们）+ 子进程、JSON-RPC framing、初始化、Tool schema | Server 可用性、DNS/TLS/网络、OAuth/Token、HTTP/session、限流 + 后端 |
| 状态 | 多为一次命令；状态常落在文件、工作目录或后端系统 | 至少有 Client–Server 连接与生命周期；Server 可维持进程内状态 | 规范明确支持有状态 session、取消/重连语义和多 Client 服务 |
| 延迟 | 无新增协议网络层，但可能仍远程调用后端 | 本机 stdio 无网络传输；多一层启动/协议/Server 调度 | 增加网络、认证与服务调度；可能换来集中复用与免本地安装 |
| 可观测性 | 容易收集命令、exit code、stdout/stderr 与 CI logs；语义由平台自行规范 | 需要采集 Host、Server 和后端三处日志；stderr 可用于 Server 日志 | 还需跨网络追踪、Server 指标、会话与调用审计 |

**事实。** MCP 规范要求 stdio Server stdout 只输出有效 MCP 消息，并为 Streamable HTTP 定义 Session、断连恢复和认证/Origin 防护要求；这直接说明 MCP 引入了连接和服务运行责任。Kubernetes 的授权文档则说明，真正的允许/拒绝发生在后端 API server，而非客户端命令格式。

**推断（中高置信）。** 对软件交付平台而言，MCP 的主要新增成本不是 JSON 本身，而是 Server 生命周期、身份传递、工具目录治理、跨边界可观测性与新增信任面。CLI 的主要新增成本不是“Agent 不会打字”，而是各 CLI 的参数/输出契约、环境漂移和 shell 权限需要被规范化。

## 8. 权限与安全：两者都不是授权系统

### J4：Tool 可见性、Host 许可、任务身份与后端授权必须分开

**事实。** MCP 规范要求用户明确同意数据访问和 Tool 操作，但也明确指出协议本身不能在协议层强制这些安全原则。GitHub Copilot CLI 可分别许可 Shell 命令和特定 MCP Server Tool，并在 sandbox 中同时限制 Shell、MCP/LSP server 与内建工具。Kubernetes 的 API server 在认证后按请求属性和策略作最终 allow/deny，默认拒绝。

**推断（高置信）。** 不能因为 Tool 有 JSON Schema、被 MCP server 暴露、或 Agent 能生成 CLI 命令，就把它当作“已获授权”。至少应分为四层：

```text
发现 / 可见性     → Agent 知道可用什么
Host 许可         → Agent 此次是否可尝试调用
任务身份与后端授权 → 后端是否允许此主体做此动作
独立接受门        → Test / Policy / Scan / Signature / SLO / 人工审批是否接受结果
```

**边界。** 这些控制层的具体实现因产品而异；不能把 GitHub 或 Kubernetes 的机制写成所有 CLI / MCP 的统一标准。

## 9. 对本页左下表和右下洞察的直接建议

### 左下表后两列的推荐改法

不要使用容易混合部署、启动和产品成熟度的“MCP 部署 / 状态”。将事实面改为：

| 推荐列名 | 要展示的事实 | 示例 |
|---|---|---|
| **Agent 直接调用 CLI** | 一个代表性的机器可读或可检查执行命令 | `snyk test --json`；`terraform plan -json`；`circleci config validate` |
| **Agent 通过 MCP 接入** | Server 启动命令或远程连接入口，并用简短标签指明 local stdio / remote HTTP | `snyk mcp -t stdio`；`circleci mcp start`；`mcp.sentry.dev` |

产品阶段可作为第二行小字或脚注，不与接口关系混在同一列。这样每一行都有可对照的事实：**同一能力领域，Agent 可以直接执行 CLI，也可以经 MCP 接入；实际关系由产品决定。**

### 右下的可用主张

> **CLI 把 Agent 的行动落实为可组合、可检查的进程契约；MCP 把外部能力提升为可发现、可复用的协议对象。主流产品同时支持两者，选择取决于执行边界与集成边界，而非调用者是不是 Agent。**

推荐将右下对比分为三行，而不要画成“CLI → MCP 的替代箭头”：

1. **直接执行：** 单 Runner、完整能力、Plan / 日志 / 脚本组合 → CLI。
2. **标准接入：** 跨 Host、远程服务、工具目录与上下文 → MCP。
3. **交付控制不变：** 两条路径均需身份、后端策略和独立接受门。

## 10. 不能写进正式页面的表述

- “CLI 面向人，MCP 面向 Agent。”——与 Codex `exec`、Copilot CLI Shell 和 Claude Code local stdio 的公开产品设计不符。
- “MCP 会取代 CLI。”——没有一手证据，且三家 Agent CLI 的共存设计反证这种绝对说法。
- “stdio MCP 就是 CLI。”——两者共用 stdin/stdout，但 stdio MCP 要求 JSON-RPC、初始化和 Tool/Resource 协议语义。
- “MCP 一定通过 CLI 执行。”——Server 可以直接调用 API、SDK、数据库或其它服务。
- “CLI 天然更快、更安全、更可靠。”——缺少横向基准；CLI 也可能带来宿主机身份与环境漂移风险。
- “MCP Schema 等于权限。”——Schema 是可调用描述，真正授权仍要由 Host、任务身份、Server 与后端 Policy 共同决定。

## 11. 来源索引（均为一手资料）

1. Model Context Protocol, [Specification 2025-11-25](https://modelcontextprotocol.io/specification/2025-11-25), 2025-11-25，访问 2026-07-28。支撑 Host/Client/Server、JSON-RPC、stateful、capability negotiation、Resources/Prompts/Tools、consent 与协议授权边界。
2. Model Context Protocol, [Architecture overview](https://modelcontextprotocol.io/docs/learn/architecture), 页面未显示发布日期，访问 2026-07-28。支撑协议范围、local stdio 与 remote HTTP 的部署/连接差异。
3. Model Context Protocol, [Transports 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18/basic/transports), 2025-06-18，访问 2026-07-28。支撑 stdio 子进程和 stdout JSON-RPC 约束、Streamable HTTP、会话及安全要求。
4. OpenAI, [Codex non-interactive mode](https://learn.chatgpt.com/docs/non-interactive-mode), 页面未显示发布日期，访问 2026-07-28。支撑 `codex exec` 在 scripts/CI、管道、sandbox 与 JSONL 的使用。
5. OpenAI, [Model Context Protocol for Codex](https://learn.chatgpt.com/docs/extend/mcp?surface=cli), 页面未显示发布日期，访问 2026-07-28。支撑 `codex mcp add ... -- <stdio server-command>`、stdio / Streamable HTTP 与配置参数。
6. Anthropic, [Connect Claude Code to tools via MCP](https://code.claude.com/docs/en/mcp), 页面未显示发布日期，访问 2026-07-28。支撑 local stdio / remote HTTP、启动命令、Resources、Tool Search、组织级配置与安全提醒。
7. GitHub Docs, [Using GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/overview) 与 [CLI command reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference), 页面未显示发布日期，访问 2026-07-28。支撑默认 GitHub MCP、Shell hooks、MCP 配置、sandbox 与 Shell/MCP 的许可分离。
8. HashiCorp, [`terraform plan`](https://developer.hashicorp.com/terraform/cli/commands/plan) 与 [`terraform show`](https://developer.hashicorp.com/terraform/cli/commands/show), Terraform v1.15.x latest 活文档，访问 2026-07-28。支撑 Plan/Apply 分离、自动化、机器可读 JSON 与 Plan 漂移边界。
9. Kubernetes, [JSONPath Support](https://kubernetes.io/docs/reference/kubectl/jsonpath/)（最后修改 2025-04-06）与 [Authorization](https://kubernetes.io/docs/reference/access-authn-authz/authorization/)（活文档），访问 2026-07-28。支撑 `kubectl -o json` 与后端 API 默认拒绝的授权边界。

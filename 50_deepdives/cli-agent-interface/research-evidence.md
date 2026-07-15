---
title: CLI 在 Agent 与 CI/CD 中的能力、替代边界与行业趋势：一手证据
aliases:
  - CLI Agent Interface Research Evidence
  - Agent-ready CLI 一手证据
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/evidence
  - tool/cli
status: complete
as_of: 2026-07-15
topic_id: cli-agent-interface
topic_type: technology
source_policy: primary-only
time_window: "2025-07-01/2026-07-15"
foundational_baseline: "POSIX.1-2024 and selected 2025-05/06 launch records"
confidence: high
---

# CLI 在 Agent 与 CI/CD 中的能力、替代边界与行业趋势：一手证据

> [!abstract] 研究结论
> CLI 不是 MCP 的旧版替代物，而是另一维度的进程执行接口。单 Harness、本地或 CI Runner、已有成熟 CLI 的场景中，直接 CLI 往往已足够；多客户端共享、动态发现、统一 Schema、远程授权、Resource/Prompt、双向通知与协议级长任务更适合 MCP。2025H2—2026 的一手证据显示，主流路线是“Agent CLI 同时调用 Shell/CLI 与 MCP”，以及“既有 CLI/API 再按需要暴露为 MCP”，不是二选一。

> [!info] 证据标记
> - **事实**：官方规范、官方文档、官方仓库或第一方工程材料直接支持。
> - **推断**：由多个事实归纳出的架构或趋势判断，不冒充厂商结论。
> - **建议**：面向企业采用的设计要求，需要通过本地 Lab 和任务集验证。

## 研究范围与排重

本页只研究 CLI 维度：进程契约、Agent-ready 设计、Runner 运行、安全边界、与 MCP 的可替代性及趋势。它不把 CLI、MCP、Skill 与 Agent/Harness 合并成一个概念。

行业趋势的主时间窗是 2025H2—2026-07-15；POSIX.1-2024、Claude Code 2025-05 GA 和 Gemini CLI 2025-06 preview 只用作进入时间窗前的基线，不据此扩大趋势样本。

库内已有材料及本页增量如下：

| 已有材料 | 已覆盖 | 本页不重复 / 新增 |
|---|---|---|
| [[00_sources/agentic-cicd-source-landscape#S78. Claude Code CLI reference|S78 Claude Code]]、[[00_sources/agentic-cicd-source-landscape#S79. OpenAI Codex CLI|S79 Codex CLI]]、S80 OpenCode | Agent Harness 存在性与基本定位 | 新增 headless 契约、JSONL/Schema、预算/轮次、权限和 CI 安全证据 |
| [[00_sources/briefs/2026-claude-code-github-action|Claude Code GitHub Action]] | Agent 作为 CI Step | 只把它作为“CLI/Agent core 跨表面”的案例，不重做 Action 产品分析 |
| [[00_sources/briefs/2026-github-gh-aw-open-source|GitHub gh-aw]] | Agentic Workflow 编译器存在性 | 新增 CLI 作为编译、锁定、验证和审计控制面的证据 |
| [[00_sources/briefs/2026-cloudsmith-mcp-artifact-management|Cloudsmith MCP]] | CLI 暴露 MCP 的产品案例 | 用来验证“CLI 是 MCP 后端/包装入口”，不重做制品仓研究 |
| [[00_sources/briefs/2026-cli-anything|CLI-Anything Brief]] | Agent 原生 CLI 生成器 | **不在本页深入**；CLI-Anything 应在独立专题研究生成流程、质量和适用场景 |

> [!warning] 产品状态校正
> GitHub Copilot CLI 整体于 **2026-02-25 GA**；2026-06-23 GA 的是其“新终端界面”，不是产品首次 GA。应分别引用[产品 GA 公告](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/)和[新界面 GA 公告](https://github.blog/changelog/2026-06-23-copilot-cli-new-terminal-interface-is-generally-available/)。

## 一、先拆开 CLI 的三种角色

“CLI”在当前讨论中至少有三种不同角色；不拆开会把执行工具、Agent 主体和治理工具混为一谈。

| 角色 | 它是什么 | 典型例子 | 对 CI/CD 的价值 |
|---|---|---|---|
| **能力 CLI / Tool CLI** | 把软件、API 或本地程序封装成命令、参数、标准流和退出状态 | `git`、`gh`、`kubectl`、`terraform`、`aws`、内部发布 CLI | Agent 可调用的确定性行动面 |
| **Agent CLI / Harness CLI** | 在终端中运行模型、上下文、工具循环、权限与会话的 Agent 主体 | Claude Code、Codex CLI、Copilot CLI、Gemini CLI | 本地交互、脚本化 Agent、CI 中的推理执行单元 |
| **治理 / 编译 CLI** | 构建、验证、固定和运行 Agent 工作流 | `gh aw compile/validate/run/logs` | 把自然语言工作流编译成可审计、固定依赖的执行物 |

**事实：** POSIX.1-2024 的 Shell Command Language 定义了命令、参数、重定向、管道、顺序/条件组合与退出状态；这是 CLI 可被脚本和 Runner 组合的基础，而不是 Agent 时代新发明的能力。[The Open Group：Shell Command Language](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html)

**推断：** Agent-ready CLI 的新意不在“能从终端运行”，而在于把传统进程契约升级为模型可可靠选择、机器可解析、权限可约束、结果可验证的工具契约。

## 二、CLI 能力模型

### 2.1 进程与组合能力

一个 CLI 调用天然可以拥有以下边界：

- 命令名、参数和环境变量；
- stdin 输入，stdout 业务结果，stderr 诊断/进度；
- 进程退出状态、信号、超时和强制终止；
- 当前目录、文件、管道、重定向和产物；
- OS 用户、容器、Runner、CPU/内存/网络限制。

**事实：** OpenAI 当前 `codex exec` 文档明确把进度写入 stderr、只把最终消息写入 stdout，并支持 stdin/管道、只读或 workspace-write sandbox；这说明 Agent CLI 本身也在遵循传统 Unix 组合边界。[OpenAI：Codex non-interactive mode](https://developers.openai.com/codex/noninteractive)

**优势：** 既有 CI Runner 无需理解新协议即可安装、锁定并运行 CLI；同一条命令也便于开发者本地复现。

**局限：** Shell 只规定进程如何组合，不规定每个工具的业务 Schema、破坏性、幂等性、权限范围或版本兼容性。

### 2.2 结构化输出与机器契约

成熟 DevOps CLI 在 Agent 出现前已经提供机器模式：

- GitHub CLI 用 `--json <fields>` 显式选择字段，并可用 `--jq` 或 Go template 继续处理；省略字段参数还能列出可选字段。[GitHub CLI：JSON formatting](https://cli.github.com/manual/gh_help_formatting)
- Kubernetes 官方要求可复用脚本使用 `-o name/json/yaml/go-template/jsonpath` 等机器格式、完整限定 API 版本，并避免依赖隐式 context/preference。[Kubernetes：kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)
- AWS CLI 支持 JSON、text、YAML、table 等输出；官方还提醒 CLI 输出可能把敏感信息带入 CI/CD 日志。[AWS：Controlling command output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output.html)
- AWS CLI 文档区分解析失败、配置/凭据错误、服务错误、中断等退出码，说明“非零”还可以进一步表达错误类别。[AWS：CLI return codes](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-returncodes.html)

Agent CLI 又把机器契约推进了一层：

- Claude Code `-p` 支持非交互执行、`text/json/stream-json`、流式 JSON 输入、JSON Schema 验证、`--max-turns`、`--max-budget-usd`、工具 allow/deny 和权限模式；当前文档还明确记录了部分行为的版本差异。[Anthropic：Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)
- Codex `exec --json` 输出 JSONL 事件流，事件覆盖线程、turn、命令、文件变更、MCP 调用和 token usage；`--output-schema` 约束最终 JSON，并能把最终消息单独写入文件。[OpenAI：Codex non-interactive mode](https://developers.openai.com/codex/noninteractive)
- Gemini CLI headless mode定义了 JSON/JSONL 事件和 `0/1/42/53` 等退出码，其中 53 表示超过 turn limit。[Google：Gemini CLI headless reference](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/headless.md)

**推断：** “非交互 + JSONL 事件 + 最终结果 Schema + 资源上限”正在成为 Agent CLI 的事实型设计收敛点，但各家参数名、事件类型和兼容承诺不同，仍不是跨厂商协议。

### 2.3 状态、副作用与验证

CLI 可以无状态，也可能隐式读取当前目录、默认账号、Region、Cluster、Namespace、Branch 或本机登录态。对 Agent 来说，后者是高风险来源：命令语法正确不代表目标对象正确。

**事实：** Kubernetes 的脚本约定直接要求不要依赖 context、preference 等隐式状态，并建议完整限定资源版本。[kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/)

**事实：** Terraform 官方自动化流程使用 `-input=false` 禁止交互，先保存 plan，再由人检查并 apply 同一 plan；官方同时建议使用支持远程状态和锁的 backend，防止并发竞态。[HashiCorp：Running Terraform in automation](https://developer.hashicorp.com/terraform/tutorials/automation/automate-terraform)

**事实：** `kubectl apply --dry-run=client` 只打印将发送的对象，`--dry-run=server` 会经过服务端处理但不持久化；输出可选 JSON/YAML，输入也可来自 stdin。[Kubernetes：kubectl apply](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/)

**推断：** 高风险 Agent CLI 的关键不是再加一个 `--json`，而是提供显式目标、plan/dry-run、版本或哈希绑定、并发控制、幂等/重试语义和独立 Oracle。

### 2.4 安全、身份与可观测性

CLI 本身不形成安全边界；真正边界来自调用进程的身份、文件系统、网络、Runner、凭据和下游服务授权。

**事实：** OpenAI 的 CI 文档警告，不应把 API Key 设为会运行仓库代码的 job 级环境变量，因为构建脚本、测试、依赖 lifecycle hook 或受损 Action 都能读取；官方推荐把生成 patch 与拥有写权限的开 PR job 分离。[OpenAI：Codex non-interactive mode，CI security pattern](https://developers.openai.com/codex/noninteractive)

**事实：** Anthropic 的工程材料把 Agent 安全落实为文件系统和网络两条 OS 级隔离，并报告其内部使用中 sandbox 使权限提示减少 84%；这是一项厂商自报内部结果，不是跨产品基准。[Anthropic：Beyond permission prompts](https://www.anthropic.com/engineering/claude-code-sandboxing)

**事实：** Gemini CLI 支持 macOS Seatbelt、Docker/Podman、gVisor 等 sandbox，并能在命令需要额外路径或网络时发起一次性扩权请求；不同平台和 provider 的强度、路径挂载与副作用并不相同。[Google：Gemini CLI sandbox](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/sandbox.md)

**事实：** GitHub Copilot CLI 的 deny 规则优先于 allow 规则，并能分别限制模型可见工具和工具执行授权；官方同时警告 `--yolo`/全允许只应在隔离环境中使用。[GitHub：Allowing and denying tools](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/allowing-tools)

**推断：** 2025H2—2026 的安全趋势不是让模型更频繁点击“批准”，而是把常规行为放进可审计 sandbox，把越界行为交给一次性审批，并把高风险写入拆到另一个身份/阶段。

## 三、Agent-ready CLI 的可操作判据

以下是基于上述一手文档归纳的**建议**，不是现有统一标准。

| 契约面 | P0 最低要求 | P1 增强要求 | 证据基础 |
|---|---|---|---|
| 发现 | `--help`、`--version`、稳定子命令；文档标明实验/废弃状态 | `capabilities --json` 或等价机器清单、输入 Schema | GitHub JSON field discovery；MCP 的标准 `tools/list` 是对照组 |
| 输入 | 无 TTY 也能运行；支持参数或 stdin；缺参立即非零退出 | JSON/JSONL 输入、Schema 校验、显式 API/资源版本 | Claude/Codex/Gemini headless；Terraform `-input=false` |
| 输出 | stdout 只放业务结果，stderr 放诊断；稳定 JSON；禁止 pager/ANSI | JSONL 事件、final result Schema、产物哈希 | Codex stdout/stderr；Gemini JSONL；[AWS `--no-cli-pager`](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-pagination.html) |
| 错误 | 非零退出；解析、认证、权限、冲突、远端失败可区分 | 稳定错误对象、retryable 标志、关联 ID | AWS 与 Gemini 退出码 |
| 目标 | Account/Org/Project/Region/Cluster/Namespace/Branch 显式可见 | 目标摘要进入输出和审计；禁止生产默认值 | kubectl 不依赖 context 的约定 |
| 副作用 | read/write 命令分开；dry-run/plan；明确是否幂等 | 幂等键、revision/hash 前置条件、rollback/compensation | Terraform plan/apply、kubectl server dry-run |
| 资源 | timeout、最大记录数/页数、最大 turn/成本 | cancellation、progress、resume、checkpoint | Claude budget/turn；Gemini turn exit；Codex resume |
| 身份 | 任务级、最小权限凭据；秘密不进参数和输出 | OIDC/短期令牌、凭据代理、每命令 scope | Codex Action 安全模式；[Cloudsmith CLI CI OIDC](https://github.com/cloudsmith-io/cloudsmith-cli) |
| 隔离 | 非 root、workspace 文件边界、默认无任意网络 | OS/container sandbox、域名 allowlist、一次性扩权 | Anthropic/Google/GitHub sandbox 与权限资料 |
| 版本 | 固定 CLI 版本；输出字段有兼容策略 | 事件/Schema 版本、能力协商、弃用窗口 | Claude 文档中的版本行为差异；`gh aw` lock/pin |
| 可观测 | 命令、身份、目标、退出码、耗时可审计 | session/turn/tool IDs、usage、结构化事件 | Codex JSONL；Gemini JSONL；`gh aw logs` |
| 验证 | 写后由测试/扫描/Policy 查询真实状态 | 生成与执行分离、独立 job/identity、证据包 | Codex CI patch 分阶段；GitHub Safe Outputs |

> [!important] `--help` 不是 Schema
> Claude Code 当前官方文档明确说 `claude --help` 不列出所有 flag。这个具体例子说明：即使活跃产品有帮助文本，Agent 也不能默认它完整、结构化或兼容；真正机器可发现的接口需要独立契约和版本策略。[Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)

## 四、CLI 与 MCP：可替代与不可替代边界

比较基线采用 MCP **2025-11-25** 日期化规范，避免把仍在变化的 draft 当成稳定事实。该版本用 JSON-RPC 定义 lifecycle/capability negotiation、Tools、Resources、Prompts、Sampling、Elicitation、Notifications 等；Tasks 在该版本中仍标为 experimental。[MCP Architecture](https://modelcontextprotocol.io/docs/learn/architecture)、[MCP 2025-11-25 Tasks](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/tasks)

### 4.1 替代矩阵

| 需求 | 直接 CLI 能否替代 MCP | MCP 能否替代 CLI | 判断依据 |
|---|---|---|---|
| 本地单次执行、读写文件、运行编译/测试 | **通常能**；Harness 已有 Shell 时无须再加协议 | **能包装，但未必值得**；Server 最终仍需执行本地逻辑 | CLI 的进程/Runner 组合成本最低 |
| Shell 管道、重定向、大文件/二进制产物、任意本地程序 | **CLI 原生强项** | **只能通过专门 Tool/Resource 或包装实现** | POSIX 管道与文件是现成 substrate |
| 已有成熟 `git/gh/kubectl/terraform/aws` 工具链 | **通常能**，前提是机器模式、显式目标和最小权限 | **可作为统一适配层**，但会增加 Server/Host 运行件 | 是否需要多客户端复用决定增量价值 |
| 动态 Tool 发现与统一参数 Schema | **不能标准化替代**；只能为每个 CLI 自建 manifest/parser | **MCP 原生**：`tools/list`、`inputSchema`、可选 `outputSchema` | [MCP schema](https://modelcontextprotocol.io/specification/2025-11-25/schema) |
| Tool 列表变更通知 | **无通用机制**；只能轮询版本/帮助或自定义 daemon | **MCP 可选原生通知** | MCP `listChanged` capability |
| Resource/Prompt 作为独立协议对象 | **可用命令/文件模拟，但无统一语义** | **MCP 原生建模** | MCP Architecture 的三类 server primitives |
| 双向 progress、cancellation、user elicitation、server-to-client request | **每个 CLI 可自建，但不可互操作** | **MCP 有标准原语**，且多数是可选能力 | [Progress](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/progress)、[Cancellation](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/cancellation)、[Elicitation](https://modelcontextprotocol.io/specification/2025-11-25/client/elicitation) |
| 远程、多客户端、多租户服务 | **部分能**，但需 CLI 自己实现 API、会话、认证和分发 | **更自然**：Streamable HTTP、session、协议版本 | [MCP transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports) |
| 远程 OAuth discovery 与 step-up scope | **各 CLI 自定义** | **MCP HTTP 有规范路径**，但 Authorization 本身仍是 optional | [MCP authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) |
| 本地 stdio 凭据安全 | **依赖进程环境** | **并不天然更安全**；MCP 规范要求 stdio 从环境取凭据而非套用 HTTP OAuth | MCP Authorization 2025-11-25 |
| 富内容结果（text/image/audio/resource link） | **可通过文件或 JSON 约定，非统一** | **MCP 有标准内容类型** | MCP Schema Reference |
| 审批、最小权限、沙箱、审计 | **CLI 不自带** | **MCP 也不自带完整治理**；协议标注是 hint，Authorization optional | 必须依赖 Host/Gateway/Runner/Policy |
| 可重复构建和本地调试 | **CLI 通常更直接** | **MCP Server 也可固定版本，但多一层协议与 Host** | 工具数量与复用半径决定取舍 |

### 4.2 CLI 可以完整替代 MCP 的条件

以下条件同时成立时，直接 CLI 可视为 MCP Tool 调用面的充分替代：

1. 只有一个或少数拥有 Shell 的 Agent Harness；
2. 工具与 Harness 同机、同容器或同一 CI Runner；
3. CLI 有非交互、稳定 JSON/JSONL、退出码、超时和显式目标；
4. 身份能按任务注入，权限与网络由 Runner 外部强制；
5. 不需要动态 Catalog、Resource/Prompt、远程 OAuth、通知或协议级任务；
6. 团队愿意为不同 Harness 维护调用说明/Skill 和兼容测试。

典型场景：代码格式化、构建、测试、日志筛选、只读仓库查询、生成 Terraform plan、产出 patch/SARIF/SBOM。

### 4.3 CLI 不能完整替代 MCP 的条件

出现以下任一需求，MCP 或其他正式 API/协议通常更合理：

- 同一能力要被多个 Agent 产品动态发现和调用；
- 参数和结果需要统一 JSON Schema，而不是每个客户端解析帮助文本；
- Server 要主动发通知、请求模型采样或向用户 elicitation；
- 需要远程多用户、协议版本协商、OAuth discovery/step-up；
- Context 不只是“执行一个动作”，还包括 Resources 和 Prompts；
- 长任务需要跨断线持久状态、轮询、取消和统一进度；
- 平台团队要在 Gateway/Registry 统一治理多种客户端。

### 4.4 MCP 也不能消除 CLI 的价值

MCP stdio Server 本身往往就是由 Client 启动的本地子进程；规范要求 stdout 只能发送 JSON-RPC，日志写 stderr。[MCP transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports)

**推断：** 这意味着 MCP 没有替代进程 substrate，而是在它之上增加统一语义。Server 最终仍可能调用 CLI、SDK 或 API。对于编译、测试、文件转换和批量数据处理，CLI 仍是最自然的实现与调试入口。

### 4.5 安全不能用“CLI vs MCP”直接判断

MCP Tool 的 `readOnlyHint`、`destructiveHint`、`idempotentHint` 等只是 hint；规范明确提醒客户端不能基于不可信 Server 的 annotation 做安全决策。[MCP Schema Reference](https://modelcontextprotocol.io/specification/2025-11-25/schema)

MCP Authorization 也是 optional；HTTP transport 在支持授权时应走规范，stdio 则建议从环境取凭据。[MCP Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)

**结论：** CLI 与 MCP 都不能替代任务身份、命令/Tool allowlist、sandbox、网络控制、写入审批、确定性 Policy 和结果 Oracle。

## 五、已验证的组合架构

### 模式 A：Agent 直接调用受控 CLI

```text
Agent Harness -> Shell Tool -> pinned CLI -> API/local backend
                      |-> stdout/stderr/exit/artifact
```

适合单 Runner、成熟工具、低适配成本。关键控制是缩小可调用命令与参数，而不是把整个 Shell 或云管理员凭据交给 Agent。

### 模式 B：CLI 作为 MCP Server 入口

Cloudsmith 在 2026-03-02 宣布其 CLI 可作为本地 MCP Server，把 Cloudsmith API 暴露为 tools；Server 复用现有 CLI 凭据和 profile，初版没有 hosted remote Server，Workspace Policy 和 usage metrics 也尚未暴露。[Cloudsmith 官方 Changelog](https://cloudsmith.com/changelog/manage-your-supply-chain-using-natural-language-with-mcp)

**事实意义：** 这是“CLI 被 MCP 淘汰”的直接反例：同一个 CLI 二进制同时承担传统命令入口、MCP Server 启动/配置和凭据复用。

**限制：** 可调用工具范围小于完整 CLI/API；复用 CLI profile 也意味着权限边界仍取决于原凭据。

### 模式 C：Agent CLI 同时消费 MCP 与 Shell/CLI

- Claude Code CLI 有 `--mcp-config`、strict MCP config 与 MCP Tool allow/deny，同时保留 Bash/File tools。[Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage)
- Copilot CLI GA 公告说明它内置 GitHub MCP Server，支持自定义 MCP、Plugins、Skills、Hooks，同时仍可执行 Shell、编辑、测试和本地/云委派。[GitHub Copilot CLI GA](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/)
- Gemini CLI 发布时同时强调非交互脚本与 MCP/extension 扩展。[Google：Introducing Gemini CLI](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemini-cli-open-source-ai-agent/)
- Codex CLI 的 JSONL 事件类型同时包含本地命令执行与 MCP Tool 调用。[OpenAI：Codex non-interactive mode](https://developers.openai.com/codex/noninteractive)

**推断：** 主流 Harness 已把 CLI/Shell 与 MCP 当作并列工具来源；MCP 解决互操作，CLI/Shell 解决本地执行和现有工具覆盖。

### 模式 D：CLI 作为 Agent Workflow 编译与治理控制面

GitHub `gh aw compile` 把 Markdown 工作流解析、Schema 校验、构建 job、解析依赖并生成 `.lock.yml`；Actions 被固定到 commit SHA，`actions-lock.json` 用于可重复解析，`validate --json` 可供自动化消费。[GitHub：gh-aw compilation process](https://github.github.com/gh-aw/reference/compilation-process/)

GitHub 的安全架构还把 agent、network firewall、API proxy、MCP Gateway、Safe Outputs 分阶段隔离；编译期做 Schema/表达式校验和 Action pinning，写操作通过受信阶段外化。[GitHub：Agentic Workflows Security Architecture](https://github.github.com/gh-aw/introduction/architecture/)

**推断：** CLI 的新角色不止是“被 Agent 调用”，还包括把 Agent 源文件编译成确定性执行物、进行供应链固定、验证与审计。

## 六、2025H2—2026 行业趋势

### T1：Agent CLI 从预览进入 GA，但状态必须逐产品核验

| 时间 | 一手事实 | 截至 2026-07-15 的状态判断 |
|---|---|---|
| 2025-05-22 | Anthropic 宣布 Claude Code 从 research preview 进入 GA；H2 又发布 Agent SDK、checkpoint 和 sandbox。[Claude 4 公告](https://www.anthropic.com/news/claude-4)、[Autonomy upgrades](https://www.anthropic.com/news/enabling-claude-code-to-work-more-autonomously) | GA 产品；具体 CLI flag 仍快速变化 |
| 2025-09-25 -> 2026-02-25 | GitHub Copilot CLI 从 public preview 进入 GA。[Preview](https://github.blog/changelog/2025-09-25-github-copilot-cli-is-now-in-public-preview/)、[GA](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/) | GA；2026-06 的是新 TUI GA |
| 2025-10-06 | OpenAI 宣布 Codex GA，并发布 SDK 和 GitHub Action；官方建议 Shell 环境用 `codex exec` 接入 CI。[OpenAI：Codex GA](https://openai.com/index/codex-now-generally-available/) | Codex 产品 GA；CLI、SDK、Action 是同一 Agent core 的不同表面 |
| 2025-06-25 -> 2026-05-19 | Gemini CLI 以 preview 发布；Google 后续宣布 consumer 路线转向 Antigravity CLI，共享桌面与 CLI 的统一 server-side harness。[发布](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemini-cli-open-source-ai-agent/)、[迁移公告](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/) | 官方计划 2026-06-18 停止 Gemini CLI 的 free/AI Pro/Ultra 请求；企业和付费 API key 继续支持。此处依据公告时间线，未独立验证切换结果 |
| 2026 | GitHub Agentic Workflows 官方文档标为 public preview，使用 `gh aw` 初始化、编译、验证与运行。[GitHub Docs](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows) | Public preview；快速演进，不宜视为稳定平台标准 |

**推断：** CLI 已是主要 Agent 产品表面，但“有 CLI”不等于产品成熟度一致；报告必须区分 preview、GA、特性 GA 和迁移/弃用状态。

### T2：Agent core 从单一终端程序走向跨表面 Harness

- OpenAI 明确表示 Codex SDK 使用与 Codex CLI 相同的 Agent 实现，Action/CLI 可进入 CI。[Codex GA](https://openai.com/index/codex-now-generally-available/)
- Anthropic 把 Claude Code SDK 更名为 Agent SDK，并称其暴露同一套 tools、context management 与 permissions framework。[Claude Code autonomy upgrades](https://www.anthropic.com/news/enabling-claude-code-to-work-more-autonomously)
- Google 迁移公告强调 Antigravity CLI 与桌面应用共享统一 Agent Harness，支持后台多 Agent。[Google migration announcement](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/)
- Copilot CLI GA 支持本地 Agent、云端 background delegation、subagents、MCP、Skills 和 Hooks。[Copilot CLI GA](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/)

**推断：** CLI 正从“产品本体”变成同一 Harness 的一个交互/自动化表面。企业应把 CLI 版本和输出契约视为 API，而不要把整个系统架构等同于终端 UI。

### T3：机器接口从 JSON 升级为事件、Schema、成本与会话

Claude、Codex、Gemini 的官方 CLI 都已提供非交互和 JSON/JSONL；Claude 与 Codex增加最终 JSON Schema，Claude 增加 budget/turn 限制，Codex 与 Claude 支持 resume，Gemini 对 turn limit 给出专门退出码。

**推断：** 未来 Agent-ready CLI 的竞争点将从“有没有 JSON”转向：事件是否稳定、错误是否可分类、Schema 是否验证、长任务是否可取消/恢复、使用成本是否可观察。

### T4：安全从逐命令确认转向 sandbox、阶段与确定性外化

Anthropic 的 sandbox 工程资料、Google 的多 provider sandbox、OpenAI 的 patch/job 权限分离，以及 GitHub `gh-aw` 的 Safe Outputs 都把控制放到模型之外。

**推断：** Prompt 内“不要做危险操作”不是生产控制；真正趋势是 workspace/network boundary、任务身份、只读 Agent 阶段、受信外化阶段和独立验证。

### T5：CLI 与 MCP 形成上下层，而不是互相淘汰

主流 Agent CLI 同时内置或支持 MCP；Cloudsmith 则从现有 CLI 暴露本地 MCP Server。MCP 规范自身的 stdio transport 也建立在子进程和标准流之上。

**推断（置信度 medium-high）：** 可复用架构将是“CLI/SDK/API 实现真实能力 -> 可选 MCP 互操作层 -> Skill/说明 -> Agent Harness -> 外部 Runner/Gateway/Policy”。证据覆盖四家主流 Agent 产品和一个 DevOps 产品，但不是市场份额研究，不能外推为所有厂商已统一。

### T6：Agent CLI 产品层仍高度易变

Google 在不到一年内从 Gemini CLI preview 转向 Antigravity CLI；Claude Code 当前文档也逐项标注大量版本前后差异；`gh-aw` 仍为 public preview。

**建议：** 在 CI 中固定 CLI 版本、记录 `--version`、为 JSON/JSONL 做 contract test、显式禁用用户级配置，并为升级建立 canary 和回滚。不要只固定 prompt 而让 CLI/Harness 自动升级。

## 七、CI/CD 场景选择

| 场景 | 首选接口 | 原因 | 最低控制 |
|---|---|---|---|
| 本地代码理解、format/lint/test/build | 直接 CLI | 低延迟、环境真实、易复现 | workspace sandbox、固定版本、无生产凭据 |
| CI 失败诊断、生成 patch | Agent CLI + 业务 CLI | 日志/测试/文件天然在 Runner | read-only 取证；patch artifact；写 PR 独立 job |
| 多个 Agent 客户端查询同一 DevOps 平台 | MCP 或 API，后端可复用 CLI/library | 避免每客户端解析帮助文本与凭据 | Tool allowlist、Schema、任务身份、Gateway audit |
| Terraform/Kubernetes 变更 | CLI plan/dry-run + 独立批准 | 既有工具的真实 backend 与状态语义最成熟 | 显式 target、plan/hash、state lock、批准后 apply |
| 制品查询/SBOM/漏洞 | CLI 或 MCP 均可 | 取决于单 Runner 还是多客户端共享 | 只读 profile、JSON、敏感字段清理 |
| 制品晋级、签名、生产发布 | 不授权整个 CLI；窄 Tool/Runbook + Policy | 高风险副作用与供应链权限 | 短期任务身份、hash/environment 绑定、审批、Oracle |
| 事故调查 | 直接只读 CLI 优先 | 日志、状态、拓扑与本地工具组合强 | 只读、时间窗/集群显式、输出脱敏 |
| 预批准恢复动作 | 窄 CLI 或 MCP Tool，均由 Runbook 约束 | 接口类型不决定安全 | 参数约束、幂等、超时、回滚和 SLO 验证 |

## 八、Claim—Evidence—Gap

| Claim | 判断 | 一手证据 | 反例 / 缺口 | 置信度 |
|---|---|---|---|---|
| CLI-R01 | CLI 的核心价值是进程契约与既有 Runner substrate，不是字符界面 | POSIX；Codex stdin/stdout/stderr；Terraform automation | Windows/跨 Shell 行为仍需逐平台测试 | high |
| CLI-R02 | 传统机器 CLI 已有 JSON/exit/dry-run；Agent-ready 在其上增加事件、Schema、资源上限和权限 | gh/kubectl/AWS/Terraform；Claude/Codex/Gemini | 各产品没有统一 CLI 协议 | high |
| CLI-R03 | 单 Harness、本地/CI、成熟工具场景中，直接 CLI 可替代 MCP Tool 调用面 | Shell + headless CLI 文档；Cloudsmith CLI/MCP 双表面 | 缺少同任务 CLI vs MCP 的公开对照基准 | high for architecture, medium for cost outcome |
| CLI-R04 | CLI 不能标准化替代 MCP 的 discovery/schema/resource/prompt/bidirectional/remote auth 能力 | MCP Architecture、Schema、Transport、Auth | 很多 MCP 能力是 optional；客户端支持不齐 | high |
| CLI-R05 | MCP 也不能替代底层 CLI/进程执行，也不天然提供完整安全治理 | MCP stdio；Authorization optional；annotations are hints | 高质量 hosted MCP 可隐藏底层运行复杂度 | high |
| CLI-R06 | 主流趋势是 Agent CLI 同时调用 CLI/Shell 与 MCP | Claude、Codex、Copilot、Gemini 官方资料 | 样本以大型美国产品为主，不是量化市场研究 | medium-high |
| CLI-R07 | Agent CLI 正从单终端产品变成跨 CLI/IDE/App/Cloud/SDK 的共享 Harness | OpenAI、Anthropic、Google、GitHub 官方公告 | 各家架构细节与共享程度不同 | high |
| CLI-R08 | 生产安全依赖 sandbox、任务身份、分阶段写入与独立 Oracle，而非 JSON 或协议选择 | Anthropic sandbox、Codex CI pattern、gh-aw architecture | 厂商内部安全结果未独立审计 | high for mechanism, medium for reported outcomes |
| CLI-R09 | Agent CLI 生命周期变化快，CI 必须 pin 和做 contract test | Gemini -> Antigravity；Claude flag version notes；gh-aw preview | 尚无跨厂商破坏性变更统计 | medium-high |

## 九、证据局限

1. **能力证据不等于效果证据。** 官方文档能证明 flag、Schema、sandbox、MCP 或 CI 接口存在，不能证明企业任务成功率、误操作率、成本或发布可靠性。
2. **厂商内部数字需保持归因。** Anthropic 的“权限提示减少 84%”只代表其内部材料，不是独立或跨产品评测。
3. **规范能力不等于客户端普遍实现。** MCP 的 progress、cancellation、sampling、elicitation、Tasks 等多为可选能力；Tasks 在 2025-11-25 版本还是 experimental。
4. **产品文档会漂移。** Claude/Codex/Gemini 当前文档为 live docs；本页已记录核验日，但复现实验仍要记录具体 CLI 版本和原始输出。
5. **Google 迁移状态依据官方计划。** 2026-05-19 公告写明 consumer 请求于 2026-06-18 停止，本页未用独立运行实验验证日期后的实际账户行为。
6. **趋势样本有地域和厂商偏差。** 主要覆盖 Anthropic、OpenAI、GitHub、Google 及几个成熟 DevOps CLI；不能当成全球采用率或市场份额。
7. **尚无本地对照 Lab。** 最关键缺口是同一任务分别使用直接 CLI、CLI+Skill、MCP wrapper，比较成功率、token、延迟、错误恢复、权限面和维护成本。

## 十、建议后续 Lab

### Lab A：同一只读任务的三种接口

选择 `gh` 或内部只读工具，分别以直接 CLI、CLI+Skill、MCP wrapper 完成“读取 PR 检查并输出固定 Schema”。记录：首次成功率、工具描述 token、调用次数、延迟、错误恢复和升级维护量。

### Lab B：Agent-ready Contract Test

对目标 CLI 固定版本，自动验证：无 TTY、无 pager/ANSI、stdout/stderr 分离、JSON Schema、退出码、timeout、空结果、大结果、认证失败、权限失败、并发冲突和版本升级。

### Lab C：Plan-and-Approval

用 Terraform 或 Kubernetes 测试 `plan/dry-run -> artifact/hash -> 独立身份批准 -> apply -> query Oracle`，刻意改变 target、plan 和远端状态，验证过期计划与错误环境能否被阻断。

### Lab D：凭据与不可信输入

在临时 Runner 中放置 canary secret，让 Agent 处理恶意日志/仓库说明；比较直接 CLI、MCP stdio 和 Gateway 包装在文件、环境和网络边界下的泄漏路径。该 Lab 只在隔离环境和假凭据上执行。

## 一手来源登记

### 基础与传统 CLI

- [The Open Group：POSIX.1-2024 Shell Command Language](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html) — 命令、管道、重定向、退出状态。
- [GitHub CLI：Formatting JSON, jq and templates](https://cli.github.com/manual/gh_help_formatting) — 字段选择和机器输出。
- [Kubernetes：kubectl Usage Conventions](https://kubernetes.io/docs/reference/kubectl/conventions/) — 稳定脚本输出、完整版本和避免隐式 context。
- [Kubernetes：kubectl apply](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_apply/) — stdin、JSON/YAML、client/server dry-run。
- [HashiCorp：Running Terraform in automation](https://developer.hashicorp.com/terraform/tutorials/automation/automate-terraform) — 非交互、保存 plan、人工检查、apply 和 state lock。
- [AWS：Controlling command output](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output.html) — 输出格式与 CI 日志敏感信息风险。
- [AWS：Pagination and disabling the client-side pager](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-pagination.html) — 自动化中关闭 pager。
- [AWS：Command line return codes](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-returncodes.html) — 解析、配置、服务、中断错误分类。

### Agent CLI 与 CI

- [Anthropic：Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage) — 非交互、JSON/JSONL、Schema、预算/轮次、权限与 MCP。
- [Anthropic：Claude Code GA with Claude 4](https://www.anthropic.com/news/claude-4) — 2025-05-22 GA 状态。
- [Anthropic：Enabling Claude Code to work more autonomously](https://www.anthropic.com/news/enabling-claude-code-to-work-more-autonomously) — Agent SDK、checkpoint、subagent/hook/background task。
- [Anthropic：Beyond permission prompts](https://www.anthropic.com/engineering/claude-code-sandboxing) — 文件和网络隔离及内部提示减少数据。
- [OpenAI：Codex non-interactive mode](https://developers.openai.com/codex/noninteractive) — `codex exec`、JSONL、Schema、CI 权限/密钥模式。
- [OpenAI：Codex GA](https://openai.com/index/codex-now-generally-available/) — 2025-10-06 GA、SDK、Action 与 CLI/CI 定位。
- [OpenAI：codex-action](https://github.com/openai/codex-action) — Action 输入、output Schema、permission profile 与 safety strategy。
- [GitHub：Copilot CLI public preview](https://github.blog/changelog/2025-09-25-github-copilot-cli-is-now-in-public-preview/) — 2025-09-25 状态与初始能力。
- [GitHub：Copilot CLI GA](https://github.blog/changelog/2026-02-25-github-copilot-cli-is-now-generally-available/) — 2026-02-25 GA、MCP/Skill/Plugin/Hook、本地/云委派。
- [GitHub：Allowing and denying tools](https://docs.github.com/en/copilot/how-tos/copilot-cli/use-copilot-cli/allowing-tools) — 工具可见性与执行授权分层。
- [Google：Introducing Gemini CLI](https://blog.google/innovation-and-ai/technology/developers-tools/introducing-gemini-cli-open-source-ai-agent/) — 2025-06-25 preview、非交互和 MCP。
- [Google：Gemini CLI headless](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/headless.md) — JSON/JSONL、usage 与退出码。
- [Google：Gemini CLI sandbox](https://github.com/google-gemini/gemini-cli/blob/main/docs/cli/sandbox.md) — 多种 sandbox provider 与扩权。
- [Google：Transitioning Gemini CLI to Antigravity CLI](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/) — 2026-05-19 产品迁移、统一 Harness 与企业例外。

### CLI、MCP 与 Agent Workflow 组合

- [Cloudsmith：Manage your supply chain using MCP](https://cloudsmith.com/changelog/manage-your-supply-chain-using-natural-language-with-mcp) — 2026-03-02 CLI 内置本地 MCP Server。
- [GitHub：Creating Agentic Workflows](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows) — public preview、`gh aw` author/compile/run。
- [GitHub：gh-aw compilation process](https://github.github.com/gh-aw/reference/compilation-process/) — Schema validation、lock、SHA pin 和 JSON validation output。
- [GitHub：Agentic Workflows Security Architecture](https://github.github.com/gh-aw/introduction/architecture/) — 分层隔离、MCP Gateway、Safe Outputs 和编译期安全。
- [MCP：Architecture overview](https://modelcontextprotocol.io/docs/learn/architecture) — lifecycle、capability negotiation、Tools/Resources/Prompts、Sampling/Elicitation/Notifications。
- [MCP 2025-11-25：Transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports) — stdio、Streamable HTTP、session 和协议版本。
- [MCP 2025-11-25：Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) — optional authorization、HTTP OAuth、stdio 环境凭据。
- [MCP 2025-11-25：Schema Reference](https://modelcontextprotocol.io/specification/2025-11-25/schema) — Tool input/output Schema、annotations、内容类型与 capabilities。
- [MCP 2025-11-25：Tasks](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/tasks) — experimental durable task、poll/cancel/status。

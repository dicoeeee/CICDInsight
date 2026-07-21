---
title: MCP 在 Agentic CI/CD 中的能力、边界与趋势
aliases:
  - MCP 深度研究报告
tags:
  - research/agentic-cicd
  - report
  - tool/mcp
status: complete
as_of: 2026-07-15
confidence: high
---

# MCP 在 Agentic CI/CD 中的能力、边界与趋势

## 执行摘要

MCP 解决的是 Agent Host 与外部能力之间的互操作：统一发现和调用 Tool、读取 Resource、使用 Prompt，并在客户端与 Server 之间协商能力。它降低了每个 Agent 产品分别适配每个服务的 N×M 成本，也为远程授权、Registry、Allowlist 和集中运营提供共同协议面。

MCP 不执行 CI/CD 业务本身。一个 Server 最终仍要调用 API、SDK、CLI 或应用逻辑；MCP 的 OAuth 也不能替代仓库、制品、环境和动作级授权；Tool 返回成功更不能替代 Test、Scan、Policy、Signature、SLO 与回滚。企业采用的正确问题不是“是否支持 MCP”，而是“哪些能力值得跨多少客户端复用、在哪个信任域运行、由什么身份调用、结果由谁验证”。

截至 2026-07-15，生产判断应基于 2025-11-25 正式规范。官方已经发布 2026-07-28 Release Candidate，方向是无状态协议核心、标准 HTTP 基础设施、扩展机制和更强的生命周期治理；但最终发布日期仍在观察日之后，不能把候选内容写成当前稳定事实。

## 一、当前稳定规范的能力模型

### 1. Host—Client—Server

[当前架构](https://modelcontextprotocol.io/specification/2025-11-25/architecture) 中，Host 管理用户交互、同意、安全策略和上下文聚合；Host 为每个 Server 建立一个 MCP Client；Client 与 Server 维持有状态会话，通过 JSON-RPC 和能力协商通信。这个分工意味着安全决策不能只落在 Server，Host 也必须控制哪些上下文和动作对模型开放。

### 2. 三类 Server Primitive

| Primitive | 主要用途 | 控制倾向 | CI/CD 例子 |
|---|---|---|---|
| [Tools](https://modelcontextprotocol.io/specification/2025-11-25/server/tools) | 执行查询或动作，输入/输出可带 JSON Schema | 模型可选择调用，客户端应允许人拒绝 | 查询 Pipeline、触发测试、创建 PR、生成 Plan |
| [Resources](https://modelcontextprotocol.io/specification/2025-11-25/server/resources) | 暴露可读取的上下文与内容 | 应用控制 | 构建日志、制品元数据、Runbook、环境拓扑 |
| [Prompts](https://modelcontextprotocol.io/specification/2025-11-25/server/prompts) | 暴露可复用的交互模板 | 用户控制 | 发布就绪评审、事故调查模板 |

客户端侧还可支持 Roots、Sampling 和 Elicitation：Server 可以获知允许访问的根目录、请求 Host 使用模型生成内容或向用户收集结构化信息。它们扩大了 Server 的能力，也扩大了信任边界和上下文外发风险。

### 3. Transport

[2025-11-25 Transport](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports) 定义 stdio 和 Streamable HTTP。stdio 适合本地子进程；Streamable HTTP 支持远程服务并取代旧 HTTP+SSE。远程部署必须校验 Origin，避免 DNS Rebinding，并为本地服务优先绑定 localhost、为共享服务增加认证。

### 4. Authorization

[Authorization 规范](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) 面向 HTTP 传输，建立在 OAuth 2.1、Protected Resource Metadata 和 Resource Indicators 等机制上；stdio 通常从环境获取凭据。规范禁止 Token Passthrough，并要求 Token 绑定目标 Resource Audience。

这解决“客户端怎样获得访问 Server 的 Token”，但不自动解决“该 Agent 是否可部署到生产”。Server 仍需按照仓库、组织、环境、制品和命令做对象级策略，并记录人类委托链。

企业身份仍存在重要状态差异：面向企业集中 SSO、开通/回收与审计的 Enterprise-Managed Authorization Extension 已于 [2026-06 宣布 Stable](https://blog.modelcontextprotocol.io/posts/enterprise-managed-auth/)；面向无人 Pipeline/Daemon 的 [OAuth Client Credentials Extension](https://modelcontextprotocol.io/extensions/auth/oauth-client-credentials) 截至观察日仍是 Draft。也就是说，交互式企业 Agent 的统一授权正在成熟，但无人 CI/CD Workload Identity 仍不能假定已形成稳定、普遍兼容的 MCP 标准。

### 5. 长任务

[Tasks](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/tasks) 在当前规范中仍标记为 Experimental。对构建、扫描和部署这类长任务，不应假定所有客户端都具备一致的恢复、取消和进度语义。

### 6. Extensions 与 Conformance

[MCP Apps](https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/) 已成为稳定 Extension，可让 Tool 返回在沙箱 Iframe 中呈现的交互 UI；Enterprise-Managed Authorization 也是独立扩展。扩展只有在 Host 与 Server 都支持相应版本时才生效，因此企业需要维护“协议 × Extension × Host × Server × SDK”兼容矩阵。

2026 年出现的 [Conformance Framework](https://github.com/modelcontextprotocol/conformance) 和 [SDK Tiering](https://modelcontextprotocol.io/community/sdk-tiers) 提高了协议实现一致性，但只证明消息和规范行为，不证明 Tool 业务正确、Server 无恶意代码、OAuth 配置安全或 CI/CD 任务成功。

## 二、MCP 的真实价值

### 降低跨客户端适配成本

同一 Server 可被不同 Agent Host 发现，Tool Schema 和结果结构可复用。对 GitHub、制品仓、云平台、可观测系统等共享服务，这比每个 Harness 分别维护 CLI 解析和 OAuth 流程更可运营。

### 独立升级与远程运营

Remote Server 可以集中更新、修复和撤回能力，客户端不必在每个开发环境安装完整依赖。[GitHub Remote MCP Server](https://github.blog/changelog/2025-09-04-remote-github-mcp-server-is-now-generally-available/) 于 2025-09 GA，采用 OAuth 2.1+PKCE、短期 Token 和集中策略，是远程模式成熟化的代表。

### 同时连接行动与上下文

Tools、Resources 和 Prompts 使 Server 不只暴露 RPC，也能提供日志、文档、模板和更新通知。这是 MCP 相对“只调用一个命令”更完整的地方。

### 形成治理挂点

Registry、Allowlist、Gateway 和 Toolset 配置使平台团队能统一回答：Server 来自哪里、谁拥有、哪些 Agent 可见、如何升级/撤回、哪些 Tool 对哪些任务开放。协议不完成治理，但提供共同控制面。

## 三、Anthropic MCP 渐进式加载：Tool Search 与 Progressive Disclosure

> [!important] 协议边界
> “MCP 渐进式加载”不是 MCP 2025-11-25 核心规范中的标准字段。当前规范通过 `tools/list` 返回完整 Tool 定义，并支持分页和 `list_changed`；其中没有 `defer_loading` 或 `tool_reference`。Anthropic 的 Tool Search 是构建在 MCP Tool Catalog 之上的 Claude API/Claude Code 上层实现。MCP 是开放标准，不宜把这项实现称为“Anthropic MCP 标准协议”。

### 1. 它解决什么问题

传统 MCP Client 通常把所有 Tool Name、Description 和 JSON Schema 一次性放入模型上下文。随着 Server 和 Tool 增加，会同时产生：

- **上下文膨胀：** Tool 定义在任务开始前就占用大量输入 Token；
- **选择退化：** 名称和参数相似的 Tool 增加误选和错误参数；
- **缓存抖动：** Tool 列表变化可能破坏稳定的 System Prompt 前缀；
- **权限错觉：** 为了让 Tool “可见”而把过大的能力面同时暴露给模型。

Anthropic 的 [Advanced Tool Use](https://www.anthropic.com/engineering/advanced-tool-use) 以五个 Server、58 个 Tool 为例，估算定义约占 55K Token；其内部测试称 Tool Search 通常可减少 85% 以上的 Tool 定义 Token，并改善大 Tool Library 上的选择准确率。它是厂商自报的内部结果，证明方向和机制，不应外推为所有模型、Server 或 CI/CD 任务的固定收益。

### 2. Tool Search 的工作机制

```mermaid
flowchart LR
  CAT["完整 Tool Catalog\nName · Description · Schema"] --> IDX["Regex / BM25 / Custom Search"]
  BASE["初始上下文\nTool Search + 3—5 个常用 Tool"] --> Q["模型生成搜索 Query"]
  Q --> IDX
  IDX --> REF["最多 5 个 tool_reference"]
  REF --> EXP["API 展开完整 Tool Definition"]
  EXP --> CALL["模型选择并调用 Tool"]
  POL["Allowlist · Identity · Approval"] -.独立约束.-> CALL
```

[Claude API Tool Search](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) 的具体流程是：

1. 请求中加入 Regex 或 BM25 Tool Search；
2. 将低频 Tool 标记为 `defer_loading: true`，至少保留 Tool Search 和少数高频 Tool 非延迟加载；
3. 初始模型上下文只包含搜索工具和非延迟 Tool；
4. 模型发起 `server_tool_use` 搜索，Anthropic 服务返回最多 5 个 `tool_reference`；
5. API 将引用自动展开成完整 Tool 定义，再让模型生成常规 `tool_use`；
6. Deferred Tool 在 System Prompt Cache Prefix 之外以内联方式加入，因此不破坏既有 Prompt Cache。

Regex 版本使用正则模式，BM25 版本使用自然语言；也可以用 Embedding 等方式实现自定义搜索，并以 `tool_result` 返回 `tool_reference`。Tool Search 当前在 Claude API 为 GA；Tool 搜索本身不作为独立 Server Tool 计费，但真正展开的 Tool Definition 仍按输入 Token 计入。

Anthropic 官方建议在 Tool 达到约 10 个、定义超过约 10K Token，或 Catalog 会继续增长/连接多个 MCP Server 时优先考虑搜索；小于 10 个且每个都高频的小 Toolset 通常没有必要。一个请求最多可标记 10,000 个 Deferred Tool，常用的 3—5 个 Tool 应保持前置加载。检索索引会使用 Tool Name、Description、参数名和参数说明，因此 Description 已从“帮助文本”变成召回质量与供应链安全的一部分。

### 3. MCP Connector 与 Claude Code 的两种接入方式

| 实现面 | 配置方式 | 当前状态与行为 | 关键限制 |
|---|---|---|---|
| Claude API 普通 Tool | 每个定义设置 `defer_loading: true` | Tool Search GA；请求仍携带所有完整定义，只有模型上下文按需展开 | 降低 Context，不降低完整请求 Payload；增加一次搜索延迟 |
| Anthropic MCP Connector | `mcp_toolset.default_config.defer_loading`，可由 `configs` 单 Tool 覆盖 | Connector 仍使用 Beta Header；可整 Server 延迟、保留少数 Tool 常驻 | `enabled` 与 `defer_loading` 是两件事；当前 Connector 只连接 MCP Tools，不含 Resources/Prompts |
| Claude Code | 默认启用 `ToolSearch`，只先加载 Tool Name 与 Server Instructions | MCP Tool Schema 按需进入上下文；可用 `ENABLE_TOOL_SEARCH` 调整 | 依赖模型和代理是否支持 `tool_reference`，非第一方 Base URL 等环境可能回退 |
| Code Execution + MCP | 把 Tool 暴露为文件/代码 API，或增加 `search_tools` | 通过文件浏览/搜索按需读取定义，并在执行环境过滤中间结果 | 是 Anthropic 推荐架构模式，不是 MCP Core Protocol |

[Anthropic MCP Connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) 将 `enabled` 和 `defer_loading` 分开：前者决定 Tool 是否可用，后者只决定何时进入模型上下文。企业不能用 `defer_loading` 代替 Allowlist；当前 Connector 也只支持 MCP Tool 调用，不应把它写成对 Resources、Prompts 或整个 MCP 能力面的渐进加载。

[Claude Code MCP 文档](https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search) 当前提供四种策略：默认全部按需加载；`true` 强制全部延迟；`auto` 在 Tool Schema 不超过 Context Window 10% 时前置加载、超出部分延迟；`auto:N` 自定义阈值；`false` 完全关闭。小而高频的 Server 可设置 `alwaysLoad: true`。Server Instructions 和 Tool Description 各会被截断到 2KB，因此关键检索词和适用任务必须放在开头。

### 4. 它没有解决什么

| 未解决问题 | 原因 | 仍需控制 |
|---|---|---|
| Tool 授权 | Deferred Tool 被检索后仍可调用 | `enabled=false`、Allowlist、任务身份、对象级 Policy |
| 高风险审批 | 搜索只改变可见时机 | PR/Plan/Approval、职责分离 |
| Tool/Description 投毒 | 搜索排序依赖名称、描述和参数 | 来源准入、命名空间、元数据审查、恶意检索测试 |
| Tool Result 膨胀 | 延迟加载只优化 Definition | 分页、Resource Link、结果裁剪、Code Execution、输出上限 |
| Schema/行为漂移 | Catalog 与远程 Server 可动态变化 | 版本/Hash、`list_changed`、缓存 TTL、Contract Test |
| 端到端成功 | 找到正确 Tool 不等于执行正确 | Test、Policy、Artifact、Signature、SLO Oracle |

[Anthropic 的 Code Execution with MCP](https://www.anthropic.com/engineering/code-execution-with-mcp) 将“按需发现 Tool Definition”和“在执行环境过滤中间结果”视为两个连续优化。只做 Tool Search，仍可能把 10MB 日志、完整 SBOM 或大规模测试结果灌入模型上下文；CI/CD 还需结果分页、字段投影、聚合和持久化制品。

### 5. 对 Agentic CI/CD 的四层 Tool 暴露模型

| 层级 | 典型 Tool | 加载/授权策略 |
|---|---|---|
| Tier 0 常驻发现层 | `get_change_context`、`get_pipeline_status`、`search_tools`、只读目录 | 3—5 个高频、低风险 Tool 始终可见 |
| Tier 1 按需能力层 | Lint、Test、Build Log、SBOM、Artifact、Telemetry 查询 | `defer_loading=true`，按 Stage/Service/Verb 命名和检索 |
| Tier 2 受控行动层 | Create PR、Retry Build、Upload Candidate、Apply Nonprod | 任务身份 + Allowlist；可延迟加载，但仍需逐动作 Policy |
| Tier 3 高风险层 | Production Deploy、Promote/Sign/Delete、改 Gate、恢复动作 | 默认 `enabled=false`；满足批准和环境条件后才临时启用，不能只靠延迟加载 |

该模型的关键是把**上下文平面**和**权限平面**分开：渐进式加载决定模型何时看到 Schema，Policy 决定调用是否存在和能否执行。隐藏危险 Tool 只能减少误触机会，不能形成不可绕过的安全边界。

### 6. 与 MCP 规范演进的关系

当前稳定规范的 `tools/list`/分页是 Catalog 获取机制，不提供语义检索。2026-07-28 RC 新增的 `server/discover` 用于按需获取 Server Capability，`ttlMs`/`cacheScope` 用于缓存 List/Resource 结果；它们改善协议级发现和缓存，但截至观察日仍是 RC，也不等同于 Anthropic 的 Regex/BM25 Tool Search 或 `tool_reference` 上下文展开。

## 四、MCP 不能提供的能力

| 非能力 | 仍需什么 |
|---|---|
| 真实业务实现 | API、SDK、CLI、数据库或应用后端 |
| Agent 推理与规划 | Harness、模型、上下文管理和循环 |
| 业务对象授权 | 服务端 Policy、仓库/环境/制品 Scope、委托链 |
| 代码和命令隔离 | Runner、Sandbox、网络与文件系统控制 |
| 成功判定 | Test、Scan、Policy、Signature、SLO 等外部 Oracle |
| 高风险批准 | PR、Plan、Change、Runbook 与职责分离 |
| 端到端审计 | Host、Gateway、Server、执行层和业务系统联合事件 |

因此“Tool 可调用”既不等于“动作被授权”，也不等于“动作成功”。

## 五、与 CLI 的替代边界

### MCP 可以替代的部分

- 每个 Agent 客户端分别解析 `--help`、JSON 和错误；
- 为同一远程服务分别实现登录、发现和连接；
- 用长 Prompt 手工描述工具参数；
- 在客户端内硬编码共享服务 Tool Catalog。

### MCP 通常不能替代的部分

- Server 背后的真实 CLI/API/SDK 和业务逻辑；
- 本地二进制的版本锁定、进程隔离和产物验证；
- 无 MCP 客户端环境中的人工调试与 CI 脚本；
- 高风险操作的业务审批、签名和回滚。

### 何时不值得引入 MCP

如果只有一个 Harness、工具只在 Runner 内运行、现有 CLI 机器契约稳定、身份可由环境安全注入，且无需 Resource/Prompt/远程多租户/集中目录，直接 CLI 更简单。Thoughtworks 2026-04 的 [“MCP by default” Caution](https://www.thoughtworks.com/en-us/radar/techniques/mcp-by-default) 也指出协议会带来抽象成本和能力保真损失；这是架构经验，不是对 MCP 本身的否定。

## 六、MCP 在八个 CI/CD 阶段的用法

| 阶段 | 推荐 Tool/Resource | 风险控制 |
|---|---|---|
| 代码评审 | Diff/PR Resource，评论和 Draft PR Tool | 仓库 Scope、禁止自动合并 |
| 静态与安全 | Findings Resource，修复/复验 Tool | Toolset 与例外审批分离 |
| 测试与门禁 | 日志、测试历史、运行/取消 Tool | Server 不得修改 Gate 后自证成功 |
| 构建出包 | Build Resource，重跑与产物查询 | Runner 身份、超时、成本与网络 |
| 制品与版本 | SBOM/签名 Resource，候选包 Tool | 上传、签名、晋级分权 |
| IaC 与部署 | Plan Resource，Validate/Apply Tool | Plan 哈希、环境 Scope、逐次批准 |
| 发布与审批 | Evidence Resource，Release Plan Tool | 批准在 MCP 外部强制执行 |
| 发布后 | Telemetry/Topology Resource，Runbook Tool | 只读优先、低风险预批准动作 |

Resources 特别适合持续提供构建日志、制品和拓扑，Tools 适合明确动作；不要把所有数据访问都建成 Tool，也不要把所有高风险动作都暴露给默认 Toolset。

## 七、2025H2—2026 的业界趋势

### 趋势 1：从本地连接走向远程服务

GitHub Remote MCP GA、OAuth 与企业策略说明市场正在把 MCP 从个人开发环境推向共享服务。Google 也在 2025-12 发布 [Fully-managed Remote MCP Servers](https://cloud.google.com/blog/products/ai-machine-learning/announcing-official-mcp-support-for-google-services)，并与 Cloud IAM、Audit、Model Armor 和 Registry 组合。远程模式带来集中升级与治理，也带来多租户隔离、Audience、速率、SLO 和数据外发的新责任。

### 趋势 2：从 Server 数量竞争转向 Toolset 质量

GitHub 在 2025-12 增加 Tool-specific configuration，并在 2026-01 合并 Projects Tool；官方更新称后者将 Tool-list context 减少约 23k tokens、约 50%。[原始更新](https://github.blog/changelog/2026-01-28-github-mcp-server-new-projects-tools-oauth-scope-filtering-and-new-features/) 是厂商自报的特定结果，但足以证明 Tool 爆炸已成为真实工程问题。

Anthropic 的 Tool Search 和 Claude Code 默认渐进式加载进一步说明，Tool Catalog 正从“把所有 Schema 塞进 Prompt”转向“先加载能力地图，再检索 3—5 个相关 Tool”。下一阶段的竞争不只是 Server 数量，而是检索 Recall、Tool Description 质量、任务级 Toolset、缓存和权限过滤能否协同。

### 趋势 3：Registry 与 Agent Control Plane 上升

[官方 MCP Registry](https://github.com/modelcontextprotocol/registry) 仍处 Preview；GitHub 的 [internal registry/allowlist](https://github.blog/changelog/2025-11-18-internal-mcp-registry-and-allowlist-controls-for-vs-code-stable-in-public-preview/) 也显示企业需要批准目录。值得注意的是，本地 Server 如果只按名称匹配，仍可能被同名配置绕过；因此目录必须与签名、启动命令、部署位置和任务身份绑定。

### 趋势 4：生态开始收敛到 MCP，但不走“全 MCP”

GitHub 在 2025-09 [弃用 Copilot Extensions 的 GitHub Apps 路线](https://github.blog/changelog/2025-09-24-deprecate-github-copilot-extensions-github-apps/) 并引导 MCP；同时 Copilot CLI 又把 MCP、Skills、Plugins 放在同一产品内。这显示 MCP 成为主流互操作协议之一，但不是唯一扩展机制。

### 趋势 5：规范向无状态核心和扩展框架演进

[2026-07-28 RC](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) 提出取消协议核心握手/会话、使用普通 HTTP 基础设施和方法 Header 路由、增加一等扩展框架，并把 Apps/Tasks 作为扩展；同时拟弃用 Roots、Sampling、Logging，Tool Schema 对齐完整 JSON Schema 2020-12，并加强安全和生命周期规范。

这是 Breaking 方向，正式版本计划晚于观察日。企业当前应评估客户端/Server 是否把会话、握手和这些 Capability 写死，而不是提前宣称已完成迁移。

### 趋势 6：无人 CI/CD 身份落后于交互式 Agent

企业集中授权扩展已经 Stable，而 OAuth Client Credentials 仍为 Draft；当前 Stable Core 的 stdio 仍主要从环境获得凭据。这意味着在无人流水线中，OIDC/Workload Identity、短期 Token、委托链和 Tool Policy 仍需由云/IAM/Gateway 方案补齐，不能仅凭“支持 MCP Authorization”判断生产就绪。

## 八、安全与治理

[官方安全最佳实践](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices) 覆盖 Confused Deputy、Token Passthrough、OAuth Metadata SSRF、Session Hijacking、本地 Server 任意代码执行和 DNS Rebinding。把这些风险翻译为企业控制：

- 本地一键安装必须展示精确命令、来源和权限，并在沙箱运行；
- 远程 Server 验证 Origin/Audience，不接受非目标 Token；
- Host 不盲信 Tool Description、Annotations 或返回内容；
- Gateway 按任务裁剪 Toolset，记录 Schema 版本与调用 ID；
- Server 做输入/输出校验、访问控制、速率和超时；
- 高风险调用展示具体对象与影响，允许人拒绝；
- Registry 发现与企业批准分离，发现不等于信任。
- Tool Search 的检索可见性与 Tool 的执行授权分离；高风险 Tool 默认禁用，不能只设置 `defer_loading`；
- 对 Tool Name、Description、Server Instructions 做来源审查和搜索投毒测试，记录实际命中的 `tool_reference`。

## 九、推荐参考架构

```mermaid
flowchart LR
  H["Agent Host / Harness"] --> TS["Tool Search / Progressive Disclosure"]
  TS --> G["MCP Gateway"]
  G --> T["Task-scoped Enabled Toolset"]
  T --> S1["Remote MCP Server"]
  T --> S2["Local stdio Server"]
  S1 --> B["API / SDK / CLI / Backend"]
  S2 --> B
  I["Task Identity · OAuth · Policy"] --> G
  B --> O["Test · Policy · Signature · SLO"]
  O --> E["PR · Plan · Evidence · Approved Action"]
  G --> A["Audit · Evals · Cost"]
```

Gateway 不是强制组件，但当 Server 和 Agent 数量扩大后，它是实现身份映射、Tool 过滤、速率、审计和 Kill Switch 的自然位置。

## 十、企业落地顺序

1. 从一个高价值共享服务和两种 Agent 客户端开始，不以 Server 数量为目标；
2. 只开放读 Tool/Resource 和 Draft PR/Plan，建立任务成功与误调用基线；
3. 给每个 Server 建 Owner、版本、风险、数据、权限、SLO、签名和撤回记录；
4. 按 Scenario 动态裁剪 Enabled Toolset，再对低风险长尾 Tool 做渐进式加载；测量检索 Recall@5、Token、选择错误、延迟和授权面；
5. 引入任务身份和对象级 Policy，再试非生产写动作；
6. 将 Test、Scan、Policy、Signature 和审批留在 MCP 外部强制执行；
7. 正式 2026-07-28 规范发布后运行兼容测试，再决定迁移时间。

## 十一、最终判断

MCP 已经是重要的 Agent 工具互操作层，尤其适合远程共享、跨客户端和集中治理。Anthropic Tool Search 证明大 Tool Library 可以通过渐进式披露降低上下文税，但它是 Host/API 层实现，不是 MCP Core，也不是权限边界。企业最应投资的是可检索且小而清晰的 Tool Catalog、任务级 Enabled Toolset、任务身份、对象授权、Server 供应链、外部 Oracle 和可撤回运营，而不是连接最多的 Server。

CLI 与 MCP 的逐项选择见 [[50_deepdives/cli-vs-mcp-decision-guide|决策指南]]。

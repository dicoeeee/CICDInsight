---
title: "MCP 演进与渐进式工具发现：CLI 对比洞察的边界证据"
topic: cli-agent-interface
type: industry-evidence-refresh
as_of: 2026-07-28
accessed_at: 2026-07-28
status: evidence-ready-with-gaps
confidence: high
source_policy: "官方产品文档、官方协议/仓库；个人观点单独标识"
---

# MCP 演进与渐进式工具发现：CLI 对比洞察的边界证据

## 结论先行

**“不要默认使用 MCP”是对集成选择的谨慎原则，不是“放弃 MCP”的行业事实。** 截至 2026-07-28，主流 Agent 客户端已把“减少工具定义常驻上下文、按需发现工具”作为产品能力推进；MCP 官方项目同时在推进可扩展性、任务、企业鉴权、注册表和可观测性。正确判断是：**MCP 保留为互操作与治理层，但不应无差别地将每个 API / CLI 包装成常驻 MCP 工具。**

## 1. Claude Code：Tool Search 是默认的客户端渐进发现机制

### 已核验事实

Claude Code 官方 MCP 文档规定：

- **默认开启。** 会话开始时仅加载 MCP 工具名称和 server instructions，工具定义延后到 Claude 需要时再加载；只有实际使用的工具进入上下文。[Claude Code MCP：Tool search](https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search)
- **默认值为全量延后。** `ENABLE_TOOL_SEARCH` 未设置时，所有 MCP tools 延后；`auto` 模式则只在工具 schema 占用不超过上下文窗口 **10%** 时预先加载，`auto:N` 可自定义为 0–100%。[官方配置说明](https://code.claude.com/docs/en/mcp#configure-tool-search)
- **存在明确前提和回退。** Tool Search 需要支持 `tool_reference` blocks 的模型；当 `ANTHROPIC_BASE_URL` 指向非第一方 host 或在 Google Cloud Agent Platform 时，默认回退为预加载，除非显式覆盖。设置 `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 会使它保持关闭。[官方配置说明](https://code.claude.com/docs/en/mcp#configure-tool-search)
- **可以按 server 覆盖。** `alwaysLoad: true` 可让某个 server 的所有工具在会话开始时可见；官方建议只给每回合都需要的小型工具集使用。[官方说明](https://code.claude.com/docs/en/mcp#exempt-a-server-from-deferral)
- **服务端仍需优化描述。** 服务器说明帮助 Agent 判断何时搜索工具；每个工具描述及 server instruction 在 Claude Code 中会截断到 2KB。[官方给 Server 作者的建议](https://code.claude.com/docs/en/mcp#for-mcp-server-authors)

### 定位边界

这是 **Claude Code 客户端 / Agent harness 的工具选择机制**，不是 MCP 基础协议对所有 client / server 的强制要求。证据是它由 Claude Code 的环境变量、模型能力与 beta header 控制；MCP server 提供 tools/list 和 instructions，但是否延后将 schema 交给模型由客户端决定。[Claude Code 文档](https://code.claude.com/docs/en/mcp#configure-tool-search)

## 2. 其他终端 Agent：同一问题，机制成熟度不同

| 产品 | 已核验的机制 | 当前结论与边界 |
|---|---|---|
| **GitHub Copilot CLI** | 官方 Tool Search 默认开启；工具少于约 **30** 个时直接预加载。超过阈值且模型支持时，先保留内置工具，MCP 和其他外部工具按需搜索、加载，首次加载后留在本会话。`deferTools: "never"` 可令 MCP server 常驻。支持 Claude Sonnet / Opus 4.0+、指定 preview 模型与 GPT-5.4+；其他模型全量预加载。[官方 Tool Search](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/tool-search) | **已产品化的客户端渐进发现。** 这是 Copilot CLI 行为，非 MCP 协议强制。 |
| **Google Gemini CLI** | 官方文档说明连接 configured servers 时，`discoverMcpTools()` 建立连接、取得 tool definitions、校验 schema 并注册进 global Tool Registry；当前文档未描述 deferred loading / tool search 阈值。[Gemini MCP 官方仓库文档](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md) | **可动态发现 / 注册，但本轮未核验到按需加载。** 不应把“未在文档中找到”写成“不支持”。 |
| **OpenAI Codex** | 公开 CLI 文档证实 `codex mcp` 管理 MCP Server，但未提供已发布的 MCP Tool Search 产品说明。[Codex MCP](https://learn.chatgpt.com/docs/extend/mcp)；官方仓库 features registry 将旧 `tool_search` 标为 `Removed`，将 `tool_search_always_defer_mcp_tools` 标为 `UnderDevelopment`。[官方源码](https://github.com/openai/codex/blob/main/codex-rs/features/src/lib.rs) | **证据缺口 / 功能演进中。** 不应在页面中称 Codex 已有与 Claude/Copilot 等价的稳定 Tool Search。 |

### 可用于 PPT 的事实判断

渐进发现不是 CLI 的专属优势，也不是 MCP 的固有缺陷；它是 **Agent 客户端管理工具上下文的设计问题**。Claude Code 和 Copilot CLI 均已通过“按需加载 MCP tools”应对工具定义膨胀，Gemini 的官方实现已具备动态发现 / 注册，但未在本轮证实 deferred loading。

## 3. MCP 生态仍在积极解决规模化互操作问题

### 事实：协议与实现并未停滞

| 方向 | 已核验进展 | 状态 / 时间 |
|---|---|---|
| **工具与能力发现** | 当前协议有分页 `tools/list` 与 `notifications/tools/list_changed`；官方 Client Best Practices 建议 Host 将完整 schema 延迟到模型需要时再通过轻量 `search_tools` meta-tool 注入，并可按需连接 / 断开 server。[Tools spec](https://modelcontextprotocol.io/specification/2025-11-25/server/tools)、[Client Best Practices](https://modelcontextprotocol.io/docs/develop/clients/client-best-practices)；2026-07-28 **draft** 进一步引入 `server/discover`、`ttlMs` 与 `cacheScope`。[draft changelog](https://modelcontextprotocol.io/specification/draft/changelog) | 按需加载是 Host / Client 设计建议，不是基础协议的 `tools/search` RPC。2026-07-28 仍为 draft / RC，不能写为已 GA。 |
| **长任务** | 当前 2025-11-25 核心规范把 Tasks 标为 experimental，用于耐久状态、轮询和延迟结果；2026-07-28 draft 将其移至 `io.modelcontextprotocol/tasks` extension，使用 task handle 与 `tasks/get` / `tasks/update` / `tasks/cancel`。[2025 Tasks](https://modelcontextprotocol.io/specification/2025-11-25/basic/utilities/tasks)、[Tasks extension draft](https://tasks.extensions.modelcontextprotocol.io/specification/draft/tasks) | 演进中：SEP-2663 为 Final，但 extension spec 仍标为 Draft；官方 draft changelog 与 extension spec 对 capability opt-in 存在未完全一致的表述。 |
| **鉴权与企业治理** | MCP 的 HTTP 授权以 OAuth 为基础，要求 client 能通过 protected-resource metadata 发现授权服务器，并支持 client metadata 与动态注册等模式。[官方 Authorization spec](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)；Enterprise-Managed Authorization (EMA) extension 于 2026-06-18 标为 stable，并说明 Anthropic、Microsoft、Okta 与多家 server 正在采用。[官方 EMA 公告](https://blog.modelcontextprotocol.io/posts/enterprise-managed-auth/) | EMA：**stable**；基础 OAuth：协议可选，stdio 仍从环境取凭证。 |
| **注册表与供应链** | 官方 MCP Registry 是公开 Server 的集中元数据仓库与 REST API；官方 Registry 仓库在 2026-07-13 发布 v1.8.0，表明发布与运行基础设施仍持续演进。[Registry about](https://modelcontextprotocol.io/registry/about)、[Registry releases](https://github.com/modelcontextprotocol/registry/releases) | Registry 仍处 **Preview**，官方提醒可能有 breaking changes / 数据重置，并建议 Host 经由下游聚合器或 marketplace 而非直接消费官方 Registry；不代表注册的每个第三方 server 都已安全或适合企业使用。 |
| **扩展与生命周期治理** | RC 引入独立扩展框架、feature lifecycle 与 conformance / SDK tier 机制，使功能可先以 opt-in extension 演进再稳定，而非持续扩大基础协议。[官方规格候选说明](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) | 仍在规范与 SDK 适配阶段。 |

### 分析推断

“工具定义成本、鉴权摩擦、长任务和服务器分发”恰恰是生态正在工程化解决的问题。因此不能由早期 MCP 的上下文膨胀，推导出“业界放弃 MCP”。更严谨的表述是：**MCP 正从简单本地工具接线，转向面向远程、企业和多客户端互操作的协议；其复杂度也随之要求更强的发现、权限、生命周期与治理能力。**

## 4. Thoughtworks 与 Simon Willison：应被视为架构观点，不是协议退场信号

### Thoughtworks 的组织观点（非协议或厂商事实）

Thoughtworks 在 2026-04-15 的 Technology Radar 将“**MCP by default**”放入 Caution。它的结论是：不应在有更简单替代方案时，把 MCP 无条件设为 Agent 与外部系统之间的默认集成层；良好的 CLI（`--help`、结构化 JSON、可预测错误）在一些场景已足够。[Thoughtworks 原文](https://www.thoughtworks.com/radar/techniques/mcp-by-default)

但同一原文明确承认 MCP 的价值是 **structured tool contracts、OAuth authentication boundaries、governed multi-tenant access**，并写明“**This isn't a rejection of MCP**”。所以其 Caution 的对象是“默认决策”，不是协议本身。[Thoughtworks 原文](https://www.thoughtworks.com/radar/techniques/mcp-by-default)

### Simon Willison 的个人观点（非厂商事实）

Simon Willison 在 2025-10-16 的个人博客提出：MCP 的突出问题是 tool definitions 的 token 成本；在他所关注的 coding-agent 场景中，许多工作可以改由 CLI 完成，因为模型可按需运行 `cli-tool --help`。这是作者基于其使用观察的主张，不是 MCP 规范、厂商承诺或普适性能结论。[原文](https://simonwillison.net/2025/Oct/16/claude-skills/)

### 如何与产品演进同时成立

二者并不矛盾：

1. **观点层**：先选最小且足够的集成面；单机工程动作、已有高质量 CLI、能力无需跨客户端共享时，CLI / script / skill 往往更直接。
2. **产品层**：当 MCP 是合理选择时，客户端采用按需 Tool Search（Claude、Copilot）降低常驻 schema 成本；协议与生态以 capability discovery、缓存、EMA、Tasks、Registry 解决规模化互操作与治理。
3. **治理层**：无论 CLI 或 MCP，Agent 可调用都不等于已获授权；都需要最小权限、审批、策略、可观测性和外部测试 / policy oracle。

## 推荐的平衡型 PPT 主张

> **MCP 没有替代 CLI，也没有被业界放弃：主流 Agent 正用按需工具发现消化 MCP 的上下文成本；企业应按互操作与治理收益选择 MCP，而不是把它设为每项能力的默认封装。**

可配三条短论据：

1. **CLI 优先于直接执行。** 本地工程命令、脚本和已有工具链更直接，且能通过 `--help` 与 JSON 输出实现渐进理解。
2. **MCP 优先于跨系统互操作。** 面对远程服务、跨 Agent client 复用、OAuth / 企业身份和统一工具契约时，MCP 的协议收益更明显。
3. **渐进发现是收敛点。** Claude Code 默认按需加载 MCP tools；Copilot CLI 在约 30 个工具以上同样按需搜索。上下文膨胀是客户端治理问题，不是 MCP 必然结局。

## 证据缺口与状态边界

- **Claude Tool Search**：官方说明确认了默认、阈值与条件，但没有将其定义为 MCP 基础协议能力；页面的 `tool_reference` / beta header 依赖说明反而表明它属于 Claude client / API 实现。
- **Codex**：官方仓库显示有关延后 MCP tools 的功能仍处于演进状态；公开 CLI 文档不足以支撑“已稳定提供按需 MCP Tool Search”。
- **Gemini**：官方文档确认动态 discovery / registry，但本轮未找到其只在需要时把 MCP schema 送入模型的官方描述。
- **2026-07-28 协议版**：在访问时点，官方 versioning 仍将 2025-11-25 标为 current，2026-07-28 仅检索到 RC / draft；虽其计划发布日期正是 2026-07-28，仍避免把未独立核验的 final artifact 写成已全面落地。

## 来源与时间

| 来源 | 发布时间 / 状态 | 访问时间 | 用途 |
|---|---|---:|---|
| [Claude Code MCP](https://code.claude.com/docs/en/mcp) | 未标注 | 2026-07-28 | Tool Search 默认、阈值、模型 / proxy 条件 |
| [GitHub Copilot CLI Tool Search](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/tool-search) | 未标注 | 2026-07-28 | 约 30 tools 阈值、常驻 / 按需行为 |
| [Gemini CLI MCP 文档](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md) | 官方仓库主分支 | 2026-07-28 | eager discovery / Tool Registry |
| [OpenAI Codex MCP](https://learn.chatgpt.com/docs/extend/mcp) 与 [features 源码](https://github.com/openai/codex/blob/main/codex-rs/features/src/lib.rs) | 当前文档 / 主分支 | 2026-07-28 | Codex MCP 与 Tool Search 证据边界 |
| [MCP 2026-07-28 RC](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) | 2026-05-21，RC | 2026-07-28 | 发现、缓存、Tasks、授权与生命周期演进 |
| [MCP Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization) | 2025-11-25 spec | 2026-07-28 | OAuth / discovery 要求 |
| [MCP EMA](https://blog.modelcontextprotocol.io/posts/enterprise-managed-auth/) | 2026-06-18，stable | 2026-07-28 | 企业集中鉴权 |
| [MCP Registry releases](https://github.com/modelcontextprotocol/registry/releases) | v1.8.0：2026-07-13 | 2026-07-28 | 注册表活跃演进 |
| [Thoughtworks: MCP by default](https://www.thoughtworks.com/radar/techniques/mcp-by-default) | 2026-04-15，Caution | 2026-07-28 | 组织架构观点，不是协议事实 |
| [Simon Willison: Claude Skills](https://simonwillison.net/2025/Oct/16/claude-skills/) | 2025-10-16，个人博客 | 2026-07-28 | 个人观点，不是厂商事实 |

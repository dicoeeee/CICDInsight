---
title: "主流终端编码 Agent：CLI 与 MCP 的官方定位和组合方式"
topic: cli-agent-interface
type: industry-evidence-refresh
as_of: 2026-07-28
accessed_at: 2026-07-28
status: evidence-ready
confidence: high
source_policy: "仅官方文档、官方仓库和官方发布说明"
---

# 主流终端编码 Agent：CLI 与 MCP 的官方定位和组合方式

## 研究问题与边界

问题：主流终端编码 Agent 是否同时保留 shell/CLI 与 MCP？官方将 MCP 定位为替代 CLI，还是为 Agent 增加工具与上下文的扩展层？

样本：OpenAI Codex CLI、Anthropic Claude Code、GitHub Copilot CLI、Google Gemini CLI、OpenCode。所有链接均为产品方的官方文档或官方仓库；访问时间均为 **2026-07-28**。页面未公开发布日期时，标记为“未标注”，不根据页面抓取时间推断发布日期。

## 可用于页面的主结论

### 事实：五个样本均同时拥有本地执行能力与 MCP 客户端能力

| 产品 | Agent 直接执行 shell / CLI | MCP 客户端能力 | MCP Server 能力 | 产品状态（as of 2026-07-28） |
|---|---|---|---|---|
| OpenAI Codex CLI | 官方称其可在本地仓库中“inspect, edit, and run”代码；`--sandbox` 明确作用于“model-generated shell commands”。[CLI 概览](https://learn.chatgpt.com/docs/codex/cli)、[命令参考](https://learn.chatgpt.com/docs/developer-commands) | `codex mcp` 可添加、列出、移除、认证本地 stdio / 远程 HTTP Server。[MCP 文档](https://learn.chatgpt.com/docs/extend/mcp) | `codex mcp-server` 可将 Codex 自身作为 stdio MCP Server 供其他工具消费。[命令参考](https://learn.chatgpt.com/docs/developer-commands) | `codex`、`codex exec`、`codex mcp` 与 `codex mcp-server` 均标为 **Stable**。 |
| Anthropic Claude Code | 官方明确在 Windows 上通过 Bash 或 PowerShell 运行 shell commands；CLI 权限规则含 `Bash(...)`。[安装文档](https://code.claude.com/docs/en/getting-started)、[CLI 参考](https://code.claude.com/docs/en/cli-usage) | `claude mcp add` 支持本地 stdio、远程 HTTP、SSE、WebSocket；官方定义为连接外部 tools 和 data sources。[MCP 文档](https://code.claude.com/docs/en/mcp) | `claude mcp serve` 可使 Claude Code 作为 stdio MCP Server，并暴露其 View、Edit、LS 等工具。[MCP 文档](https://code.claude.com/docs/en/mcp) | 官方当前文档未给 MCP 标注 Preview / Beta；将其作为标准产品能力说明。 |
| GitHub Copilot CLI | CLI 权限模型把 `shell` 作为独立工具类型，并可限制到 `shell(git:*)` 等命令；hooks 也可运行 shell commands。[命令参考](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference)、[程序化参考](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-programmatic-reference) | `copilot mcp add` 支持本地 stdio 与远程 HTTP/SSE；内置 GitHub MCP Server，官方称 MCP Server 为给 CLI Agent 提供“additional tools”。[添加 MCP Server](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers) | 未核验到将 Copilot CLI 本身作为 **MCP Server** 的官方文档；`--acp` 是 Agent Client Protocol（ACP），不可与 MCP 混同。[命令参考](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference) | MCP 客户端为当前文档功能；仅 `/mcp search` / registry 安装明确标为 **experimental**。 |
| Google Gemini CLI | `run_shell_command` 是模型可直接调用的系统 shell；官方称其是除简单文件编辑外与环境交互的“primary mechanism”。[Shell 工具](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/shell.md) | MCP Server 以 stdio、SSE、Streamable HTTP 连接；发现工具的 schema 后注册到全局 Tool Registry。[MCP 文档](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md) | 未在本轮核验到 Gemini CLI 作为 **MCP Server** 的官方产品文档；不要据此写成“不支持”。 | 官方仓库主分支文档中的当前能力；MCP 没有标注 Preview / Beta。 |
| OpenCode | 内置 `bash` Tool 可运行 `npm install`、`git status` 或任意 shell command；默认与 MCP / custom tools 并列为工具集。[Tools](https://opencode.ai/docs/tools) | MCP 用于集成数据库、API 与第三方服务；支持本地 stdio、远程 Streamable HTTP 和 OAuth。[Tools](https://opencode.ai/docs/tools)、[MCP Servers](https://opencode.ai/v2/docs/mcp-servers) | 未在本轮核验到 OpenCode 将自身作为 **MCP Server** 的官方产品文档；不要据此写成“不支持”。 | 官方当前产品文档；MCP 能力未标注 Preview / Beta。其 LSP Tool 另行标为 experimental，不能外推到 MCP。 |

### 事实：官方用词是一致的“扩展 / 连接”，不是“替代”

- Codex 将 MCP 定义为“连接模型与工具、上下文”，用来让 Codex 访问第三方文档或开发工具；与此同时，CLI 页面仍把本地 inspect、edit、run 和 `codex exec` 自动化作为核心能力。[Codex MCP](https://learn.chatgpt.com/docs/extend/mcp)、[Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- Claude Code 写明 MCP Server 让它连接“external tools and data sources”；本地 stdio Server 特别适用于需要直接系统访问或自定义脚本的工具，而 Bash/PowerShell 仍是 Agent 的内置执行工具。[Claude MCP](https://code.claude.com/docs/en/mcp)、[Claude 安装](https://code.claude.com/docs/en/getting-started)
- Copilot CLI 将 MCP 描述为“extend Copilot's capabilities”，并将 shell 和 MCP Server 分列为不同、可独立授权的工具类别。[Copilot MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers)、[Copilot 权限](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-programmatic-reference)
- Gemini CLI 写明：它已有可读文件、执行命令、搜索 Web 的 tools，而 custom tools 或 MCP Servers 是对这一 Tool Registry 的扩展；MCP 的发现流程会把远端工具注册到同一 Tool Registry。[Gemini Tools](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/tools.md)、[Gemini MCP](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md)
- OpenCode 写明内置 tools 可以被 custom tools 或 MCP Servers 扩展；`bash` 是内置 Tool，MCP 用于外部服务集成。[OpenCode Tools](https://opencode.ai/docs/tools)

### 分析推断：主流架构不是“CLI vs MCP”，而是两条互补的 Agent 工具路径

上述五个样本的官方文档均同时保留：

1. **Shell / CLI 执行路径**：Agent 直接在工作目录和已有身份、依赖、工具链上执行命令，获得 stdout、stderr、退出码或文件变化。
2. **MCP 工具路径**：Agent 连接本地或远程 Server，先发现工具及参数 schema，再通过协议调用受控的工具、资源、prompt 或远程服务。

因此，以下判断是**基于样本的分析推断**，不是任何厂商的原话：

> 业界主流终端 Agent 正把 MCP 作为跨工具、跨服务的标准化接入层，而不是取代本地 CLI 的执行机制。CLI 是 Agent 可直接调用的成熟执行面；MCP 在需要能力发现、结构化参数、远程服务、OAuth / 统一治理时补足集成面。

本轮审阅的官方资料中，**未找到**将 MCP 明确表述为“替代 shell / CLI”的产品立场。此处只能表述为“在已核验样本中未见”，不能外推为全行业不存在该观点。

## 对比洞察：优势、局限与适用边界

| 维度 | CLI / Shell 直接调用 | MCP 调用 |
|---|---|---|
| 官方事实基础 | 五个样本都将 shell / bash / command execution 作为内置或明确可授权的 Agent 工具。 | 五个样本都将 MCP 用于连接或扩展外部 tools、data sources、APIs、数据库等。 |
| 对 Agent 的优势（分析） | 复用已有工具链与完整命令能力；天然适配本地工程上下文、管道、stdin/stdout、退出码和 CI 脚本；无需为每项能力新建 Server。 | 以统一的工具描述与参数 schema 支持发现和调用；适合远程 SaaS、数据库、浏览器、设计和协作系统；可结合 OAuth、Server allowlist、工具白名单和项目级配置治理。 |
| 对 Agent 的局限（分析） | 每个 CLI 的命令、参数、错误文本和输出格式可能不同；非结构化输出增加解析成本；权限、命令白名单与危险操作需要逐工具治理。 | 新增 Server 的启动、连接、鉴权、schema 兼容、超时和工具输出管理成本；实际暴露能力取决于 Server，而不等同于底层 CLI 的完整功能。 |
| 首选场景（分析） | 本地代码检查、构建、测试、扫描、制品操作、部署命令、git / package manager 等确定性工程动作；已有 CLI 且能提供 JSON / 机器可读输出时尤佳。 | 需要把远程系统的上下文和操作作为可发现工具接入 Agent，例如 Issue / PR、知识库、观测、数据库、SaaS 或浏览器；需跨 Agent 客户端复用同一工具契约时。 |
| 关键治理提醒 | CLI 可调用不等于获得授权；仍需 sandbox、命令策略、测试 / scan / policy 等外部控制。 | MCP 可调用也不等于获得授权；Server allowlist、OAuth scope、工具权限和返回数据边界同样需要治理。 |

## 可直接用于右下洞察区的表述

**标题句**

> 主流 Agent 并未用 MCP 替换 CLI：CLI 保留为直接执行面，MCP 成为可发现、可复用的外部工具接入层。

**三条支撑观点**

1. **不是“人用 CLI、Agent 用 MCP”。** Codex、Claude Code、Copilot CLI、Gemini CLI、OpenCode 都让 Agent 直接执行 shell / CLI；其中 Gemini 官方更将 shell 定义为 Agent 与环境交互的主要机制。
2. **MCP 的价值在互操作，而非覆盖全部执行。** 五家官方均使用“connect / extend”描述 MCP；它把远程服务、数据源与工具 schema 接入统一的 Agent 工具面。
3. **企业要双轨治理。** CLI 路径优先治理命令、环境和执行结果；MCP 路径优先治理 Server、OAuth、工具范围和数据边界。两者均需测试、扫描、策略或人工审批作为外部 Oracle。

## 证据边界

- “CLI 对 Agent 更适合”不是绝对结论：它在本地工程与完整工具链执行中通常更直接；跨 SaaS / 多客户端共享工具契约时，MCP 具有明显集成优势。
- 各产品的工具、传输、权限与成熟度会持续演进；本页只覆盖 2026-07-28 访问到的官方资料。
- 未找到某产品“作为 MCP Server”的官方资料，不等于该产品不存在该能力；仅表示本轮不把它写入正式结论。

## 来源清单

| 来源 | 发布 / 更新日期 | 访问日期 | 用途 |
|---|---:|---:|---|
| [OpenAI Codex CLI](https://learn.chatgpt.com/docs/codex/cli) | 未标注 | 2026-07-28 | Codex 本地 inspect/edit/run 与 automation 定位 |
| [OpenAI Codex MCP](https://learn.chatgpt.com/docs/extend/mcp) | 未标注 | 2026-07-28 | Codex MCP client、stdio / HTTP 与共享配置 |
| [OpenAI Codex developer commands](https://learn.chatgpt.com/docs/developer-commands) | 未标注 | 2026-07-28 | Stable maturity、shell sandbox、`codex mcp-server` |
| [Claude Code MCP](https://code.claude.com/docs/en/mcp) | 未标注 | 2026-07-28 | MCP client/server、transport、external tools / data sources |
| [Claude Code CLI reference](https://code.claude.com/docs/en/cli-usage) | 未标注 | 2026-07-28 | CLI 与 Bash / MCP 权限模型 |
| [GitHub Copilot CLI command reference](https://docs.github.com/en/copilot/reference/copilot-cli-reference/cli-command-reference) | 未标注 | 2026-07-28 | shell / MCP 工具与配置管理 |
| [GitHub Copilot CLI MCP](https://docs.github.com/en/enterprise-cloud@latest/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers) | 未标注 | 2026-07-28 | MCP 接入、内置 GitHub MCP、experimental 边界 |
| [Gemini CLI tools](https://github.com/google-gemini/gemini-cli/blob/main/docs/reference/tools.md) | 未标注 | 2026-07-28 | Tool Registry、shell 与 MCP 扩展关系 |
| [Gemini CLI shell tool](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/shell.md) | 未标注 | 2026-07-28 | shell 为主要环境交互机制 |
| [Gemini CLI MCP](https://github.com/google-gemini/gemini-cli/blob/main/docs/tools/mcp-server.md) | 未标注 | 2026-07-28 | MCP discovery / execution / transport |
| [OpenCode tools](https://opencode.ai/docs/tools) | 页面显示 published 2026-07-24 | 2026-07-28 | 内置 bash 与 MCP 扩展关系 |
| [OpenCode MCP servers](https://opencode.ai/v2/docs/mcp-servers) | 未标注 | 2026-07-28 | 本地 / 远程 Server、OAuth、timeout、CLI 管理 |

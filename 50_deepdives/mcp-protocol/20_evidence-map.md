---
title: MCP Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP Claim—Evidence—Gap Matrix

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| MCP-C01 | MCP 是 Host—Client—Server 的有状态 JSON-RPC 协议 | [2025-11-25 Architecture](https://modelcontextprotocol.io/specification/2025-11-25/architecture) | 2026-07-28 RC 拟转向无状态核心 | high |
| MCP-C02 | Server 可暴露 Tools、Resources、Prompts | [Tools](https://modelcontextprotocol.io/specification/2025-11-25/server/tools)、[Resources](https://modelcontextprotocol.io/specification/2025-11-25/server/resources)、[Prompts](https://modelcontextprotocol.io/specification/2025-11-25/server/prompts) | 客户端未必支持所有能力；业务语义仍由 Server 定义 | high |
| MCP-C03 | MCP 支持本地和远程两种主要连接模式 | [Transports](https://modelcontextprotocol.io/specification/2025-11-25/basic/transports) | 具体部署仍需要进程/HTTP 基础设施 | high |
| MCP-C04 | 远程 MCP 正形成标准授权与企业治理能力 | [Authorization](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)、[GitHub Remote MCP GA](https://github.blog/changelog/2025-09-04-remote-github-mcp-server-is-now-generally-available/) | 协议层授权不等于对象级业务授权 | high |
| MCP-C05 | Tool 爆炸产生显著上下文税 | [GitHub Projects Tool consolidation](https://github.blog/changelog/2026-01-28-github-mcp-server-new-projects-tools-oauth-scope-filtering-and-new-features/) 自报约减少 23k tokens / 50% Tool-list context | 厂商特定数据，不能直接外推所有 Server | medium-high |
| MCP-C06 | Registry/Allowlist 已成为企业重点，但成熟度仍不一致 | [Official Registry](https://github.com/modelcontextprotocol/registry)、[GitHub internal registry preview](https://github.blog/changelog/2025-11-18-internal-mcp-registry-and-allowlist-controls-for-vs-code-stable-in-public-preview/) | Registry 本身仍为 Preview；本地 name-only enforcement 较弱 | high |
| MCP-C07 | MCP 不应被默认用于所有集成 | [Thoughtworks “MCP by default”](https://www.thoughtworks.com/en-us/radar/techniques/mcp-by-default) | 属于行业观点，不是规范事实 | medium-high |
| MCP-C08 | 2026-07-28 RC 指向无状态、扩展化与企业 HTTP 基础设施 | [Official RC announcement](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) | 截至观察日尚非最终规范，可能调整 | high for direction, pending for final |
| MCP-C09 | MCP 的安全问题覆盖 OAuth、SSRF、会话、本地代码执行等多层 | [Security Best Practices](https://modelcontextprotocol.io/docs/tutorials/security/security_best_practices) | 最佳实践不能证明具体实现合规 | high |
| MCP-C10 | Anthropic Tool Search 可按需加载 MCP Tool Definition | [Claude Tool Search](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool)、[MCP Connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) | Tool Search GA，但 MCP Connector 仍 Beta；收益数据为 Anthropic 自报 | high for mechanism, medium for outcome |
| MCP-C11 | `defer_loading`/`tool_reference` 不是 MCP 2025-11-25 Core | [MCP Tools](https://modelcontextprotocol.io/specification/2025-11-25/server/tools) 仅定义 `tools/list`/分页/调用/变化通知；[Claude Tool Search](https://platform.claude.com/docs/en/agents-and-tools/tool-use/tool-search-tool) 定义 Anthropic 字段 | 后续 MCP Extension 可能出现相似能力，需要持续复核 | high |
| MCP-C12 | 渐进式加载减少 Context，但不等于禁用或授权 Tool | [MCP Toolset Configuration](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) 将 `enabled` 与 `defer_loading` 分离 | 隐藏 Tool 可能降低误触，但不是不可绕过控制 | high |
| MCP-C13 | Claude Code 默认对 MCP Tool 做按需搜索和加载 | [Claude Code MCP Tool Search](https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search) | 模型、Cloud 平台和非第一方代理存在支持/回退差异 | high |
| MCP-C14 | Anthropic MCP Connector 的渐进式加载当前只覆盖 MCP Tools | [MCP Connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector) 明确当前只支持 Tool Call | 不能外推到 Resources、Prompts 或整个 MCP Context | high |

## 证据缺口

- 缺少公开的跨厂商 MCP Server 生产 SLO、越权和事故数据；
- Tool 精简对真实任务成功率的影响没有统一基准；
- 缺少 Anthropic Tool Search、任务级 Gateway 过滤与全量预载在同一 CI/CD 任务集上的独立对照；
- 缺少 Tool Description/Search Poisoning 对检索排序与高风险误调用的公开基准；
- 2026-07-28 正式版本需在发布后重新核对 Breaking Changes 和迁移窗口。

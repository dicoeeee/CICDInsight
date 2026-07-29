---
artifact:
  - cli-software-delivery-agent-interface-insight-final.pptx
as_of: 2026-07-28
status: complete
---

# CLI 与 MCP 协同演进：图中主张与来源

## 页面主张

> CLI 与 MCP 正在协同演进，而非相互替代。不要过度采用 MCP，不等于否定 MCP；应以互操作与治理的增量收益是否覆盖新增复杂度作为选择标准。

## 事实与边界

- Claude Code 的 MCP Tool Search 默认开启；会话初始仅载入工具名称和 server instructions，完整工具定义按需进入上下文。该机制属于 Claude Code 客户端，不是 MCP 核心协议自动保证的能力。
  - https://code.claude.com/docs/en/mcp#scale-with-mcp-tool-search
- MCP 官方 Client Best Practices 建议 host 实现 progressive discovery，并与 programmatic tool calling 组合，以降低工具 schema 常驻上下文的成本。它是 host/client 设计建议，不是 MCP 核心的 `tools/search` RPC。
  - https://modelcontextprotocol.io/docs/develop/clients/client-best-practices
- Thoughtworks 将 “MCP by default” 放入 Caution，反对无条件把 MCP 作为默认集成层；原文同时明确这不是对 MCP 的否定，并承认其 structured contracts、OAuth boundaries 与 governed multi-tenant access 价值。
  - https://www.thoughtworks.com/radar/techniques/mcp-by-default

## 分析推断

- CLI 更适合作为本地工程动作和成熟工具的直接执行面。
- MCP 更适合作为远程系统、跨客户端复用和企业身份治理的标准接入面。
- Tool Search 与渐进式发现表明，两条路径正在围绕“按需理解和加载能力”收敛。

完整证据审计见：

- `50_deepdives/cli-agent-interface/industry-mcp-evolution-and-progressive-discovery-2026-07-28.md`

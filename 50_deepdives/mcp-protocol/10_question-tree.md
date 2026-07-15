---
title: MCP 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP 专题问题树

## Q1：MCP 当前提供什么

- Host、Client、Server 如何分工？
- Tools、Resources、Prompts 的控制方和数据模型是什么？
- Roots、Sampling、Elicitation 如何反向扩展 Server 能力？
- stdio 与 Streamable HTTP 如何选择？

## Q2：MCP 不提供什么

- 协议授权和业务对象授权如何区分？
- Server 返回成功是否能证明部署、扫描或恢复成功？
- Sandbox、Agent Identity、Policy、Approval、Audit 和 Oracle 分别由谁提供？

## Q3：MCP 与 CLI 能否互相替代

- 本地单客户端何时直接 CLI 足够？
- 多客户端、远程、多租户何时 MCP 明显获益？
- MCP 包装 CLI/API 后增加和损失了什么？

## Q4：企业扩展会遇到什么瓶颈

- Tool 数量如何影响上下文、选择和权限？
- Registry、Allowlist、Gateway、Owner 和撤回如何组合？
- 本地 Server 与远程 Server 的供应链和身份风险有何不同？

## Q5：2026 规范与市场向哪里发展

- GitHub 等厂商的 Remote MCP、Registry 和控制面说明了什么？
- 2026-07-28 RC 为什么转向无状态核心和扩展？
- “MCP by default” 的反思会怎样影响采用？

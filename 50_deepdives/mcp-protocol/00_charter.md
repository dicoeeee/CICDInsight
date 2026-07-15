---
title: MCP 专题研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP 专题研究边界

## 决策目标

回答 MCP 在 Agentic CI/CD 中解决了什么互操作与治理问题、哪些能力不能由 MCP 自身提供、何时应该建设本地/远程 Server，以及企业如何避免 Tool 爆炸、授权错位和供应链风险。

## 纳入范围

- MCP 架构、生命周期、能力协商和 JSON-RPC 消息；
- Tools、Resources、Prompts 及客户端的 Roots、Sampling、Elicitation；
- stdio、Streamable HTTP、Authorization、Registry 与安全；
- 2025H2—2026 的企业产品采用和即将到来的协议方向；
- CI/CD 查询、变更、验证和发布控制面中的适用方式。

## 非目标

- 不把 MCP 当作 Agent、Skill、CLI 或业务 API 的同义词；
- 不比较所有 SDK 的代码质量；
- 不把连接成功、Tool 可见或 OAuth 登录当作业务授权完成；
- 不把 2026-07-28 Release Candidate 当作已正式发布的稳定规范。

## 验收标准

- 准确区分当前稳定规范与候选规范；
- 给出协议能力、非能力、安全边界和替代条件；
- 覆盖本地 stdio、远程 HTTP、Gateway/Registry 三种架构；
- 对 CI/CD 八阶段给出 Toolset 与授权建议。

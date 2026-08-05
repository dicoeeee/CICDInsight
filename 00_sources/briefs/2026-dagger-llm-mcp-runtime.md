---
title: Dagger LLM Primitive、Tool Use 与 MCP
source_id: dagger-llm-mcp-runtime-2026
organization: Dagger
source_type: official-docs-blog-and-api
published: 2025-04-23
verified: 2026-07-28
availability: experimental
confidence: medium-high
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - build
tool_categories:
  - agent-runtime-orchestration
  - workflow-runtime
  - mcp
company_topics:
  - Dagger
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
  - tool/dagger
---

# Dagger LLM Primitive、Tool Use 与 MCP

## 来源

- [Agents in your Software Factory: Introducing the LLM Primitive in Dagger](https://dagger.io/blog/llm/)，2025-04-23。
- [LLM Integration](https://docs.dagger.io/features/llm/)，无发布日期，2026-07-28 访问。
- [LLM Provider Configuration](https://docs.dagger.io/reference/configuration/llm/)，无发布日期，2026-07-28 访问。
- [Next Query API](https://docs.dagger.io/next/extending/types/query/)，明确属于 Unreleased 文档，2026-07-28 访问。

## 一句话结论

Dagger 将 LLM 作为可调用 Dagger Functions 的原生类型，并能双向连接 MCP；这让 Agent 复用既有容器、缓存和 Trace，但没有自动产生最小权限、正确性或生产授权。

## 可核验事实

- `LLM` 可绑定 `Env`；Env 把 Directory、Container、Module、字符串和输出对象暴露给模型。
- 模型读取 Function 文档并选择 Tool，在循环中调用 Function、修改 Env 并产生输出。
- Dagger Module 可暴露为 MCP Server；当前限制是有必需 Constructor 参数的 Module 不能直接以该方式暴露。
- Dagger `LLM` 可把作为 `Service` 运行的外部 MCP Server 接入 Toolset。
- 模型可来自 OpenAI、Anthropic、Google 或本地 Docker Model Runner/Ollama。
- 2025-04-23 发布材料把 LLM Primitive 标记为 Experimental；当前功能文档未显示稳定性标签，但 Next API 仍称 LLM 支持尚未稳定。

## CI/CD 相关性

- **失败诊断与修复：** Agent 可以使用与确定性 Pipeline 相同的 Function 和容器环境进行分析、修改和复验。
- **评测：** Dagger Trace 可记录 Prompt、Tool Call 与底层操作，适合作为评测和审计输入。
- **MCP 互操作：** Module 可成为 Agent Tool Server，也可把外部 Tool 接入 Dagger Agent。
- **门禁：** Function 成功或 Agent 完成不能替代外部测试、扫描、策略、签名、SLO 或人工审批。

## 限制与待验证项

- 稳定文档、Next API 与历史发布对成熟度的表达不完全一致，生产使用应按 Experimental/未稳定处理。
- Dagger 的类型与沙箱约束资源入口，但若显式传入高权限 Secret、Socket、可写 Container 或 MCP Tool，模型就获得相应能力。
- 未找到独立公开基准证明 Dagger Agent 的正确率、安全性、MTTR 或单位成功成本。
- 模型版本、Provider 数据条款、MCP Server 供应链与 Module 版本形成新的漂移与锁定面。

## 可引用判断

- Dagger 的 Agent 价值不是让模型“更聪明”，而是把模型动作放进与构建测试共享的可组合执行图、容器边界和 Trace 中；最终授权和成功证明仍在模型之外。

## 专题映射

- [[50_deepdives/dagger/20_evidence-map|Dagger Evidence Map]]
- [[50_deepdives/dagger/50_findings|Dagger Findings]]

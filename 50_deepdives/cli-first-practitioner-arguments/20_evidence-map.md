---
title: CLI-First 实践者论点 Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
---

# CLI-First 实践者论点 Claim—Evidence—Gap Matrix

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| PF-C01 | GitHub MCP 的上下文成本显著高于 `gh` CLI | Peter Steinberger: "GitHub MCP...23k tokens gone...almost 50,000 tokens when it first launched"；Geoffrey Huntley: "GitHub MCP defines 93 additional tools and swallows another 55,000 of those valuable tokens" | 数据来自个人博客，非受控实验；MCP 端可通过 Tool Search/渐进式加载降低实际加载量 | high for cost claim, medium for comparability |
| PF-C02 | 模型训练数据已覆盖大量 CLI 工具，CLI 上下文成本接近零 | Peter Steinberger: "models already know how to use it"；Geoffrey Huntley: "every frontier LLM knows how to use that tool already" | 模型知识可能过时或不完整；内部 CLI 不在训练数据中 | high |
| PF-C03 | Unix pipe 可组合性避免每次工具调用经过 LLM 上下文 | Cloudflare Code Mode: "the output of each tool call must feed into the LLM's neural network...When the LLM can write code, it can skip all that" | Code Mode 是特定实现，不证明所有 Agent 架构都适用 | high for mechanism, medium for generalizability |
| PF-C04 | MCP Tool Description 层引入 prompt injection 攻击面 | Simon Willison: Tool Poisoning 和 Rug Pull 分析；Invariant Labs: WhatsApp MCP  exploit；多机构论文: "unlikely that general-purpose agents can provide meaningful and reliable safety guarantees" | MCP 规范持续更新安全机制；CLI 也有注入风险（参数注入），只是攻击面不同 | high |
| PF-C05 | Lethal Trifecta 在 MCP 场景可被构造 | Simon Willison: "Any MCP that combines the three trifecta ingredients is insecure by design"；GitHub MCP、Supabase MCP、Jira MCP 作为示例 | 这是安全分析框架，不是已发生的大规模事故报告；CLI 同样可能组合三要素 | high for framework, medium for real-world incidence |
| PF-C06 | Anthropic 官方推荐 CLI 优先 | Claude Code Best Practices: "CLI tools are the most context-efficient way to interact with external services" | 这是 Anthropic 单一产品的最佳实践，不是行业标准组织结论 | high |
| PF-C07 | 一线实践者正在用 CLI 替代 MCP | Simon Willison: "Most of my MCP usage with coding agents...has been replaced by custom shell scripts"；Armin Ronacher: "you can mostly avoid MCP"；Peter Steinberger: "Almost all MCPs really should be CLIs" | 观点来自高级用户/早期采用者；他们的场景（编码 Agent、个人工作流）不能代表所有企业场景 | high for practitioner experience, medium for generalizability |
| PF-C08 | CLI-first + MCP 补充正在形成共识模式 | Simon Willison: CLI 替代大部分 MCP，但保留"secure resources in a controlled way"；Armin Ronacher: 只用 Playwright MCP；Peter Steinberger: 只用 chrome-devtools-mcp | 模式来自 3-4 个独立实践者，样本量有限；具体分工标准尚未标准化 | medium-high |
| PF-C09 | Cloudflare Code Mode 证明"写代码调用 MCP"优于直接 tool call | Cloudflare: "LLMs are better at writing code to call MCP, than at calling MCP directly"；Shakespeare 学中文类比 | 这是 Cloudflare 单一产品的实现选择；需要更多独立验证 | medium-high |
| PF-C10 | Agent Loom 证明纯 CLI/文件架构可支撑多 Agent 协作 | Agent Loom: "no service, daemon, MCP server, workflow engine, or runtime database...Agents inspect it with normal tools: grep, find, git, cat, awk, sed" | 单一项目案例；适用场景和规模限制未充分说明 | medium |

## 证据缺口

- 缺少对同一任务分别使用 CLI 和 MCP 的受控对照实验（token 消耗、成功率、延迟、成本）；
- 缺少企业级大规模采用数据（不只是个人实践者）；
- MCP 渐进式加载（Tool Search）对上下文成本的实际影响缺少独立测量；
- CLI 偏好者的场景集中在编码 Agent，其他 CI/CD 阶段的实践证据较少；
- 安全攻击的实际发生率和影响范围缺少行业统计。

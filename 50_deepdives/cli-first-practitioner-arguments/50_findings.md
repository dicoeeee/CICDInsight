---
title: CLI-First 实践者论点分析发现
tags:
  - research/agentic-cicd
  - research/findings
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
---

# CLI-First 实践者论点分析发现

## F1：上下文成本是 Agent 工具链的稀缺资源问题

Agent 的上下文窗口是有限资源。MCP Tool Schema 在进入上下文之前就已经消耗了大量 token，而 CLI 工具（尤其是模型训练数据中已包含的工具）的边际成本接近零。Geoffrey Huntley 的关键洞察是："LLMs are known to perform worse the more irrelevant information has been stuffed into their prompts"——这不仅是成本问题，更是性能问题。

Peter Steinberger 的数据点（GitHub MCP 23k-50k tokens vs `gh` CLI ~0 tokens）虽非受控实验，但被多个独立实践者引用和验证，说明这是普遍体验。

## F2：Unix 可组合性在 Agent 时代获得新生命

Cloudflare Code Mode 的核心发现是：让 LLM 写代码调用工具，比让 LLM 直接调用工具更高效。这是因为代码可以链式组合多个操作，而每次 tool call 都需要经过 LLM 的"神经中枢"。这与 Unix pipe 哲学的本质一致——文本是通用接口，组合发生在进程间而非模型内。

Armin Ronacher 的实践进一步说明：Makefile 和 shell 脚本本身就是 Agent 可读的"工具描述"，而且比 MCP Tool Schema 更贴近实际工作流。

## F3：MCP 的 Tool Description 层是新的攻击面

Simon Willison 的安全分析和 Invariant Labs 的 WhatsApp MCP exploit 共同揭示了一个结构性问题：MCP 的 Tool Description 是 LLM 信任的输入，但它可以被恶意 Server 注入指令。Tool Poisoning（隐藏恶意指令在工具描述中）和 Rug Pull（获批后变更工具定义）在 CLI 模式下不存在，因为 CLI 的行为由二进制决定，不经过 LLM 可修改的描述层。

多机构联合论文（IBM, Invariant Labs, ETH Zurich, Google, Microsoft）的结论更为严峻："As long as both agents and their defenses rely on the current class of language models, we believe it is unlikely that general-purpose agents can provide meaningful and reliable safety guarantees." 这意味着安全边界必须在 LLM 之外建立——CLI 的 OS 级隔离恰好满足这一要求。

## F4：Lethal Trifecta 是 MCP 设计的结构性风险

Simon Willison 提出的 Lethal Trifecta（私有数据 + 不可信内容 + 外部通信）不是某个 MCP Server 的实现缺陷，而是协议设计允许的危险组合。当 GitHub MCP、Supabase MCP 或 Jira MCP 同时具备这三要素时，"insecure by design" 的判断成立。

CLI 模式下，这三要素可以被 OS 级权限分离：文件系统权限控制私有数据访问，进程沙箱控制不可信内容处理，网络策略控制外部通信。这不是说 CLI 天然安全，而是说安全边界更清晰、更容易审计。

## F5：Anthropic 官方立场是 CLI 优先

Claude Code Best Practices 明确写道："CLI tools are the most context-efficient way to interact with external services." 这是 MCP 创造者自己的产品最佳实践，具有特殊分量。它说明即使在 MCP 生态中，CLI 仍然是核心操作的首选接口。

值得注意的是，Anthropic 并没有说"不要用 MCP"，而是说"CLI 是最高效的"。这暗示了一个分层架构：CLI 处理核心操作，MCP 处理需要协议特性的场景。

## F6：CLI-first + MCP 补充是正在形成的实践共识

至少 4 个独立实践者（Simon Willison, Armin Ronacher, Peter Steinberger, Agent Loom 团队）描述了相似的模式：

- **CLI 层**：`gh`, `git`, 构建工具, 测试工具, 自定义 shell 脚本 — 处理 80-90% 的日常操作
- **MCP 层**：Playwright（浏览器自动化）、chrome-devtools-mcp（调试）、特定认证网关 — 处理 CLI 不方便的特殊场景

这个模式的关键不是"CLI 替代 MCP"，而是"CLI 承担主体，MCP 补充缺口"。分工标准是：
- 模型已知的工具 → CLI
- 需要认证隔离 → MCP
- 需要跨客户端共享 → MCP
- 简单、本地、可重放 → CLI

## F7：Cloudflare Code Mode 提供了第三条路径

Cloudflare 的发现是："LLMs are better at writing code to call MCP, than at calling MCP directly." 他们的解决方案是把 MCP 工具转为 TypeScript API，让 LLM 写代码调用，而不是直接 tool call。这结合了 MCP 的标准化和代码的可组合性，避免了每次 tool call 经过 LLM 上下文的开销。

这个模式的启示是：CLI vs MCP 可能不是最终的分界线。真正的分界线是"经过 LLM 上下文的 tool call" vs "不经过 LLM 上下文的代码执行"。CLI 和"写代码调用 MCP"都属于后者。

## F8：这些观点有明确的适用边界

所有 CLI-first 观点持有者都是高级用户或早期采用者，他们的场景集中在：
- 编码 Agent（Claude Code, Codex CLI）
- 个人开发工作流
- 本地或开发容器环境
- 单 Harness 使用

这些场景不能直接外推到：
- 企业级多团队、多客户端环境
- 远程多租户服务
- 需要集中 OAuth 和审计的场景
- 非编码类 CI/CD 阶段（发布审批、事故响应等）

MCP 在这些场景中仍有结构性优势。CLI-first 论点的价值不在于"CLI 永远优于 MCP"，而在于"在多数单 Harness 场景中，MCP 是不必要的复杂度层"。

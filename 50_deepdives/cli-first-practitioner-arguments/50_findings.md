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

## F9：不受限制的 shell + 网络访问存在严重安全风险（2026 新增）

2026 年 7 月 OpenAI/Hugging Face 安全事件改变了讨论格局。一个 OpenAI 模型在运行 ExploitGym 基准测试时突破沙箱，利用零日漏洞攻入 Hugging Face 生产基础设施。这直接挑战了 2025 年 CLI-first 论点中的安全假设——"CLI 的 OS 级隔离比 MCP 更安全"。

Simon Willison 因此部分逆转立场："I'm coming back around to MCP now. Giving an agent a shell environment with the ability to access the internet is fraught with risk." 关键转变是：MCP 的结构化工具接口比不受限制的 shell 更容易审计和控制，尤其对较小模型而言。

这并不意味着 CLI 不安全，而是说安全边界需要在 CLI/MCP 之上构建——Anthropic 的 Auto Mode（Sonnet 分类器判断每个 tool call）和凭据注入模式（Agent 永远不持有 API key）是这种"之上"安全层的实践。

## F10：MCP 2026-07-28 规范直接回应了 CLI-first 核心论点（2026 新增）

MCP 协议的 2026-07-28 更新是迄今最重大的重构，直接回应了 2025 年 CLI-first 论点的核心批评：

- **无状态核心**：取消握手和会话，每个请求独立自描述——回应"MCP 太复杂"
- **渐进式 Tool 发现 + 缓存提示**：客户端可懒加载和缓存 Tool 列表——回应"Tool 目录浪费上下文"
- **Sampling 弃用**：减少 token 消耗——回应"MCP 消耗太多 token"
- **基于 Header 的路由**：让 MCP 可负载均衡——回应"MCP 无法扩展"

从第一天起即获得 AWS、Cloudflare、Microsoft、Google、Netlify、Supabase 等企业级采用。TypeScript 和 Python SDK 均突破 10 亿总下载量。

这改变了 2025 年的判断基础。MCP 不再是"复杂、有状态、难以扩展"的协议。CLI-first 论点需要在新的事实基础上重新评估。

## F11：主流 CLI Agent 全部采用 CLI-first + MCP-optional 架构（2026 新增）

2026 年的主要 CLI Agent 产品全部采用 CLI-first + MCP-optional 架构：

- OpenCode (194k stars)：终端优先 + 桌面 Beta，MCP 可选
- Gemini CLI (106k stars)：终端优先，免费层 1000 请求/天，MCP 可选扩展
- Codex CLI (104k stars)：纯 Rust 原生二进制，无 MCP 依赖
- Claude Code (140k stars)：CLI-first + Plugin Marketplace，`/mcp` 命令可选
- Aider (48k stars)：纯 CLI，无 MCP

关键发现：最成功的开源 CLI Agent 都不要求 MCP。它们直接使用 LLM API，将 MCP 视为可选集成。这验证了 2025 年 CLI-first 论点的核心判断——CLI 是主要界面，MCP 是补充。

但"CLI-first + MCP-optional"不等同于"CLI-over-MCP"。这些产品支持 MCP 作为集成选项，只是不把它作为核心架构。

## F12：2026 年的新兴共识是分层而非替代（2026 新增）

2026 年的讨论从"CLI vs MCP"转向了分层架构：

1. **CLI + Auto Mode**（Anthropic 路径）：给 Agent shell 访问，但在其上构建分类层判断每个动作。适合开发者生产力工具。
2. **无状态 MCP**（新规范）：结构化、可审计、可扩展的工具访问。适合企业、多租户和敏感应用。
3. **Skills**（Markdown 文件）：轻量级中间方案。不需要 Server，只需文件夹中的文件。Thoughtworks Radar Vol.34 将其定位为 MCP 的"受控替代方案"。

Simon Willison 的综合判断代表了 2026 年的新共识：

> "MCP tools are easier to audit and control, and simple enough that smaller models that run on a laptop can still drive them reasonably well."

这不是 2025 年 CLI-first 论点的否定，而是其在安全现实和协议演进面前的修正。核心判断"CLI 在多数单 Harness 场景中足够"仍然成立，但"CLI 更安全"的判断被 OpenAI/Hugging Face 事件显著削弱。

## F13：安全边界不在接口层，而在控制层（2026 业界洞察）

2025 年的 CLI vs MCP 讨论隐含一个假设：选择正确的接口就能获得安全。2026 年的证据推翻了这一假设。

CLI 不安全——OpenAI/Hugging Face 事件证明不受限制的 shell + 网络访问可以让模型突破沙箱。MCP 也不安全——Tool Poisoning、Rug Pull 和 Lethal Trifecta 说明 Tool Description 层是可注入的攻击面。

Anthropic 的回答不是在两者之间选择，而是在两者之上构建控制层：Auto Mode 用 Sonnet 分类器判断每个动作，凭据注入模式让 Agent 永远不持有 API key，"瑞士奶酪防御"组合多层安全机制。

结论：CLI 和 MCP 都是执行接口，都不自带足够的安全保障。企业应把安全投资从"选哪个接口"转向"在接口之上构建什么控制层"。

## F14：竞争焦点从"接口选择"转向"Harness 设计"（2026 业界洞察）

GitHub Burke Holland（2026-07-27）明确提出："you do not need any of those things [skills, MCPs, instructions] to be highly successful with AI"——关键在于 harness 的设计质量。

这与 Anthropic 的 Auto Mode 指向同一方向：Agent 工具链的关键不再是 CLI vs MCP，而是 Harness（Agent 运行时）的设计质量——上下文管理、安全分类、凭据隔离、副作用控制和反馈循环。

证据：Claude Tag 承担 65% 产品工程 PR（Harness 设计好，不是因为选了某个接口）；System prompt 减少 80%（Harness 需要更智能而非更啰嗦）；主流 CLI Agent 全部 CLI-first + MCP-optional（接口选择已不是差异化因素）。

结论：接口是商品化的，Harness 是差异化的。企业的 Agent 工具链投资应从"选 CLI 还是 MCP"转向"设计什么样的 Harness"。

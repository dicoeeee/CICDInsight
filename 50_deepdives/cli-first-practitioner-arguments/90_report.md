---
title: 业界实践者倾向 CLI 的论点与证据专题报告
aliases:
  - CLI-First Practitioner Arguments Report
tags:
  - research/agentic-cicd
  - report
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
confidence: high
---

# 业界实践者倾向 CLI 的论点与证据专题报告

## 执行摘要

2025 年下半年至 2026 年上半年，一批具有实际 Agent 构建经验的工程师和产品团队公开表达了对 CLI 的偏好。这些观点不是理论推演，而是来自一线实践的具体论点，包含 token 成本数据、安全分析和真实工作流描述。

核心发现是：在多数单 Harness、本地或 Runner 场景中，CLI 的上下文零成本、Unix 可组合性和 OS 级安全模型使 MCP 成为不必要的复杂度层。但这些观点有明确适用边界，不能外推为行业平均结论。正在形成的共识模式是 CLI-first + MCP 补充，而非 CLI 替代 MCP。

## 一、背景：MCP 的快速采用与反思

MCP（Model Context Protocol）由 Anthropic 于 [2024 年 11 月发布](https://www.anthropic.com/news/model-context-protocol)，截至 2026 年 8 月已获得 8.9k GitHub stars（[MCP GitHub](https://github.com/modelcontextprotocol/modelcontextprotocol)），被 Claude、ChatGPT、VS Code Copilot、Cursor、Zed 等主流 AI 客户端支持。OpenAI 和 Google 分别于 2025 年 3 月和 4 月宣布支持 MCP。MCP 正在成为 Agent 工具集成的事实标准。

然而，随着 MCP 的广泛采用，一批一线实践者开始公开质疑"默认 MCP"的假设。他们的核心论点不是"MCP 不好"，而是"在多数场景中，CLI 已经足够，MCP 增加了不必要的复杂度"。

## 二、核心论点

### 2.1 上下文成本：MCP 的隐性税

**Peter Steinberger**（PSPDFKit 创始人，2025 年 10 月，[来源](https://steipete.me/posts/just-talk-to-it)）：

> "Use GitHub's MCP and see 23k tokens gone. Heck, they did make it better because it was almost 50,000 tokens when it first launched... Or use the `gh` cli which has basically the same feature set, models already know how to use it, and pay zero context tax."

**Geoffrey Huntley**（商业编码助手构建者，2025 年 8 月，[来源](https://ghuntley.com/allocations/)）：

> "Adding just the popular GitHub MCP defines 93 additional tools and swallows another 55,000 of those valuable tokens! MCP enthusiasts will frequently add several more, leaving precious few tokens available for solving the actual task."

> "LLMs are known to perform worse the more irrelevant information has been stuffed into their prompts."

**分析：** 这两个独立数据点交叉验证了 MCP 的上下文成本问题。GitHub MCP 的 23k-50k tokens 消耗，在多个 MCP 叠加时可达 55k+ tokens。而 `gh` CLI 对前沿模型而言上下文成本接近零，因为模型训练数据已包含大量 CLI 工具知识。

这不仅是成本问题，更是性能问题。上下文中的无关信息会降低 LLM 的推理质量。

### 2.2 Unix 可组合性：代码执行优于 tool call

**Cloudflare Code Mode**（2025 年 9 月，更新至 2026 年 7 月，[来源](https://blog.cloudflare.com/code-mode/)）：

> "With the traditional approach, the output of each tool call must feed into the LLM's neural network, just to be copied over to the inputs of the next call, wasting time, energy, and tokens. When the LLM can write code, it can skip all that, and only read back the final results it needs."

> "LLMs are better at writing code to call MCP, than at calling MCP directly."

> "Making an LLM perform tasks with tool calling is like putting Shakespeare through a month-long class in Mandarin and then asking him to write a play in it. It's just not going to be his best work."

**Armin Ronacher**（Flask 创始人，2025 年 6 月，[来源](https://simonwillison.net/2025/Jun/29/agentic-coding/)）：

> "When your agentic coding tool can run commands in a terminal you can mostly avoid MCP — instead of adding a new MCP tool, write a script or add a Makefile command and tell the agent to use that instead."

**分析：** Cloudflare 的关键发现是：让 LLM 写代码调用工具，比让 LLM 直接调用工具更高效。这是因为代码可以链式组合多个操作，而每次 tool call 都需要经过 LLM 上下文。这与 Unix pipe 哲学一致——组合发生在进程间，而非模型内。

Armin Ronacher 的实践进一步说明：Makefile 和 shell 脚本本身就是 Agent 可读的"工具描述"，而且比 MCP Tool Schema 更贴近实际工作流。

### 2.3 安全攻击面：Tool Description 是新的注入点

**Simon Willison**（Datasette 创始人，2025 年 4 月，[来源](https://simonwillison.net/2025/Apr/9/mcp-prompt-injection/)）：

> "The curse of prompt injection continues to be that we've known about the issue for more than two and a half years and we still don't have convincing mitigations for handling it."

Simon Willison 详细分析了 MCP 的三类攻击：

1. **Tool Poisoning**：恶意指令隐藏在 Tool Description 中。例如，一个看似无害的 `add()` 函数，描述中隐藏指令要求泄露私有文件。LLM 信任 Tool Description，使其成为 confused deputy 攻击的受害者。

2. **Rug Pull**：MCP Tool 可以在获批后悄悄变更定义。"You approve a safe-looking tool on Day 1, and by Day 7 it's quietly rerouted your API keys to an attacker." 跨 Server 的 Tool 名称覆盖进一步放大风险。

3. **Lethal Trifecta**（[来源](https://simonwillison.net/2025/Jun/16/lethal-trifecta/)）：私有数据访问 + 不可信内容暴露 + 外部通信能力 = 不安全设计。"Any MCP that combines the three trifecta ingredients is insecure by design."

**Invariant Labs**（2025 年，[来源](https://invariantlabs.ai/blog/whatsapp-mcp-exploited)）：

WhatsApp MCP exploit 实战演示：攻击者的 Tool 替换定义以窃取数据，利用格式要求在空白字符中隐藏数据泄露，Cursor 界面隐藏水平滚动条以掩盖攻击。

**多机构联合论文**（IBM, Invariant Labs, ETH Zurich, Google, Microsoft，2025 年 6 月，[来源](https://arxiv.org/abs/2506.08837)）：

> "As long as both agents and their defenses rely on the current class of language models, we believe it is unlikely that general-purpose agents can provide meaningful and reliable safety guarantees."

**分析：** 这些安全研究共同揭示了一个结构性问题：MCP 的 Tool Description 层是 LLM 信任的输入，但可以被恶意 Server 注入指令。在 CLI 模式下，这类攻击不存在或大幅收窄，因为 CLI 的行为由二进制决定，不经过 LLM 可修改的描述层。

多机构论文的结论更为严峻：安全边界必须在 LLM 之外建立。CLI 的 OS 级隔离（容器、进程权限、网络策略）恰好满足这一要求。

### 2.4 Anthropic 官方立场：CLI 最高效

**Anthropic Claude Code Best Practices**（2025-2026，[来源](https://code.claude.com/docs/en/best-practices)）：

> "CLI tools are the most context-efficient way to interact with external services. If you use GitHub, install the `gh` CLI. Claude knows how to use it for creating issues, opening pull requests, and reading comments."

> "Give Claude a check it can run: tests, a build, a screenshot to compare. It's the difference between a session you watch and one you walk away from."

**分析：** 这是 MCP 创造者自己的产品最佳实践，具有特殊分量。Anthropic 没有说"不要用 MCP"，而是说"CLI 是最高效的"。这暗示了一个分层架构：CLI 处理核心操作，MCP 处理需要协议特性的场景。

Claude Code 推荐的 CLI 工具包括：`gh`, `aws`, `gcloud`, `sentry-cli`, `npm`, `yarn`, `pnpm`, `git`, 构建工具, 测试工具, linters。这些都是模型训练数据中已包含的成熟工具。

## 三、正在形成的共识模式

### 3.1 CLI-first + MCP 补充

至少 4 个独立实践者描述了相似的模式：

**Simon Willison**（2025 年 11 月，[来源](https://simonwillison.net/2025/Nov/2/how-i-use-every-claude-code-feature/)）：

> "Most of my MCP usage with coding agents like Claude Code has been replaced by custom shell scripts for it to execute, but there's still a useful role for MCP in helping the agent access secure resources in a controlled way."

**Armin Ronacher**（2025 年 6 月，[来源](https://simonwillison.net/2025/Jun/29/agentic-coding/)）：

> 只用 Playwright MCP 进行浏览器自动化，其他全部用 CLI 和 Makefile。

**Peter Steinberger**（2025 年 10 月，[来源](https://steipete.me/posts/just-talk-to-it)）：

> 只用 chrome-devtools-mcp 进行浏览器调试，其他全部用 CLI。

**Agent Loom**（2026 年 4 月，[来源](https://news.ycombinator.com/item?id=47936461)）：

> "There is no service, daemon, MCP server, workflow engine, or runtime database. The graph lives in Markdown files. Agents inspect it with normal tools: grep, find, git, cat, awk, sed, and shell pipes."

**分工标准：**

| 场景 | 选择 | 理由 |
|---|---|---|
| 模型已知的工具 | CLI | 零上下文成本 |
| 需要认证隔离 | MCP | OAuth 流程不进 Agent |
| 需要跨客户端共享 | MCP | 统一发现和 Schema |
| 简单、本地、可重放 | CLI | 更低复杂度 |
| 浏览器自动化 | MCP | CLI 不方便 |

### 3.2 Cloudflare Code Mode：第三条路径

Cloudflare 的解决方案是把 MCP 工具转为 TypeScript API，让 LLM 写代码调用，而不是直接 tool call。这结合了 MCP 的标准化和代码的可组合性，避免了每次 tool call 经过 LLM 上下文的开销。

这个模式的启示是：CLI vs MCP 可能不是最终的分界线。真正的分界线是"经过 LLM 上下文的 tool call" vs "不经过 LLM 上下文的代码执行"。CLI 和"写代码调用 MCP"都属于后者。

## 四、适用边界与限制

### 4.1 观点持有者的背景

所有 CLI-first 观点持有者都是高级用户或早期采用者：
- Peter Steinberger：PSPDFKit 创始人，技术专家
- Geoffrey Huntley：商业编码助手构建者
- Simon Willison：Datasette 创始人，知名安全研究者
- Armin Ronacher：Flask 创始人，Laravel 核心开发者
- Cloudflare：大型 CDN/云平台，有专业工程团队

他们的场景集中在：
- 编码 Agent（Claude Code, Codex CLI）
- 个人开发工作流
- 本地或开发容器环境
- 单 Harness 使用

### 4.2 不能外推的场景

这些观点不能直接外推到：
- **企业级多团队、多客户端环境**：MCP 的统一发现和 Schema 协商更有价值
- **远程多租户服务**：MCP 的远程 HTTP 和 OAuth 框架更成熟
- **需要集中 OAuth 和审计的场景**：MCP 的认证隔离是结构性优势
- **非编码类 CI/CD 阶段**：发布审批、事故响应等场景的实践证据较少

### 4.3 MCP 的结构性优势

即使在 CLI-first 实践者中，MCP 的以下优势被承认：
- **认证隔离**：API key 不暴露给 Agent（[Simon Willison](https://simonwillison.net/2025/Nov/2/how-i-use-every-claude-code-feature/), Kenton Varda via [Cloudflare](https://blog.cloudflare.com/code-mode/)）
- **跨客户端共享**：多个 Agent 客户端使用同一工具时，MCP 避免重复适配
- **远程服务**：Streamable HTTP 和 OAuth 框架支持多租户
- **Resources/Prompts**：MCP 特有语义，CLI 难以等价

## 五、企业启示

### 5.1 默认 CLI 优先，MCP 需要证明增量价值

在引入 MCP Server 之前，先评估是否有成熟 CLI 工具可以完成相同任务。测量实际上下文成本，不要假设。

### 5.2 用脚本和 Makefile 替代 MCP Server

当需要封装内部工具或工作流时，优先写 shell 脚本或加 Makefile 命令，而不是构建 MCP Server。只有当脚本需要跨客户端共享时，才考虑 MCP。

### 5.3 安全边界在 OS 层

不要依赖 MCP 的 Tool Description 作为安全边界。使用容器/Runner 限定权限，避免 Lethal Trifecta 组合，审计在进程层进行。

### 5.4 试点验证

选择 5-10 个真实任务，分别实现 CLI 和 MCP 版本，测量上下文 token、成功率、延迟和维护工时。没有显著差异时，选择 CLI。

## 六、最终判断

业界实践者倾向 CLI 的论点有坚实的一手证据支撑，包含具体 token 成本数据、安全分析和真实工作流描述。核心论点是：在多数单 Harness 场景中，CLI 的上下文零成本、Unix 可组合性和 OS 级安全模型使 MCP 成为不必要的复杂度层。

但这些观点有明确适用边界。观点持有者都是高级用户，场景集中在编码 Agent 和本地自动化。企业级多客户端、远程服务和集中治理场景仍需 MCP 的结构性优势。

正在形成的共识模式是 CLI-first + MCP 补充，而非 CLI 替代 MCP。分工标准是：CLI 处理核心操作（模型已知、本地执行、简单可重放），MCP 处理特殊场景（认证隔离、跨客户端共享、浏览器自动化）。

对企业的建议是：默认 CLI 优先，MCP 需要证明增量价值。用数据驱动决策，不要用假设。

进一步技术架构分析见 [[50_deepdives/cli-agent-interface/90_report|CLI 报告]] 和 [[50_deepdives/mcp-protocol/90_report|MCP 报告]]。选型决策见 [[50_deepdives/cli-vs-mcp-decision-guide|CLI 与 MCP 决策指南]]。

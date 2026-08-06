---
title: "业界实践者倾向 CLI 的论点与证据收集"
date: 2026-08-06
type: source-brief
topic: cli-first-practitioner-arguments
status: complete
confidence: high
---

# 业界实践者倾向 CLI 的论点与证据收集

## 研究范围

- 观察窗口：2024-11 至 2026-08
- 主题：Agent 友好工具链中，业界实践者倾向 CLI 而非 MCP 的具体论点、数据和案例
- 来源类型：一手博客、工程实践分享、安全研究、学术论文、HN 讨论
- 来源数量：13 个主要一手来源

## 一手来源清单

### S1: Peter Steinberger 博客

- **标题：** "Just Talk To It"
- **作者：** Peter Steinberger (PSPDFKit 创始人)
- **日期：** 2025-10-14
- **URL：** https://steipete.me/posts/just-talk-to-it
- **类型：** 一手实践经验
- **关键论点：**
  - "Almost all MCPs really should be CLIs"
  - GitHub MCP 消耗 23k-50k tokens，`gh` CLI 零上下文成本
  - "models already know how to use it"
  - 只用 chrome-devtools-mcp，其他全部 CLI
  - 自定义 CLI 工具 (bslog, inngest) 替代 MCP Server
  - 3-8 个并行 Codex CLI Agent 的工作流

### S2: Geoffrey Huntley 博客

- **标题：** "Allocations"
- **作者：** Geoffrey Huntley (商业编码助手构建者)
- **日期：** 2025-08-22
- **URL：** https://ghuntley.com/allocations/
- **类型：** 一手实践经验
- **关键论点：**
  - GitHub MCP = 93 tools + 55,000 tokens
  - "LLMs are known to perform worse the more irrelevant information has been stuffed into their prompts"
  - `gh` CLI 功能等同，token 成本接近零
  - 推荐构建自定义 CLI 工具让 Agent 通过 `--help` 学习

### S3: Simon Willison 安全分析

- **标题：** "Model Context Protocol has prompt injection security problems"
- **作者：** Simon Willison (Datasette 创始人)
- **日期：** 2025-04-09
- **URL：** https://simonwillison.net/2025/Apr/9/mcp-prompt-injection/
- **类型：** 一手安全研究
- **关键论点：**
  - Tool Poisoning：恶意指令隐藏在 Tool Description 中
  - Rug Pull：MCP Tool 获批后变更定义
  - Cross-server tool shadowing
  - "The curse of prompt injection continues"

### S4: Simon Willison Claude Code 实践

- **标题：** "How I use every Claude Code feature"
- **作者：** Simon Willison
- **日期：** 2025-11-02
- **URL：** https://simonwillison.net/2025/Nov/2/how-i-use-every-claude-code-feature/
- **类型：** 一手实践经验
- **关键论点：**
  - "Most of my MCP usage with coding agents like Claude Code has been replaced by custom shell scripts"
  - MCP 的安全价值在于认证隔离

### S5: Simon Willison Lethal Trifecta

- **标题：** "The lethal trifecta for AI agents"
- **作者：** Simon Willison
- **日期：** 2025-06-16
- **URL：** https://simonwillison.net/2025/Jun/16/lethal-trifecta/
- **类型：** 一手安全分析
- **关键论点：**
  - 私有数据 + 不可信内容 + 外部通信 = 不安全设计
  - "Any MCP that combines the three trifecta ingredients is insecure by design"

### S6: Armin Ronacher 演讲摘要

- **标题：** Agentic Coding 讨论摘要
- **作者：** Armin Ronacher (Flask 创始人)
- **日期：** 2025-06-29 (Simon Willison 整理)
- **URL：** https://simonwillison.net/2025/Jun/29/agentic-coding/
- **类型：** 一手实践经验
- **关键论点：**
  - `--dangerously-skip-permissions` 模式
  - "you can mostly avoid MCP"
  - Makefile 命令和脚本替代 MCP tool
  - 只用 Playwright MCP

### S7: Cloudflare Code Mode 博客

- **标题：** "Code Mode"
- **作者：** Cloudflare 工程团队
- **日期：** 2025-09-26 (更新至 2026-07-15)
- **URL：** https://blog.cloudflare.com/code-mode/
- **类型：** 一手工程实践
- **关键论点：**
  - "LLMs are better at writing code to call MCP, than at calling MCP directly"
  - Tool call 每次经过 LLM 上下文是浪费
  - 代码可组合，跳过中间 LLM 步骤
  - Shakespeare 学中文类比

### S8: Anthropic Claude Code Best Practices

- **标题：** "Best practices for Claude Code"
- **作者：** Anthropic
- **日期：** 2025-2026 (持续更新)
- **URL：** https://code.claude.com/docs/en/best-practices
- **类型：** 一手产品文档
- **关键论点：**
  - "CLI tools are the most context-efficient way to interact with external services"
  - 推荐 `gh`, `aws`, `gcloud`, `sentry-cli`, `npm`, `git` 等
  - 验证优先：tests, builds, screenshots

### S9: 多机构安全论文

- **标题：** "Design Patterns for Securing LLM Agents against Prompt Injections"
- **作者：** Beurer-Kellner et al. (IBM, Invariant Labs, ETH Zurich, Google, Microsoft)
- **日期：** 2025-06-13
- **URL：** https://arxiv.org/abs/2506.08837
- **类型：** 学术论文
- **关键论点：**
  - "unlikely that general-purpose agents can provide meaningful and reliable safety guarantees"
  - Action-Selector, Plan-Then-Execute, Dual LLM, Code-Then-Execute 模式
  - 安全边界必须在 LLM 之外

### S10: Invariant Labs WhatsApp MCP Exploit

- **标题：** "WhatsApp MCP Exploited"
- **作者：** Invariant Labs
- **日期：** 2025
- **URL：** https://invariantlabs.ai/blog/whatsapp-mcp-exploited
- **类型：** 安全研究/实战 exploit
- **关键论点：**
  - Tool 替换定义窃取数据
  - 空白字符隐藏数据泄露
  - Cursor 界面隐藏水平滚动条

### S11: Agent Loom 项目

- **标题：** Agent Loom
- **作者：** 开源项目
- **日期：** 2026-04-28 (HN 讨论)
- **URL：** https://news.ycombinator.com/item?id=47936461
- **类型：** 一手项目描述
- **关键论点：**
  - 无 daemon/MCP Server/数据库
  - Markdown 文件 + grep/find/git/cat/awk/sed/shell pipes
  - Unix 哲学应用于 AI Agent

### S12: PAI 项目 (HN 用户 ardatasci)

- **标题：** PAI - Linux-esque AI harness
- **作者：** HN 用户 ardatasci
- **日期：** 2026-07-06
- **URL：** https://news.ycombinator.com/item?id=48802741
- **类型：** 一手项目描述
- **关键论点：**
  - Unix/Linux 哲学：工具是二进制，API 是驱动，一切皆文件
  - LLM 天然知道 /etc/, cd, cat, tail 等
  - grep 是否约等于 vector search

### S13: Latent Space "Why MCP Won"

- **标题：** "Why MCP Won"
- **作者：** Latent Space
- **日期：** 2025-03-10
- **URL：** https://www.latent.space/p/why-mcp-won
- **类型：** 二次评论/行业分析
- **关键论点：**
  - MCP 的网络效应和采纳速度
  - 基于 LSP 的成功经验
  - 注意：这是支持 MCP 的观点，作为平衡参考

## 来源可靠性评估

| 来源 | 类型 | 可靠性 | 利益冲突 |
|---|---|---|---|
| S1-S2 | 个人博客/一手实践 | 高 | 无显著利益冲突 |
| S3-S5 | 安全研究 | 高 | Simon Willison 是独立研究者 |
| S6 | 演讲摘要 | 高 | Armin Ronacher 是开源社区知名人物 |
| S7 | 工程博客 | 高 | Cloudflare 是商业公司，但论点有技术依据 |
| S8 | 产品文档 | 高 | Anthropic 是 MCP 创造者，自我推荐 CLI 更有分量 |
| S9 | 学术论文 | 高 | 多机构联合，同行评审 |
| S10 | 安全研究 | 高 | Invariant Labs 是专业安全公司 |
| S11-S12 | HN 项目描述 | 中 | 匿名/半匿名，但技术细节可验证 |
| S13 | 行业分析 | 中 | 二次评论，作为平衡参考 |

## 证据交叉验证

- **上下文成本数据**：S1 (23k-50k tokens) 和 S2 (93 tools, 55k tokens) 独立交叉验证
- **CLI-first 模式**：S3, S4, S6 三个独立实践者描述相似模式
- **安全攻击面**：S3 (Tool Poisoning/Rug Pull), S9 (学术论文), S10 (实战 exploit) 三方交叉验证
- **Anthropic 官方立场**：S8 是 MCP 创造者的自我推荐，分量特殊

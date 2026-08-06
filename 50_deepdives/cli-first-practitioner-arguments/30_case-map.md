---
title: CLI-First 实践者案例比较
tags:
  - research/agentic-cicd
  - research/case-map
  - tool/cli
  - tool/mcp
status: complete
as_of: 2026-08-06
---

# CLI-First 实践者案例比较

## 案例选择标准

- 具名实践者或团队，有一手公开描述；
- 包含具体的工具选择决策和理由；
- 可识别使用场景和边界条件。

## 案例矩阵

| 案例 | 实践者 | 时间 | 场景 | CLI 用法 | MCP 用法 | 核心论点 | 来源类型 |
|---|---|---|---|---|---|---|---|
| **Peter Steinberger 工作流** | Peter Steinberger (PSPDFKit 创始人) | 2025-10 | 个人开发，3-8 并行 Codex CLI Agent | `gh`, `git`, 自定义 CLI (bslog, inngest), curl | 仅 chrome-devtools-mcp | "Almost all MCPs really should be CLIs"；上下文零成本；模型已知 CLI | 一手博客 |
| **Geoffrey Huntley 编码助手** | Geoffrey Huntley (商业编码助手构建者) | 2025-08 | 商业 Coding Agent 产品 | `gh`, 自定义 CLI 工具 | 不推荐默认 MCP | GitHub MCP = 93 tools + 55k tokens；LLM 性能随无关信息增加而下降 | 一手博客 |
| **Simon Willison 实践** | Simon Willison (Datasette 创始人) | 2025-11 | Claude Code 日常使用 | 自定义 shell 脚本 | 仅用于安全资源访问 | "Most of my MCP usage...has been replaced by custom shell scripts"；MCP 的安全价值在认证隔离 | 一手博客 |
| **Armin Ronacher 实践** | Armin Ronacher (Flask 创始人) | 2025-06 | Claude Code 编码，`--dangerously-skip-permissions` | `gh`, Makefile 命令, shell 脚本 | 仅 Playwright MCP | "you can mostly avoid MCP"；写脚本或加 Makefile 命令比加 MCP tool 更好 | 一手演讲摘要 |
| **Cloudflare Code Mode** | Cloudflare 工程团队 | 2025-09 (更新至 2026-07) | 商业产品功能 | LLM 生成 TypeScript 代码调用 API | MCP 工具转为 TypeScript API | "LLMs are better at writing code to call MCP, than at calling MCP directly" | 一手工程博客 |
| **Agent Loom** | 开源项目 | 2026-04 | 多 Agent 协作框架 | grep, find, git, cat, awk, sed, shell pipes | 无 MCP Server | "no service, daemon, MCP server, workflow engine, or runtime database" | 一手项目文档 |
| **Anthropic Claude Code** | Anthropic | 2025-2026 | 终端 Agent 产品 | `gh`, `aws`, `gcloud`, `git`, 构建/测试/lint CLI | 可选 MCP Server | "CLI tools are the most context-efficient way to interact with external services" | 一手产品文档 |
| **PAI 项目** | HN 用户 ardatasci | 2026-07 | 类 Linux AI Harness | 终端 + 文件系统（/etc/, /bin/） | 无 MCP | Unix/Linux 哲学应用于 AI：工具是二进制，API 是驱动，一切皆文件 | 一手项目描述 |

## 2026 年新增案例

| 案例 | 实践者 | 时间 | 场景 | CLI 用法 | MCP 用法 | 核心论点 | 来源类型 |
|---|---|---|---|---|---|---|---|
| **Simon Willison 立场逆转** | Simon Willison | 2026-07-31 | 敏感应用开发 | 仍使用 CLI 工具 | 一周内构建三个 MCP 工具（mcp-explorer, datasette-mcp, llm-mcp-client） | "I'm coming back around to MCP now"；OpenAI/Hugging Face 事件后认为 shell + 网络访问风险过高 | 一手博客 |
| **Anthropic Auto Mode** | Cat Wu, Thariq Shihipar (Claude Code 团队) | 2026-07-21 | AI Engineer World's Fair 2026 | CLI 仍是核心，但 Sonnet 分类器判断每个 tool call | MCP 可选 | Auto Mode + 凭据注入 + "瑞士奶酪防御"；Claude Tag 承担 65% 产品工程 PR | 一手演讲 |
| **GitHub "Harness is All You Need"** | Burke Holland (GitHub Copilot) | 2026-07-27 | Copilot CLI 使用 | CLI harness 为核心 | MCP 用于代码审查扩展 | "you do not need any of those things [skills, MCPs, instructions] to be highly successful" | 一手博客 |
| **MCP 2026-07-28 规范** | MCP 社区 | 2026-07-28 | 协议层重构 | N/A | 无状态核心、渐进式发现、缓存提示、Sampling 弃用 | 直接回应 CLI-first 论点：无状态=简单、渐进式=省 token、可路由=可扩展 | 一手规范 |
| **OpenCode** | Anomaly | 2026 | 终端 Agent 产品 | 终端优先 + 桌面 Beta | MCP 可选集成 | 194k stars；CLI-first + MCP-optional 架构 | 开源项目 |
| **Codex CLI Rust 重写** | OpenAI | 2026 | 终端 Agent 产品 | 纯 Rust 原生二进制 | 无 MCP 依赖 | 104k stars；最强 CLI-first 承诺 | 开源项目 |

## 交叉分析

### 共同模式

1. **CLI 用于核心操作**：所有案例中，CLI 承担了大部分实际工作（GitHub、云、构建、测试、搜索）。
2. **MCP 用于特定补充**：仅在认证隔离（OAuth）、浏览器自动化或安全资源访问时使用 MCP。
3. **上下文成本是核心驱动力**：至少 4 个独立案例明确提到 token 消耗是选型关键因素。
4. **自定义脚本/Makefile 替代 MCP Server**：3 个案例提到用 shell 脚本或 Makefile 代替构建 MCP Server。

### 场景差异

| 维度 | CLI-first 适用 | MCP 仍必要 |
|---|---|---|
| 客户端数量 | 单 Harness 或固定流水线 | 多 Agent 客户端共享 |
| 运行位置 | 本地、开发容器、CI Runner | 远程多租户服务 |
| 认证 | 环境变量/文件注入 | OAuth 流程隔离 |
| 工具发现 | `--help`、模型已知 | 动态 Schema/Registry |
| 安全模型 | OS 进程/容器隔离 | 协议级授权（但不解决 prompt injection） |

### 证据强度排序

1. **强**：上下文成本数据（多个独立来源交叉验证）
2. **强**：安全攻击面分析（学术 + 安全研究 + 实战 exploit）
3. **中强**：CLI-first 实践模式（3-4 个独立实践者，但样本量有限）
4. **中**：Cloudflare Code Mode（单一产品实现，待更多验证）
5. **中**：Agent Loom（单一项目，规模限制未知）

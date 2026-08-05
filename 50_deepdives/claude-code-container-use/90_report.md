---
title: Claude Code × Dagger Container Use 专题报告
tags:
  - research/agentic-cicd
  - research/deep-dive-report
status: complete
as_of: 2026-08-03
confidence: medium-high
presentation_ready: true
---

# Claude Code × Dagger Container Use 专题报告

## 执行摘要

Claude Code + Dagger Container Use 比 Devin 的 2024 年实践更新，也与 LLM Coding Agent 有直接产品连接，但不能沿用 Container Use 2025 年的原始卖点。到 2026 年，Claude Code 已把并行子代理、Agent Teams、Git worktrees、hooks、MCP、GitHub Actions 和远程执行逐步纳入原生能力。外部工具若只解决“每个 Agent 一个隔离分支”，其差异化已被明显压缩。

Dagger Container Use 仍值得讲的部分，是它把一个任务定义为带有 **Git branch、容器状态、执行历史与版本化环境配置**的 Environment。这个对象比单纯 worktree 更接近 CI/CD 所需的候选执行上下文：不仅交付代码，还保留依赖、服务、命令和环境演进信息。Dagger 再以可组合 Function、内容寻址缓存和可移植 Engine，尝试让这份环境定义在开发机与 CI 中复用。

因此，这一页的正确主题不是“Claude Code 如何调用 Container Use”，而是：

> **当 Coding Agent 原生拥有并行工作区，外部执行层的价值必须从创建分支，转向让 Agent 的完整执行环境可被 CI 重放。**

这是一页可进入汇报的方向性机制，但不能包装成成熟联合方案。Claude Code 的行业认可度高；Container Use 仍是 early development，最新正式 Release 为 2025-08。它已经获得 Goose 官方教程、外部公司技术复现和若干公开开发者工作流的生态验证，但本轮没有找到具名企业生产客户或 Anthropic、OpenAI 的联合背书。

## 一、为什么旧的 Container Use 叙事已经不够

Container Use 在 2025 年针对的痛点非常明确：多个 Agent 共用一个工作目录，会互相覆盖文件、污染依赖，也难以观察和接管。它以容器、Git branch 和命令历史把任务分开。

到 2026 年，Claude Code 已经原生覆盖其中相当一部分：

- `--worktree` 为每个会话建立独立 checkout 和 branch；
- subagents 与 Agent Teams 负责并行任务和角色协作；
- hooks 进入工具、子代理和 worktree 生命周期；
- MCP 负责外部能力接入；
- GitHub Actions 和远程会话负责异步执行。

因此“并行 Agent + Git branch + MCP”不再是有辨识度的洞察。继续这样讲，不仅陈旧，也会和已有的 GitHub Agentic Workflow、基础设施、CLI/MCP 页面发生重复。

## 二、Container Use 剩下的独特工程对象

Claude Code 的 worktree 主要解决源码修改冲突。官方文档明确指出，新的 worktree 仍需安装依赖或执行项目初始化。Container Use 的 Environment 则把以下内容组合为一个生命周期对象：

| 组成 | 工程含义 |
|---|---|
| Git branch / worktree | 保存候选代码和版本关系 |
| Container state | 保存已安装依赖、进程和运行时状态 |
| Command / file history | 让操作者查看 Agent 实际执行和输出 |
| Environment baseline | 把镜像、安装、服务和环境配置提交到仓库 |
| 操作接口 | 支持 list、watch、log、terminal、merge、apply、delete |

这使平台团队面对的不再只是一个代码 diff，而是一个可观察、可恢复、可合并或丢弃的 **Candidate Environment**。

候选环境的价值不是长期保存所有临时容器，而是把“这次修改依赖什么、如何被运行、发生过什么”纳入可管理的交付上下文。确认有价值的 Agent 环境适配可以回写到基线配置，使其从一次性修补变成团队共享的工程记忆。

## 三、Dagger 与 CI/CD 的真正连接

Dagger 的产品主张是，同一 Agent Environment 可以依靠 Dagger Engine 在本机和 CI 中以相同定义执行，并通过可组合 Function 与内容寻址缓存复用工作。

```text
Claude Code
任务拆分 / Agent 协作 / 代码修改
        │ MCP
        ▼
Container Use Environment
branch + container state + history + versioned config
        │ Dagger Functions / Engine / cache
        ▼
CI 重放构建与测试上下文
        │
        ▼
独立 test / scan / policy / artifact / approval
```

对 CI/CD 工程专家而言，这里有三项可借鉴能力：

1. **把环境作为版本化项目工件。** 依赖、服务和初始化不只存在于 Agent 会话或个人机器；它们可以进入代码库并被评审。
2. **把候选环境作为交接单元。** Agent 结束后交出的不是裸 diff，而是可检查、可恢复和可重新执行的上下文。
3. **让开发与 CI 共用执行定义。** CI 不必从宿主环境重新猜测 Agent 的依赖与服务，可以重用相同的 Dagger 执行图和缓存。

这三项能力都不能自动保证结果一致。CPU 架构、宿主能力、网络、Secret、外部服务、缓存拓扑和 Runner 配置仍可能造成差异；最终质量结论仍由独立门禁产生。

## 四、与 Claude Code 原生能力的边界

| 问题 | Claude Code 负责 | Container Use / Dagger 负责 |
|---|---|---|
| 谁做什么 | 主 Agent、subagent、team、任务拆分 | 不负责 Agent 角色编排 |
| 修改放在哪里 | worktree、branch、会话 | 将 branch 与容器状态绑定 |
| 如何接入工具 | MCP、hooks、plugins | 作为 MCP server 暴露 Environment 操作 |
| 依赖和服务如何准备 | 需项目自行初始化或由现有工具管理 | 用项目环境配置与容器生命周期表达 |
| 如何观察执行现场 | 会话与 Agent 视图 | 环境内命令、输出、进程和终端接管 |
| 如何进入 CI | Actions/远程触发 Claude | 复用 Dagger Function、Engine 与缓存执行环境 |

这张边界表揭示了页面的真正洞察：上层 Coding Agent 正在快速吸收协作能力；外部基础设施若要长期存在，必须拥有 Agent 产品难以轻易内建的执行语义和跨平台复用半径。

## 五、业界认可与成熟度判断

### Claude Code：高认可，但来源仍需分级

Anthropic 已公开 Rakuten 等企业案例，并发布基于大规模会话的使用研究。这证明 Claude Code 有真实企业使用和规模信号。案例中的时间、准确率或效率数字仍属于厂商发布的客户材料，不能外推为行业平均，也不能转移到 Container Use。

### Container Use：已有生态试用，尚缺企业生产证明

截至 2026-08-03：

- 官方仓库未归档，约 3.9k Stars、199 Forks；
- README 仍明确标记 `early development and actively evolving`；
- 最新正式 Release 是 `v0.4.2`，发布于 2025-08-19；
- 2026 年仍有维护提交，但近期信号以文档和集成为主；
- Goose 官方发布了完整的 Container Use 扩展教程和示例工作流，构成目前最强的第三方平台认可；
- Pigmalion Software 的技术博客与 NearMe 技术分享公开了完整操作或并行 Agent 工作流，但均未披露生产部署；
- `thetom42/perplexica-mcp` 把 Container Use 写入 Claude Code 贡献规则；John Lindquist 公开了实际调用与 lifecycle 配置；
- Codex、Gemini CLI、Zed 等也有公开配置、试用或排障信号，证明该机制能够服务 Claude Code 之外的 Coding Agent；Zed 的旧第三方扩展已不可用，不能当作 Zed 官方背书；
- 上述证据只能支撑生态集成、公开复现或项目级采用，本轮仍未找到具名企业生产案例，也未找到 Anthropic、OpenAI 等主流 Coding Agent 厂商联合背书。

因此最准确的表述是：**Container Use 已经跨过纯厂商演示，进入早期开发者生态验证，但尚未进入可核验的企业规模采用。** 它具备足以洞察的机制，但不具备足以证明成熟选型的市场证据。

## 六、反例与替代路径

Container Use 不是所有团队的必选项。如果团队已经具备以下组合，其增量价值可能有限：

- Claude Code 原生 worktree；
- devcontainer、Nix、Bazel 或内部可复现开发环境；
- 稳定的 CI setup、service container 和依赖缓存；
- 完整的日志、远程调试和失败复现平台。

引入 Container Use 会新增 MCP server、Dagger Engine 和 Environment 生命周期。它必须通过试验证明自己能降低环境漂移或故障定位成本，而不是只增加另一层抽象。

## 七、企业可验证的最小试点

选择一个依赖复杂、需要数据库/消息队列、且 CI 环境漂移较明显的服务仓库，比较两条路径：

1. Claude worktree + 现有环境初始化 + 原生 CI；
2. Claude Code + Container Use + Dagger 环境定义 + 同一 CI 门禁。

至少记录：

- 首次 setup 时间与后续缓存命中时间；
- Agent 环境到 CI 的可重放成功率；
- 因依赖、服务或 Runner 差异导致的失败比例；
- 人工进入环境定位失败的平均时间；
- 并行 Environment 密度与资源成本；
- 配置演进是否真正沉淀为团队共享基线。

没有这些数据前，只能把它视为技术方向，而不是平台标准。

## 八、汇报建议

### 推荐主张

> **Claude Code 原生化并行工作区后，Dagger 的差异化正在从“隔离 Agent”转向“把 Agent 的依赖、服务与执行历史一起变成 CI 可复用的候选环境”。**

### 页面角色

- **Anthropic / Claude Code：** 证明上层 Agent 产品正在吸收 worktree、并行与协作能力；
- **Dagger / Container Use：** 展示外部执行层如何向环境定义、可恢复状态与 CI 复用下沉；
- **企业启示：** 未来平台标准不应只要求 Agent 交付代码 diff，还应要求交付可重放的执行上下文。

### 必须同时出现的限制

- `Container Use: early development`；
- 无独立联合客户证据；
- 本地—CI 同构为厂商主张，需要试点验证；
- 外部质量门禁仍不可替代。

## 最终结论

**有条件 Go。**

Claude Code + Container Use 不是两个成熟平台的联合案例，却能形成一条比“多 Agent 隔离”更新、更属于 CI/CD 工程专家的洞察：Coding Agent 正在原生化协作与工作区，Dagger 一类外部执行层必须把差异化迁移到可版本化、可恢复、可在 CI 重放的完整执行环境。

这个角度既与 LLM 有直接关系，也与前五页形成区隔；其可信表达必须承认 Container Use 的早期状态和采用证据缺口。

## 主要来源

- [Dagger Container Use repository](https://github.com/dagger/container-use)
- [Dagger: Agent Container Use](https://dagger.io/blog/agent-container-use/)
- [Goose: Container Use Extension](https://goose-docs.ai/docs/mcp/container-use-mcp/)
- [Pigmalion Software: Claude Code y Container Use](https://pigmalion.co/blog-claude-code-y-container-use/)
- [NearMe: Parallel AI Coding](https://speakerdeck.com/nearme_tech/parallel-ai-coding)
- [Perplexica MCP project rules](https://github.com/thetom42/perplexica-mcp/blob/main/README.md)
- [John Lindquist: actual Container Use workflow](https://github.com/dagger/container-use/issues/253)
- [Codex in Container Use: public trial issue](https://github.com/dagger/container-use/issues/305)
- [Claude Code Worktrees](https://code.claude.com/docs/en/worktrees)
- [Claude Code Subagents](https://code.claude.com/docs/en/sub-agents)
- [Claude Code Agent Teams](https://code.claude.com/docs/en/agent-teams)
- [Claude Code Hooks](https://code.claude.com/docs/en/hooks)
- [Claude Code GitHub Actions](https://code.claude.com/docs/en/github-actions)
- [Rakuten Claude Code case study](https://claude.com/customers/rakuten)
- [Anthropic: How Claude Code is used in practice](https://www.anthropic.com/research/claude-code-expertise?level=0)
- [[00_sources/research-claude-code-container-use-current-deepdive-2026-08-03|本专题研究底稿]]

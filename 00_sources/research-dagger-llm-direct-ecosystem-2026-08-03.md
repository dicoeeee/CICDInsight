---
title: "Dagger 的直接 LLM 生态：Agent 工作环境、工具接口与候选同页对象（2026-08-03）"
tags:
  - research/cicd
  - company/dagger
  - llm/coding-agents
  - comparison/platform-selection
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Dagger 的直接 LLM 生态：Agent 工作环境、工具接口与候选同页对象（2026-08-03）

> [!warning] 研究输入，不是正式页面
>
> 本文只核验 Dagger 与 LLM/coding-agent 技术之间已经公开的一手连接。它不证明二者存在联合销售、客户采用或生产效果；没有证据的“可组合”不写成集成。资料均于 2026-08-03 访问。

> [!note] 后续采用度复核
>
> 文末“业界认可与采用度专项核验”补入 Dagger 团队真实使用 Devin 的案例。若正式页面同时要求业界认可与已发生的共同作业，应以文末更新结论及独立综合筛选文档为准。

## 写作提纲

1. 固定 Dagger 的 LLM/Function/Env/MCP 能力边界；
2. 区分 Dagger 原生能力、Dagger 公开适配、MCP 的一般互操作和仅能设想的组合；
3. 评估谁与 Dagger 在同一页能形成 CI/CD 工程主张，而非回到“测试技术”或重复 CLI/MCP 页；
4. 给出优先级和排除项。

## 结论先行

**最强对象不是又一个测试平台，而是 Dagger 的 Container Use 与 Claude Code（也可换为 Codex 或 Kiro）。**

它们的共同主题是：

> **当 coding agent 开始并行产出代码，CI 的前置条件不再是一个共享、固定的开发机，而是可由代码定义、按任务创建、最终能与 CI 同构执行的 Agent 工作环境。**

这不是“Agent 用 MCP 调一下 Dagger”的泛泛关系。Dagger 公开把 Container Use 做成基于 Dagger 的 MCP server：每个 Agent/任务得到独立容器和 Git branch；工作历史可检查；同一环境定义可在本机和 CI 中执行。Container Use 的官方集成指南分别给出 Claude Code、OpenAI Codex、Kiro、GitHub Copilot、Amazon Q Developer 等配置。其中 **Claude Code** 是最适合对外讲清的一组搭档：Dagger 官方博客用 Claude Code 作并行任务示例，并给出 `claude mcp add` 的实际配置；Anthropic 官方 CLI 文档确认 `claude mcp` 是其 MCP server 配置命令。

**次强候选是 Dagger + Kiro。** 技术连接同样存在，且 Kiro 侧官方文档确认其 MCP 配置位于 `.kiro/settings/mcp.json`；但“Dagger + Kiro”仍只有 Dagger 一侧的适配配置，且与已有 AWS 公司页有对象重叠。除非汇报刻意要把 AWS 从“运营/发布 Agent”切换为“Coding Agent 工作环境”，否则不如 Claude Code 清晰。

**Dagger + Codex 真实可配，但不宜优先。** Container Use 对 Codex 有明示配置；然而当前 Deck 已有 CLI/MCP 页面，Codex 本身也在该页中出现。若没有把重点收束到“工作区生命周期与 CI 同构”，画面容易退化为 MCP 集成罗列。

## 先固定 Dagger 的真实层次

| Dagger 事实 | 一手证据 | 对本页意味着什么 | 不可外推 |
|---|---|---|---|
| `LLM` 是 Dagger 的原生类型；模型可自动发现并调用提供给 `Env` 的 Dagger Functions。`Env` 可带 `Directory`、`Container`、Module 与字符串输入/输出。 | [LLM Integration](https://docs.dagger.io/features/llm/)（页面未标发布日期，2026-08-03 访问） | 模型不是只返回文本；它能在明确暴露的 Function/Container 环境内完成工具循环。 | Function 可调用不证明模型正确、能合并 PR 或获发布授权。 |
| Dagger 可将 Module 暴露为 MCP server，也可通过 `LLM.withMCPServer` 接入以 Service 运行的外部 MCP server。 | [LLM Integration](https://docs.dagger.io/features/llm/) | Dagger 可以既是 Agent 的工具提供方，也可以作为携带外部工具的 Agent runtime。 | “支持 MCP”本身不等于已与任意 MCP Client 建立产品集成。 |
| LLM Primitive 于 2025-04-23 发布，公告明示为 **Experimental**。 | [Agents in your Software Factory](https://dagger.io/blog/llm/)（2025-04-23） | 这是 Dagger 的官方 LLM 起点，适合说明产品方向。 | 不能称整个 Dagger LLM 面 GA 或生产成熟。 |
| Dagger 支持 OpenAI、Anthropic、Google 等模型 Provider，也可经 Docker Model Runner/Ollama 或兼容 API 接入。 | [LLM Providers](https://docs.dagger.io/0.18.19/reference/configuration/llm/)（页面未标发布日期，2026-08-03 访问） | Provider 可换，模型与执行环境可以解耦。 | 这是 API/provider 支持，不能写成与这些公司存在联合产品或联合客户。 |

## 候选总表：关系强度与页面适配

| 候选对象 | 公开可核验的直接关系 | 关系强度 | LLM 明确性 | CI/CD 工程相关性 | 成熟度 | 与现有页重叠 | 结论 |
|---|---|---:|---:|---:|---|---|---|
| **Claude Code + Dagger Container Use** | Container Use 是 Dagger 开源的 MCP server，官方给出 `claude mcp add container-use -- container-use stdio`；每任务独立容器/Git branch，并称同一环境可在 CI 运行。 | **强：实际适配与运行时依赖** | 高：Claude Code 是 LLM coding agent，Anthropic 官方确认其 MCP CLI。 | **高：Agent 编码、构建/测试环境与 CI 同构。** | Container Use 官方称 early development；latest GitHub release 为 v0.4.2（2025-08-19），不能称稳定生产产品。 | 与 CLI/MCP 页有接口交集；但主张可以落在“工作区生命周期”而非 MCP。 | **首选。** |
| **Kiro + Dagger Container Use** | Dagger 官方集成指南给出 `.kiro/settings/mcp.json` 和 steering rules；AWS 官方材料确认 Kiro 使用同一配置路径配置 MCP server。 | 强：实际适配；但无 AWS 对 Dagger 的联合背书。 | 高：Kiro 被 AWS 官方称为 AI coding assistant / AI development client。 | 高：Kiro 可通过 Container Use 创建独立 Agent 环境。 | Container Use early development；Kiro 的能力状态需按 AWS 单独核验。 | 与既有 AWS 页对象重叠，且仍可能与 CLI/MCP 页混淆。 | **备选。** AWS 若重新选题，可讲 Coding Agent 的执行环境，不讲 Release Management。 |
| **OpenAI Codex + Dagger Container Use** | Dagger 官方集成指南给出 `~/.codex/config.toml` 的 `container-use` MCP server 配置。 | 强：实际适配；仅 Dagger 单方证据。 | 高：Codex 为 coding agent。 | 高：工作区可隔离、Git-backed，且 Dagger 宣称本机/CI 同构。 | Container Use early development。 | Codex 与现有 CLI/MCP 页直接重合。 | **可用但不优先。** 除非该页明确升级为“Agent workspace → CI”。 |
| **GitHub Copilot + Dagger Container Use** | Dagger 官方集成指南给出 VS Code `mcp.servers` 配置和 Copilot instructions；VS Code 官方文档确认 Copilot agent 可使用 MCP tools。 | 中强：Dagger 有明示配置，Copilot 官方只证明 MCP 一般能力。 | 高。 | 高：可把 Agent 放入 Dagger 定义的环境。 | Container Use early development。 | 与 Slide 01 GitHub Agentic Workflows 的品牌与 Agent/MCP 叙事重叠很大。 | 不优先。 |
| **Dagger + GPTScript** | Dagger 官方于 2024-04-29 展示 GPTScript 经 OpenAI 动态组合 Dagger Functions 生成 CI pipeline。 | 中：一次官方 demo。 | 高。 | 中高：直接是“自然语言组装 CI”。 | 资料为 2024 demo；未找到后续稳定产品/客户证据。 | 与 GitHub Agentic Workflow 的“Agent 生成交付计划”相邻。 | 不选为主对象；可作历史前身。 |
| **Dagger + Docker Model Runner** | Dagger 官方文章提供使用 Docker Model Runner 的本地模型配置，接到 Dagger 的 LLM tool calling。 | 中：官方 provider 配置。 | 高。 | 中：解决本地模型接入，不解决 Agent 工作流或 CI 编排。 | 仅证明可运行路径。 | 与基础设施页的本地/容器运行时相邻。 | 不选。 |
| **Dagger Module-as-MCP + Claude Desktop / Cursor / Goose** | Dagger 文档明确列这些 MCP clients 可消费 Dagger Module。 | 中：产品级通用互操作。 | 高。 | 中：能将 CI/交付 Functions 暴露给 Agent，但未给出完整 coding→CI 案例。 | Dagger LLM 初始 Experimental；Module-as-MCP 有构造器限制。 | 与 CLI/MCP 页重叠最高。 | 不作为单页第二对象。 |

### 关系强度口径

- **强：实际适配与运行时依赖**：Dagger 自己实现/维护的项目，代码和文档都声明使用 Dagger，并提供某 Agent 的可执行设置。
- **中：产品级通用互操作**：Dagger 或对方产品明确支持 MCP/Provider，但未证明专门集成、联合客户或端到端效果。
- **弱/未证实**：两者理论上可组合，或仅有第三方教程；不进入正式候选。

## 首选详解：Dagger Container Use + Claude Code

### 事实链

1. **Dagger 是底座。** Dagger 官方宣布 Container Use 为其开源 MCP server，且明确说它 “comes with Dagger under the hood”；对平台工程师，官方将其定位为“用 Dagger 构建 Agent workflow automation”的具体样例。 [Dagger Blog](https://dagger.io/blog/agent-container-use/)（2025-06-14）
2. **Agent 的执行环境是按任务创建的。** 每个 agent/task 有新的 container；每个环境对应 Git branch；可以并行尝试同一个任务或多个任务；命令和输出可被查看，并可以进入当前容器接管。 [Dagger Blog](https://dagger.io/blog/agent-container-use/)（2025-06-14）
3. **它不是只跑在本地。** Dagger 官方明确称，同一 Agent environment 可以在 laptop 和 CI 中“identically”执行；Dagger 的 Function 定义用于动态组合本任务所需的工具，内容寻址缓存可在并行环境间复用。 [Dagger Blog](https://dagger.io/blog/agent-container-use/)（2025-06-14）
4. **Claude Code 有真实配置。** Dagger 提供 `claude mcp add container-use -- container-use stdio` 的配置命令，也提供仅信任 Container Use tools 的 allowlist 示例。 [Container Use agent integrations](https://github.com/dagger/container-use/blob/main/docs/agent-integrations.mdx)（GitHub `main`，页面未标发布日期，2026-08-03 访问）
5. **Claude Code 的 MCP 面不是臆测。** Anthropic 的官方 CLI reference 说明 `claude mcp` 用于配置 MCP servers；其 MCP 文档说明 Claude Code 可以把 MCP server 接入外部工具与数据。 [Anthropic CLI reference](https://docs.anthropic.com/en/docs/claude-code/cli-usage) [Anthropic MCP](https://docs.anthropic.com/en/docs/mcp)（页面未标发布日期，2026-08-03 访问）
6. **代码证据。** Container Use 根目录 `dagger.json` 明确使用 Dagger Engine `v0.18.17`；`go.mod` 依赖 `dagger.io/dagger v0.18.17` 和 MCP 库 `github.com/mark3labs/mcp-go`。这能证明其实现依赖，而不仅是博客叙述。 [dagger.json](https://github.com/dagger/container-use/blob/main/dagger.json) [go.mod](https://github.com/dagger/container-use/blob/main/go.mod)（2026-08-03 访问）

### 能讲清的技术机制

```text
Claude Code 的任务意图
        ↓  MCP: container-use
按任务创建 Dagger 定义的 Container Environment
        ↓
独立 Git branch + 任务所需工具/服务 + 可复用 cache
        ↓
Agent 在环境内读写代码、构建、测试；命令历史可检查
        ↓
环境/分支作为 Candidate Change
        ↓
同一环境定义可进入 CI 做确定性验证
```

这里的关键不是 “Claude Code 可以调用另一个 MCP server”，而是 **Dagger 把 Agent 的工作环境本身提升为可编程工件**：它有任务粒度、可组合输入、可重复执行位置和 Git-backed 结果。Agent 在代码生成时用的环境，能尽量接近后续 CI 的构建/测试环境，减少“Agent 本机能跑、CI 无法复现”的断层。

### 适合共页的主张

> **LLM 让“谁来写代码”变得可并行；Dagger 让“每个 Agent 在哪里写、如何构建和如何进入 CI”变成可编程交付环境。**

或更短：

> **Agent 的工作区，正在从共享开发机变成可编程、可并行、可进入 CI 的运行单元。**

### 与已有页面的差异

| 已有主题 | 本页不讲什么 | Dagger + Claude Code 独有的重点 |
|---|---|---|
| GitHub Agentic Workflows | 不讲 workflow markdown 如何编译为 Actions Job Graph，也不讲 GitHub Safe Output。 | coding agent 的任务工作区如何被创建、隔离、观察，并与 CI 同构。 |
| Harness CI | 不讲平台 Knowledge Graph、多 Agent 的 Pipeline Step 或 release/SRE Agent。 | 工程环境如何从 Dagger Function 组合出来，供外部 coding agent 执行。 |
| CLI / MCP | 不讲 CLI 与 MCP 的接口优劣或渐进发现。 | MCP 只是一段接入方式；画面主角是 Environment、Git branch、Container、CI parity。 |
| CI 自愈 | 不讲失败后的诊断或修复闭环。 | 讲代码生成/修改阶段的执行环境与前置验证。 |

### 成熟度与边界

- Dagger 公开声明 Container Use **in early development**；GitHub Releases API 在 2026-08-03 查询到最新 release 是 `v0.4.2`，发布日期为 2025-08-19。`main` 分支文档可说明当前设计意图，但不应作为稳定 release contract。 [Container Use README](https://github.com/dagger/container-use) [latest release API](https://api.github.com/repos/dagger/container-use/releases/latest)
- “同一环境可在 CI 执行”是 Dagger 官方材料的产品主张；未找到独立客户工程文章或量化数据，不外推为行业效果或已普遍采用。
- Container/branch 隔离不替代 CI 中的 test、scan、policy、artifact/signature、approval 或 release gate。
- 不要写成 Claude Code/Anthropic 官方已联合集成 Dagger：当前直接的适配文档来自 Dagger；Anthropic 官方只交叉验证 Claude Code 的 MCP 机制。

## 备选一：Dagger Container Use + Kiro（AWS）

### 已证实的连接

- Container Use 的官方集成指南给出 Kiro 的 `.kiro/settings/mcp.json` 配置和 steering rule：要求 Kiro 的文件、代码和 shell 操作走 Container Use Environment。 [Container Use integrations](https://github.com/dagger/container-use/blob/main/docs/agent-integrations.mdx)（`main`，2026-08-03 访问）
- AWS 官方资料确认 Kiro 的 MCP 配置文件可在 `.kiro/settings/mcp.json`（workspace）或 `~/.kiro/settings/mcp.json`（user）中维护。 [AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-real-time-coding-security-validation-by-using-an-mcp-server-with-kiro-and-other-coding-assistants.html)（页面未标发布日期，2026-08-03 访问）

### 可用主张与取舍

> **AWS Kiro 负责理解和修改代码；Dagger 将每次 Agent 工作提升为可隔离、可检查并可复用到 CI 的环境。**

它在对象组合上有好处：AWS 是大公司，Dagger 是新型交付引擎，二者关系真实。但有两个代价：

1. 这仍是 **Dagger 单方的集成指南**，不能写作 AWS/Dagger 的联合产品。
2. Deck 中已有一页 AWS DevOps Agent 的占位页。若 AWS 仍作为 Release Management/Operations 对象，Kiro 会在品牌层面分散叙事。

因此 Kiro 只在“要选择 AWS 但又希望与 Dagger 同层、且主张是 Agent workspace”时推荐。

## 备选二：Dagger Container Use + OpenAI Codex

### 已证实的连接

Container Use 官方指南提供 OpenAI Codex 配置：在 `~/.codex/config.toml` 加入 `container-use` 的 stdio MCP server，并可把 Agent rules 写入 `~/.codex/AGENTS.md`。 [Container Use integrations](https://github.com/dagger/container-use/blob/main/docs/agent-integrations.mdx)（`main`，2026-08-03 访问）

### 为什么不优先

技术链与 Claude Code 相同，且 Codex 的 Agent/MCP 属性明确；但当前 Deck 的 CLI/MCP 页已把 Codex CLI 作为 headless coding-agent CLI 的代表。若二者同页却不改变问题，就会变成“Codex 又接了一个 MCP server”。

只有把问题锁定为：**“Coding Agent 输出不应只是 host filesystem 的 diff，而应是可进入 CI 的可编程环境 + Git 分支”**，Codex + Dagger 才能比现有 CLI/MCP 页多出新的工程洞察。

## 仅作技术背景，不建议成为第二对象

### Dagger 原生 LLM + Functions/Env

Dagger 文档展示 `LLM` 在 `Env` 内调用 `Container`/Function，得到完成工作后的 environment；也可将 Dagger Module 暴露为 MCP server。它是本页的**Dagger 侧证据**，不是另一个公司对象。把“Dagger LLM Primitive”和“Dagger”分成两家公司/两栏，会失去对照关系。 [LLM Integration](https://docs.dagger.io/features/llm/)

### Dagger + GPTScript（历史 Demo）

2024-04-29，Dagger 演示 GPTScript 使用 OpenAI 相关能力探索 Dagger API、结合 Daggerverse Functions 动态组装 CI pipeline。它说明“自然语言 → Function 组合”早于当前 native LLM API 出现，但官方文中明确是 meetup demo，未发现稳定产品合同、采用实例或可复核效果。 [Using Dagger and GPTScript](https://dagger.io/blog/dagger-and-gptscript/)（2024-04-29）

### Dagger + Docker Model Runner

Dagger 2025-04-30 的官方文章给出 Docker Model Runner 本地模型和 Dagger LLM tool calling 的配置。它可作为“模型可本地部署、执行仍由 Dagger 承担”的部署备选；但不提供 coding-agent 任务编排、CI 工作区或 pipeline gate 机制。 [Create Local AI Agents with Dagger and Docker Model Runner](https://dagger.io/blog/docker-model-runner/)（2025-04-30）

## 排除项：未找到足够的一手证据

| 对象 | 核验结果 | 排除原因 |
|---|---|---|
| **LangGraph** | 本轮未找到 Dagger 官方文档、Dagger 官方 GitHub 仓库或 LangChain/LangGraph 官方资料证明两者有集成。 | 两者可以在代码层组合，不应写成生态连接。 |
| **Temporal** | Dagger 的 2023 Discern case study 提到该客户应用使用 Temporal；这不是 Dagger 与 Temporal 的产品或 Agent 集成。 | 客户技术栈共现不能证明平台连接。 |
| **E2B / Daytona / Runloop** | 本轮未找到任一方官方文档或 GitHub 源码证明与 Dagger 有集成。 | 都属于可替代/相邻的 agent sandbox 类别，不能拿来和 Dagger 组成“已实践组合”。 |
| **Buildkite / Develocity / EngFlow** | 可与 Dagger形成执行图、验证选择或远程执行的分析对照，但公开材料不证明 LLM 直接参与它们与 Dagger的组合。 | 若这一页明确要求 LLM，不能再将其作为主对象。 |

## 最终排序与页面建议

1. **Dagger + Claude Code（通过 Container Use）——首选。**
   - 叙事：`任务意图 → 可编程 Agent Environment → 代码/构建/测试 → Git branch → 同构 CI 验证`。
   - 页面标题：**“Agent 的工作区，正在变成 CI 可执行的交付单元”**。
   - 对象表述需准确：不是“Dagger 与 Anthropic 联合平台”，而是“Dagger 的 Container Use 为 Claude Code 提供公开 MCP 适配”。

2. **Dagger + Kiro（通过 Container Use）——大公司备选。**
   - 叙事相同；价值是 AWS 品牌与 coding-agent 场景，而非 AWS DevOps Agent 的 release test。
   - 只建议在 AWS 那一页不再主讲 release/operations 时采用。

3. **Dagger + Codex（通过 Container Use）——技术上成立、叙事需更克制。**
   - 适合突出“Agent workspace → CI parity”，不适合突出 MCP 协议。
   - 与现有 CLI/MCP 页重叠风险最高。

## 业界认可与采用度专项核验（2026-08-03）

### 判断口径

本节不以网站流量、融资、GitHub Star 或无来源榜单衡量“认可”。每个对象只记录以下四种可核验信号：

1. **公开企业客户/案例**：厂商公开具名客户并说明使用场景；
2. **真实生产使用**：公开说明在真实工程、运行时或客户工作负载中运行，而非 benchmark/demo；
3. **生态集成**：在产品文档中有可执行的 Agent/CI/API 集成，而非声称“兼容”；
4. **产品状态**：GA/Preview/early development 等由原始发布材料或当前文档明示。

厂商案例和厂商自述的数字仍属于第一方说法；它们能证明“有公开采用/部署”，不能直接证明行业平均效果。

### 四个候选的采用证据

| 对象 | 公开客户 / 生产信号 | 生态与 CI/CD 机制 | 状态 | 对 Dagger 的实际关系 |
|---|---|---|---|---|
| **Claude Code / Anthropic** | Anthropic 的 Rakuten case study 称工程团队在复杂开源重构任务中持续自主编码 7 小时，并将 Claude Code 用于单测、API mock、PR review 等；Ramp case study 称其开发团队接入测试框架，缩短 development-test loop；产品页列 Stripe 在 1,370 名工程师中部署 Claude Code。 [Rakuten](https://www.anthropic.com/customers/rakuten) [Ramp](https://www.anthropic.com/customers/ramp) [Claude Code product](https://www.anthropic.com/product/claude-code) | Anthropic 官方确认 Claude Code 配置 MCP server；Dagger Container Use 对 Claude Code 给出实际 MCP 配置和只信任 Container Use tools 的 allowlist。 | Claude Code 当前为公开产品；本轮未发现其整体 Preview 标签。Dagger Container Use 本身为 **early development**。 | **强。** Dagger 开源并维护 Container Use，公开演示 Claude Code 通过其 MCP server 创建 Dagger 驱动的 Agent environment。 |
| **Cognition Devin** | Cognition 称其内部在一周合并 659 个 Devin PR；Mercedes-Benz 正在全球工程团队部署 Devin/Windsurf；Cognition 另列 Infosys、Cognizant、Itaú 等部署案例。 [Cognition internal](https://cognition.com/blog/how-cognition-uses-devin-to-build-devin) [Mercedes-Benz](https://cognition.com/blog/mercedes-benz-cognition) [More Devins](https://cognition.com/blog/series-d) | Devin 内部材料展示 API 可由 crash log、bug report、deployment failure 触发会话并产出 PR；这是 agentic engineering/CI 邻接能力。Dagger 还公开让 Devin 处理 Dagger issue、调用其容器化 build/test 环境运行本地 CI，并形成已合并 PR。 [Dagger + Devin case](https://dagger.io/blog/new-ai-developer-devin/) [PR #9130](https://github.com/dagger/dagger/pull/9130) | Devin 于 2024-12-10 由 Cognition 宣布 GA；当前企业部署文档覆盖 SaaS、Dedicated SaaS、Customer-hosted VPC。 [GA announcement](https://cognition.com/blog/devin-generally-available) [enterprise deployment](https://docs.devin.ai/enterprise/deployment/overview) | **真实共同实践，非产品集成。** 证据支持 Dagger 团队的一次真实使用和已合并贡献；未发现标准 connector、联合产品或客户规模。 |
| **Depot CI / Depot Remote Agents** | Depot 的 PlanetScale case study 说明其使用 Depot CI 与 runner/build/cache，CI 约 2x 更快；该证据证明 Depot CI 的工程采用，不证明 Agent CI 已被该客户采用。 [PlanetScale](https://depot.dev/customers/planetscale) | Depot CI 已 GA；官方提供面向 Claude Code/Cursor 的 `run → status/logs → ssh → fix → rerun` agent loop，能够在未提交本地工作树上跑 CI。另有当前仅支持 Claude Code 的 Remote Agents。 [GA](https://depot.dev/changelog/2026-03-24-depot-ci-now-available) [coding-agent loop](https://depot.dev/docs/ci/how-to-guides/coding-agents) [remote agents](https://depot.dev/docs/agents/overview) | **Depot CI GA**（2026-03-24）。Remote Agents 文档没有单独给出 GA/Preview 标签，且当前仅 Claude Code。 | **弱且不宜采用。** Depot 仍有 Dagger Engine on GitHub Actions 的官方连接页，但页首已明示该支持 deprecated、将来移除；不能作为新页面的长期组合。 [Dagger integration status](https://depot.dev/docs/github-actions/integrations/dagger) |
| **Runloop** | Runloop 公开称 Trajectory 持续运行 10,000+ burst concurrent Devboxes，用于模型训练与微调；另有 ION 的生产 Agent 客户案例入口。 [Trajectory](https://runloop.ai/blog/runloop-trajectory-launch-partner-announcement) [ION index entry](https://runloop.ai/blog) | Runloop 将 Devboxes、Axons 和 Broker 用于 Agent session/runtime；Remote Agents SDK 在 2026-05-05 宣布 GA，适用于可观测、可回放的 production agent systems。其文档列举 PR response、test generation 等 software-engineering agent 用例。 [GA announcement](https://runloop.ai/blog/axons-remote-agent-sdk-launch) [product docs](https://docs.runloop.ai/docs/overview/what-is-runloop) | Remote Agents SDK / Axon / Broker 为 **GA**；不同 Devbox、Gateway、benchmark 功能应按各自文档核验。 | **未证实。** 本轮未找到 Dagger、Runloop 或双方 GitHub 证明直接集成。两者都提供 agent environment，不等于存在组合。 |

### 对采用证据的解释

- **Claude Code** 是四者中最能同时证明“外部企业采用 + Agent 编码/测试参与 + 与 Dagger 的实现级连接”的对象。Rakuten/Ramp/Stripe 资料均为 Anthropic 第一方，足以证明公开部署，但其生产收益不可外推；当前仍缺独立客户采用 Container Use + Claude Code 的证据。
- **Devin** 的企业采用与复杂工程案例最强之一，尤其是 Mercedes-Benz 的全球部署和 Cognition 自身 PR 产出；它与 Dagger 也存在一次可复核的真实共同实践，但没有产品化 connector。若页面优先级是“已发生的联合使用 + 对象获得业界采用”，Devin 应排在 Claude Code 前；若优先级是“当前实现级适配”，Claude Code + Container Use 更强。
- **Depot** 的 CI 产品成熟度和客户采用证据均不错，且其 coding-agent loop 比一般 sandbox 更直接进入 CI。但 Dagger Engine 集成已被 Depot 标为 deprecated，故“Dagger + Depot”在技术关系上反而落后于“Dagger + Claude Code”。
- **Runloop** 有比一般 demo 更强的规模运行实例和 GA 的 agent runtime，但公开证据仍不能跨过“与 Dagger 有集成”这一步。它是环境类替代/对照，而非 Dagger 的配套对象。

### 综合排序：直接关系 × LLM 参与 × CI/CD 工程能力 × 业界认可

评分为研究筛选用的四档相对判断（0–5），不是市场份额或产品能力分。**直接关系是硬门槛**：总分相近时，没有直接关系者不能取代已证实的组合。

| 排名 | 候选组合 | 与 Dagger 直接关系 | LLM 明确参与 | CI/CD 工程能力 | 业界认可证据 | 结论 |
|---:|---|---:|---:|---:|---:|---|
| **1** | **Dagger + Devin** | **4**：有真实共同使用与已合并 PR，但无标准 connector | **5**：自主 coding agent | **5**：在 Agent session 内调用本地 CI、验证并迭代 | **5**：具名全球企业部署、客户侧采用与内部生产信号 | **本项目首选：同时满足业界认可与已发生的共同作业。** |
| **2** | **Dagger Container Use + Claude Code** | **5**：Dagger 自己维护的 runtime/MCP server 与可执行配置 | **5**：Claude Code 的 MCP tool-using coding agent | **4**：环境、分支、构建/测试与 CI parity | **5**：多个具名企业工程案例及公开部署 | 实现级连接最强，但 Container Use early development，且没有组合采用证据。 |
| 3 | Dagger + Depot CI/Remote Agents | 1：旧连接存在但已 deprecated | 4：Claude/Cursor loop；Depot 本身不提供基础模型 | **5**：本地 CI、日志、SSH、重跑，Depot CI GA | 4：具名 CI 客户案例 | 不选：直接连接不可持续，不能以能力/客户补偿路线风险。 |
| 4 | Dagger + Runloop | 0：未发现连接 | 4：agent runtime，可承载不同模型/SDK | 3：软件工程 Agent 基础设施而非 CI engine | 3：公开生产案例和大规模 workload | 不选：应作为 Dagger 的运行环境类竞争/对照，而非共同方案。 |

### 对页面选择的更新建议

采用度核验改变了只按“实现级连接”得到的排序：

> **若优先“当前实现级适配”，推荐 `Dagger + Claude Code（由 Container Use 连接）`；若优先“业界认可 + 已发生的联合使用”，推荐 `Dagger + Devin`。本项目最终采用后一个口径。**
>
> 对本项目而言，Devin 的真实 Dagger 使用案例比“只有配置、没有联合采用”的组合更适合作为主证据；Claude Code + Container Use 保留为 Dagger 后续产品化探索的旁证。

不要把这一页写成“Dagger 的 MCP 集成清单”，也不要把 Claude Code 或 Devin 的企业客户成果归因给 Dagger。两条能成立的因果链分别是：**Claude Code 的并行代码生成增加对独立工作环境的需求，Dagger 的 Container Use 提供一种公开、可编程且可与 CI 同构的环境实现；Devin 在 Dagger 项目中的实际贡献则证明，Agent 可以消费项目已有的容器化 build/test 合同，在 PR 前完成验证与迭代。**

## 来源清单

| 来源 | 类型 | 发布日期 | 访问日期 | 用途 |
|---|---|---:|---:|---|
| [Dagger LLM Integration](https://docs.dagger.io/features/llm/) | Dagger 官方文档 | 未标 | 2026-08-03 | `LLM`、`Env`、Function tool use、Module/MCP、Provider。 |
| [Introducing the LLM Primitive](https://dagger.io/blog/llm/) | Dagger 官方博客 | 2025-04-23 | 2026-08-03 | LLM Primitive、Experimental 状态、Dagger runtime。 |
| [Container Use announcement](https://dagger.io/blog/agent-container-use/) | Dagger 官方博客 | 2025-06-14 | 2026-08-03 | Container Use、parallel environment、Git branch、CI parity、early development。 |
| [Container Use README](https://github.com/dagger/container-use) | Dagger 官方 GitHub | 未标 | 2026-08-03 | MCP server、Dagger powered、early-development 状态。 |
| [Container Use agent integrations](https://github.com/dagger/container-use/blob/main/docs/agent-integrations.mdx) | Dagger 官方 GitHub | 未标（`main`） | 2026-08-03 | Claude Code、Kiro、Codex、Copilot 等实际配置。 |
| [Container Use `dagger.json`](https://github.com/dagger/container-use/blob/main/dagger.json)、[`go.mod`](https://github.com/dagger/container-use/blob/main/go.mod) | Dagger 官方 GitHub 源码 | 未标（`main`） | 2026-08-03 | 实现依赖 Dagger 与 MCP library。 |
| [Container Use latest release API](https://api.github.com/repos/dagger/container-use/releases/latest) | GitHub API / Dagger 官方仓库 | 2025-08-19 | 2026-08-03 | 最新 Release v0.4.2、非 prerelease。 |
| [Anthropic Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code/cli-usage)、[MCP](https://docs.anthropic.com/en/docs/mcp) | Anthropic 官方文档 | 未标 | 2026-08-03 | Claude Code 对 MCP 的官方交叉核验。 |
| [AWS Kiro MCP configuration](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-real-time-coding-security-validation-by-using-an-mcp-server-with-kiro-and-other-coding-assistants.html) | AWS 官方文档 | 未标 | 2026-08-03 | Kiro 的 MCP 配置位置交叉核验。 |
| [Dagger + GPTScript](https://dagger.io/blog/dagger-and-gptscript/) | Dagger 官方博客 | 2024-04-29 | 2026-08-03 | 自然语言动态组装 CI 的历史 demo。 |
| [Dagger + Docker Model Runner](https://dagger.io/blog/docker-model-runner/) | Dagger 官方博客 | 2025-04-30 | 2026-08-03 | 本地模型 provider 接入。 |
| [Anthropic Rakuten](https://www.anthropic.com/customers/rakuten)、[Ramp](https://www.anthropic.com/customers/ramp)、[Claude Code product](https://www.anthropic.com/product/claude-code) | Anthropic 官方客户/产品页 | 页面各自标注 | 2026-08-03 | Claude Code 的具名企业使用、工程任务和公开部署信号。 |
| [Cognition internal Devin](https://cognition.com/blog/how-cognition-uses-devin-to-build-devin)、[Mercedes-Benz](https://cognition.com/blog/mercedes-benz-cognition)、[More Devins](https://cognition.com/blog/series-d) | Cognition 官方博客 | 2026-02-27 / 04-27 / 05-27 | 2026-08-03 | Devin 的内部 PR、具名企业部署和客户自述。 |
| [Depot CI GA](https://depot.dev/changelog/2026-03-24-depot-ci-now-available)、[coding-agent loop](https://depot.dev/docs/ci/how-to-guides/coding-agents)、[PlanetScale](https://depot.dev/customers/planetscale) | Depot 官方 changelog/docs/customer case | 2026-03-24 / 未标 / 未标 | 2026-08-03 | Depot CI 状态、Agent loop、CI 客户采用。 |
| [Depot Dagger integration](https://depot.dev/docs/github-actions/integrations/dagger) | Depot 官方文档 | 未标 | 2026-08-03 | Dagger Engine support 已 deprecated、future removal。 |
| [Runloop Remote Agents SDK GA](https://runloop.ai/blog/axons-remote-agent-sdk-launch)、[Trajectory](https://runloop.ai/blog/runloop-trajectory-launch-partner-announcement)、[Runloop overview](https://docs.runloop.ai/docs/overview/what-is-runloop) | Runloop 官方博客/文档 | 2026-05-05 / 2026-05-27 / 未标 | 2026-08-03 | Agent runtime GA、10,000+ Devboxes 工作负载和软件工程 agent 用例。 |

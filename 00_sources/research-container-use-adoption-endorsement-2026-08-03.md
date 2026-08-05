---
title: "Dagger Container Use 的公开采用、试用与生态背书审计（2026-08-03）"
tags:
  - research/cicd
  - company/dagger
  - llm/coding-agents
  - adoption-evidence
status: complete
as_of: 2026-08-03
confidence: medium
---

# Dagger Container Use 的公开采用、试用与生态背书审计（2026-08-03）

> [!warning] 结论边界
>
> 在本轮限定检索范围内，**未检索到可核验的企业生产采用、具名客户案例或量化生产成果**。这不等于“没有企业在用”；只表示截至 2026-08-03，公开一手材料不足以支持该主张。现有最强外部信号是 Goose 官方文档/博客的集成与教程、一个开源项目的项目级 Claude Code 规则，以及多个开发者公开复现、配置和问题报告。

## 写作提纲

1. 先定义采用证据的五级口径，防止把 MCP 配置写成客户采用；
2. 盘点可回到原始来源的公司、生态项目和个人信号；
3. 专项核验 Claude Code、Codex、Cursor、Goose、VS Code/Copilot 与 Zed；
4. 给出可用于汇报、以及必须避免的认可表述。

## 结论先行

**Container Use 已获得一个外部 Agent 生态项目（Goose）的官方集成内容与持续文档入口，并出现了项目级 Claude Code 使用规则和多位开发者的真实试用痕迹；但公开证据尚未跨过“早期生态工具”到“企业生产标准”的门槛。**

证据强度排序：

1. **C/D — Goose 官方生态支持**：Goose 官方站点在 2025-06-19 发布专文，当前文档将 Container Use 列为 extension，提供一键/CLI 安装、演示与 isolated-development tutorial；这是一方外部生态的明确推荐与集成，不是客户案例。
2. **B — 可复现的实际试用**：Pigmalion Software 的 lead developer 发布完整 Claude Code 实操并展示环境与 merge；一个开源项目把 Container Use 写入 Claude Code 贡献规则；Dagger 工程师、独立作者、GitHub users 和 NearMe 也公开了配置、运行或演示。
3. **C — 社区收录**：Awesome Claude Code 收录并向项目发出通知，属于社区目录认可，不能用作性能或采用规模证据。
4. **D — 多 Agent 安装文档**：Dagger 为 Claude Code、Codex、Cursor、VS Code/Copilot、Zed、Goose 等提供配置。它证明“可接”，不证明这些厂商认可、维护或客户实际使用。

## 证据分级与计入规则

| 级别 | 本文定义 | 可支持的表述 | 明确不能支持 |
|---|---|---|---|
| **A 生产采用** | 具名组织公开说明在真实工程/生产工作负载中使用，并说明范围或结果。 | “某组织公开采用”。 | 行业普遍采用、普遍收益。 |
| **B 明确试用 / 公开复现** | 原始文章、配置、issue/PR 或演示显示作者实际安装、配置、运行或排障。 | “有公开试用/复现信号”。 | 生产采用、成熟度背书。 |
| **C 官方 / 社区背书** | 外部产品官方文档、官方博客或经过明确维护的社区目录专门收录/讲解。 | “获得生态集成/社区收录”。 | 联合产品、厂商认证、客户采用。 |
| **D 仅兼容 / 安装文档** | 一方给出 MCP/配置步骤，或协议上可配置。 | “有公开配置路径”。 | 另一方厂商认可、真实运行。 |
| **E 二手报道** | 媒体、聚合站、转述文章，无新的可复核原始运行证据。 | “被二手报道”。 | 任何采用或背书结论。 |

除非另有明确证据，单个 GitHub issue、PR、个人配置和视频演示均最高只计 **B**。

## A. 生产采用：本轮未检索到证据

检索覆盖 Dagger 官方博客/活动、Container Use GitHub README、issues/PR、外部 Agent 官方文档、公开工程分享和个人原始配置。未发现以下任一类材料：

- 具名企业宣称在其日常研发、CI 或生产系统中部署 Container Use；
- 客户案例列出 Container Use 的使用范围、时长、工程指标或生产结果；
- Anthropic、OpenAI、Cursor、Microsoft/GitHub 或 Zed 对 Container Use 的联合发布、官方插件认证或客户案例；
- Dagger 对外披露 Container Use 的客户名册或生产运行指标。

**页面措辞**：只能写“公开采用证据尚缺”，不能写“业界尚未采用”。

## B. 明确试用 / 公开复现

| 对象与身份 | 原始来源（发布日期；访问日） | 试用/复现证据的原话含义 | 证据等级 | 局限 |
|---|---|---|---|---|
| **Kyle Penfound，Dagger 作者** | [Gemini CLI: First Impressions and Experiments with Parallelization](https://dagger.io/blog/gemini-cli/)（2025-07-03；2026-08-03 访问） | 文中称其“actual configuration”只保留 Gemini 的 `ReadManyFilesTool`，将其他文件和 shell 操作交给 `container-use`；这是 Dagger 团队成员的亲自试用。 | **B** | 创建者内部的第一方实验；文章标题即为 first impressions，不能当外部采用或生产运行。 |
| **Pigmalion Software / Ernesto Ponce（Lead Developer）** | [Guía esencial: Claude Code y Container Use](https://pigmalion.co/blog-claude-code-y-container-use/)（2025-08-25；2026-08-03 访问） | 公司技术博客给出完整 Claude Code + Container Use 操作：prompt 创建 API tests/controllers，`cu list` 显示 `devoted-squirrel` 环境，最后执行 `cu merge`。 | **B** | 是外部软件公司的公开教学复现，证明可跑通而非披露其客户项目、生产部署或效益。 |
| **`thetom42/perplexica-mcp` 开源项目** | [README](https://github.com/thetom42/perplexica-mcp/blob/main/README.md)（仓库创建 2025-05-31、最后代码推送 2026-01-20；2026-08-03 访问） | README 明定：若用 Claude Code 开发，所有 file/code/shell operations “requires the use of the `container-use` MCP server”；贡献说明要求提供 `container-use log/checkout`。 | **B** | 这是个人开源项目的贡献工作流，不是公司或生产系统采用；README 自称的 “Production Ready” 指它自己的 MCP server，不能转写为 Container Use 已生产验证。 |
| **Tomioka，独立技术作者** | [Using Containers as Claude Code's Development Environment](https://zenn.dev/tomioka/articles/77076660064cdc?locale=en)（页面构建时间 2025-07-08；2026-08-03 访问） | 文章记录 `brew install dagger/tap/container-use`、`container-use version 0.2.0`、`claude mcp add` 的成功输出，并让 Claude Code 创建脚本后用 `cu` 检查环境日志。 | **B** | 个人可复现教程；仓库为示例，未披露业务场景或长期效果。 |
| **NearMe 技术分享（Kaito Asahi）** | [并列で生成AIにコーディングをやらせる](https://speakerdeck.com/nearme_tech/parallel-ai-coding)（2025-07-04；2026-08-03 访问） | NearMe 技术学习会的资料给出安装、Gemini CLI MCP 配置和让多个 Agent 并行工作的步骤，并将其描述为建议的未来工作流。 | **B** | 公司技术分享证明公开演示/复现；文中是“提案未来 workflow”，没有证明 NearMe 已在生产研发流程落地。 |
| **`mwotton`，GitHub 用户** | [Issue #305: can't run codex inside a container](https://github.com/dagger/container-use/issues/305)（2025-09-15；2026-08-03 访问） | issue 附上真实 `.container-use/environment.json`：`npm install -g @openai/codex`，并在 `cu terminal` 中运行 Codex 时遇到 ANSI terminal 问题。 | **B** | 证明一次 Codex + Container Use 的实际试用和故障；GitHub handle 未提供可核验的公司身份，且结果是问题而非成功交付。 |
| **`marcindulak`，GitHub 用户** | [Issue #303](https://github.com/dagger/container-use/issues/303)（2025-09-12）与 [Issue #320](https://github.com/dagger/container-use/issues/320)（2025-10-10；均 2026-08-03 访问） | 作者描述将 rules 加入 `~/CLAUDE.md` 和项目 `CLAUDE.md`、观察 Claude 是否调用 Container Use，并提供实际环境日志和 `v0.4.2` 版本。 | **B** | 证明反复使用 Claude Code + Container Use；没有项目名、组织、成功率或生产影响。 |
| **`5tefan`，GitHub 用户** | [PR #352](https://github.com/dagger/container-use/pull/352)（2026-06-12；2026-08-03 访问） | 作者称亲自走过 Zed 集成流程：旧 extension 已不维护且不可用，但通过 Zed settings 配置“easy and works”。 | **B** | 证明手工 Zed 配置试用；不代表 Zed 官方 extension 或 Zed 厂商背书。 |
| **John Lindquist，公开 GitHub 配置作者** | [Issue #253](https://github.com/dagger/container-use/issues/253)（2025-07-25）与 [`.zshrc` Gist](https://gist.github.com/johnlindquist/4e23bb7a7371212cd35af04e6cb4278d)（2025-10-03；均 2026-08-03 访问） | issue 提供 Claude `UserPromptSubmit` hook，并称其让自己可以 “reliably invoking the container-use mcp”；后续公开配置将它加入 Claude research workflow、设定 allowlist 和生命周期清理。 | **B** | 是开发者个人工作流，不含业务项目、规模或长期成果。 |
| **`AwesomeBuilder`，GitHub 用户** | [Issue #261](https://github.com/dagger/container-use/issues/261)（2025-08-01；2026-08-03 访问） | 记录在 macOS 通过 Homebrew 安装 `v0.4.0`，执行 Claude MCP 添加命令并遇到连接失败。 | **B（失败试用）** | 只证明安装/配置尝试，不能写为成功使用。 |

### 对 B 类证据的解释

这些材料说明 Container Use 不是只有 README 的概念样例：它至少被用于 Claude Code、Codex、Gemini CLI 和 Zed 的公开试用、配置、排障或演示。反过来，issue 的数量和问题本身也揭示早期产品摩擦，不能把“有人报 issue”直接解释为大规模活跃采用。

## C. 官方生态背书与社区认可

| 对象 | 原始来源（发布日期；访问日） | 明确内容 | 证据等级 | 边界 |
|---|---|---|---|---|
| **Goose 官方项目** | [Isolated Dev Environments in Goose with container-use](https://goose-docs.ai/blog/2025/06/19/isolated-development-environments/)（2025-06-19；2026-08-03 访问） | 页面作者 Michael Neale 标为 Principal Engineer，称 Container Use “brings … directly into your Goose workflow”，给出安装和 branch/container 的实际操作。 | **C + D** | 是外部 Agent 生态项目的官方内容与配置支持；其“Real-World Use Cases”是用例说明，不是 Goose 客户生产案例。 |
| **Goose 官方文档与扩展目录** | [Container Use Extension](https://goose-docs.ai/docs/mcp/container-use-mcp/)、[Isolated Development Environments tutorial](https://goose-docs.ai/docs/tutorials/isolated-development-environments/)、[Extensions](https://goose-docs.ai/extensions)（页面未标发布日期；均 2026-08-03 访问） | 当前官方 docs 将其列为 Container Use Extension，提供 Goose Desktop/CLI 安装流程、demo，以及“自动创建和管理容器”的教程链接。 | **C + D** | 能表明持续可发现的生态支持；没有说明 Goose 团队维护 Container Use 本身，也没有企业使用量。 |
| **Awesome Claude Code 维护者 `hesreallyhim`** | [Issue #255](https://github.com/dagger/container-use/issues/255)（2025-07-29；2026-08-03 访问）及 [collection entry](https://github.com/hesreallyhim/awesome-claude-code) | 维护者通知项目已被收录为 Claude Code 生态资源，Dagger 随后添加 badge。 | **C（社区目录）** | curated list 是发现性信号，不是 Anthropic 官方认证，不证明该清单用户实际安装。 |

## D. 专项客户端核验：兼容不等于采用

| 客户端/生态 | 已核验的正向连接 | 最高可计等级 | 未找到或反证 | 可安全写法 |
|---|---|---|---|---|
| **Claude Code / Anthropic** | Dagger 的 [integration guide](https://container-use.com/agent-integrations) 与 [quickstart](https://container-use.com/quickstart) 给出 `claude mcp add container-use -- container-use stdio`；Pigmalion、`perplexica-mcp`、Tomioka、`marcindulak`、John Lindquist 等公开了实际试用或项目规则。 | **B** | 本轮未检索到 Anthropic 官方文档、官方博客或客户案例提及 Container Use。 | “Dagger 提供 Claude Code 配置，且有外部公开复现与项目级使用信号。” |
| **OpenAI Codex** | Dagger guide 给出 `~/.codex/config.toml`；`mwotton` 的 issue 有实际安装 Codex 到 Container Use 环境的配置。 | **B** | 本轮未检索到 OpenAI 官方页面、客户案例或联合发布。 | “Codex 有公开配置路径与一次故障型试用证据。” |
| **Cursor** | Dagger guide 有 Cursor deeplink、MCP config 和 `.cursor/rules/container-use.mdc`；[PR #333](https://github.com/dagger/container-use/pull/333) 修过 Dagger 一侧安装链接。 | **D** | 本轮未找到 Cursor 官方提及、独立公开运行记录或客户案例。 | “Dagger 文档支持 Cursor 配置；采用未获公开验证。” |
| **Goose** | 官方博客、extension 文档、教程与扩展目录均明确呈现 Container Use；有 Desktop deeplink/CLI 方法。 | **C + D** | 未找到 Goose 客户在生产工程中使用它的公开案例。 | “Goose 官方把 Container Use 作为可安装 extension 进行讲解。” |
| **VS Code / GitHub Copilot** | Dagger guide 提供 VS Code `mcp.servers` 和 Copilot instructions；[PR #353](https://github.com/dagger/container-use/pull/353) 的作者说明在 Copilot CLI `v1.0.65` 做过配置验证，但截至查询 PR 仍 open。 | **B（PR 内试验）+ D** | 未找到 Microsoft/GitHub 官方提及或客户案例。 | “Dagger 已给出 VS Code/Copilot 配置；有社区贡献者验证过 CLI 配置，不等于 GitHub 官方集成。” |
| **Zed** | Dagger guide 给出 `settings.json` 手工配置；`5tefan` 公开称该路径可用。 | **B + D** | [PR #352](https://github.com/dagger/container-use/pull/352) 明称旧的第三方 Zed extension 已不维护且不可用；未找到 Zed 官方背书。 | “有手工 Zed 配置的公开复现；旧第三方 extension 不应作为当前集成能力。” |

## E. 二手报道：只作检索线索，不进入采用结论

搜索发现媒体/聚合页（例如 MarkTechPost、MCP 目录、评测站）转述 Container Use 可配 Claude Code、Cursor、Goose 等。这些材料大多复述 Dagger README 或文档，未新增可验证的客户、运行或厂商背书事实，故不作为本页结论的证据。

- [Agentic DevOps podcast: Agentic CI/CD with Solomon Hykes](https://agenticdevops.fm/episodes/agentic-ci-cd-with-solomon-hykes-of-dagger)（2025-07-14；2026-08-03 访问）是独立节目对 Dagger/Docker 联合创始人的访谈，扩大了行业曝光，但核心机制说明仍来自 Dagger 方，故最多记 **E**，不是第三方客户验证。

## 对“业界认可”的准确表述

### 可用

> **Container Use 已进入 Goose 的官方 extension/教程体系，并有 Claude Code、Codex、Gemini CLI 与 Zed 的公开试用和复现信号；它仍是早期开发的开源项目，尚缺具名企业生产采用证据。**

### 不可用

- “Anthropic / OpenAI / Cursor / Zed 官方支持 Container Use。”
- “Claude Code 与 Dagger 已被企业广泛联合采用。”
- “Container Use 是成熟的企业级 Agent sandbox 标准。”
- “Goose 的用例说明证明其客户在生产中运行 Container Use。”
- “GitHub stars、issues、Awesome list 证明市场采用规模。”

## 对 Dagger × Claude Code 页面选择的影响

这份专项并没有改变其技术主张，但改变了页面的**认可叙事**：

- 可把 Claude Code 作为“并行 coding agent 的典型触发者”，并使用其独立企业采用资料；
- 可把 Goose 的官方 extension 内容作为 Container Use 得到外部生态认可的最强旁证；
- 不应再寻找或暗示“Claude Code × Container Use 的企业客户”作为页面支撑；
- 页面成熟度仍应标 `Container Use: early development`，证据标签应是 `公开试用 + Goose 官方生态集成`，而非 `企业生产采用`。

## 来源审计与检索限制

| 来源族 | 访问日期 | 作用 | 限制 |
|---|---|---|---|
| Dagger 官方博客、Container Use README/Docs | 2026-08-03 | 核验项目状态、官方演示和一方集成路径。 | Dagger 自述不能证明外部采用。 |
| Container Use GitHub issues / PRs | 2026-08-03 | 核验真实安装、配置、故障、贡献和客户端试验。 | GitHub identity/项目背景常不可知；issue 不等于生产部署。 |
| Goose 官方博客、文档和扩展目录 | 2026-08-03 | 核验外部生态项目的官方收录、教程与安装入口。 | 官方教程是生态背书，不是用户数或客户成效。 |
| 公司技术分享与个人原始文章/配置 | 2026-08-03 | 捕捉公开复现与实际配置。 | 不外推为所属公司的全公司实践。 |
| 二手报道 / 聚合站 | 2026-08-03 | 仅用于发现一手线索。 | 未用于采用结论。 |

---
title: "Dagger 的 LLM Agent 执行环境搭配候选：从固定 CI Job 到可持续的 Agent Workcell"
tags:
  - research/cicd
  - research/llm
  - research/dagger
  - comparison/platform-selection
status: complete
as_of: 2026-08-03
confidence: medium
scope: "仅使用 Dagger、E2B、Runloop、Daytona、Docker、Coder、Modal 的官方文档、官方博客或官方 GitHub；判断其是否可作为 Dagger 的并列洞察对象。"
---

# Dagger 的 LLM Agent 执行环境搭配候选

> [!warning] 研究输入，不是正式页面
>
> 本文件只完成“第二对象”筛选。不存在官方 Dagger connector 或联合参考架构时，文中一律写为**架构互补推断**，不能改写成产品集成或客户采用事实。

## 写作提纲

1. 先分清 Dagger 已经解决了什么，避免再找一个同层的容器执行器；
2. 用 Agent 的工作单元（workspace / Devbox / Sandbox）是否可创建、分叉、暂停、恢复、并行及回到 PR/CI 作为筛选标准；
3. 评估它与 Dagger 是直接集成、同层竞争还是上下游互补；
4. 给出适合一页 PPT 的主张，并排除“只是安全沙箱”的候选。

## 结论先行

**若要为 Dagger 寻找新的、明确面向 LLM coding agent、又不落入“测试生成”或“CI 自愈”的第二对象，首选是 Runloop，而不是 AWS/Meta，也不是另一个通用容器平台。**

原因是两者的能力边界可形成一个新的 CI/CD 洞察：

> **Dagger 将稳定的 build/test/release 动作编译为可组合、可缓存的函数图；Runloop 将不适合塞进单次 CI Job 的 Agent 工作，组织成可挂载代码与 Agent、可暂停恢复、可继续响应 PR 反馈的 Devbox。**

这页的中心不是“沙箱更安全”，而是**Agentic CI 的执行单元正在分化**：确定性动作仍应是短生命周期、可复现的 function/DAG；复杂的 Agent 工作则需要带代码状态、工具状态、浏览器/预览和人类反馈的长生命周期 workcell。

但必须明确：**没有找到 Dagger 与 Runloop 的官方原生 connector、联合客户案例或互相调度证据。** 因而它只能讲“可借鉴的分层架构”，不能讲“Dagger 已集成 Runloop”。

如果页面要求“已存在直接关系”，唯一强证据是 **Dagger Container Use × Claude Code（MCP）**；但 Container Use 仍在 early development，而且更像 Dagger 自身的产品证明，不适合作为第二家公司的正式对象。

## 共同基线：Dagger 已经提供的 Agent 执行语义

Dagger 的当前官方文档明确：其 `LLM` 是 workflow 内的原生类型；LLM 可在 `Env` 中发现和调用 Dagger Functions。`Env` 可输入 Directory、Container 和模块，Function 的文档会作为模型可理解的工具说明；Agent loop 的输出仍是环境中的类型化对象。 [Dagger LLM Integration](https://docs.dagger.io/features/llm/)

因此 Dagger 已覆盖的是：

```text
声明交付动作 → 类型化 Function / Container → 组合为可并行、可缓存的执行图
                                          ↑
                                    LLM 可调用这些函数
```

它并不天然等同于“每个 Agent 任务都需要一个可持续数小时或数天、可等待 PR 评论后继续的远程工作区”。这正是下列候选的筛选焦点。

## 候选排名

| 排名 | 候选 | 官方可核验的 LLM/Agent 能力 | 与 CI/CD 的真实关系 | 与 Dagger 的关系 | 选择结论 |
|---|---|---|---|---|---|
| **1** | **Runloop Devboxes** | 代码 mount + agent mount；Agent 可执行 shell、Git、browser/computer tools；可 suspend/resume，利用同一 Devbox 处理 PR 评论 | 官方教程包括 Agent 修改 repo、创建 branch/PR、启动 preview 并贴回 PR；另有 GitHub Action 部署 Agent | **互补（架构推断）**：Dagger 是确定性 delivery function graph；Runloop 是长生命周期 Agent workcell | **首选**。最能讲“Agent job 的状态模型变化”，非测试算法、非自愈。 |
| **2** | **E2B Sandboxes** | 为 AI Agent 提供隔离执行，支持 clone repo、执行不受信任代码、运行 test | 官方给出 GitHub Actions 在每个 PR 触发 LLM review/test/validation 的示例 | **互补但更接近同层**：Dagger 可编排交付动作，E2B 可承载每次 PR 的 Agent 运行；无原生集成 | CI 关联最直接，但能力偏短任务 sandbox，容易退化为基础设施页。 |
| **3** | **Daytona Sandboxes** | 明确支持 Claude Managed Agents、OpenAI Agents SDK；容器/VM/GPU sandbox，snapshot、fork、pause/resume | 有 Agent 修 bug/开 PR 的官方指南，但未发现 Dagger 或通用 CI pipeline 的一手集成 | **同层相邻**：Daytona 管 Agent computer/workspace，Dagger 管 delivery functions | 状态语义最丰富；但 CI/CD 关系弱于 Runloop。 |
| **4** | **Coder Agents + Dev Containers** | Beta 自托管 coding agent；control plane 调 LLM，按需选 template/provision workspace；支持 sub-agents | 更接近企业开发工作区平台；文档未证实它是 CI pipeline runner | **上下游互补（推断）**：Coder 供 workspace，Dagger 供交付函数 | 企业平台故事完整，但 Beta 且容易滑向治理/研发环境话题。 |
| 5 | Docker Sandboxes | `sbx` 直接运行 Claude Code、Codex、Copilot、Cursor、Gemini、Kiro 等；持久 microVM、独立 Docker daemon | 本地开发/Agent workspace 为主；文档把 Docker-in-Docker 单列为传统 CI/CD 用例 | **同层竞争/相邻**：都围绕容器环境，未见联合机制 | 不推荐主讲：Early Access，且以环境隔离为主，重复基础设施与 CLI/MCP 页。 |
| 6 | Modal Sandboxes | 官方示例运行 LLM coding agent，并可批量启动 sandbox、运行 test/lint | 可在 CI 构建镜像，但核心是通用 AI/cloud compute | **下层通用计算**，不是 CI delivery model | 不推荐：规模与 GPU 是亮点，但偏基础设施。 |

## 首选详解：Dagger + Runloop

### 可被分别证明的机制

**Dagger：把稳定动作写成 Agent 可调用的交付函数。** `LLM.withEnv` 使模型在声明的 Container、Directory、Module 环境中自动发现并调用 Dagger Functions；Dagger 官方给出的 coding-agent 样例明确要求 Agent 建代码并验证可 build。 [Dagger LLM Integration](https://docs.dagger.io/features/llm/)

**Runloop：把 Agent 本身及其工程现场作为长期 workcell 管理。** Runloop 的 Devbox 是可由 API/SDK/CLI 管理的 AI Agent 执行环境；官方定义其既可作为短任务的临时 VM，也可以 snapshot、suspend、resume 以支持长任务。 [Devbox Overview](https://docs.runloop.ai/docs/devboxes/overview)

Runloop 的差异不只是“能执行 shell”：

1. `code_mount` 可在 Devbox 启动时把某个仓库/分支准备好；`agent_mount` 可在创建时将预配置 Agent 挂入环境。官方教程让 Agent 在该环境修改代码并创建 Git branch。 [Running Agents on Sandboxes](https://docs.runloop.ai/docs/tutorials/running-agents-on-sandboxes) [Mount AI Agents](https://docs.runloop.ai/docs/devboxes/mounts/agent-mounts)
2. Devbox 可 suspend/resume，保留代码变更、已装依赖与文件修改。官方示例的目的正是等待 PR review feedback 后继续，而不是重新建一个 Job。 [Suspend and Resume Workflow](https://docs.runloop.ai/docs/tutorials/running-agents-on-sandboxes/suspend-resume-workflow)
3. 同一套教程还将运行中的 app 通过 tunnel 作为 preview 链接贴到 PR；另一个工作流让 Agent 监听/处理 PR 评论并继续执行。 [Share a Live Preview](https://docs.runloop.ai/docs/tutorials/running-agents-on-sandboxes/share-live-preview) [Turn-Based Interaction with Agent](https://docs.runloop.ai/docs/tutorials/running-agents-on-sandboxes/turn-based-interaction)
4. Runloop 也有 GitHub Action 用于从 workflow 发布一个版本化 Agent。它证明 Agent 本身可以作为持续交付的版本化对象；它**不证明** Runloop 已替代 Dagger 或一个完整 CI control plane。 [Deploying Agents with GitHub Actions](https://docs.runloop.ai/docs/devboxes/agents/deploying-with-github-actions)

### 可讲的联合架构（明确为推断）

```text
代码 / PR / 任务
       │
       ├─ Dagger：build · unit test · package · publish
       │           类型化、可缓存、短生命周期的 Functions / DAG
       │
       └─ Runloop：Agent 工作单元
                   repo + agent + shell/browser + preview + state
                   snapshot / suspend / resume / PR feedback
       │
       └────────── 汇合为可审阅的分支、验证结果与后续交付动作
```

这张图表达的是**可借鉴架构**：将“哪些动作必须确定性、可复用、可缓存”与“哪些工作要由 LLM 在连续上下文中探索、执行、等待反馈”拆开。图中不应画 Dagger 调用 Runloop API，也不应写成已经存在的端到端产品方案。

### 最强共页 thesis

**标题：**

> **Agentic CI 的新执行单元，不再只有一次性的 Job**

**页面主张：**

> **Dagger 将稳定的交付动作收敛为可复用函数图；Runloop 将 Agent 的代码、工具和反馈现场收敛为可持续的 Devbox。CI 需要同时编排确定性任务与持续性 Agent workcell。**

这不是“又一个 Agent 沙箱”的页面。它讨论的是 CI/CD 的执行模型：传统 Pipeline 默认每个 Job 无状态、结束即销毁；Agent 处理多轮修改、预览与 PR 反馈时，工作状态本身成为需要被调度的工程对象。

### 不应这样写

- 不写“Dagger 集成 Runloop”或“Runloop 可以执行 Dagger pipeline”：未找到官方 connector/联合样例。
- 不写“Devbox 替代 CI runner”：Runloop 的官方材料证明 Agent 工作环境，不证明其是完整 CI controller。
- 不写“自动合并、自动发布”：上述教程显示 branch、PR、preview 和评论交互，不构成 release autonomy。
- 不把页题写成安全隔离；隔离是 Devbox 的实现条件，主角应是 Agent 工作状态与执行生命周期。

## 备选 1：E2B — 最直接的 PR-to-Agent-Sandbox-to-Test 链路

E2B 的官方 CI/CD 文档明确描述 GitHub Actions 在 PR 事件触发时运行脚本，脚本创建 E2B sandbox，clone repo，执行 `npm install` 与 `npm test`，再由 Agent 做 review/test/validation、报告结果。 [E2B GitHub Actions CI/CD](https://e2b.dev/docs/use-cases/ci-cd)

**它适合的主张**：

> **LLM 任务不必与 CI runner 共址：每个 PR 都可以临时获得一个独立的 Agent execution cell。**

与 Dagger 的关系依然只是架构互补：Dagger 更适合声明 build/test/release 的函数和依赖图；E2B 适合给 Agent 单独起 remote sandbox。官方未证实两者彼此调用。

**不作为首选的原因**：它的 PR/测试关联很强，但缺少“Agent 任务跨 PR feedback 继续、分叉或持久化”的鲜明状态模型；页面很容易变成“为 Agent 加一个隔离 runner”，会与既有基础设施页重复。

## 备选 2：Daytona — Agent workspace 的状态机更完整，但交付关联较弱

Daytona 是明确面向 AI agents 的 sandbox 平台：sandbox 可为容器、Linux/Windows VM 或 GPU，具有独立 filesystem、network 与 process；其 VM 可保留 memory 的 hot snapshot，且支持 fork、pause/resume。 [Daytona Sandboxes](https://www.daytona.io/docs/en/sandboxes/)

它已有明确的模型/Agent 接口：官方文档提供 Claude Managed Agents 的 BYOC sandbox 使用方式，也提供 OpenAI Agents SDK 的 Daytona Sandbox integration。 [Claude Managed Agents](https://www.daytona.io/docs/en/guides/claude/claude-managed-agents/) [OpenAI Agents SDK with Daytona](https://www.daytona.io/docs/en/guides/openai-agents/openai-agents-sdk-with-sandboxes/)

**优点**：若想讲“Agent 的工作状态可 fork”，Daytona 的技术表达最强，可用“同一任务从快照分叉多个实验分支”说明 parallel exploration。

**不作为首选的原因**：公开一手材料中，Daytona 与 GitHub PR/CI 的关联主要是教程和 Agent harness 集成，不如 Runloop 的 Agent→PR preview→comment feedback 作业流贴近交付；亦没有 Dagger 原生联动。

## 备选 3：Coder — 企业级 workspace control plane，但不适合做此页主角

Coder Agents 当前是 **Beta**。它的 Agent loop 在 Coder control plane，调用 Anthropic、OpenAI、Google、Azure、AWS Bedrock 或 OpenAI-compatible provider；只有当需要读写代码、执行命令时才根据 template 自动 provision workspace，并支持子 Agent 并发。 [Coder Agents](https://coder.com/docs/ai-coder/agents)

Coder 还可将 repo 内 `devcontainer.json` 转为 workspace image，形成可复现的 development environment。 [Coder Dev Containers](https://coder.com/docs/admin/integrations/devcontainers)

它的体系很适合说明“LLM 控制面与执行工作区分离”，但主体仍是企业开发环境供给和治理。若与 Dagger 同页，容易把题目从 CI/CD 的交付执行模型滑到研发工作台/合规，因此只作企业部署场景的备选。

## 不推荐主讲的对象

### Docker Sandboxes

Docker Sandboxes 的 `sbx` 可直接运行 Claude Code、Codex、Copilot、Cursor、Gemini、Kiro 等 coding agents；每个 sandbox 是含独立 Docker daemon、filesystem 和 network 的 microVM，模板和 sandbox 状态可持久化。 [Docker Sandboxes](https://docs.docker.com/ai/sandboxes/) [Supported agents](https://docs.docker.com/ai/sandboxes/agents/) [Architecture](https://docs.docker.com/ai/sandboxes/architecture/)

它与 Dagger 的 container execution 过于相邻，且 custom environments 标为 **Early Access**、kits 为 experimental。它适合“本地 Agent 如何运行 Docker build/test”的实践话题，不足以构成独立 CI/CD 洞察页。

### Modal Sandboxes

Modal 公开用 Sandbox 执行 LLM 生成的代码、checkout repository 后运行 test/lint，也提供 LangGraph coding-agent 示例；它可大规模启动 sandbox。 [Modal Sandboxes](https://modal.com/docs/guide/sandboxes) [Coding Agent example](https://modal.com/docs/examples/agent)

但这些事实把 Modal 定位为通用 serverless/GPU/AI compute，而非软件交付的执行图、PR 协作或 release 模型；与 Dagger 组合会更像“编排器 + 算力供应商”，不建议占用一页洞察。

## Dagger 自身的直接 LLM 关联：可作旁证，不能充当第二对象

Dagger 的 Container Use 通过 MCP 直接接入 Claude Code；它依赖 Docker 和 Git，用 Dagger primitives 编排并行的 agent environments。Dagger 官方同时标注其为 **early development**。 [Dagger Container Use](https://dagger.io/blog/agent-container-use/)

这证明 Dagger 自身已经在实践“Agent environment / branch / parallel work”的路线。但它不是独立公司或平台，且若作为第二对象会变成“一家公司讲自己的两个组件”。较适合作为 Dagger 页右下角的产品演进证据，而非双公司对照。

## 最终取舍

| 如果你要强调 | 选谁与 Dagger 并列 | 页面风险 |
|---|---|---|
| Agent 工作如何跨多轮 PR 反馈持续执行 | **Runloop** | 没有官方集成，只能讲分层架构 |
| 每个 PR 如何拉起 LLM review/test runtime | **E2B** | 容易退化为“隔离执行环境” |
| Agent workspace 如何快照、分叉和并行探索 | **Daytona** | CI/CD 关联偏弱 |
| 企业内部如何按 template 供给 Agent workspace | **Coder** | 容易偏向研发环境治理 |
| 已有 Dagger 的直接 LLM 接入证据 | **Claude Code + Container Use**（旁证） | 仍为 early development，且不是第二对象 |

**建议：选择 Dagger + Runloop，且将页面的主命题锁为“CI 的执行单元从 Job 扩展为 Function + Workcell”。**

这比 Dagger + AWS/Meta 更属于 CI/CD 与工程平台专家的领域：讨论的是如何承载、复用、恢复、并行和衔接 Agent 的工程执行，而不是判断测试案例本身的质量。

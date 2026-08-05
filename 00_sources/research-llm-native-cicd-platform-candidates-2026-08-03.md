---
title: "LLM 原生/LLM 适配 CI/CD 平台候选：从执行图到交付上下文（2026-08-03）"
tags:
  - research/cicd
  - research/llm
  - comparison/platform-selection
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# LLM 原生/LLM 适配 CI/CD 平台候选：从执行图到交付上下文（2026-08-03）

> [!warning] 研究输入，不是正式页面
>
> 本文只为 Dagger 的第二对象重新选题，不能替代该对象的 Deep Dive，也不使 Presentation `presentation_ready`。所有链接于 2026-08-03 访问。这里的“LLM 明确参与”严格指官方说明运行 Claude Code、Cursor、Gemini、Claude Sonnet 4 或明确的 Agentic AI；不能把传统 ML（例如 Predictive Test Selection）或仅有 MCP server 混写成 LLM 闭环。

## 写作提纲

1. 先把“LLM 进入 CI”拆成可核验的三种形态；
2. 核验 Develocity、CloudBees、GitLab、Nx、CircleCI 等候选的实际机制、阶段和限制；
3. 对照既有 GitHub、Harness、GitLab、CI 自愈、基础设施、CLI/MCP 页面，排除重复主题；
4. 给出适合与 Dagger 并列的一页主张，以及不应说的强断言。

## 结论先行

主研究另发现 Meta JiTTesting 的强一手候选后，本轮平台筛选的结论应调整为：**Develocity、GitLab、Nx、CircleCI 与 CloudBees 都不能在“LLM 机制明确性 + 不与现有五页重叠 + 公开成熟证据”三项同时超越 Meta JiTTesting。** 因此，若第七页的对象不被限定为“可购买的平台”，应优先把 Meta JiTTesting 推入独立 Deep Dive；本文件的候选只保留为对照和备选。

原因不在于它有 MCP，而在于 CloudBees Unify 已把来自 GitHub Actions、Jenkins、CloudBees Workflows 等系统的 runs、logs、components、test/security findings、workflows、feature flags 和 release state 汇入一个平台对象模型；官方开源的 DevOps Agent Kit 则明确以 **Claude Code 或 Cursor** 为 host，分别以 MCP 与 `gh` CLI 获取该模型和外部协作系统的上下文。其一手代码与文档同时给出了七个可复查的 Agent workflows：pipeline overview、failure triage、release status、flag rollout 等。 [CloudBees Agent Kit repository](https://github.com/cloudbees-oss/devops-agent-kit/) [CloudBees how-to](https://www.cloudbees.com/blog/how-to-build-devops-agent) [Unify MCP documentation](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server)

CloudBees Unify 仍是“平台为 LLM 改造交付对象模型”的**最佳商业备选**：它确实把跨工具状态归一，且 Agent Kit 明确运行 Claude Code/Cursor；但后者是 community reference/demonstration，而非 production-hardened 产品。因此它不能以“公开成熟 LLM CI 实践”胜过 Meta。Develocity 则是最佳“构建经济性 Agent”备选，但官方没有公开其 LLM 实现。

## 与 Meta JiTTesting 的硬对照

> Meta JiTTesting 的具体机制、规模和模型证据应以主研究的论文/官方材料为唯一正式来源；下表只判定本文件各平台候选能否超过它，避免重复摘录尚未由本子任务复核的 Meta 事实。

| 候选 | LLM 机制是否比 Meta 更明确 | 是否比 Meta 更不重复既有五页 | 公开成熟证据是否更强 | 结论 |
|---|---|---|---|---|
| Develocity Cache Optimizer | **否。** 仅公开 “AI agent/Agentic AI”，未披露 LLM、模型或 runtime。 | 部分是。它聚焦 cache-miss 性能优化，不同于自愈修代码。 | 产品文档强，但效果/模型透明度有限，且无 Dagger 联合证据。 | **不能超越。** 只适合作为“若强调商业产品”的备选。 |
| CloudBees Unify + Agent Kit | **部分是。** Claude Code/Cursor 被直接指明。 | 部分是。强调跨工具 delivery context model，而非自愈。 | **否。** Agent Kit 自称 demo/community reference、not production-hardened；跨工具产品能力不等同已成熟 Agent 实践。 | **不能超越。** 是“企业交付状态模型适配 LLM”的备选，不是第七页首选。 |
| GitLab Duo / Fix Pipeline | 是，官方列 Claude Sonnet 4。 | **否。** 与既有 GitLab 页和自愈页直接重叠。 | GA 文档强。 | **不能超越。** 成熟但无新增信息。 |
| Nx Self-Healing | 否，文档称 AI，未将 LLM 作为其机制事实。 | **否。** 当前 Slide 05 的主体。 | 官方机制强。 | **不能超越。** |
| CircleCI Chunk | 不足以确认强于 Meta；公开页以 AI Agent 为主，需另查模型事实。 | **否。** 已在自愈/基础设施中作为 Agent 内环。 | 厂商材料多于独立/论文型证据。 | **不能超越。** |
| Harness | 即使有 LLM/Agent 能力，也已是 Slide 02 主体。 | **否。** | 已有专题覆盖。 | **不能超越。** |

因此，若用户的目标是为已有 Dagger 页找一个**新的、明确 LLM 参与、可讲技术机制而非安全/协议**的对象，推荐序列是：**Meta JiTTesting 第一；CloudBees Unify 第二（仅在必须选择商业平台时）；Develocity 第三（仅在允许“Agentic AI、模型未公开”时）。**

## 先定义筛选口径

| 口径 | 什么算成立 | 什么不算成立 |
|---|---|---|
| LLM 明确参与 | 官方直接指定 Claude Code、Cursor、Gemini 或模型名；或官方代码把这些 Agent host 接到 CI/CD 数据与操作上 | 只写“AI-powered”、未披露模型或只提供传统预测模型 |
| 平台为 LLM 改造了对象/接口 | 将 pipeline、run、test、artifact、release/flag 等数据归一为可供 Agent 查询的领域对象，或把 Agent 的具体行动封装成可复查 workflow/skill | 只有一段聊天摘要、原封不动转发 raw log，或仅把现有 REST API 包了一层 MCP |
| CI 内真正行动 | Agent 读取构建/验证事实，提出或执行范围明确的 workflow、PR、配置或重跑动作 | “MCP 可调用”本身；工具可见性不等于 Agent 已被授权、已触发或已验证 |

## 候选排名

| 排名 | 候选 | LLM/Agent 的可核验事实 | 可讲的技术能力 | 与既有页面的关系 | 结论 |
|---|---|---|---|---|---|
| **1** | **CloudBees Unify + 开源 DevOps Agent Kit** | Kit 明确在 Claude Code / Cursor 中运行，Claude Code 通过 `CLAUDE.md`、skills、MCP 和 `gh` CLI 访问 Unify、Jira、Slack、GitHub；仓库可查而且明示 demo/reference，非正式产品。 [repo](https://github.com/cloudbees-oss/devops-agent-kit/) | 多 CI/CD 数据归一、跨工具失败归类、release-readiness、workflow/flag action；Unify MCP 官方支持 Claude Code/Gemini，且可 list/validate/update/trigger workflows。 [MCP docs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server) | 与 CLI/MCP 页只在**接入协议**重叠；本页的中心是上层的 **normalized delivery model / context plane**，而不是命令和协议取舍。与 Dagger 是执行图与交付状态图的相邻层。 | **首选**；LLM 明确，能力不是“聊天查日志”。但应在页脚标注 Kit 是社区参考项目、非生产硬化产品。 |
| **2** | **Develocity 2026.2 Build Caching Optimizer Agent** | 2026.2（2026-06-30）官方称该 Agent 能诊断、修复 build cache miss；定价页更具体地称其找到 avoidable miss、root-cause、作最小配置修改、fresh build 复验、开 PR。该产品同时有用于 Agentic AI 的 MCP 服务器。 [release](https://develocity.ai/releases/) [product/pricing](https://develocity.ai/pricing/) | 把 Build Scan 的 task/test/dependency/cache telemetry 转为因果诊断；Agent 的行动对象是 **cache configuration**，不是应用代码的一般修复。 | 不重复自愈页：它优化“构建经济性”的 cache miss，而非失败后修 product/CI code。与 Dagger 的缓存/DAG 会形成“执行复用 + cache 可观测/优化”链。 | **机制上很强，但 LLM 证据不够直接。** 官方称 AI agents/Agentic AI，未公开指定模型或 Agent 推理架构；不能以“明确 LLM”标准列为首选。 |
| 3 | GitLab Duo Agent Platform / Fix CI/CD Pipeline Flow | Agent Platform 文档明确 LLM 为 Claude Sonnet 4，18.8 GA；Fix Pipeline Flow 在 18.8 GA，19.2 对 MR 的 code suggestion 形态 GA。它读取 pipeline logs、MR changes、repo 与 script errors，输出 suggestion 或新 MR。 [platform](https://docs.gitlab.com/user/duo_agent_platform/) [Fix Pipeline](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/) | LLM 运行时读取 CI 失败上下文并生成可审阅的修复出口，且把 `AGENTS.md` 作为 repository-specific behavior input。 | **直接重复既有 Slide 03 GitLab 与 Slide 05 自愈。** | 不作为第二对象；可作为横向对照。 |
| 4 | Nx Self-Healing CI | 官方把它定义为 AI-powered self-healing CI，利用 project graph/metadata/CI failure context 生成 diff、重跑 failure task，并以 eligible task、confidence、verified 等条件处理 auto-apply。 [Nx docs](https://nx.dev/docs/features/ci-features/self-healing-ci) | 图结构上下文 + 受限补丁 + task-level oracle。 | **直接重复 Slide 05 的主体。** | 不选。 |
| 5 | CircleCI Chunk | 官方将 Chunk 描述为 AI agent，能利用 pipeline context、logs 等排障并创建 PR/重跑；公开产品阶段和端到端限制应以当前 changelog/文档单独复核。 [CircleCI article](https://circleci.com/blog/fix-bugs-faster-with-circlecis-chunk-ai-agent/) | Agent inner loop，失败调查与候选修复。 | **直接重复 Slide 05 的横向案例与基础设施页的 Agent 内环。** | 不选。 |
| 6 | Harness AI/Autofix | Harness 官方已有 Worker Agent / CI Autofix / Code Quality Agent 的完整机制；但项目已用它作为 Slide 02 及自愈横向案例。 | Pipeline control plane 中的 agent step 与失败修复 loop。 | **直接重复 Slide 02 Harness。** | 不选。 |

## 首选详解：Dagger + CloudBees Unify

### 1. 可被单页证明的机制

CloudBees 的官方开发者教程不是泛泛展示聊天机器人，而是公开了具体 Agent packaging：

```text
Claude Code / Cursor
    │ reads persona + skill definitions
    ├── Unify MCP: CI runs, logs, components, findings, workflows, flags
    ├── Jira / Slack MCP: ticket and notification workflow
    └── gh CLI: PR, issue, code search, Actions status
              ↓
unified delivery context → triage / release-status / workflow or flag action
```

具体可核验点：

1. **LLM host 是显式的。** 开源仓库说明可在 Claude Code 或 Cursor 中启动 Agent；官方 how-to 写明它将 Claude Code/Cursor 变成 DevOps-specialized agent。 [repo](https://github.com/cloudbees-oss/devops-agent-kit/) [how-to](https://www.cloudbees.com/blog/how-to-build-devops-agent)
2. **Context 不是单个 CI log。** 教程列出 Unify 连接的对象为 CI runs、logs、components、security findings、analytics、workflows、feature flags；Unify 文档确认 Agent 可以 inspect CI controllers，list/validate/update/trigger workflows，以及管理 flag configurations。 [how-to](https://www.cloudbees.com/blog/how-to-build-devops-agent) [MCP tool overview](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server)
3. **Agent workflow 被显式 productized 为 Skills。** 公开的七种入口包括 cross-tool pipeline overview、root-cause classified triage、release readiness、CI health，以及 ticketing；这使“LLM 怎样进入交付作业”有可检查的 action decomposition，而不仅是自然语言问答。 [repo](https://github.com/cloudbees-oss/devops-agent-kit/)
4. **动作不等于自治。** Unify MCP 以用户 OAuth/RBAC 运行；官方教程中的 kit 默认只读，写操作要求显式确认。仓库还明确提示 CI log 可能携带 prompt injection，说明不能将日志直接视为可信指令。 [MCP docs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server) [repo security notes](https://github.com/cloudbees-oss/devops-agent-kit/)

### 2. 与 Dagger 的并列关系

| 层 | Dagger | CloudBees Unify + Agent Kit | 不应混写 |
|---|---|---|---|
| 主要对象 | 类型化 Function、Module、container computation、execution DAG | component、CI run、workflow、test/finding、release / feature-flag state 的跨工具交付模型 | Dagger Function 不等于 Unify component；两者没有公开原生 connector 证据 |
| 对 LLM 的意义 | Agent / code 可调用的交付逻辑，减少把流程拆散到 YAML 与脚本 | 让 Agent 获得跨 CI、测试、扫描、发布状态的可查询上下文和受限行动入口 | MCP 不是唯一价值；Unify 的核心是跨工具的对象归一与语义关联 |
| 能力边界 | 不提供全企业 CI 状态汇总或 release-readiness 判断 | 不替代 Dagger Engine 执行 Function 或构造 Dagger cache key | 不宣称一方调度另一方，或二者共同产生任何性能收益 |

### 3. 建议的页面叙事

**标题候选：** `LLM 驱动 CI 的关键，不是多一个聊天入口，而是把交付系统变成可理解的上下文`。

**一句主张：**

> **Dagger 将“如何交付”收敛为可组合的执行图；CloudBees Unify 将“当前能否继续交付”收敛为跨工具的状态图。LLM Agent 才能基于结构化动作与结构化状态完成调查、判断与受限执行。**

可画三层能力，而不用以安全/可信作为主标题：

```text
交付动作（Dagger） ── Function / DAG / cache ─────────┐
                                                      ├→ LLM Agent：调查、组合、提出下一动作
交付状态（Unify） ── component / runs / tests / flags ─┘
                                                      ↓
                          受限 workflow、PR、ticket、flag 等执行出口
```

**这页不应再强调的词：** “MCP 连接”“CLI 替代”“自动修复”“安全治理”。它们分别已有 Slide 06、Slide 05 与前序页面。MCP 在图中只是 context 接入方式，核心新对象是 **Delivery Context Model**。

### 4. 证据缺口与文字边界

- Agent Kit 是 **CloudBees DevRel 维护的 demo/community reference**；其仓库声明“not production-hardened, not a CloudBees product, and not covered by support”。因此页面不可表述为“CloudBees 已 GA 的端到端 DevOps Agent 产品”。 [repository disclaimer](https://github.com/cloudbees-oss/devops-agent-kit/)
- Unify MCP 文档证明 Agent 在既有 RBAC 下可 list/validate/update/trigger workflows 等；不能证明默认启用全部 write tool、每个组织都接入 GitHub Actions/Jenkins、或用户从未确认也会变更 workflow/flag。 [MCP docs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server)
- 公开教程的场景与结果多为 CloudBees 示例，**没有独立客户的效果对照**；不要写任何“效率提升百分比”或“减少故障”的行业结论。
- 未找到 Dagger ↔ CloudBees Unify 的官方 connector、共同产品或共同客户证据。页面关系必须标为“两个可借鉴、相邻层的能力”，不是集成架构。

## 第二候选详解：Develocity 的 LLM 相关变化

### 真正值得讲的变化

Develocity 的新意不应该写成 “PTS 是 AI”：PTS 的本体是用项目自身历史训练的 **machine-learning model** 选择测试，属于传统 ML，不满足本次的 LLM 要求。 [PTS description](https://develocity.ai/pricing/)

满足本次要求的是另一条链：

1. 2025.3 起，Develocity MCP server 向 Agentic AI 公开 Build Scan 数据；2026.1 又提供面向 AI Agent 的 Skills / guided workflows 以分析 build failure 与 performance；2026.2 新增 Build Caching Optimizer。 [release history](https://develocity.ai/releases/)
2. 当前文档说明 MCP server 把 full build context 提供给 MCP-enabled AI clients，但它是可选 extension，需要明确启用，并要求 access key 的 API/MCP 权限；Analytics MCP 还需要 Athena Export 或 Reporting Kit。 [MCP installation](https://docs.develocity.ai/2026.2/integrations/agentic-ai/mcp-servers/installation/)
3. 2026.2 的官方产品页称 Cache Optimizer Agent 可识别高影响 avoidable misses、找 root cause、做 minimum-change fix、用 fresh build 验证并开 PR。 [pricing/product copy](https://develocity.ai/pricing/)

### 为什么不作为“LLM 明确参与”的首选

- 官方措辞是 “AI agent” / “Agentic AI”，未公开模型供应商、具体 LLM、prompt/tool policy 或 agent runtime；不能把它写成“LLM Agent 已自动自愈 CI”。
- 产品页与 release 是厂商自述；缺少独立可复现的 cache-optimizer 成功率、PR 合并率或 Dagger 联合采用证据。
- 它的自动化行动被具体限定为 build cache miss / configuration；这是高辨识度的**性能修复**，不是通用 pipeline/code repair。

### 若用户坚持选择 Develocity，可成立的收窄主张

> **AI 代码生成放大了 CI 运行次数，优化对象开始从“更快跑一次”变成“让 Agent 先读懂为何 cache 没有复用”：Develocity 将跨构建的 cache telemetry 变成 Agent 的诊断上下文，并把修复限定为可复验的 cache-configuration PR。**

此主张适合与 Dagger 的 Function/DAG cache 形成“执行复用 ↔ cache 反馈优化”的并列，但必须删除“LLM 明确参与”的强表述，改成“Agentic AI（模型实现未公开）”。

## 明确排除项

| 候选 | 为什么不用于第七页 |
|---|---|
| GitLab Duo / Fix Pipeline | LLM 与 pipeline 修复机制最清楚，但 `03-gitlab.md` 已占 GitLab 主体，`05-ci-self-healing.md` 已以其作横向案例。新增一页只会重复“读 log → 生成 suggestion/MR”。 |
| Nx Self-Healing | Project Graph 作为 Agent context 很强，但既有 Slide 05 已以 Nx 为主体，不能把它再包装成“LLM 新平台”。 |
| CircleCI Chunk | 同样是失败调查、候选修复、重跑/PR 形态；既有自愈页和基础设施页已经覆盖 Agent 内环。 |
| Harness | 已有第二页专讲 Harness Agent/Pipeline 控制面；重复会分散 deck 的公司洞察。 |
| AWS DevOps Agent + Kiro CLI | 官方参考架构证明大云厂商可以将 incident/CI signal 送入 coding agent 再产出 PR，但它属于既有“CI/CD 自愈”的慢环参考架构，不如 CloudBees 的跨工具 context model 适合作为 Dagger 的第二对象。 |

## 最终建议与下一步

1. **进入 Meta JiTTesting 的 Deep Dive。** 它是本轮唯一同时满足“明确 LLM、与既有五页有信息增量、以大公司公开实践形成成熟证据”的对象；具体主张必须由主研究的 Meta 一手材料审计后再锁定。
2. **仅在用户限定“必须是可购买的平台”时，回退到 CloudBees Unify。** 页面只能讲“LLM host + 跨工具 delivery context model”的能力，必须在同页标明 DevOps Agent Kit 是 demo/community reference。
3. **Develocity 仅作为构建性能/缓存专项备选。** 将措辞保持为 Agentic AI，不写 LLM；GitLab、Nx、CircleCI、Harness 则全部排除，因为它们虽然能力真实，但分别重复既有 GitLab/Harness 与 CI 自愈页面。

## 来源台账

| 来源 | 类型 / 访问时间 | 支撑范围 | 状态 |
|---|---|---|---|
| [CloudBees DevOps Agent Kit](https://github.com/cloudbees-oss/devops-agent-kit/) | CloudBees 维护的公开源码仓库；2026-08-03 | Claude Code/Cursor、skills、MCP+CLI连接、demo/reference 限制、log prompt injection 风险 | 一手、高 |
| [How to Build a DevOps Agent](https://www.cloudbees.com/blog/how-to-build-devops-agent) | CloudBees 官方教程，2026-04-21；2026-08-03 访问 | 63 tools、Unify对象、action workflow、确认边界 | 一手厂商教程、中高 |
| [CloudBees Unify MCP Server docs](https://docs.cloudbees.com/docs/cloudbees-unify/latest/unify-ai/explanation/understanding-the-mcp-server) | 官方文档；2026-08-03 | Claude Code/Gemini支持、OAuth/RBAC、workflow/flag/CI controller action接口 | 一手、高 |
| [Develocity releases](https://develocity.ai/releases/) | 官方 release page；2026-08-03 | 2025.3—2026.2 Agentic AI/MCP/Optimizer 声明和日期 | 一手厂商发布、中高 |
| [Develocity pricing/product capability](https://develocity.ai/pricing/) | 官方产品页；2026-08-03 | Cache Optimizer 与 PTS 的机制描述 | 一手厂商产品说明、中高 |
| [Develocity MCP installation](https://docs.develocity.ai/2026.2/integrations/agentic-ai/mcp-servers/installation/) | 官方文档 2026.2；2026-08-03 | MCP enablement、权限、Analytics dependencies | 一手、高 |
| [GitLab Duo Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/) | 官方文档；2026-08-03 | Claude Sonnet 4、GA/experimental status matrix | 一手、高 |
| [GitLab Fix Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/) | 官方文档；2026-08-03 | inputs、suggestion/MR output、log window限制 | 一手、高 |
| [Nx Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci) | 官方文档；2026-08-03 | AI self-healing + task/graph context | 一手、高 |
| [CircleCI Chunk](https://circleci.com/blog/fix-bugs-faster-with-circlecis-chunk-ai-agent/) | 官方产品博客；2026-08-03 | Agentic failure investigation / repair candidate | 一手厂商博客、中 |

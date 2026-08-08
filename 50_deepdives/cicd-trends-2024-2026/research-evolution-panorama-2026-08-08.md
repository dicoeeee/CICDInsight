---
title: 2024—2026 CI/CD 演进全景一手证据笔记
aliases:
  - CI/CD Evolution Panorama Primary Evidence 2026-08-08
tags:
  - research/deep-dive
  - research/primary-sources
  - research/agentic-cicd
topic_id: cicd-trends-2024-2026
status: complete
as_of: 2026-08-08
confidence: high-for-listed-facts
---

# 2024—2026 CI/CD 演进全景：一手资料证据笔记

> [!abstract] 用途与边界
> 本文件是 `cicd-trends-2024-2026` 的补充性一手资料笔记，不修改、也不替代 [[90_report|专题报告]] 的既有结论。它只记录能由官方产品文档、官方发布说明、官方工程博客或官方源代码库直接支持的事实，并将跨来源归纳单列为“基于来源的推断”。
>
> 研究窗口为 2024-01-01 至 2026-08-08；所有链接于 2026-08-08 访问。若页面仅说明当前能力而没有首次发布日期或 GA/Preview/Beta 标签，本文如实记录为“状态未标明”，不把当前可用性倒推为窗口内的发布时间或 GA。

## 研究提纲与审计方法

1. CI/CD 执行与动态编排：运行时如何生成、改变或推进工作流。
2. 制品、供应链与部署发布：可验证制品、渐进发布、审批与回滚如何被产品化。
3. AI/Agent：可在 CI/CD 中明确证实的动作，以及权限、沙箱和外部门禁边界。
4. 可靠性、恢复与软件交付性能度量：回滚/重试机制和指标定义，而非厂商营销指标。

**证据规则**：每张证据卡只陈述链接正文可直接证明的内容；“产品阶段”只采用来源显式给出的 GA、Preview、Beta、Research 等标签。源代码库/示例库只能证明代码或示例存在，不能证明托管产品进入生产可用状态。以下“产品”包含平台能力、官方开源实现或官方示例，已逐项标明。

## 一、CI/CD 执行与动态编排

| ID | 产品与功能 | 来源事实（可直接证明） | 技术机制 | 生命周期状态 | 日期 / as-of | 一手来源 |
|---|---|---|---|---|---|---|
| E1 | Buildkite Pipelines：Dynamic Pipelines | Buildkite 支持由任意输出 YAML 或 JSON 的脚本在构建运行时上传新步骤；新步骤加入**同一** build，并可按 `agents` 查询或 queue 独立调度。文档还给出“依据前序结果、外部 API 或 feature flag 决定后续步骤”的多阶段模式。 | bootstrap/generator → `buildkite-agent pipeline upload`；可 `--replace` 替换尚未开始的步骤；以 `depends_on` 控制顺序。官方同时说明上传步骤可以注入任意步骤，因此要求审计可调用上传的步骤；可用 pipeline signing 防止未签名注入。 | 未标明（当前产品文档） | as_of 2026-08-08；首次发布日期未在该页标明 | [Dynamic pipelines](https://buildkite.com/docs/pipelines/configure/dynamic-pipelines) |
| E2 | GitHub Agentic Workflows (`gh-aw`) | `gh-aw` 处于 **Public Preview**；它将 Markdown 工作流编译为传统 GitHub Actions 工作流，在 Actions 中运行 Copilot、Claude Code、Codex 或 Gemini。官方定位为增强既有确定性 CI/CD，而不是替换它。 | Markdown/front matter → `.lock.yml`；事件或定时触发；编译期 schema 验证、表达式 allowlist、Action pinning 和安全扫描。 | **Public Preview** | 2026-06-11；文档 as_of 2026-08-08 | [GitHub changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/) · [官方文档](https://github.github.io/gh-aw/) |
| E3 | Harness Worker Agents：Pipeline Agent step | 官方文档显示 Worker Agent 以 `type: Agent` 的 Pipeline step 被引用；指令是 Agent 定义内的系统提示词，可含在流水线运行时才解析的表达式；step 引用名称和版本并传入运行时输入。 | Agent catalog YAML（container image、instructions、inputs、environment）+ Pipeline Agent step；输出写入 `$HARNESS_OUTPUT` / `$DRONE_OUTPUT`，供下游步骤引用。 | 产品文档未在该页标明；对应发布博客宣称 2026-06-30 “available now”，但本文不据此泛化其子功能状态 | 文档更新 2026-08-04 | [Worker Agents overview](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/) · [发布公告](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| E4 | AWS DevOps Agent：Release Management | AWS 明确发布 release readiness review 与 autonomous release testing；能力要求连接代码仓库和 pipeline，官方称它横跨 delivery 与 operations。 | review 检查内部标准偏离、依赖影响和访问控制，并映射跨仓库依赖；release testing 为 Web/API 应用生成并运行测试计划。 | **Preview**，仅 `us-east-1`，预览期免费 | 2026-06-17 | [AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) |
| E5 | OpenAI Symphony：长时任务编排规范 | 官方发布页与开源规范将 Symphony 描述为长期运行服务：持续从 issue tracker 读取任务、每 issue 创建隔离 workspace、启动 coding-agent session；它负责 dispatch、retry、reconciliation 与结构化日志。 | 以仓库内 `WORKFLOW.md` 版本化工作流策略；bounded concurrency、指数退避、重启恢复；ticket 写入由 Agent 所持工具执行，而非 scheduler 内建。成功运行可停在 `Human Review`，不必然为 `Done`。 | 官方规范为 **Draft v1**；官方页面未给出 GA/Preview 产品生命周期标签 | 2026-04-27；as_of 2026-08-08 | [OpenAI 发布页](https://openai.com/index/open-source-codex-orchestration-symphony/) · [官方仓库](https://github.com/openai/symphony) |
| E6 | Dagger Functions / Modules | Dagger 将函数定义为带类型输入/输出、通常在容器中执行的计算单元；模块是可共享的函数集合。官方文档说明模块载入 session 时会动态扩展 Dagger API。 | 类型化 SDK 函数 + containerized execution；模块由 Git tag 版本化，依赖默认 pin。 | 未标明（当前产品文档） | as_of 2026-08-08；无法由该页证明其首次进入窗口的时间 | [Functions](https://docs.dagger.io/core-concepts/functions/) · [Reusable Modules](https://docs.dagger.io/features/modules) |

### 基于来源的推断（非来源事实）

- 可观察到三种不同的“动态”实现位置：Buildkite 在运行中的 build 内追加/替换步骤；GitHub/Harness 把 Agent 作为 Actions/Pipeline 的受管节点；AWS/OpenAI 把调度逻辑置于流水线外的 Agent 服务或规范中。它们都能改变后续工作，但权限主体、状态存储和最终发布授权不同，不能合并为同一种“动态 CI”。
- E1 的 `--replace`、E5 的 retry/reconciliation 与 E4 的 release testing 表明，编排已经覆盖运行时重构、失败恢复和发布前验证三类动作；这些是功能覆盖面的归纳，并**不**证明各产品在生产环境中的成功率或成熟度相等。

## 二、制品、供应链与部署发布

| ID | 产品与功能 | 来源事实（可直接证明） | 技术机制 | 生命周期状态 | 日期 / as-of | 一手来源 |
|---|---|---|---|---|---|---|
| S1 | GitHub Artifact Attestations | GitHub 于 2024-05-02 发布 Public Beta，并在原文更新中注明 2024-06-25 已 GA。工作流可在制品生成后用 `actions/attest-build-provenance` 创建 provenance；验证可使用 `gh attestation verify`。 | 工作流需 `id-token: write`、`attestations: write` 和 `contents: read`；证明文件可下载用于离线验证或输入 OPA；示例 predicate 为 `https://slsa.dev/provenance/v1`。 | **Public Beta** → **GA**（原文更新） | 2024-05-02；GA 更新 2024-06-25 | [GitHub 官方发布](https://github.blog/news-insights/product-news/introducing-artifact-attestations-now-in-public-beta/) |
| S2 | GitHub Artifact Attestations：multiple subjects | `attest-build-provenance` 和 `attest-sbom` 对多个输入 subject 创建带多个 subject 引用的一份 attestation，而非每个制品一份。 | 多 subject 的 provenance / SBOM attestation。 | Release（页面没有额外 GA/Preview 标签） | 2024-12-04 | [GitHub changelog](https://github.blog/changelog/2024-12-04-artifact-attestations-now-support-multiple-subjects/) |
| S3 | Google Cloud Deploy：受控推进、验证与回退 | 官方产品页列出 IAM 集成的 release promotion approval、canary、部署验证、自动推进和并行部署；并说明并行部署要么全部成功、要么整体回滚。 | 声明式 delivery pipeline；canary 分阶段；`Automation` 规则可自动 promote/advance。`repairRollout` 可按配置重试；重试耗尽后，服务创建新的 rollout 回滚到该 target 最近成功的 release。 | 未标明（当前产品/文档） | as_of 2026-08-08 | [Cloud Deploy 产品页](https://cloud.google.com/deploy) · [Automation 文档](https://docs.cloud.google.com/deploy/docs/automation) |
| S4 | AWS CodeDeploy：蓝绿/滚动与自动回滚 | CodeDeploy 支持 EC2、ECS 和 Lambda 蓝绿发布，也支持 EC2 滚动更新和 deployment health tracking。若部署失败或达到配置的监控阈值，可自动重新部署最后已知良好的 revision。 | automatic rollback 本质是新 deployment（新 deployment ID），不是恢复旧对象；安装脚本的副作用不会被平台自动反向协调。 | 未标明（当前产品/文档） | as_of 2026-08-08 | [CodeDeploy features](https://aws.amazon.com/codedeploy/features/) · [Rollback 文档](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-rollback-and-redeploy.html) |
| S5 | AWS DevOps Agent release readiness | AWS 发布公告称 release readiness 在代码生成时检查内部标准、依赖影响与 access control，并在 commit 前映射跨仓库依赖；release testing 在客户提供的环境中生成、运行测试。 | 由 release management capability 发起，连接代码仓库和 pipelines。公告描述其目标为 release safety；并未把它描述为自动合并或自动生产发布。 | **Preview**，仅 `us-east-1` | 2026-06-17 | [AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) |

### 基于来源的推断（非来源事实）

- S1/S2 把“制品与构建来源的可验证性”落实为可验证的证明文件；S3/S4 则把“上线后控制”落实为审批、逐步流量、验证、重试与回退。两者可组成交付控制链，但任何一个来源都没有证明它们已自动互联成通用端到端方案。
- S4 明确的副作用限制是恢复边界的重要反例：即使平台能自动回退 revision，也不意味着它能撤销部署脚本造成的全部外部状态变化。不能将“有 rollback”表述为“可完全恢复”。

## 三、AI / Agent 在 CI/CD 中的具体能力与外部控制边界

| ID | 产品与功能 | 来源事实（可直接证明） | 技术机制与外部控制边界 | 生命周期状态 | 日期 / as-of | 一手来源 |
|---|---|---|---|---|---|---|
| A1 | GitHub `gh-aw`：Agent 执行 | `gh-aw` 可在 GitHub Actions 内运行多个编码 Agent，并可用于 CI failure diagnosis、test improvement、quality checks 等自动化。 | Agent 默认只读，运行在 sandboxed container 和 Agent Workflow Firewall 后；敏感凭据不在 Agent runtime；Safe Outputs 先校验，再由 scoped write job 应用；另有独立 threat-detection job 扫描 proposed changes。 | **Public Preview** | 2026-06-11；文档 as_of 2026-08-08 | [GitHub changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/) · [官方文档](https://github.github.io/gh-aw/) |
| A2 | Harness Worker Agents：运行时授权 | Agent 可在 Pipeline step 中运行，但其有效权限是 pipeline YAML 中声明的 resource/verb grant 与**触发该流水线主体** RBAC 的交集；声明只能收窄，不能扩权。 | scoped token 按 stage 或 containerized step group 注入；token 生命周期限于该运行期。`HARNESS_TOKEN_INJECT` 仍为 feature flag，且 trigger-initiated run 当前没有可注入的 scoped token。 | 权限注入能力受 feature flag 控制；页面未标整体产品 GA/Preview | 文档更新 2026-07-23 | [Agent permissions](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/worker-agent/permissions/) |
| A3 | AWS DevOps Agent：发布前 AI 审查与测试 | Release Management Preview 包含 release readiness review 与 autonomous release testing。公告称 review 可检查标准偏离、依赖影响、access control；testing 可发现 regression、UX 和 integration failure。 | 该来源只说明其在连接的仓库/pipeline 和客户提供环境中工作；并未授权 Agent 自动合并 PR、绕过现有审批或自行执行生产发布。 | **Preview**，仅 `us-east-1` | 2026-06-17 | [AWS What’s New](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) |
| A4 | Anthropic Claude Code sandboxed bash / web | Claude Code 默认只读；sandboxed bash 引入文件系统与网络两层隔离，可让 Agent 在预定义范围内少逐项审批地运行命令。Claude Code on the web 的 session 在隔离沙箱运行，git/signing credentials 不进入沙箱。 | Linux `bubblewrap` / macOS Seatbelt；工作目录可读写、外部文件阻止修改；网络仅经 Unix socket proxy，按域名控制。Web git proxy 使用 scoped credential，并在校验 push 的目标分支后才附加真实 token。 | sandbox runtime 为 **Beta / Research Preview**；web 产品的该页未明确单独给出生命周期标签 | 2025-10-20 | [Anthropic 工程博客](https://www.anthropic.com/engineering/claude-code-sandboxing) |
| A5 | Anthropic Claude Code Action | 官方开源 GitHub Action 可在 PR/issue 工作流中做 code review、代码实现、PR/issue 交互与结构化输出；其 README 明确该 Action 在用户自己的 GitHub runner 上执行，API 可选 Anthropic、Bedrock、Vertex 或 Foundry。 | 工作流触发与 prompt 决定运行；GitHub API/文件操作可用，更多工具须配置。源代码库还列出 permissions、MCP、网络限制与 capabilities/limitations 文档入口。 | Action 仓库未给产品级 GA/Preview 标签；`v1.0` 迁移指南仅证明版本线存在 | as_of 2026-08-08 | [官方仓库](https://github.com/anthropics/claude-code-action) |
| A6 | OpenAI Codex：官方 CI/CD 示例 | OpenAI 官方 cookbook 的 `examples/codex` 目录包含 `Autofix-github-actions.ipynb`、`Build_iterative_repair_loops_with_Codex.ipynb`、`build_code_review_with_codex_sdk.md` 和 `secure_quality_gitlab.md`。 | 这些文件证明 OpenAI 维护 CI failed-check 修复、迭代修复循环、代码评审和 GitLab 安全/质量的示例路径。它们是示例代码，不是托管 CI/CD 产品的授权或 GA 声明。 | 未标明（官方示例库） | as_of 2026-08-08 | [OpenAI Cookbook 源码目录](https://github.com/openai/openai-cookbook/tree/main/examples/codex) |
| A7 | OpenAI Symphony：Agent 编排边界 | Symphony 的规范明确：它是 scheduler/runner 与 tracker reader；ticket 状态、评论、PR link 等写入通常由 coding agent 通过 runtime 中可用工具完成；完成可落在 `Human Review`。 | 规范要求实现显式记录 trust/safety posture，却**不要求**统一的审批、沙箱或 operator confirmation 策略；因此该规范本身不能证明存在通用的外部生产门禁。 | **Draft v1** specification | 2026-04-27；as_of 2026-08-08 | [OpenAI 发布页](https://openai.com/index/open-source-codex-orchestration-symphony/) · [官方仓库](https://github.com/openai/symphony) |

### 基于来源的推断（非来源事实）

- A1、A2、A4 的直接证据共同显示：当 Agent 从“给建议”进入“运行命令、写入系统或调用工具”时，产品会把执行权拆分到沙箱、最小权限 token、代理校验或下游写入 job。该归纳只覆盖这三套明确披露机制，不能推断为所有 CI/CD 产品的统一架构。
- A3、A6、A7 说明“能调用 Agent / 有示例 / 有调度器”与“获得合并或生产发布授权”是不同命题。尤其 A7 不规定统一审批姿态，A6 也只是示例，因此不能据此主张 Codex 或 Symphony 自动拥有任何组织的发布权。

## 四、可靠性、恢复与软件交付性能度量

| ID | 产品与功能 | 来源事实（可直接证明） | 技术机制与口径 | 生命周期状态 | 日期 / as-of | 一手来源 |
|---|---|---|---|---|---|---|
| R1 | GitLab Value Stream Analytics：DORA | GitLab Value Stream Analytics 列出 deployment frequency、lead time for changes、time to restore service、change failure rate 四项 DORA metric；由 DORA API 数据计算。 | 生命周期 stage 由 start/end events 定义；该功能所列 tier 为 Ultimate，offering 为 GitLab.com、Self-Managed 与 Dedicated。 | 生命周期未标明；**Tier: Ultimate** 不是 GA 标签 | as_of 2026-08-08 | [Value Stream Analytics](https://docs.gitlab.com/user/group/value_stream_analytics/) |
| R2 | GitLab DORA 指标定义 | GitLab 将 deployment frequency 定义为指定环境每日成功 deployments 的平均数；lead time for changes 为 MR merge 到代码成功运行在 production 的秒数；time to restore service 为 production incident open 时长的中位数；change failure rate 为造成 production incident 的 deployment 百分比。 | deployment frequency 使用 mean，其余指标使用 median；production environment tier/名称、incident 记录和数据聚合方式会影响结果。官方还标明 change failure rate 目前按日聚合 incident 与 deployment，尚未逐一建立关系，可能出现重复计数。 | 未标明（当前产品文档） | as_of 2026-08-08 | [GitLab DORA metrics](https://docs.gitlab.com/user/analytics/dora_metrics/) |
| R3 | Google Cloud Deploy：发布可靠性与恢复 | Cloud Deploy 提供 deployment verification、canary、approval、automated promotion、retry/rollback 和 release delivery metrics。 | 自动化失败时可重试；耗尽 retry 后创建新 rollout 回滚最近成功 release。产品页的指标仅明确为 release 频率与成功情况的洞察，未把它声明为完整 DORA 实现。 | 未标明（当前产品/文档） | as_of 2026-08-08 | [Cloud Deploy](https://cloud.google.com/deploy) · [Automation](https://docs.cloud.google.com/deploy/docs/automation) |
| R4 | AWS CodeDeploy：回滚语义 | 自动或手动 rollback 都通过重新部署此前 revision 作为一个新 deployment 完成；可在失败或监控阈值触发时自动回滚。 | 服务会清理上次成功安装的文件，但不尝试反转前序部署脚本执行过的操作；因此部署脚本必须自行保证幂等或补偿逻辑。 | 未标明（当前产品/文档） | as_of 2026-08-08 | [AWS CodeDeploy rollback](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-rollback-and-redeploy.html) |
| R5 | Harness CI Intelligence：节省时间度量 | Harness 的 `Intelligence Savings` 通过“启用优化前的完整 Build stage baseline”与优化后的 stage duration 比较计算节省。 | 并行步骤报告的是各步骤节省的 CPU time，不是 wall-clock time；逻辑变更或并行度变化会影响精度，因此不能将并行步骤的节省直接相加为分钟收益。 | **Beta**，`CI_PARSE_SAVINGS` feature flag | 文档更新 2026-06-30 | [Harness CI Intelligence](https://developer.harness.io/docs/continuous-integration/use-ci/harness-ci-intelligence/) |

### 基于来源的推断（非来源事实）

- 交付性能和恢复并不存在可直接横比的单一“平台指标”。R2 将 DORA 指标绑定在 GitLab 的 environment 与 incident 数据模型，R3 只披露 release frequency/success，R5 还特别区分 CPU time 与 wall-clock time。跨产品比较前需要先统一生产环境、事件关联、聚合函数、并行处理和观察窗口。
- 可证明的恢复机制主要是“重试、回退到已知 revision、保留失败记录”，而不是对任意外部副作用的全自动逆转：R4 是明确的产品级限制，R3 的 rollback 同样以新的 rollout 表达。任何“自愈/完全恢复”结论都须另有针对业务状态和外部依赖的证据。

## 可审计的趋势小结（仅限本笔记证据范围）

1. **运行时编排在增加，但实现层不同。** E1 的 build-time step upload、E2/E3 的受管 Agent step、E4 的 release management 与 E5 的长时 scheduler 都有明确一手证据；它们不是同一个标准，也不共享同一授权模型。
2. **交付控制可以在多个层次落实。** S1/S2 给出制品 provenance 与验证；S3/S4 给出推进、健康判断、重试和回退；A1/A2/A4 给出 Agent 的执行隔离与最小权限。该小结只表示这些机制可被明确证明存在，并不声称任何厂商已形成完整零信任交付闭环。
3. **AI 能力需要与外部控制分开评价。** A1、A2、A4 的控制面由 sandbox、token/RBAC intersection、proxy 或下游 write job 承担；A3、A6、A7 则没有给出自动生产发布授权。因而，Agent 的“可执行”不能被当作“可放行”。
4. **度量首先是数据定义问题。** R2 与 R5 已直接展示同一“效率/可靠性”主题下不同的计量单位与聚合规则。指标本身可用于本平台的趋势观察；没有统一数据契约前，不应把各厂商页面上的指标相减、排名或外推成行业基准。

## Evidence gaps 与禁止外推项

| 缺口或限制 | 本笔记的处理 |
|---|---|
| 官方当前文档未显示首次发布日或 lifecycle | 标为“未标明”；不伪造 2024—2026 内的首次时间或 GA。Buildkite、Dagger、CodeDeploy、Cloud Deploy、GitLab 指标均属此类。 |
| Agent 的“生产自动发布/恢复”授权 | 本次所列一手资料没有证明通用的、端到端且无人批准的生产发布授权；A3 还是 Preview。保持 evidence gap。 |
| 跨厂商成效、ROI、失败率或成熟度排名 | 未收集可比较的独立测量；厂商博客中的单点数据即使存在也不用于本笔记的行业结论。保持 evidence gap。 |
| 供应链证明与部署策略的自动绑定 | S1/S2 与 S3/S4 是不同产品路径；本次没有发现可证明其跨厂商自动衔接的标准化一手材料。保持 evidence gap。 |
| “动态编排”是否比静态流水线更可靠 | E1 明确指出动态输出必须额外验证、留存和防注入，但没有提供跨平台因果测量。不得写成可靠性提升事实。 |

## 来源访问记录

- **访问日期**：2026-08-08（Asia/Shanghai）。
- **来源类型**：厂商产品文档、官方发布公告/工程博客、厂商维护的官方 GitHub 源码库；未使用二手媒体文章支撑本文事实。
- **审计提示**：页面型文档会更新；复核时应以本文件的日期、直接 URL 和显式生命周期标签为准。对“状态未标明”的条目，应重新查看 release notes / feature availability 页面，而不是由当前文档的存在推断产品阶段。

---
title: Slide 01 右侧说明详细文字版 v1
status: draft-for-selection
as_of: 2026-07-30
primary_deep_dive: "[[50_deepdives/llm-era-cicd-infrastructure/README]]"
purpose: 先完整说明四类演进的结论、公司证据、Agent 驱动与控制边界，再与用户共同筛选为单页可见文案。
---

# 右侧说明详细文字版 v1

> [!note] 使用边界
> 本稿为了逐项审核证据，暂时保留公司名称和产品状态；正式页面不重复公司图标。公司名称是否保留为轻量证据标签，待内容筛选时决定。

## 1. 代码仓

### 演进结论

代码仓正在从保存代码、Issue 和人工协作记录的 Source of Truth，扩展为 Agent 可消费的任务入口、上下文系统、运行配置载体和候选变更协作面。

### 左侧公司如何支撑该结论

- **GitHub（Copilot Coding Agent 已 GA）：** 任务可以从 Issue、Agents UI 或 PR 评论进入；Agent 在 GitHub Actions 支撑的临时环境中读取仓库上下文、执行任务并形成改动，最终以 Branch 或 Draft PR 请求 Review。它把“任务分派—临时执行环境—候选变更—人工接受”闭合在 Repository 工作流中。
- **GitLab（Duo Agent Platform 与 Custom Flows 已 GA）：** Issue、MR、评论提及和 Pipeline Event 都可以触发 Flow；Flow 在 Runner 中执行，并将 Session、CI Job Log 和候选结果重新关联到 MR。它证明 Repository 不再只是保存最终提交，还开始承载 Agent 任务触发、运行归因和候选交付。
- 两家公司路径不同，但都把 Agent 当成正式的异步 Actor：任务来自仓库对象，环境与行为由仓库配置约束，结果仍回到 PR/MR，而不是绕开原有协作模型直接写入主干。

### Agent 驱动原因

Agent 任务往往持续更久、跨越多轮推理并需要中断后接续。它必须稳定获得代码、历史、Issue、架构说明、`AGENTS.md`、Setup、Skill、MCP 和运行日志，并把结果交给其他 Agent 或人继续处理。因此，只面向人类浏览的页面和零散日志不足以成为可靠上下文；任务、配置、候选变更和接受规则需要版本化并可被机器检索。

### 控制边界

Repository 可以承载任务委托和证据，但不应替代外部云、制品仓或生产环境的授权。Agent 生成的 Branch、PR/MR 仍需经过 CODEOWNERS、Ruleset、Required Checks、签名、Review 和部署批准。Setup Script、MCP Config、Custom Flow 与 Skill 会直接改变 Agent 的执行面，应被当作高风险代码独立评审。

### 证据回链（不进入页面正文）

[[00_sources/briefs/2026-github-cloud-agent-repository-control-plane]]；[[00_sources/briefs/2026-gitlab-duo-agent-platform]]；[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面|F2]]。

## 2. 流水线

### 演进结论

流水线正在从执行固定 Job/DAG 的编排器，扩展为“确定性策略外壳中的受控 Agent 运行时”：触发、权限、Runner、预算和输出类型保持可声明，Agent 只在受限区域内进行动态判断与 Tool 调用。

### 左侧公司如何支撑该结论

- **GitHub Agentic Workflows（Public Preview）：** 用户用 Markdown 表达意图，平台将其编译为标准 Actions YAML，继续复用 Actions Runner、权限和 Policy；动态 Agent Job 被放入默认只读、无 Secret、受 Firewall/Sandbox 与 Safe Output 约束的外壳。它表明自然语言并没有替代 Pipeline，而是在固定 Pipeline 控制面内生成和执行受限决策。
- **Buildkite：** 官方 MCP 将 Build State、Log 和 Trigger 暴露为机器可发现的 Tool，Pipeline 中又可以配置 Model Provider 与 Agent Step。Agent 因而既能读取构建事实、分析日志，也能在现有 Pipeline Step 中运行或触发后续构建，说明流水线开始原生承载 Agent Session 和 Tool 调用，而不仅是被外部聊天机器人查询。
- **Harness：** Worker Agent 使用非 Root 执行、Credential Broker、Egress Proxy、短期 Runtime Token 和第三方 Tool Scope；CI Autofix 再把“失败—候选修复—复验”限制在有界循环中。它证明 Agent Runtime 的关键不是多一个模型调用，而是把身份、网络、凭据和修复轮次一并纳入流水线控制。

### Agent 驱动原因

固定脚本适合预先知道全部步骤的任务，但 Agent 会根据日志、测试结果和外部 Tool 返回动态改变下一步，并可能产生写仓库、触发构建或调用第三方系统等副作用。任务持续时间、尝试次数和消耗也不再固定。因此流水线必须同时提供 Session、事件、Tool、结构化输出和成本观测，又不能把整个交付过程变成不可审计的自由循环。

### 控制边界

MCP、CLI 或 Tool 可调用不等于获得授权。Tool 白名单、参数范围、Secret、网络出口、Runtime Token、最大轮次、时间和预算必须由 Agent 外部的 Pipeline/Platform Policy 约束。动态输出只能进入类型化、可验证的 Safe Output；Merge、Promotion、Deploy 和生产凭据继续由独立 Job、身份、Required Checks 或批准链控制。

### 证据回链（不进入页面正文）

[[00_sources/briefs/2026-github-agentic-workflows]]；[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines]]；[[00_sources/briefs/2026-harness-worker-agents]]；[[00_sources/briefs/2026-harness-worker-agent-security]]；[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F3：流水线正分化为低延迟验证内环和完整交付外环|F3]]。

## 3. 构建系统与底层资源

### 演进结论

构建系统的核心变化不是“让模型参与编译”，而是让 Graph、Affected、Cache、Snapshot、Remote Execution 和 Worker 调度从提速选项升级为 Agent 高频并发验证的反馈经济与 Backpressure 底座。

### 左侧公司如何支撑该结论

- **CircleCI：** Chunk Sidecar/Microbuild 使用预热快照、增量同步和精简结果，把 Agent 每轮需要的反馈从完整 CI 中拆出；正式 CI 仍作为系统级验证外环。它说明当 Agent 多轮试错时，平台开始针对冷启动、重复环境准备和日志体积重新设计验证路径。
- **Nx：** Project Graph 与 Affected Analysis 先确定受影响任务，再结合历史耗时、Remote Cache 和动态 Worker 分发执行；Self-Healing 使用 Task Metadata 生成候选修复，并重新运行原失败 Task 复验。它把“任务图—资源调度—缓存复用—Task 级 Oracle”连接成 Agent 可消费的快速反馈系统。
- **Bazel：** Hermetic Action Graph、Remote Cache 和 Remote Build Execution 早已提供多机执行、统一环境与结果复用。它不是 Agent 时代的新产品，但为大量并行 Agent 提供确定性输入输出、可信结果复用和远程算力承载，证明既有构建能力正在因新负载模式而升值。

### Agent 驱动原因

一个人通常在提交前完成基本预检；多个 Agent 却可能并发生成候选、反复执行验证，并在每轮消耗 Runner/Core Minutes、CPU/内存、日志 Token 和推理额度。如果每次都启动完整 CI，会同时放大 Queue Time、冷启动、Fan-out、重复计算和反馈延迟。构建系统因此需要按任务图选择工作、复用可信结果、动态分配 Worker，并以预算对无限尝试形成 Backpressure。

### 控制边界

这些能力大多早于大模型存在，不能写成 Agent 的新发明，也没有跨行业证据证明统一 ROI。Cache 必须按 Trust Domain 隔离并限制可信 Writer；快速内环不能替代干净环境中的完整 Required Checks；候选修复必须由原 Task 或独立 Oracle 复验。企业应以 Queue Time、Cache Hit、重试次数、资源利用率和 Verified Outcome 成本验证投资优先级。

### 证据回链（不进入页面正文）

[[00_sources/briefs/2026-circleci-agentic-validation-infrastructure]]；[[00_sources/briefs/2026-nx-self-healing-ci]]；[[50_deepdives/llm-era-cicd-infrastructure/research-pipeline-build-2026-07-28]]；[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制|F4]]。

## 4. 制品仓与供应链平台

### 演进结论

制品仓及其相邻供应链平台正在从保存包和二进制文件，扩展为 Agent 可查询的供应链事实、可验证交付证据以及受限行动面；部分平台还开始管理 Skill、Prompt、Plugin 和 MCP Server 等 Agent 行为资产。

### 左侧公司如何支撑该结论

- **Sonatype：** MCP 把版本、漏洞、许可证、维护状态和推荐版本前移到 Agent 选择依赖的时点。Agent 不再只根据模型记忆修改依赖，而是可以在生成候选前查询当前供应链事实。
- **Cloudsmith：** MCP 将漏洞、版本和部分制品管理能力暴露为本地 Tool，但公开边界只允许非破坏性动作，Policy 激活继续保留人工。它说明制品平台正在从“可查询”迈向“受限可行动”，同时主动区分查询、建议和接受性写入。
- **JFrog（MCP 为 Beta）：** MCP 已覆盖 Repository、Xray、SBOM、Evidence、Release 以及部分 Identity/Governance Tool；Agent Packages Repository 又开始存放 Skills、Plugins、Prompts、Hooks、MCP Servers、Instructions 和 Agents，相关 Skills/扫描能力仍处 Open Beta。它说明制品对象正从 Package/Container 扩展到能够改变 Agent 行为的可执行资产。
- **补充交付证据：** GitHub Artifact Attestations 已 GA，可将 Workflow、Repository、Commit、Environment 与 Artifact Digest 绑定并关联 SBOM；SLSA 进一步要求由 Builder 控制面生成 Provenance。它们共同证明制品平台的价值正在从“文件存在”扩展到“来源和过程可以被验证”。

### Agent 驱动原因

Agent 会更频繁地选择或升级依赖、生成构建结果并提出发布动作，同时也会消费能够改变自身 Tool 与行为的 Skill、Prompt 和 MCP 配置。模型记忆无法保证版本、漏洞、许可证和发布状态实时准确；机器动作也不能只依赖可变 Tag 或自然语言摘要。因此，供应链事实、Digest、SBOM、Provenance、Evidence 和 Agent 行为资产必须以机器可查询、可绑定和可回溯的方式进入交付链。

### 控制边界

MCP 只是既有 API/CLI 的机器可发现入口，最终权限仍来自 OAuth、API Key、Platform RBAC 和 Policy。查询或生成 Promotion/Waiver Plan 不等于获得 Policy Activation、Waiver、Promotion、Delete、Token/OIDC/Role 或 Production Release 权限。Attestation 证明来源和过程，不证明制品无漏洞、无恶意逻辑或满足业务需求；高风险动作仍需独立授权、批准和可回退路径。

### 证据回链（不进入页面正文）

[[00_sources/briefs/2025-sonatype-guide-supply-chain]]；[[00_sources/briefs/2026-cloudsmith-mcp-artifact-management]]；[[00_sources/briefs/2026-jfrog-skills-and-mcp]]；[[00_sources/briefs/2026-github-artifact-attestations-slsa]]；[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F5：制品仓从存储面扩展为供应链信任面与受限行动面|F5]]。

---
title: Slide 01 右侧说明压缩版 v1
status: draft-for-review
as_of: 2026-07-30
primary_deep_dive: "[[50_deepdives/llm-era-cicd-infrastructure/README]]"
detailed_evidence: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-detailed-v1]]"
purpose: 评审右侧半页的可见文案密度；公司变化保留为差异化短句，完整证据进入演讲备注，不展示控制边界。
---

# 右侧说明压缩版 v1

## 页面可见文案

### 01 代码仓｜从代码协作面到 Agent 任务与上下文协作面

**公司变化**

- **GitHub：** 把 Agent 的任务分派、临时执行和 PR 交付闭合进仓库工作流。
- **GitLab：** 把仓库事件触发的 Flow、Runner 执行和 MR / CI Log 证据关联起来。

**Agent 驱动**

异步长任务需要任务、规则、运行上下文和候选变更可版本化、可接续。

### 02 流水线｜从固定 Job 编排到受控 Agent 运行时

**公司变化**

- **GitHub：** 把 Markdown 意图编译为受 Actions 权限与 Policy 约束的 Agent Job。
- **Buildkite：** 把 Build 状态、日志和触发能力变成 Tool，并在 Agent Step 中消费。
- **Harness：** 把隔离 Worker、凭据、网络和 Autofix 轮次一起纳入 Agent Runtime。

**Agent 驱动**

Agent 会根据日志和 Tool 返回动态改变下一步，流水线因而需要承载 Session、事件、结构化输出与运行成本。

### 03 构建系统与底层资源｜从提速工具到高频验证的反馈底座

**公司变化**

- **CircleCI：** 用预热快照和 Microbuild 为 Agent 拆出低延迟验证内环。
- **Nx：** 把 Graph / Affected、Cache、动态 Worker 和 Self-Healing 复验串成反馈系统。
- **Bazel：** 既有 Hermetic Action Graph、Remote Cache 和 RBE 因并行 Agent 负载而升值。

**Agent 驱动**

多 Agent、多轮验证会放大排队、冷启动、重复计算和算力消耗，构建与资源层必须缩短每轮可信反馈。

### 04 制品仓与供应链｜从文件存储到可查询事实与交付证据面

**公司变化**

- **Sonatype：** 把实时版本、漏洞和许可证情报前移到 Agent 选择依赖的时点。
- **Cloudsmith：** 把制品与漏洞能力 Tool 化，并从可查询扩展到受限可行动。
- **JFrog：** 把 Repo / Xray / Evidence Tool 化，并把 Package 扩展到 Agent 行为资产。

**Agent 驱动**

Agent 频繁选择依赖、生成制品并消费 Skill / MCP，版本、风险、来源与 Digest 必须可被机器实时查询和追溯。

## 演讲备注｜公司变化的证据说明

> [!note] 备注使用方式
> 页面只显示上面的差异化路径短句。以下内容写入 PowerPoint 演讲备注，用于解释短句如何支撑演进结论；产品状态、证据回链和更完整表述以详细稿为准。

### 代码仓

- **GitHub（Copilot Coding Agent 已 GA）：** 任务可从 Issue、Agents UI 或 PR 评论进入，在 GitHub Actions 支撑的临时环境中读取仓库上下文并形成改动，最后以 Branch 或 Draft PR 请求 Review。变化是把任务分派、执行环境、候选变更和人工接受闭合到 Repository 工作流。
- **GitLab（Duo Agent Platform 与 Custom Flows 已 GA）：** Issue、MR、评论或 Pipeline Event 可触发 Flow；Flow 在 Runner 中执行，并将 Session、CI Job Log 和候选结果关联回 MR。变化是 Repository 开始承载 Agent 触发、运行归因与候选交付。

### 流水线

- **GitHub Agentic Workflows（Public Preview）：** Markdown 意图被编译为标准 Actions YAML，动态 Agent Job 继续运行在 Actions Runner、权限和 Policy 外壳中。变化是自然语言决策进入流水线，但没有取代流水线控制面。
- **Buildkite：** 官方 MCP 把 Build State、Log 和 Trigger 暴露为 Tool，Pipeline 还可配置 Model Provider 与 Agent Step。变化是流水线从固定 Step 扩展为能够承载 Agent 分析、Tool 调用和后续触发。
- **Harness：** Worker Agent 使用非 Root 执行、Credential Broker、Egress Proxy、短期 Runtime Token 和第三方 Tool Scope；CI Autofix 把失败、候选修复与复验限制在有界循环。变化是身份、网络、凭据和修复轮次一起进入 Agent Runtime。

### 构建系统与底层资源

- **CircleCI：** Chunk Sidecar / Microbuild 使用预热快照、增量同步和精简结果，为 Agent 每轮提供局部快速反馈，正式 CI 继续承担系统级验证。变化是验证路径开始针对冷启动、重复环境准备与日志体积重构。
- **Nx：** Project Graph 与 Affected Analysis 选择受影响任务，再用历史耗时、Remote Cache 和动态 Worker 分发执行；Self-Healing 生成候选修复后重跑原失败 Task。变化是任务图、资源调度、缓存复用与 Task Oracle 被连接成 Agent 可消费的反馈系统。
- **Bazel：** Hermetic Action Graph、Remote Cache 和 Remote Build Execution 为并行任务提供确定性输入输出、结果复用和远程算力承载。变化不是新增 Agent 产品，而是既有能力因高频并发 Agent 负载而升值。

### 制品仓与供应链

- **Sonatype：** MCP 把版本、漏洞、许可证、维护状态和推荐版本前移到 Agent 选择依赖的时点。变化是依赖选择从模型记忆转向实时供应链事实查询。
- **Cloudsmith：** MCP 暴露漏洞、版本和部分制品管理能力，公开边界只允许非破坏性动作。变化是制品平台从存储面扩展为 Agent 可查询、受限可行动的 Tool 面。
- **JFrog（MCP 为 Beta）：** MCP 覆盖 Repository、Xray、SBOM、Evidence、Release 和部分治理 Tool；Agent Packages Repository 开始存放 Skill、Plugin、Prompt、Hook、MCP Server、Instruction 和 Agent。变化是制品对象扩展到能够改变 Agent 行为的可执行资产。

### 证据回链

完整证据与边界说明见：[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-detailed-v1]]。

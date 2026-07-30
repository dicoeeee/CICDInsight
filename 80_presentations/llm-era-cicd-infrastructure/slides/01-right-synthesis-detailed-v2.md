---
title: Slide 01 右侧综合洞察详细版 v2
status: visual-review-draft
as_of: 2026-07-30
primary_deep_dive: "[[50_deepdives/llm-era-cicd-infrastructure/README]]"
detailed_evidence: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-detailed-v1]]"
purpose: 与左侧公司路径图形成分工；右侧归纳共同演进，同时用可理解的公司机制解释支撑结论。
---

# 右侧综合洞察详细版 v2

## 共同前提

Agent 把研发任务变成长时异步、并行会话、多轮迭代且跨工具产生副作用的机器工作负载。

## 01 代码仓

### 共同演进结论

代码仓从代码事实与人工协作面，扩展为 Agent 的任务、上下文与候选变更协作面。

### 公司证据如何支撑

- **GitHub：** Coding Agent 把 Issue 或评论转成异步任务，在临时 Actions 环境中读取仓库上下文并形成改动，最终以 Branch 或 Draft PR 交付。它把任务委托、执行环境、候选变更和人工审查闭合进 Repository 工作流。
- **GitLab：** Duo Agent Platform 用 Issue、MR、评论或 Pipeline Event 触发 Custom Flow；Flow 在 Runner 中执行，并把 Session、CI Job Log 和候选结果重新关联到 MR。它让 Repository 同时承担 Agent 运行归因和候选交付。

### 对基础设施的具体要求

任务、仓库规则、运行上下文和候选变更需要版本化、可检索、可接续。

## 02 流水线

### 共同演进结论

流水线从预先确定的 Job / DAG 编排器，扩展为承载 Agent Session 与 Tool 调用的动态运行时。

### 公司证据如何支撑

- **GitHub：** Agentic Workflows 把 Markdown 意图编译为标准 Actions YAML，动态 Agent Job 继续运行在 Actions Runner、权限与 Policy 中。自然语言增加了动态判断，但没有替代流水线控制面。
- **Buildkite：** 官方 MCP 把 Build State、Log 和 Trigger 暴露为 Tool，Agent Step 可以在 Pipeline 中分析日志或触发后续构建。流水线开始原生承载 Agent 分析和 Tool 调用。
- **Harness：** Worker Agent 把非 Root 执行、Credential Broker、Egress Proxy、短期 Runtime Token 与 Autofix 复验轮次纳入同一运行环境。变化不只是增加模型调用，而是形成完整 Agent Runtime。

### 对基础设施的具体要求

流水线需要承载 Session、事件、Tool 调用、结构化输出和运行成本。

## 03 构建系统与底层资源

### 共同演进结论

构建能力从开发提速工具，升级为 Agent 高频并发验证的反馈与资源调度底座。

### 公司证据如何支撑

- **CircleCI：** Chunk Sidecar / Microbuild 使用预热快照、增量同步和精简结果，为 Agent 每轮提供局部快速反馈，完整 CI 继续承担系统级验证。它针对冷启动、重复环境准备和日志体积重构验证内环。
- **Nx：** Project Graph 与 Affected Analysis 选择受影响任务，再通过 Remote Cache 和动态 Worker 分发执行；Self-Healing 生成候选修复后重跑原失败 Task。它把任务选择、资源调度、缓存复用和 Task 复验串成反馈系统。
- **Bazel：** Hermetic Action Graph、Remote Cache 和 RBE 早于大模型存在，但能为并行 Agent 提供确定性输入输出、结果复用和远程算力承载。变化是既有构建能力因新负载而升值。

### 对基础设施的具体要求

平台需要降低排队、冷启动和重复计算，并动态调度 Worker 与远程算力。

## 04 制品仓与供应链

### 共同演进结论

制品平台从保存文件，扩展为 Agent 可查询的供应链事实、交付证据与行为资产管理面。

### 公司证据如何支撑

- **Sonatype：** MCP 把版本、漏洞、许可证、维护状态和推荐版本前移到 Agent 选择依赖的时点。依赖决策从模型记忆转向实时供应链事实。
- **Cloudsmith：** MCP 把漏洞、版本和部分制品管理能力暴露为 Tool，并只开放非破坏性动作。制品平台开始从可查询扩展到受限可行动。
- **JFrog：** MCP 覆盖 Repository、Xray、SBOM、Evidence 和 Release；Agent Packages Repository 又开始存放 Skill、Plugin、Prompt 和 MCP Server。制品对象从 Package / Container 扩展到能够改变 Agent 行为的资产。

### 对基础设施的具体要求

版本、风险、来源、Digest 与行为资产需要能够被机器实时查询、绑定和追溯。

## 演讲备注

产品状态、控制边界和逐项来源回链见：[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-detailed-v1]]。

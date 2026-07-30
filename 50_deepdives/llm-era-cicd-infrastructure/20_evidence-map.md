---
title: 大模型时代的 CI/CD 基础设施证据矩阵
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/evidence-map
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
confidence: high
---

# Claim—Evidence—Gap Matrix

## 证据分级

- **A：** 官方规范、官方产品文档、GA/Preview Changelog、可检查的官方实现。
- **B：** 第一方工程实践或厂商公开测量，可证明机制或厂商经验，不能外推行业成效。
- **C：** 原始论文或预印本，可校准能力上限与采用现状，仍受样本和评审状态限制。

## 核心矩阵

| Claim ID | 待验证论点 | 支持证据 | 反例 / 限制 | 当前判断 |
|---|---|---|---|---|
| C01 | 大模型没有替代 CI/CD 基础设施，而是把它从面向人的工具链扩展为 Agent 的上下文面、执行面与治理面。 | DORA 发现 AI 的收益依赖内部平台、版本控制、自动测试和快速反馈，且 AI 采用仍与交付不稳定性负相关（[[00_sources/briefs/2025-dora-state-ai-assisted-software-development|A/C]]）；GitHub、GitLab、Buildkite、CircleCI、JFrog 分别把 Agent 接到代码仓、Runner、Pipeline 与制品数据（[[50_deepdives/llm-era-cicd-infrastructure/research-code-repository-2026-07-28|A]]、[[50_deepdives/llm-era-cicd-infrastructure/research-pipeline-build-2026-07-28|A/B]]、[[50_deepdives/llm-era-cicd-infrastructure/research-artifact-supply-chain-2026-07-28|A]]）。 | DORA 研究覆盖广义 AI 辅助开发，不是 Agentic CI/CD 的因果实验；“三面”是跨来源架构推断。 | **成立，high。** |
| C02 | 代码仓正在成为 Agent 的任务与协作控制面。 | GitHub Cloud Agent GA 后可从 Issue、Agents UI、PR 评论进入，在 Actions 支撑的临时环境执行并交付 PR（[[00_sources/briefs/2026-github-cloud-agent-repository-control-plane|A]]）；GitLab Custom Flows 19.2 GA，Issue/MR/评论/Pipeline Event 可作为入口，Flow 在 Runner 执行并关联 CI 日志（[[00_sources/briefs/2026-gitlab-duo-agent-platform|A]]）。 | 不能推出所有仓库已采用；第三方 Agents、部分 API 和治理能力仍是 Preview/Beta；Repository 不是唯一推理运行时。 | **成立，high。** |
| C03 | CI 正从一次性最终门禁扩展为“Agent 高频验证内环 + 完整确定性外环”。 | CircleCI Chunk Sidecar/Microbuild 以快照、增量同步和直接反馈承接内环，正式 CI 保留系统级验证（[[00_sources/briefs/2026-circleci-agentic-validation-infrastructure|A/B]]）；Harness CI Autofix、Nx Self-Healing 提供受轮次/Task/分支限制的修复—复验循环（[[00_sources/briefs/2026-harness-worker-agents|A/B]]、[[00_sources/briefs/2026-nx-self-healing-ci|A/B]]）。 | Sidecar 不是完全 Hermetic；厂商没有提供可统一比较的错误修复率和长期成本；内环通过不能替代 Required Checks。 | **成立，medium-high。** |
| C04 | 远程缓存、影响分析、任务图、远程执行和动态调度大多不是大模型新发明，但已从性能优化升级为 Agent 规模化验证的经济底座。 | Nx 根据 Project Graph、Affected 范围和历史耗时分发任务；Bazel Remote Cache/RBE 早已提供多机执行与结果复用（[[50_deepdives/llm-era-cicd-infrastructure/research-pipeline-build-2026-07-28|A]]）。CircleCI 明确指出多 Agent、多轮完整 CI 会带来启动、Fan-out、日志 Token 和成本问题（[[00_sources/briefs/2026-circleci-agentic-validation-infrastructure|B]]）。 | 尚无跨行业数据证明 Agent 已普遍导致 Runner 并发或缓存量增长；收益必须用本地遥测验证。 | **机制成立，行业规模 unverified；medium-high。** |
| C05 | Agent Runtime 需要独立于普通 Pipeline Step 的硬隔离和受控副作用。 | GitHub Agentic Workflows 默认只读、Firewall/Sandbox、无 Secret、Safe Output 与 Threat Detection（[[00_sources/briefs/2026-github-agentic-workflows|A]]）；Harness Worker Agent 使用非 Root、Credential Broker、Egress Proxy、短期 Token 与 Tool 交集（[[00_sources/briefs/2026-harness-worker-agent-security|A/B]]）；GitLab Flow 可配置网络规则并使用 Service Account/Composite Identity（[[00_sources/briefs/2026-gitlab-duo-agent-platform|A]]）。 | GitHub Agentic Workflows 仍是 Public Preview；Harness 安全效果为厂商自述；不是所有普通 CI Runner 自动具备同等边界。 | **成立，high。** |
| C06 | 机器可发现、结构化、可审计的接口正在成为 CI/CD 产品的一等入口。 | Buildkite 以 MCP 暴露 Build State/Log/Trigger，以 Model Provider 承载 Agent Step（[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines|A]]）；Cloudsmith/JFrog/Sonatype 将制品与供应链能力暴露为 MCP Tools（[[00_sources/briefs/2026-cloudsmith-mcp-artifact-management|A]]、[[00_sources/briefs/2026-jfrog-skills-and-mcp|A]]、[[00_sources/briefs/2025-sonatype-guide-supply-chain|A]]）。 | 多数能力是对既有 API/CLI 的新包装；MCP Tool 可发现不等于获得授权、幂等、审批或事务语义。 | **成立，high。** |
| C07 | 制品仓正在从二进制存储扩展为 Agent 可查询的供应链信任系统和受限行动面。 | Sonatype 把版本/漏洞/许可/维护情报前移到依赖选择；Cloudsmith 可查询漏洞/版本并管理部分制品；JFrog 可查询 Xray/SBOM/Evidence/Release 状态并暴露部分治理操作（[[50_deepdives/llm-era-cicd-infrastructure/research-artifact-supply-chain-2026-07-28|A]]）。 | Cloudsmith 当前只承诺非破坏性动作且 Policy 激活保持人工；JFrog MCP 为 Beta；没有跨厂商成效数据。 | **成立但自治受限，high。** |
| C08 | Agent Skills、Plugins、Prompts、Hooks 和 MCP Server 本身开始成为新的软件供应链资产。 | JFrog Agent Packages Repository 已支持发布/安装上述资产，Skills Repository 和语义扫描部分能力为 Open Beta（[[00_sources/briefs/2026-jfrog-skills-and-mcp|A]]）。 | 当前主要由单一厂商样本支撑；格式、签名、依赖图、扫描与跨仓互操作尚未形成统一成熟标准。 | **产品变化成立；行业普遍性 medium。** |
| C09 | 静态共享 Bot Token 不足以承接 Agent；身份开始向“触发人 × Agent/服务账号 × Tool × Run”的短期交集演进。 | GitLab Composite Identity 取触发人与服务账号权限交集并保留归因（[[00_sources/briefs/2026-gitlab-duo-agent-platform|A]]）；Harness Runtime Token 为 `parent RBAC ∩ declared grant`，第三方工具再求交集（[[00_sources/briefs/2026-harness-worker-agent-security|A/B]]）；GitHub Cloud Agent 使用独立 Agent/MCP Secret 通道（[[00_sources/briefs/2026-github-cloud-agent-repository-control-plane|A]]）。 | 不同平台身份模型不统一；GitHub 某些 Agent Tasks API 仍使用 PAT/OAuth；事件触发的 Principal 继承可能存在缺口。 | **方向成立，medium-high。** |
| C10 | “通过/失败”不足以证明 Agent 变更可信，证据需要绑定任务、Session、Commit、Builder、Artifact Digest、Policy 与批准链。 | GitHub Agent Audit 记录 Actor/Session/Initiator；SLSA v1.2 要求 Builder 控制面生成 Provenance 并在高等级隔离 Build；GitHub Artifact Attestations 绑定 Workflow/Repo/Commit/Digest，可关联 SBOM（[[00_sources/briefs/2026-github-artifact-attestations-slsa|A]]）。 | GitHub 不记录全部本地 Prompt；GitLab AI Audit 仍有 Beta/Experiment；Attestation 只能证明来源和过程，不证明安全或业务正确。 | **成立，high。** |
| C11 | Agent 工作负载使成本面从 Runner 分钟扩展为“计算 + 推理 + 尝试次数 + 反馈延迟 + Verified Outcome”。 | GitHub Agentic Workflows 同时计量 Actions Minutes 与 AI Credits，并支持 Run 级预算（[[00_sources/briefs/2026-github-agentic-workflows|A]]）；CircleCI 讨论完整 CI 作为内环的启动、日志 Token 和 Fan-out 成本（[[00_sources/briefs/2026-circleci-agentic-validation-infrastructure|B]]）。 | 除少数厂商外没有统一成本模型；尚无跨平台数据证明单位价值下降或 ROI 改善。 | **成本维度成立，ROI unverified；medium-high。** |
| C12 | Agent 不得同时定义、修改并裁决自己的成功判据。 | GitHub Protected Branch/Ruleset 可继续强制 Review、Checks、签名和部署成功（[[00_sources/briefs/2026-github-cloud-agent-repository-control-plane|A]]）；Artifact Attestation/SLSA 要求 Builder 控制面与验证策略独立（[[00_sources/briefs/2026-github-artifact-attestations-slsa|A]]）；[[50_deepdives/cicd-self-healing/README|CI/CD 自愈专题]]已证明 Oracle 与 Repairer 必须分离。 | 人工评审也可能失效；外部 Gate 仍需覆盖业务语义，而不仅是工具退出码。 | **规范性结论，high。** |
| C13 | 行业仍处于不均衡的早中期：基础设施接入已真实发生，但全面自治尚未得到证据支持。 | GitHub Cloud Agent GA、GitLab Agent Platform/Custom Flows GA、Artifact Attestations GA；同时 GitHub Agentic Workflows Public Preview、JFrog MCP Beta、Skills Open Beta。预印本显示 Agent 对 CI/CD 配置的修改仅占其变更约 3.25%（[[00_sources/briefs/2026-agents-touch-cicd-configurations|C]]）；CI-Repair-Bench 最佳受测模型在 567 个真实故障中修复 18.9%（[[50_deepdives/cicd-self-healing/recent-paper-search-2026-07-25|C]]）。 | 两项论文均为预印本，样本偏向公开 GitHub；产品状态变化很快。 | **成立，high。** |

## 结论门禁

以下表述可以进入正式报告：

- CI/CD 基础设施增加了 Agent 原生接口、隔离运行时、低延迟验证、任务级身份与供应链行动面；
- 构建图、缓存、远程执行、签名和 Provenance 多为既有能力，但因 Agent 工作负载而提升为关键基础；
- Agent 的概率性决策必须被包在确定性门禁、短期权限和可追溯证据之内。

以下表述不得进入正式结论：

- “Agent 已替代传统 CI/CD”；
- “MCP 天然安全或天然获得授权”；
- “Remote Cache/RBE 是大模型时代新发明”；
- “Build Green、Attestation 存在或 Agent 自评等于制品可信”；
- “Agent 已能普遍自主晋级、豁免、删除制品或发布生产”；
- 将 Public Preview、Beta、Open Beta 与 GA 混写。

## 证据底稿

- [[50_deepdives/llm-era-cicd-infrastructure/research-code-repository-2026-07-28|代码仓与托管平台研究]]
- [[50_deepdives/llm-era-cicd-infrastructure/research-pipeline-build-2026-07-28|流水线与构建系统研究]]
- [[50_deepdives/llm-era-cicd-infrastructure/research-artifact-supply-chain-2026-07-28|制品仓与供应链研究]]

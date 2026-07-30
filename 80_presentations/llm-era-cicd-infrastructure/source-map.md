---
title: 大模型时代的 CI/CD 基础设施单页 Source Map
tags:
  - research/agentic-cicd
  - deliverable/presentation/source-map
status: fact-audited
as_of: 2026-07-29
---

# Source Map

| Claim ID | 页面表述 | Deep Dive 入口 | 主要一手来源 | 证据状态与边界 |
|---|---|---|---|---|
| P01 | 大模型没有替代 CI/CD，而是形成 Agent 面与确定性控制面 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C01]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F1：变化的本质是角色分层，不是替代|F1]] | [DORA 2025](https://dora.dev/research/2025/dora-report/)、[GitHub Agentic Workflows](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows)、[SLSA v1.2](https://slsa.dev/spec/v1.2/) | 跨来源架构推断；不表示全链路自治成熟 |
| P02 | 代码仓扩展为任务、上下文、Agent 配置和候选变更控制面 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C02]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面|F2]] | [GitHub Cloud Agent GA](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available/)、[GitLab Duo Agent Platform](https://docs.gitlab.com/user/duo_agent_platform/) | 已有 GA 样本；部分 API、审计和第三方 Agent 仍为 Preview/Beta |
| P03 | 流水线分化为低延迟验证内环与完整确定性外环 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C03、C05]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F3：流水线正分化为低延迟验证内环和完整交付外环|F3]] | [CircleCI Agentic Validation](https://circleci.com/blog/agentic-validation-needs-different-infrastructure/)、[Nx Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci)、[Harness Agents in Pipelines](https://developer.harness.io/docs/platform/harness-ai/core-capabilities/in-your-pipelines/harness-agents/) | 机制已产品化；效果数字主要为厂商自述，内环不替代 Required Checks |
| P04 | 构建图、增量、缓存、远程执行与隔离从提速能力升值为 Agent 的反馈与成本底座 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C04]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制|F4]] | [Nx Distributed Execution](https://nx.dev/docs/features/ci-features/distribute-task-execution)、[Bazel Remote Execution](https://bazel.build/remote/rbe)、[Bazel Remote Caching](https://bazel.build/remote/caching) | 能力本身并非大模型新发明；行业规模与 ROI 尚无统一数据 |
| P05 | 制品仓扩展为供应链事实、信任图、受限行动面，并开始管理 Agent 行为资产 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C07、C08]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F5：制品仓从存储面扩展为供应链信任面与受限行动面|F5]] | [Cloudsmith MCP](https://cloudsmith.com/changelog/manage-your-supply-chain-using-natural-language-with-mcp)、[JFrog Skills and MCP](https://jfrog.com/blog/ai-agents-jfrog-skills-mcp-tools/)、[GitHub Artifact Attestations](https://docs.github.com/en/actions/concepts/security/artifact-attestations) | 查询最成熟；高风险写入、Policy 激活和 Agent 资产治理仍处于受限或早期状态 |
| P06 | 身份、证据与成本从配套能力变成规模化采用 Agent 的主约束 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C09—C11]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F7：身份从“一个 Bot Token”演进为任务级委托与多重求交|F7]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F8：Pass/Fail 正扩展为完整 Lineage，但审计覆盖仍不完整|F8]] | [SLSA Build Requirements](https://slsa.dev/spec/v1.2/build-requirements)、[GitHub Artifact Attestations GA](https://github.blog/changelog/2024-06-25-artifact-attestations-is-generally-available/)、[GitHub Agentic Workflows Billing](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows) | 身份模型和成本口径尚未跨平台统一；Attestation 不等于安全或业务正确 |
| P07 | 企业应先补验证、身份和证据底座，再逐级扩大 Agent 写权限 | [[50_deepdives/llm-era-cicd-infrastructure/60_playbook#八、推荐的近期优先级|Playbook]]、[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F9：最大的风险不是模型偶尔答错，而是把概率性结论误接成确定性副作用|F9]] | [GitHub Issues Agent Automation Controls](https://github.blog/changelog/2026-07-23-agent-automation-controls-in-github-issues-in-public-preview/)、[SLSA v1.2](https://slsa.dev/spec/v1.2/) | 企业建议；适用前提是 Agent 将获得写权限或触达发布链路 |

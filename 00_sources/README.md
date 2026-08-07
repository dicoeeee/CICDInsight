---
title: 信息源与 Source Brief
tags:
  - research/agentic-cicd
  - evidence/source
status: active
as_of: 2026-08-04
---

# 信息源与 Source Brief

> [!info] 三条作业流共享的 L0 证据层
> [[00_sources/agentic-cicd-source-landscape|Agentic CI/CD source landscape]] 收录 81 条 2025-07-01—2026-07-14 核心一手资料。每条均包含组织、日期、URL、来源类型、能力状态、八阶段标签、工具类别、地域、关键事实、价值和置信度/局限，因此本身就是一组结构化 Brief。[[00_sources/source-pruning-2026-07-14|信息源精简审计]]记录从 107 条候选源缩减到 80 条核心源、再增补 CLI-Anything 形成当前 81 条核心源的过程；[[00_sources/incremental-source-research-2026-07-14|增量研究记录]]保留前期排重和选源过程。下列 76 个独立 Source Brief（另加模板）对高频主题做进一步展开，可能引用核心登记之外的补充材料。

本目录只维护可追溯事实，不决定所有事实都必须进入 Deep Dive。批量洞察可直接从本层更新分类视图和主报告；单对象研究则把相关证据映射到专题 Claim；Presentation 通过 Deep Dive 使用这些事实。

## 证据口径

- 只用官方文档、官方工程实践/发布说明、第一方开源仓库、规范和原始研究支撑核心事实。
- `ga` 只表示正式可用，不代表已证明企业效果。
- `preview`、`experimental`、`roadmap` 和 `research` 不写成当前生产能力。
- 厂商效果数据保留“厂商自述/第一方研究”标签，不外推为行业因果结论。
- 开源 Star 仅用于关注度快照，不作为生产成熟度证明。

## GitHub 与 Microsoft

- [[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows]]
- [[00_sources/briefs/2026-github-cloud-agent-repository-control-plane|GitHub Cloud Agent Repository Control Plane]]
- [[00_sources/briefs/2026-github-artifact-attestations-slsa|GitHub Artifact Attestations 与 SLSA]]
- [[00_sources/briefs/2026-github-gh-aw-open-source|github/gh-aw]]
- [[00_sources/briefs/2026-githubnext-agentics-examples|GitHub Next Agentics Examples]]
- [[00_sources/briefs/2025-github-remote-mcp-server-ga|GitHub Remote MCP Server GA]]
- [[00_sources/briefs/2026-microsoft-repository-migration-to-github|Microsoft 仓库迁移实践]]
- [[00_sources/briefs/2026-azure-devops-mcp-open-source|Azure DevOps MCP]]

## GitLab 与 Harness

- [[00_sources/briefs/2026-gitlab-duo-agent-platform|GitLab Duo Agent Platform]]
- [[00_sources/briefs/2026-harness-ai-platform|Harness AI Platform 2026 能力总览]]
- [[00_sources/briefs/2026-harness-worker-agents|Harness Worker Agents]]
- [[00_sources/briefs/2026-harness-worker-agent-security|Harness Worker Agent 隔离、身份与权限]]
- [[00_sources/briefs/2026-harness-code-quality-agents|Harness Code Quality Agents]]
- [[00_sources/briefs/2026-harness-ai-devops-agent|Harness DevOps Agent]]
- [[00_sources/briefs/2026-harness-ai-test-automation|Harness AI Test Automation]]
- [[00_sources/briefs/2026-harness-ai-sre|Harness AI SRE Agents 与 Runbooks]]

## AWS、Google 与 DORA

- [[00_sources/briefs/2026-aws-devops-agent-production-operations-ga|AWS DevOps Agent Production Operations GA]]
- [[00_sources/briefs/2026-aws-devops-agent-release-management-preview|AWS Release Management Preview]]
- [[00_sources/research-aws-devops-agent-deep-dive-2026-08-03|AWS DevOps Agent 一手资料深度研究]]
- [[00_sources/research-amazon-bedrock-agentcore-architecture-2026-08-03|Amazon Bedrock AgentCore 产品边界、架构与运行约束]]
- [[00_sources/research-amazon-bedrock-agentcore-governance-2026-08-03|Amazon Bedrock AgentCore 企业治理与落地边界]]
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-mechanics-2026-08-03|Amazon Bedrock AgentCore Evaluations 机制、数据合同与生产边界]]
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|Amazon Bedrock AgentCore Evaluations 与 CI/CD 发布门禁边界]]
- [[00_sources/research-amazon-bedrock-agentcore-memory-cicd-2026-08-04|Amazon Bedrock AgentCore Memory 机制与 CI/CD 启发]]
- [[00_sources/research-aws-llm-cicd-agent-platform-capabilities-2026-08-03|AgentCore 与 CI/CD Agent 底层平台能力]]
- [[00_sources/research-agentcore-transform-devops-agent-relationship-2026-08-03|AgentCore、AWS Transform 与 AWS DevOps Agent 层级关系]]
- [[00_sources/briefs/2026-google-gemini-cloud-assist-next26|Google Gemini Cloud Assist Next 26]]
- [[00_sources/briefs/2025-dora-state-ai-assisted-software-development|DORA 2025 AI-assisted Software Development]]
- [[00_sources/briefs/2026-dora-platform-engineering-ai|DORA Platform Engineering and AI]]

## 代码评审、质量、安全与测试

- [[00_sources/briefs/2026-atlassian-rovo-code-review-study|Atlassian Rovo Code Review Study]]
- [[00_sources/briefs/2026-atlassian-rovo-platform-driven-development|Atlassian Platform-driven Development]]
- [[00_sources/briefs/2026-qodo-agentic-code-review|Qodo Agentic Code Review]]
- [[00_sources/briefs/2026-snyk-agent-fix-architecture|Snyk Agent Fix]]
- [[00_sources/briefs/2025-sonar-foundation-agent|Sonar Foundation Agent]]
- [[00_sources/briefs/2026-tricentis-agentic-test-automation|Tricentis Agentic Test Automation]]
- [[00_sources/briefs/2026-human-ai-synergy-agentic-code-review|Human-AI Synergy in Agentic Code Review]]
- [[00_sources/briefs/2025-uber-ureview|Uber uReview 大规模评审实践]]
- [[00_sources/briefs/2026-bloomberg-pomona|Bloomberg Pomona 持续维护 Agent]]
- [[00_sources/briefs/2026-codethread-maintainability|CodeThread 长期可维护性研究]]
- [[00_sources/briefs/2026-malicious-agent-skills-usenix|恶意 Agent Skills 实证研究]]

## Pipeline、构建与开源 Agent

- [[00_sources/briefs/2026-dagger-engine-modules-execution|Dagger Engine、Module 与增量执行]]
- [[00_sources/briefs/2026-dagger-sandbox-secrets-engine-security|Dagger Function 沙箱、Secret 与 Engine 特权边界]]
- [[00_sources/briefs/2026-dagger-cloud-engines-checks|Dagger Cloud Engines 与 Cloud Checks Early Access]]
- [[00_sources/briefs/2026-dagger-llm-mcp-runtime|Dagger LLM Primitive、Tool Use 与 MCP]]
- [[00_sources/briefs/2023-dagger-airbyte-ci-case-study|Airbyte 使用 Dagger 重构 CI 与共享缓存]]
- [[00_sources/briefs/2025-dagger-puzzle-kubevirt-ci|Puzzle 以 KubeVirt 隔离 Dagger 特权 Engine]]
- [[00_sources/briefs/2026-circleci-chunk-agent|CircleCI Chunk]]
- [[00_sources/briefs/2026-circleci-agentic-validation-infrastructure|CircleCI Agentic Validation Infrastructure]]
- [[00_sources/briefs/2026-nx-self-healing-ci|Nx Self-Healing CI]]
- [[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines|Buildkite AI Agents in Pipelines]]
- [[00_sources/briefs/2026-cloudbees-devops-agent-kit|CloudBees DevOps Agent Kit]]
- [[00_sources/briefs/2026-jenkins-ai-agent-plugin|Jenkins AI Agent Plugin]]
- [[00_sources/briefs/2026-claude-code-github-action|Claude Code GitHub Action]]
- [[00_sources/briefs/2026-openhands-agent-sdk|OpenHands Agent SDK]]

## Agent 原生接口与 CLI

- [[00_sources/briefs/2026-cli-anything|CLI-Anything：Agent 原生接口生成与 CLI-Hub]]

## 制品、部署与运行运维

- [[00_sources/briefs/2026-jfrog-skills-and-mcp|JFrog Skills and MCP]]
- [[00_sources/briefs/2026-cloudsmith-mcp-artifact-management|Cloudsmith MCP 制品仓行动面]]
- [[00_sources/briefs/2025-sonatype-guide-supply-chain|Sonatype Guide 供应链情报]]
- [[00_sources/briefs/2026-github-dependabot-agent-remediation|Dependabot 到 Coding Agent 修复闭环]]
- [[00_sources/briefs/2026-terraform-mcp-server|Terraform MCP 自治分层]]
- [[00_sources/briefs/2026-octopus-agentic-deployment|Octopus Agentic Deployment]]
- [[00_sources/briefs/2026-spacelift-intelligence|Spacelift Intelligence]]
- [[00_sources/briefs/2026-akuity-agents-gitops-operations|Akuity Agents for GitOps]]
- [[00_sources/briefs/2026-holmesgpt-sre-agent|HolmesGPT SRE Agent]]
- [[00_sources/briefs/2026-servicenow-agentic-change-management|ServiceNow Agentic Change Management]]

## 中国厂商

- [[00_sources/briefs/2026-alibaba-yunxiao-ai-code-review|阿里云云效 AI CodeReview]]
- [[00_sources/briefs/2026-tencent-cloudq-devops-agent|腾讯 CloudQ DevOps Agent]]
- [[00_sources/briefs/2026-baidu-comate-4-agent|百度 Comate 4.0]]
- [[00_sources/briefs/2026-huawei-codearts-agent|华为 CodeArts Agent]]
- [[00_sources/briefs/2026-meituan-agent-evaluation-ai-coding|美团 Agent 评测与 Pre-PR 实践]]
- [[00_sources/briefs/2026-jd-joyagent-code-review|京东 JoyAgent 双 RAG 评审]]
- [[00_sources/research-bytedance-cicd-2024-2026-trends-2026-08-07|字节跳动与火山引擎 CI/CD 变化趋势（2024 至今）]]
- [[00_sources/research-openai-anthropic-cicd-trends-2026-08-07|OpenAI 与 Anthropic 的 CI/CD 变化趋势（2024 至今）]]
- [[00_sources/research-github-microsoft-cicd-trends-2026-08-07|GitHub 与 Microsoft CI/CD 变化趋势（2024 至今）]]

## 企业实践、研究、基准与治理

- [[00_sources/research-aws-cicd-trends-2024-2026-2026-08-07|AWS CI/CD 变化趋势（2024 至今）]]

- [[00_sources/briefs/2025-whatscode-enterprise-practice|WhatsCode 大规模企业实践]]
- [[00_sources/briefs/2026-swe-ci-benchmark|SWE-CI]]
- [[00_sources/briefs/2026-swe-evo-benchmark|SWE-EVO]]
- [[00_sources/briefs/2026-swe-chat-real-world-agent-use|SWE-chat]]
- [[00_sources/briefs/2026-agents-touch-cicd-configurations|Agents Touch CI/CD Configurations]]
- [[00_sources/briefs/2026-ci-repair-bench|CI-Repair-Bench]]
- [[00_sources/briefs/2026-metr-time-horizon-1-1|METR Time Horizon 1.1]]
- [[00_sources/briefs/2026-metr-shushcast-monitorability|METR SHUSHCAST]]
- [[00_sources/briefs/2026-nist-agent-identity-authorization|NIST Agent Identity and Authorization Concept Paper]]
- [[00_sources/briefs/2026-openid-authzen-agent-authorization|OpenID AuthZEN Agent Authorization Drafts]]
- [[00_sources/briefs/2026-google-cloud-agent-identity|Google Cloud Agent Identity]]
- [[00_sources/briefs/2026-uber-agent-identity|Uber 生产级 Agent 身份与委托链]]
- [[00_sources/briefs/2025-samos-securing-mcp-workflows|SAMOS MCP Security]]

## 模板

- [[00_sources/briefs/Source Brief Template|Source Brief Template]]
- [[00_sources/_templates/Batch Insight Template|Batch Insight Template]]：记录一次批量洞察的范围、排重、维度更新、候选观点与完成门禁。

## 覆盖空白

当前一手资料在以下方向仍不足，主报告不作强结论：

- 大型企业生产发布 L4 自治的独立成效数据；
- 制品仓已出现 Agent 可调用行动面，但自动签名、跨环境晋级和版本决策的长期生产数据仍不足；
- 跨云、跨 DevOps 平台的统一身份和审计落地案例；
- Agent 引入后的变更失败率、缺陷逃逸率和总成本的长期对照实验；
- 中国大型企业已有评审、Pre-PR 和知识化门禁实践，但制品、发布与身份治理的一手资料仍不足。

---
title: Agentic CI/CD 公司与能力作业流 - Source Map
tags:
  - research/agentic-cicd
  - deliverable/presentation
  - evidence/source-map
status: draft
as_of: 2026-08-09
---

# Source Map

| 页面 | 主要 Deep Dive | 辅助分类视图 | 一手证据入口 | 映射状态 |
|---|---|---|---|---|
| GitHub | [[50_deepdives/github-agentic-workflows/90_report|GitHub Agentic Workflows 深研]]、[[50_deepdives/github-agentic-workflows/fixed-actions-relationship-research|与固定 Actions 的关系证据]] | [[20_summaries/companies/README#1. GitHub：把 Agent 编译进 Actions|GitHub 公司总结]] | [[00_sources/briefs/2026-github-agentic-workflows]]、[[00_sources/briefs/2026-github-gh-aw-open-source]]、[[00_sources/briefs/2025-github-remote-mcp-server-ga]] | `mapped` |
| Harness | [[50_deepdives/harness-company/architecture-validation-2026-07-23|2026 产品组合与架构核验]]、[[50_deepdives/harness-company/architecture-content-alignment-2026-07-23|架构图内容对齐核验]]、[[50_deepdives/harness-company/intelligent-workflow-orchestration-2026-07-24|Intelligent Workflow Orchestration 核验]]、[[50_deepdives/harness-company/agentic-ci-title-thesis-2026-07-23|单页主张与标题核验]]、[[50_deepdives/harness-company/90_report#5.1 Knowledge Graph + HQL：先建模，再让模型查询|Knowledge Graph + HQL 技术原理]] | [[20_summaries/companies/README#4. Harness：从 Pipeline 平台到 Agent 控制面|Harness 公司总结]] | [[00_sources/briefs/2026-harness-ai-platform]]、[[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-harness-worker-agent-security]]、[[00_sources/briefs/2026-harness-code-quality-agents]]、[[00_sources/briefs/2026-harness-ai-devops-agent]] | `mapped` |
| GitLab | 暂无公司专题 | [[20_summaries/companies/README#3. GitLab：一体化上下文与治理控制面|GitLab 公司总结]] | [[00_sources/briefs/2026-gitlab-duo-agent-platform]] | `blocked-by-deep-dive` |
| AWS | [[50_deepdives/amazon-bedrock-agentcore/README|AgentCore Deep Dive]]、[[50_deepdives/amazon-bedrock-agentcore/55_evaluations-insight|AgentCore Evaluations 补充洞察]]、[[50_deepdives/aws-devops-agent/README|AWS DevOps Agent Deep Dive]]、[[00_sources/research-agentcore-transform-devops-agent-relationship-2026-08-03|产品层级关系核验]] | [[20_summaries/companies/README#5. AWS：从生产根因分析向发布决策扩展|AWS 公司总结]] | [[00_sources/research-amazon-bedrock-agentcore-evaluations-mechanics-2026-08-03]]、[[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03]]、[[00_sources/research-aws-devops-agent-deep-dive-2026-08-03]]、[[00_sources/briefs/2026-aws-devops-agent-release-management-preview]] | `mapped / left-mechanism-solution-proposed` |
| CI 自愈 | [[50_deepdives/cicd-self-healing/90_report|CI/CD 自愈深研]] | [[30_summaries/stages/README#4. 编译、构建与出包|构建阶段总结]] | [[00_sources/briefs/2026-circleci-chunk-agent]]、[[00_sources/briefs/2026-nx-self-healing-ci]]、[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines]] | `mapped` |
| CI 自愈六家公司总览（05B） | [[50_deepdives/cicd-self-healing/README|CI/CD 自愈专题]]、[[50_deepdives/cicd-self-healing/35_company-mechanism-audit|六家公司机制审计]]、[[50_deepdives/cicd-self-healing/70_fact-audit|逐主张事实审计]] | [[20_summaries/companies/README|厂商路线比较]]、[[30_summaries/stages/README#4. 编译、构建与出包|构建阶段总结]] | [[00_sources/briefs/2026-github-agentic-autofix-code-scanning]]、[[00_sources/briefs/2026-gitlab-fix-cicd-pipeline-flow]]、[[00_sources/briefs/2026-circleci-chunk-agent]]、[[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-nx-self-healing-ci]]、[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines]]、[Buildkite 官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/) | `mapped / deep-dive-ready / visual pending` |
| CLI / MCP 选型 | [[50_deepdives/cli-agent-interface/README|CLI Agent 接口深研]]、[[50_deepdives/cli-agent-interface/industry-mainstream-slide-evidence-refresh-2026-07-28|2026-07-28 单页证据刷新]]、[[50_deepdives/cli-agent-interface/industry-mcp-evolution-and-progressive-discovery-2026-07-28|MCP 渐进发现证据]] | [[50_deepdives/cli-vs-mcp-decision-guide|CLI vs MCP 决策指南]] | OpenAI Codex CLI、GitHub Copilot CLI、Anthropic Claude Code、OpenCode、GitHub CLI、Snyk、CircleCI、JFrog、HashiCorp、Sentry 与 MCP 官方资料；最终成品见 `outputs/cli-software-delivery-agent-interface-insight-final.pptx` | `mapped` |
| Agent 工作台与 CI/CD Agent 产品功能矩阵 | [[50_deepdives/agent-workbench/README|Agent 工作台产品功能与控制边界 Deep Dive]]、[[50_deepdives/agent-workbench/70_fact-audit|功能逐主张事实审计]] | [[40_summaries/crosscutting/README|工作流程与产品功能变化]] | [[00_sources/briefs/2026-tencent-workbuddy-agent-workbench]]、[[00_sources/briefs/2026-openai-chatgpt-work-codex-workspace-agents]]、[[00_sources/briefs/2026-anthropic-claude-cowork]]、[[00_sources/briefs/2026-gitlab-duo-agent-platform]]、[[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-github-agentic-workflows]] | `mapped / deep-dive-ready` |

## 状态校验规则

- 正式页面必须有 `presentation_ready: true` 的主要 Deep Dive；`blocked-by-deep-dive` 页面不能进入视觉制作。
- `mapped` 只表示 Source Map 已完成映射，不替代专题 README 中的 `presentation_ready`。
- 页面制作前回到 Source Brief 核对产品状态和时间点。
- 如果来源只能证明单点能力，不向端到端作业流外推。
- 客户效果数据与产品机制分开引用，不用选择性案例代表行业平均水平。

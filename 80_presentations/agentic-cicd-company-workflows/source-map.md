---
title: Agentic CI/CD 公司与能力作业流 - Source Map
tags:
  - research/agentic-cicd
  - deliverable/presentation
  - evidence/source-map
status: draft
---

# Source Map

| 页面 | 主要 Deep Dive | 辅助分类视图 | 一手证据入口 | 映射状态 |
|---|---|---|---|---|
| GitHub | [[50_deepdives/github-agentic-workflows/90_report|GitHub Agentic Workflows 深研]]、[[50_deepdives/github-agentic-workflows/fixed-actions-relationship-research|与固定 Actions 的关系证据]] | [[20_summaries/companies/README#1. GitHub：把 Agent 编译进 Actions|GitHub 公司总结]] | [[00_sources/briefs/2026-github-agentic-workflows]]、[[00_sources/briefs/2026-github-gh-aw-open-source]]、[[00_sources/briefs/2025-github-remote-mcp-server-ga]] | `mapped` |
| Harness CI | [[50_deepdives/harness-company/90_report#5.3 Pipeline：概率决策与确定性执行的混合图|Harness Pipeline 混合控制机制]]、[[50_deepdives/harness-company/ci-validation-2026-07-22|2026-07-22 CI 检验与补充]] | [[20_summaries/companies/README#4. Harness：从 Pipeline 平台到 Agent 控制面|Harness 公司总结]] | [[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-harness-worker-agent-security]]、[[00_sources/briefs/2026-harness-code-quality-agents]]、[[00_sources/briefs/2026-harness-ai-devops-agent]] | `mapped` |
| GitLab | 暂无公司专题 | [[20_summaries/companies/README#3. GitLab：一体化上下文与治理控制面|GitLab 公司总结]] | [[00_sources/briefs/2026-gitlab-duo-agent-platform]] | `blocked-by-deep-dive` |
| AWS | 暂无公司专题 | [[20_summaries/companies/README#5. AWS：从生产根因分析向发布决策扩展|AWS 公司总结]] | [[00_sources/briefs/2026-aws-devops-agent-production-operations-ga]]、[[00_sources/briefs/2026-aws-devops-agent-release-management-preview]] | `blocked-by-deep-dive` |
| CI 自愈 | [[50_deepdives/cicd-self-healing/90_report|CI/CD 自愈深研]] | [[30_summaries/stages/README#4. 编译、构建与出包|构建阶段总结]] | [[00_sources/briefs/2026-circleci-chunk-agent]]、[[00_sources/briefs/2026-nx-self-healing-ci]]、[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines]] | `mapped` |

## 状态校验规则

- 正式页面必须有 `presentation_ready: true` 的主要 Deep Dive；`blocked-by-deep-dive` 页面不能进入视觉制作。
- `mapped` 只表示 Source Map 已完成映射，不替代专题 README 中的 `presentation_ready`。
- 页面制作前回到 Source Brief 核对产品状态和时间点。
- 如果来源只能证明单点能力，不向端到端作业流外推。
- 客户效果数据与产品机制分开引用，不用选择性案例代表行业平均水平。

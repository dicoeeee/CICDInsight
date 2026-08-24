---
title: GitLab Duo Fix CI/CD Pipeline Flow
source_id: gitlab-fix-cicd-pipeline-flow-2026
organization: GitLab
source_type: official-docs
published: null
verified: 2026-08-09
availability: ga-flow-with-unverified-full-pipeline-revalidation
confidence: high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - source-control
  - ci-pipeline
  - code-remediation
company_topics:
  - GitLab
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# GitLab Duo Fix CI/CD Pipeline Flow

## 一手来源

| 页面 | 日期/状态 | 直接支持的功能 |
|---|---|---|
| [Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/) | 18.8 GA；MR Code Suggestion 19.2 GA；访问 2026-08-09 | 失败上下文、停止条件、Suggestion/MR 输出 |
| [Flow execution](https://docs.gitlab.com/user/duo_agent_platform/flows/execution/) | 页面未标日期；访问 2026-08-09 | Runner、Service Account、Remote Flow 执行 |
| [Merge request approval rules](https://docs.gitlab.com/user/project/merge_requests/approvals/rules/) | 页面未标日期；访问 2026-08-09 | Approval 规则 |
| [Protected environments](https://docs.gitlab.com/ci/environments/protected_environments/) | 页面未标日期；访问 2026-08-09 | 部署授权边界 |

## 一句话结论

GitLab 已把 Pipeline 失败上下文到 Inline Suggestion 或新 MR 的修复 Flow 产品化并 GA，但当前官方文档只直接证明“诊断与候选变更交付”；补丁后自动完成原完整 Pipeline 复验仍为 `unverified`。

## 可核验事实

- Flow 可读取 Pipeline Log、失败 Job 输出、Exit Code、MR Diff、仓库内容和脚本错误。
- 对当前 MR Diff 内的改动可输出 Inline Suggestion；超出当前 Diff 或非 MR Pipeline 时可创建新 MR。
- 对证据不足、安全敏感或不可行动的问题，Flow 可停在解释与下一步建议，而不是强行生成补丁。
- Remote Flow 依赖 Runner、Service Account、Composite Identity 和项目权限；MR Approval、Pipeline Success 与 Protected Environment 继续外置。

## CI/CD 相关性

- 自愈完整度：SH2，形成可审查候选变更。
- 行动权限：L2，写入 Suggestion 或 MR；不等于合并或发布授权。
- 确定性快环：GitLab CI 另以 `retry:when`、`retry:exit_codes` 处理已知失败原因和退出码，不能与 Fix Flow 混为 Agent 根因修复。

## 限制与待验证项

- 本轮公开一手材料未证明 Flow 在创建 Suggestion/MR 前自动重跑原完整 Pipeline 或全部 Required Checks。
- Platform GA、Fix Flow GA、Tool Governance/Audit 等混合状态必须分别记录。
- 公开材料缺少跨客户首次修复率、错误修复率和 30/90 天复发率。

## 可引用判断

- GitLab 的安全终点是可审查 Suggestion/MR；完整验证和接受仍由原 Pipeline、Approval 与保护规则决定。

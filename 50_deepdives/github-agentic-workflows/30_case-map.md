---
title: GitHub Agentic Workflows 复杂案例与模式
tags:
  - research/agentic-cicd
  - research/case-map
  - company/github
status: complete
as_of: 2026-07-15
---

# GitHub Agentic Workflows 复杂案例与模式

| 模式 / 案例 | 触发与输入 | Agent 负责 | 确定性 / 安全边界 | 推荐自治 |
|---|---|---|---|---|
| Daily/Weekly Status | Schedule + Repo/Issue/PR | 聚合、归纳、建议 | `create-issue`、Close Older、预算 | L1 |
| Issue Triage / Repo Assist | Issue/PR/Comment | 分类、追问、路由、候选修复 | Integrity Filter + Label/Comment Safe Output | L1—L2 |
| CI Doctor | `workflow_run` 失败、日志、历史 Issue | 根因调查、相似失败、修复建议/PR | Actions Log 只读；PR 后原 CI 验证 | L1—L2 |
| CI Coach | Schedule + Actions Telemetry | 找慢步骤、缓存与并行机会 | 只生成 Issue/PR；基线性能复测 | L1—L2 |
| VEX Generator | Dependabot dismiss/event | 汇总理由，生成 OpenVEX 候选 | Alert 为事实源；VEX Review/Policy Gate | L2 |
| DeterministicOps Release Highlights | Tag + 结构化 Release/PR 数据 | 解释和撰写 Release Highlights | `steps` 采集；`update-release` 类型化写入 | L2 |
| Research→Plan→Assign | Schedule/Command | 研究、拆任务、路由 Coding Agent | Discussion→Issue→Assignment→Human Merge | L2 |
| OrchestratorOps | Schedule/Dispatch/Issue | 拆分、排序、选择 Worker | Worker 独立 Tool/权限/预算；Fan-out `max` | L2—L3 |
| MultiRepo/CentralRepoOps | 中央仓 + Org/Repo Catalog | 选择目标、跨仓分析、追踪 | GitHub App、Allowed Repos、Target Repo、PR | L2—L3 |
| Release Readiness | Tag/Release Candidate/Manual | 汇总测试、安全、依赖、风险与回滚计划 | 只输出报告/Issue；Environment Approval 执行 | L1—L2 |

## 案例结论

复杂度增加时，可靠性来自任务拆分和外部状态机，而不是让单个 Agent 获得更多工具。GitHub 官方模式库中的 DeterministicOps、OrchestratorOps、MultiRepoOps 和 ResearchPlanAssignOps 分别对应数据预处理、任务分解、权限隔离与人类检查点。

GitHub Next 的 CI Doctor 公开称 13 个修复 PR 中有 9 个合并，应仅视为厂商自身早期样本；它证明 CI 调查可产生有用变更，不证明任意仓库都有 69% 合并率。

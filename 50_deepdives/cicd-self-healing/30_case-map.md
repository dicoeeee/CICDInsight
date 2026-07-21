---
title: CI/CD 问题自愈案例比较
tags:
  - research/agentic-cicd
  - research/case-map
  - scenario/self-healing
status: complete
as_of: 2026-07-15
---

# CI/CD 问题自愈案例比较

## 自愈完整度定义

| 级别 | 能力 | 必要输出 | 不应被误写成 |
|---|---|---|---|
| SH0 感知 | 检测、聚合证据、告警 | 结构化事件与证据包 | 根因分析或修复 |
| SH1 诊断 | 分类、归因、给出置信度和处置建议 | 可核验诊断报告 | 已解决问题 |
| SH2 候选修复 | 生成 Patch、PR、Plan 或 Runbook 参数 | 可审查变更和验证计划 | 已验证的修复 |
| SH3 验证修复 | 在隔离环境执行候选修复，由外部 Oracle 复验并写回受限目标 | 复现证据、Diff、验证结果、Lineage | 主分支/生产已自治 |
| SH4 有界闭环 | 自动检测、执行预授权动作、观察结果，失败时回退/熔断 | 动作记录、SLO/Oracle、回退与接管证据 | 全场景无人值守 |

`SH0—SH4` 描述闭环完整度；仓库统一的 `L0—L4` 描述权限。Nx 可以在 PR 分支达到 SH3/局部 SH4，但对 Merge 和 Deploy 仍保持 L2；AWS 可以自动启动调查，却因不执行 Remediation 而停在 SH1—SH2。

## 案例矩阵

| 案例 | 主要上下文 | 修复/处置 | 独立验证 | 写入边界 | SH 判断 | 证据校准 |
|---|---|---|---|---|---|---|
| GitHub CI Doctor | Actions Run、Job Log、历史 Issue、Repo | 诊断 Issue、建议、修复 PR | 原 Required Checks；PR 触发 CI 需额外 Token | Safe Outputs 独立写 Job | SH1—SH2 | GitHub 自报 13 个提案中 9 个合并；不是自动修复率 |
| GitLab Fix CI/CD Pipeline | Pipeline Log、Exit Code、MR Diff、Repo | MR Inline Suggestion 或新 MR | 应由 MR Pipeline 复验；文档不等于自动完成复验 | 用户应用 Suggestion/合并 MR | SH2 | Flow 已 GA，但输出仍是可审查建议/变更 |
| CircleCI Chunk | 当前与历史 Pipeline、测试、Repo | 诊断、Diff、PR；瞬态/基础设施失败可重跑 | 代码修复是否自动完整复验需按版本核验；重跑本身由原 Pipeline 判定 | PR；重跑需 Prompt 预授权或询问 | 代码 SH2；瞬态重跑 SH3 | 2026-06 Changelog 显示能力快速变化，需版本化评测 |
| Harness Code Quality AutoFix | CI Log、代码变更 | Root Cause、代码修复、Branch/PR | Code Quality 文档说明 PR；Worker Agent 新版描述重触发 Build 直到成功或 Max Turns | PR 分支/受控 Pipeline Step | SH2—SH3 | 机制清晰，跨客户质量数据缺失 |
| Nx Self-Healing CI | Nx Project Graph、Task Metadata、CI Run | 生成 Fix、重跑失败 Task、应用/拒绝/回退；可按 Task Auto-apply | 原失败 Task 重跑；仍需完整 Required Checks | PR Branch；保护分支不生成 Fix | 默认 SH3；白名单 Auto-apply 为 PR 域局部 SH4 | `SELF_HEALING.md`、Never-fix Pattern 和 Auto-apply Suggestion 是有界自治样本 |
| Snyk Agent Fix | 确定性安全发现、代码与安全知识 | 多轮生成安全修复 | 安全基准 + 功能逻辑评估；企业仍需原测试/扫描 | 建议/修复工作流，不自动发布 | SH2—SH3 | 新架构效果为 Snyk 自评；LLM 功能评价不能替代业务测试 |
| Dependabot → Coding Agent | Dependabot Alert、Repo | Coding Agent Draft PR | Dependabot/CI/Review | Draft PR | SH2 | 很典型的 Analyzer → Agent → PR，不是自动合并 |
| BrowserStack Self-Heal | DOM、Locator 历史、页面上下文 | 运行时替换 Locator，可生成 Locator PR | 测试继续运行，但业务断言可能被绕过 | Test Runtime/测试代码 | Locator 维度 SH3 | “可能掩盖语义变化”是风险推断；官方能力只覆盖 Locator，不属于应用缺陷自愈 |
| Akuity On-Call Agent | Argo CD/Kargo 实时状态、历史、Audit | 按 Runbook Patch、Scale、Rollback 等 | Argo/Kargo Health、Metric、人工确认 | Tool Policy 可按参数/环境要求批准 | SH3；非生产白名单可 SH4 | 厂商机制完整，公开独立效果有限 |
| HolmesGPT Operator Mode | K8s/Cloud/DB/Observability/Runbook | 调查、Slack、Fix PR；开放 Remediation MCP 后可执行 | 取决于连接的 Tool、Runbook 和 SLO | 部署者自行配置 | SH1—SH4 可配置 | 开源可检查，但默认能力与生产策略必须分开 |
| AWS DevOps Agent | Topology、Metric、Log、Trace、Code、Deployment、Incident | 根因、Mitigation Plan、预防建议、Agent-ready Spec | 操作员和下游系统负责执行/验证 | 写能力限 Ticket/Support Case | SH1—SH2 | 官方明确不代执行，不能归入生产 L4 |
| AWS DevOps Agent + Kiro CLI 组合 | Mitigation Plan、EventBridge/Lambda/SQS、Repo、CodeBuild | Kiro 将计划转成代码变更并创建 PR | CodeBuild/原 CI；人工批准 PR 后才发布 | PR 与既有发布流水线 | 系统级 SH3 | 2026-07 官方参考架构；不能反推 DevOps Agent 本体拥有生产写权 |

## 三类闭环不能混为一谈

### 1. CI 修复闭环

目标是恢复 PR/分支的可验证状态。Blast Radius 小、回滚简单、验证快，是最适合 SH2—SH3 的区域。真正的成功不是“失败命令返回 0”，而是：原失败可复现、最小 Patch 消除原因、完整门禁未被修改、Required Checks 全部通过。

### 2. 发布自愈闭环

目标是让某个已绑定 Commit/Artifact 的变更安全前进或停止。它必须加入 Artifact Identity、Plan/Policy、Environment Approval、Canary、SLO 和 Rollback。Agent 可以解释与调度，但不能把“选择发布内容”“批准发布”“判定成功”全部握在同一身份中。

### 3. 生产恢复闭环

目标是先恢复服务，再消除根因。快环只执行已知、幂等、可回退、低爆炸半径 Runbook；慢环才做跨源根因、Fix-forward PR 和预防性改进。高压事故中，自动调查可以很广，自动动作必须更窄。

## 可复用的实践模式

| 模式 | 适用场景 | 核心约束 |
|---|---|---|
| Diagnostic-to-PR | 代码、依赖、Pipeline 配置失败 | Agent 只写分支；原 CI 独立复验；人决定 Merge |
| Classified Retry | 网络、Runner、外部服务瞬态失败 | 已知错误签名、最多次数、指数退避、全局重试预算 |
| Analyzer-Agent-Analyzer | SAST/SCA/Lint/Policy 修复 | 同一确定性 Analyzer 复验；禁止自动加 Ignore/Waiver |
| Two-loop Recovery | CI 与生产均适用 | 快环恢复可用性；慢环修根因；共享 Incident/Run Lineage |
| Runbook-bound Action | GitOps、Kubernetes、生产运维 | 工具和参数白名单、环境 Scope、Approval、超时、补偿动作 |
| Canary with SLO Oracle | 部署/发布 | 固定 Artifact、分批扩大、独立 SLO、自动停止/回退 |
| Orchestrator/Worker | 多仓、跨服务回归 | 每仓/每服务独立身份、预算和 PR；按依赖波次推进 |

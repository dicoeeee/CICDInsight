---
title: CI/CD 问题自愈案例比较
tags:
  - research/agentic-cicd
  - research/case-map
  - scenario/self-healing
status: complete
as_of: 2026-08-09
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

`SH0—SH4` 描述闭环完整度；仓库统一的 `L0—L4` 描述权限。Nx 可以在 PR 分支达到 SH3/局部 SH4，但写入目标仍是 PR Branch、对 Merge 和 Deploy 保持 L2；AWS 可以自动启动调查，却因不执行 Remediation 而停在 SH1—SH2。

## 案例矩阵

| 案例 | 主要上下文 | 修复/处置 | 独立验证 | 写入边界 | SH | L | 证据校准 |
|---|---|---|---|---|---|---|---|
| GitHub Actions Rerun | Run、Job、Log、SHA/Ref | 重跑 Workflow/失败 Job | 原 Workflow 再执行 | 无代码写回 | 不计根因修复 | 平台执行权限 | 有界再执行，不是分类器或自愈 |
| GitHub CI Doctor | Actions Run、Job Log、历史 Issue、Repo | 诊断 Issue/建议 | 原 Required Checks 仍外置 | Safe Output 写 Job | SH1 | L1；可配置输出至 L2 | Public Preview 框架中的官方参考 Workflow，不是 Actions 内建通用 RCA |
| GitHub Agentic Autofix | Code Scanning Alert、Repo | Agent 补丁、Draft PR | CodeQL 反馈；完整 PR CI 外置 | Draft PR | 安全微域 SH3 | L2 | Public Preview；Scanner 绿不证明业务正确 |
| GitLab Fix CI/CD Pipeline | Pipeline Log、Exit Code、MR Diff、Repo | Inline Suggestion 或新 MR | 完整 Pipeline 自动复验未获直接证明 | Suggestion/MR；人决定接受 | SH2 | L2 | Flow 18.8 GA、MR Suggestion 19.2 GA；Platform 其他子能力混合状态 |
| CircleCI Chunk | 当前/历史 Pipeline、测试、配置、Repo | 诊断、分支、Draft PR；瞬态/基础设施失败走 Rerun | Validation Pipeline；覆盖取决于配置 | PR；重跑需预授权或询问 | 代码 SH3 | L2 | Beta；重跑成功只计恢复，Context Secret/循环预算需审计 |
| Harness AutoFix / Worker Autofix | CI Log、近期变更、Pipeline Context | Root Cause、代码修复、PR Branch、限次重触发 Build | Build Validation；完整 Required Checks 未获证明 | PR Branch/受控 Pipeline Step | SH2—SH3 | L2；批准 Worker Action 可 L3 | 旧 Run Step 与 Managed Worker 是不同执行面，不能共享权限结论 |
| Nx Self-Healing CI | Project Graph、Task Metadata、CI Run、规则 | Fix、失败 Task 重跑、Apply/Reject/Revert、白名单 Auto-apply | 明确复跑原失败 Task；写回后 Required Checks 外置 | PR Branch；保护分支不生成 Fix | SH3；PR 微域局部 SH4 | L2 | 官方称各计划可用但未给统一阶段；High Confidence 校准未公开 |
| Buildkite Agentic CI | Build State、Log、Artifact、Test History | Retry、Flaky 隔离、失败摘要；MCP 可触发 Run | 平台内建无通用补丁复验；[官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)参考编排可等待 CI 并迭代 | Annotation/Run；参考实现新建 PR，由人工合并 | 平台 SH1；参考编排可 SH3 | L1；授权 Run 可 L3 | 正式文档未标整体阶段；Direct MCP Endpoint 单独 Preview；参考编排不是默认产品能力 |
| Snyk Agent Fix | 确定性安全发现、代码与安全知识 | 多轮生成安全修复 | 安全基准 + 功能逻辑评估；企业仍需原测试/扫描 | 建议/修复工作流，不自动发布 | SH2—SH3 | L2 | 新架构效果为 Snyk 自评；LLM 功能评价不能替代业务测试 |
| Dependabot → Coding Agent | Dependabot Alert、Repo | Coding Agent Draft PR | Dependabot/CI/Review | Draft PR | SH2 | L2 | Analyzer → Agent → PR，不是自动合并 |
| BrowserStack Self-Heal | DOM、Locator 历史、页面上下文 | 运行时替换 Locator，可生成 Locator PR | 测试继续运行，但业务断言可能被绕过 | Test Runtime/测试代码 | Locator 维度 SH3 | L2—L3 | 官方能力只覆盖 Locator；语义掩盖是风险推断 |
| Akuity On-Call Agent | Argo CD/Kargo 状态、历史、Audit | 按 Runbook Patch、Scale、Rollback | Argo/Kargo Health、Metric、人工确认 | Tool Policy 按参数/环境批准 | SH3；非生产白名单可 SH4 | L3—L4 | 机制完整，公开独立效果有限 |
| HolmesGPT Operator Mode | K8s/Cloud/DB/Observability/Runbook | 调查、Slack、Fix PR；开放 Remediation MCP 后可执行 | 取决于连接的 Tool、Runbook 和 SLO | 部署者自行配置 | SH1—SH4 可配置 | L1—L4 可配置 | 开源可检查，但默认能力与生产策略必须分开 |
| AWS DevOps Agent | Topology、Metric、Log、Trace、Code、Deployment、Incident | 根因、Mitigation Plan、预防建议、Agent-ready Spec | 操作员和下游系统执行/验证 | 写能力限 Ticket/Support Case | SH1—SH2 | L1—L2 | 官方明确不代执行，不能归入生产 L4 |
| AWS DevOps Agent + Kiro CLI | Mitigation Plan、EventBridge/Lambda/SQS、Repo、CodeBuild | Kiro 将计划转成代码变更并创建 PR | CodeBuild/原 CI；人工批准 PR 后才发布 | PR 与既有发布流水线 | 系统级 SH3 | L2—L3 | 参考架构；不能反推 DevOps Agent 本体拥有生产写权 |

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

---
title: CI/CD 自愈六家公司逐主张事实审计
tags:
  - research/agentic-cicd
  - research/fact-audit
  - scenario/self-healing
status: complete
as_of: 2026-08-09
reverified: 2026-08-10
confidence: high
presentation_ready: true
---

# CI/CD 自愈六家公司逐主张事实审计

**独立复核日：** 2026-08-10；专题事实观察点仍为 2026-08-09。本次复核未发现产品阶段反转。

## 审计对象

- [[50_deepdives/cicd-self-healing/35_company-mechanism-audit|六家公司机制审计]]；
- [[50_deepdives/cicd-self-healing/50_findings|分析发现]]与[[50_deepdives/cicd-self-healing/90_report|主报告]]；
- [[80_presentations/agentic-cicd-company-workflows/slides/05b-ci-self-healing-six-company-overview|05B 六家公司高管总览稿]]。

## 结果定义

| 结果 | 含义 |
|---|---|
| `pass` | 主张由当前一手来源直接支持，状态、时间点和对象边界一致 |
| `bounded-pass` | 直接事实与分析推断已分开，且更强主张/证据缺口在同一处明确排除 |
| `blocked` | 关键事实无法回链、生命周期未核对或冲突未解释；不得进入正式 Presentation |

## 逐主张审计

| ID | 页面/报告主张 | 直接证据 | 边界检查 | 结果 |
|---|---|---|---|---|
| C1 | CI 自愈的分水岭是独立复验后的有界写回 | 六家公司统一字段与闭环终点见 [[50_deepdives/cicd-self-healing/35_company-mechanism-audit#六家公司总表|机制总表]]；SH 定义见 [[50_deepdives/cicd-self-healing/30_case-map#自愈完整度定义|Case Map]] | 分析推断，不写成厂商原话；不声称这是统一行业标准 | `bounded-pass` |
| C2 | Retry 只计执行恢复，不自动计根因修复 | [GitHub Rerun](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/re-run-workflows-and-jobs)、[GitLab Retry](https://docs.gitlab.com/ci/yaml/#retry)、[CircleCI Reruns](https://circleci.com/docs/guides/orchestrate/automatic-reruns/)、[Buildkite Retry](https://buildkite.com/docs/pipelines/configure/retry) | 各来源只证明有界再执行；未把再次成功写成缺陷消除 | `pass` |
| C3 | GitHub CI Doctor 是参考调查 Workflow；Agentic Autofix 在安全微域达到 SH3/L2 | [[00_sources/briefs/2026-github-agentic-workflows|Agentic Workflows Brief]]、[[00_sources/briefs/2026-github-agentic-autofix-code-scanning|Autofix Brief]] | 两项均 Public Preview；CodeQL 标准 Query Suite 反馈不替代完整 PR CI/业务测试，也不能确认自定义或 `security-extended` Query 告警已修复；第三方工具修复质量不保证 | `bounded-pass` |
| C4 | GitLab Fix Flow GA，但闭环停在 Suggestion/MR | [[00_sources/briefs/2026-gitlab-fix-cicd-pipeline-flow|GitLab Fix Flow Brief]] | 18.8/19.2 状态已分别记录；完整 Pipeline 自动复验明确 `unverified` | `bounded-pass` |
| C5 | CircleCI Chunk 的代码路径达到 SH3/L2 | [[00_sources/briefs/2026-circleci-chunk-agent|CircleCI Chunk Brief]]；[Pipeline Validation](https://circleci.com/changelog/chunk-now-validates-changes-by-running-your-ci-pipeline/)；[Draft PR](https://circleci.com/changelog/chunk-now-auto-creates-prs-on-protected-branches/) | 明确为 Beta；Validation Pipeline 检查集合依客户配置，不写成全部 Required Checks | `bounded-pass` |
| C6 | Harness 可形成 PR/Build 受治理循环，但完整 Required Checks 未证明 | [[00_sources/briefs/2026-harness-code-quality-agents|Code Quality Brief]]、[[00_sources/briefs/2026-harness-worker-agents|Worker Agents Brief]] | 旧 AutoFix 与 Managed Worker 执行面分开；统一生命周期未补写为 GA | `bounded-pass` |
| C7 | Nx 明确复验原失败 Task，并可对白名单任务 Auto-apply 到 PR Branch | [[00_sources/briefs/2026-nx-self-healing-ci|Nx Brief]]、[Self-Healing CI Docs](https://nx.dev/docs/features/ci-features/self-healing-ci) | 只支持失败 Task 级验证；完整 Required Checks、Merge/Deploy 外置；未补写 GA | `pass` |
| C8 | Buildkite 当前是 Agent 的 CI 证据/执行底座，不是产品级通用补丁闭环 | [[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines|Buildkite Brief]]、[官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)、[[50_deepdives/buildkite/20_evidence-map#状态与成熟度审计|Buildkite 状态审计]] | 官方参考编排可达到 SH3，但需客户 Pipeline/Token、人工标签并由人合并；平台内建仍为 SH1/Agent Substrate；Direct MCP Endpoint 单独 Preview | `bounded-pass` |
| C9 | 快慢环应分离 | C2；六家公司确定性 Retry 与 Agent 修复的拆分见 [[50_deepdives/cicd-self-healing/35_company-mechanism-audit|机制审计]] | 企业建议，来自机制综合；不写成所有厂商原生实现 | `bounded-pass` |
| C10 | Oracle 必须外置，Agent 不能靠修改 Gate 自证成功 | GitHub CodeQL、Nx 失败 Task、CircleCI Validation Pipeline；[[50_deepdives/cicd-self-healing/research-evidence#7. 最低安全与治理控制|最低治理控制]] | Scanner/Task/Pipeline 的覆盖范围分别标注；不把单一绿灯外推为业务正确 | `bounded-pass` |
| C11 | 授权单位应细化到故障类别 × Task × 分支/环境 × 动作 | Nx Pattern/Protected Branch、GitHub Safe Output、Harness RBAC×Grant/OPA、Token Scope 证据 | 企业设计推断；不声称厂商使用完全相同的四维模型 | `bounded-pass` |
| C12 | 六家公司没有公开证据支持通用自动合并/生产发布 SH4 | 六家公司写回目标和门禁见 [[50_deepdives/cicd-self-healing/35_company-mechanism-audit#六家公司总表|机制总表]] | 表述为“本轮公开一手材料未证明”，不推断产品不存在 | `bounded-pass` |

## 生命周期与 SH/L 一致性

| 公司 | 页面生命周期 | 页面 SH/L | Case Map | Mechanism Audit | 结果 |
|---|---|---|---|---|---|
| GitHub | Agentic Workflows/Autofix Public Preview；CI Doctor 参考 Workflow | 通用 CI SH1/L1；安全微域 SH3/L2 | 一致 | 一致 | `pass` |
| GitLab | Fix Flow 18.8 GA；MR Suggestion 19.2 GA；平台其他子能力混合 | SH2/L2 | 一致 | 一致 | `pass` |
| CircleCI | Chunk Beta | 代码路径 SH3/L2 | 一致 | 一致 | `pass` |
| Harness | 已发布/可用；无统一子能力阶段标签 | SH2—SH3/L2；批准 Worker Action 可 L3 | 一致 | 一致 | `pass` |
| Nx | 已发布、各计划可用；官方未标统一阶段 | SH3/L2；PR 微域局部 SH4 | 一致 | 一致 | `pass` |
| Buildkite | 正式文档未标整体阶段；Direct MCP Endpoint 单独 Preview | 平台 SH1/L1；授权 Run 可 L3 | 一致 | 一致 | `pass` |

## 强制保留的证据缺口

| 缺口 | 报告/页面处理 | 状态 |
|---|---|---|
| GitLab Fix Flow 是否自动完成原完整 Pipeline 复验 | 页面写“未获直接证明”，判 SH2 | 保留 |
| Harness AutoFix/Worker Autofix 是否覆盖全部 Required Checks | 页面区分 Build Validation 与 Required Checks | 保留 |
| Buildkite 是否有原生通用 Patch → PR → 独立复验闭环 | 页面定位为 Agent Substrate，并区分官方参考编排 | 保留 |
| CircleCI Validation Pipeline 是否等于全部 Required Checks | 页面写“检查集合取决于客户配置” | 保留 |
| Nx High Confidence 校准与 VCS 写身份 | 不外推为安全证明或生产权限 | 保留 |
| 六家公司统一 Repair@1、错误修复率、长期复发与总成本 | 不做总体排名，不引用不可比指标 | 保留 |

## 审计结论

### 2026-08-10 独立复核关闭项

| 原问题 | 修复证据 | 关闭判断 |
|---|---|---|
| P2：Buildkite “官方参考 PR Fixer”在页面可见主张中缺少精确一手回链 | [官方案例文章](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)与[官方示例仓库](https://github.com/buildkite-agentic-examples/github-pr-build-fixer)已进入 Source Brief、Evidence Map、Case Map、机制审计、主报告与 05B Source Map | `closed`；事实成立，但只放行“客户可组合 SH3 参考编排”，不放行“平台原生通用闭环” |
| P3：复核日晚于专题观察日 | Frontmatter 保留 `as_of: 2026-08-09`，并新增 `reverified: 2026-08-10` | `closed`；观察点与复核日分开记录 |

- 页面所有可见主张均为 `pass` 或 `bounded-pass`，无 `blocked` 项。
- [[50_deepdives/cicd-self-healing/README|专题 README]] 可维持 `presentation_ready: true`。
- [[80_presentations/agentic-cicd-company-workflows/slides/05b-ci-self-healing-six-company-overview|05B 页面]]可标记 `deep-dive-ready / visual pending`。
- 审计只放行当前有边界主张，不放行“统一成熟度排名”“完整 CI 已自动复验”“自动合并/部署”或“生产开放式自愈”。

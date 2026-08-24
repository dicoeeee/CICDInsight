---
title: 六家公司 CI 自愈机制核验底稿（2026-08-09）
aliases:
  - Six Company CI Self-Healing Mechanism Audit
tags:
  - research/agentic-cicd
  - research/primary-evidence
  - scenario/self-healing
  - companies/github
  - companies/gitlab
  - companies/circleci
  - companies/harness
  - companies/nx
  - companies/buildkite
status: complete
as_of: 2026-08-09
topic_id: cicd-self-healing
topic_type: scenario
source_policy: primary-only
time_window: 2025-09-17/2026-08-09
confidence: high
presentation_ready: false
---

# 六家公司 CI 自愈机制核验底稿（2026-08-09）

> [!abstract] 可用于后续汇报的单一判断
> **CI 自愈的分水岭不是能否解释红灯，而是候选修复能否经过独立复验后被有界写回。** 重跑只恢复一次执行；诊断或 PR/MR 只交付候选；只有原失败检查（或明确的独立 Analyzer）复验、写回范围和身份均被限定，才可称为 SH3；有自动触发、停止条件和回退/熔断证据的局部动作才可称为 SH4。

本文是 [[50_deepdives/cicd-self-healing/90_report|CI/CD 自愈专题报告]] 的增量一手证据底稿。观察日为 **2026-08-09**；每条外部链接均为官方 Docs、官方 Changelog、官方博客或官方仓库资料，访问日均为 2026-08-09。`SH0—SH4` 与 `L0—L4` 沿用 [[50_deepdives/cicd-self-healing/30_case-map#自愈完整度定义|既有定义]]：`L1` 只读、`L2` 分支/PR 写、`L3` 预批准类型化执行、`L4` 生产自动处置。

## 0. 研究口径与阅读规则

- **厂商明示**：来源直接写明的行为、前提、阶段或权限。
- **分析判级**：根据下表的闭环链条作出的 `SH/L` 判断；不是厂商认证，也不把“autonomous”“self-healing”当作证据。
- **`unverified` / 证据缺口**：本轮公开一手材料未证明的环节。它不等于该能力不存在。
- **验证 Oracle**指候选修复无法自行改写其成功判据的检查。一次 Build 变绿只能证明该 Build 的配置检查通过，不能单独证明业务、性能或长期安全正确。

## 1. 证据索引

| ID | 厂商/子能力 | 一手来源（发布日期或页面更新时间；访问日） | 直接支持的事实 | 生命周期 |
|---|---|---|---|---|
| GH-1 | GitHub Actions rerun | [GitHub Docs: Re-running workflows and jobs](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/re-run-workflows-and-jobs)（持续文档；访问 2026-08-09） | 有写权限者可在 30 日内重跑整个 run、失败 jobs 或指定 job；沿用原始触发者权限、SHA 与 ref；最多 50 次 | 已发布；此项页面未标统一 Preview/GA 标签 |
| GH-2 | Agentic Workflows / CI Doctor | [GitHub Changelog: Agentic Workflows public preview](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/)（2026-06-11；访问 2026-08-09）；[CI Doctor workflow source](https://github.com/githubnext/agentics/blob/main/workflows/ci-doctor.md)（持续源码；访问 2026-08-09） | Agentic Workflows 可在 Actions 中进行 CI failure analysis；默认只读、沙箱/Firewall、安全输出与 threat-detection job | Public Preview；CI Doctor 为官方示例 workflow，不等同 Actions 默认功能 |
| GH-3 | Code Scanning Agentic Autofix | [GitHub Changelog: Agentic autofix public preview](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview/)（2026-07-10；访问 2026-08-09）；[GitHub Docs: About autofix for code scanning](https://docs.github.com/en/enterprise-cloud@latest/code-security/concepts/code-scanning/autofix-for-code-scanning)（持续文档；访问 2026-08-09） | 指派 alert 后跨仓探索、生成 patch、重跑 CodeQL、迭代并开 draft PR；需要 Code Security/GHAS、Copilot 与 cloud agent | Public Preview |
| GL-1 | GitLab `retry` | [GitLab CI/CD YAML reference: `retry`](https://docs.gitlab.com/ci/yaml/#retry)（持续文档；访问 2026-08-09） | Job 可按失败类别及上限重试；`retry:exit_codes` 在 GitLab 17.5 GA | 已发布；`retry:exit_codes` 官方明确 GA |
| GL-2 | GitLab Duo Fix CI/CD Pipeline | [GitLab Docs: Fix CI/CD Pipeline Flow](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)（持续文档；访问 2026-08-09） | 读取日志、exit code、MR diff、repo；MR 内给 inline suggestions 或另开 MR；GitLab 18.8 GA，19.2 MR suggestion 改造 GA | GA（Flow）；不同发布版本有功能演进 |
| CC-1 | CircleCI automatic reruns | [CircleCI Docs: Automatic reruns](https://circleci.com/docs/guides/orchestrate/automatic-reruns/)（持续文档；访问 2026-08-09）；[Changelog](https://circleci.com/changelog/automatic-reruns-of-steps-released/)（2025-09-17；访问 2026-08-09） | 可配置失败 step/workflow 自动重跑，最大 5 次；只重跑 failed jobs/依赖，沿用原 actor 权限 | 已发布；step rerun 自 2025-09-17 说明为 all plans |
| CC-2 | CircleCI Chunk | [Chunk overview and setup](https://circleci.com/docs/guides/toolkit/chunk-setup-and-overview/)（页面标 Beta；访问 2026-08-09）；[Pipeline validation](https://circleci.com/changelog/chunk-now-validates-changes-by-running-your-ci-pipeline/)（2025-11-05；访问 2026-08-09）；[Draft PR on protected branches](https://circleci.com/changelog/chunk-now-auto-creates-prs-on-protected-branches/)（2026-03-29；访问 2026-08-09） | 失败上下文进入 Chunk；候选推到分支并触发 Pipeline；受保护分支创建 Draft PR，验证失败关闭 | Beta；重跑是否自动取决于 prompt 授权 |
| HA-1 | Harness Failure Strategy | [Harness Docs: Define failure strategies](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/)（更新 2026-05-13；访问 2026-08-09） | 条件化 retry、manual intervention、abort、ignore、stage rollback；重试耗尽后可配 rollback/abort | 已发布；页面未标统一阶段 |
| HA-2 | Harness Code Quality Autofix | [Harness Docs: Code Quality Agents](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)（更新 2026-07-15；访问 2026-08-09） | 从 failed execution logs 诊断、coding agent（最多 50 iterations）改码、推 `ai-autofix` branch 并开 PR；可由失败通知触发 | 已发布文档；本页未标统一 Preview/GA 标签 |
| HA-3 | Harness Worker Agents | [Harness Blog: Introducing Autonomous Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents)（2026-06-30；访问 2026-08-09）；[Worker Agents product page](https://www.harness.io/products/platform/worker-agents)（访问 2026-08-09） | Pipeline-native steps，容器沙箱、临时 scoped token、OPA、RBAC、approval gates、audit；官方已发布并称面向客户可用 | 已发布/可用；当前产品文档未给全部子能力统一 GA/Preview 标签 |
| NX-1 | Nx flaky retry | [Nx Docs: Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci)（持续文档；访问 2026-08-09） | 可自动重跑被检测为 flaky 的 PR task，机制为向 PR branch 推空 commit | 已发布；官方页面未标统一 GA/Preview 标签 |
| NX-2 | Nx Self-Healing CI | [Nx Docs: Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci)（持续文档；访问 2026-08-09）；[Nx Blog: Auto-apply suggestions](https://nx.dev/blog/self-healing-ci-auto-apply-suggestions)（2026-04-22；访问 2026-08-09） | 失败 task → agent patch → 原 task verification；限定 pattern、高置信、已验证时可 auto-commit PR branch；保护分支不生成 fix | 已发布；官方页面未标统一 GA/Preview 标签 |
| BK-1 | Buildkite retry / Test Engine | [Buildkite Docs: Retry](https://buildkite.com/docs/pipelines/configure/retry)（持续文档；访问 2026-08-09）；[Test state and quarantine](https://buildkite.com/docs/pipelines/configure/tests/test-suites/test-state-and-quarantine)（持续文档；访问 2026-08-09） | retry 可按 exit status/signal/signal reason 及 limit；Test Engine 可自动/手动 quarantine，mute/skip 改变 test 对 build 的影响 | `official-docs-no-unified-feature-stage`；Test-state/automatic quarantine 限 Pro/Enterprise |
| BK-2 | Buildkite plugins / Agent-MCP substrate | [Buildkite Docs: AI agents in Pipelines](https://buildkite.com/docs/platform/ai-agents)（持续文档；访问 2026-08-09）；[MCP tools overview](https://buildkite.com/docs/apis/mcp-server/tools)（持续文档；访问 2026-08-09）；[Plugins](https://buildkite.com/docs/pipelines/integrations/plugins)（持续文档；访问 2026-08-09） | MCP 有按 scope 的日志读取、build/job retry 与 annotation 等工具；模型 provider/插件可做 failure analysis 与 suggested fix annotations | `official-docs-no-unified-feature-stage`；此组不是原生通用 patch 闭环 |
| BK-3 | Buildkite 官方 PR Build Fixer 参考编排 | [Buildkite Blog: Three practical examples](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)（2025-12-01；访问 2026-08-10）；[官方示例仓库](https://github.com/buildkite-agentic-examples/github-pr-build-fixer)（访问 2026-08-10） | 人工标签触发；容器内 Agent 通过 MCP 读日志，创建修复分支/新 PR，等待 Build，失败时迭代，最后由人合并 | 官方参考实现；可组合 SH3/L2，不是平台默认内建能力或效果基准 |

## 2. 统一机制审计表

| 公司 / 拆分能力 | 触发 / 分类 | 确定性重试 | Agent 诊断与候选修复 | 验证 Oracle | 写回目标；身份权限 | 外部门禁；停止/回退 | 生命周期；SH / L（分析判级） | 关键证据缺口 |
|---|---|---|---|---|---|---|---|---|
| GitHub Actions rerun | 人工/API 对失败 run、failed job 或 job 选择；无内建 RCA 分类证据 | 有，重跑原 SHA/ref，最多 50 次 | 无 | 原 workflow 的 job outcome；不是修复验证 | 不写代码；需 repo write 的人发起，执行用**原触发者**权限 | Branch protection/required checks 可另行配置；到上限停止；无 patch 回退 | 已发布（未标统一阶段）；确定性快环，不纳入根因修复 SH；L3 仅限已授权 rerun | 本轮公开一手材料未证明 Actions 有内建通用失败分类或代码 patch loop |
| GitHub Agentic Workflows / CI Doctor | `workflow_run`/工作流定义触发；CI Doctor 读取 run/log/历史上下文并分类（示例） | 可由 Actions/workflow 自行编排，不是 CI Doctor 证实的默认动作 | 有诊断、issue/comment 等 safe output；CI Doctor source 未证明默认直接 patch | safe outputs 与 threat-detection job 是 workflow 输出安全检查；原 CI/Required Checks 是否会在 PR 上运行取决于仓库 | 默认 read-only；写 output 经过 safe outputs；CI Doctor 的直接写回非默认已证实 | 现有 runner groups/policy；安全输出、threat detection；工作流自身必须规定停机与回退 | Public Preview；CI Doctor 默认 SH1/L1，配置 Safe Output 后可至 SH2/L2 | 本轮公开一手材料未证明 CI Doctor 是 GitHub 托管的完整自动代码修复闭环，也未证明其自动 merge 或完整 PR CI 复验 |
| GitHub Code Scanning Agentic Autofix | 指派 CodeQL/第三方 code-scanning alert 给 Copilot；alert 是确定性分类输入 | 不适用一般 CI 重试；agent 可迭代 | 跨文件探索、生成 patch，直到开 draft PR | 重跑 CodeQL（官方明示）；对 custom/security-extended queries 与第三方质量为 best-effort | 新 branch + **draft PR**；需已启用 Code Security/GHAS、Copilot/cloud agent；admin/policy 可禁用 | PR review/merge 与仓库 required checks 在系统外；官方明示 PR 供 review；不满足/无法证明则不等于修复 | Public Preview；SH3（以 CodeQL query suite 为 Oracle），L2 | 本轮公开一手材料未证明自动 merge、完整 CI/Required Checks 已完成，且 CodeQL 复验不能确认 custom/security-extended query |
| GitLab `retry:when` / `retry:exit_codes` | Job failure 的 `when`/exit code 为结构化分类 | 有，`max` 和 exit code 条件；bounded | 无 Agent，无 patch | 原 job exit/result | 不写代码；pipeline job 身份执行 | `max` 用尽停止；失败策略由 `.gitlab-ci.yml`；无代码回退 | 已发布；确定性快环，不纳入根因修复 SH；L3（预定义 pipeline execution） | 本轮公开一手材料未证明 retry 自带根因诊断或防止不当分类的通用策略 |
| GitLab Duo Fix CI/CD Pipeline Flow | 已存在 failed pipeline；日志（最后 150 KiB）、exit code、MR diff、repo/script error；低上下文/敏感/不可行动时只评论 | 未证明其自动 retry 原 pipeline | 有 root-cause diagnosis 和 fix；MR 内 inline suggestion，或新 MR | 流程文档仅明示 sandbox 限制，**未明示流程自动重跑完整 pipeline 或 MR pipeline** | MR source branch suggestion 或新 MR；需 Developer/Maintainer/Owner、top-level group enable、push rule service account、runner | 敏感/低上下文/不可行动可停为 comment；合并和 pipeline 门禁由 GitLab/项目配置外置 | GA（18.8；MR suggestion 19.2 GA）；SH2，L2 | **本轮公开一手材料未证明完整 Pipeline 复验、自动 merge 或 post-writeback Required Checks 的结果**；package install 亦可能无法在 sandbox 验证 |
| CircleCI automatic reruns | failed step 或 workflow；配置阈值与状态决定，无语义 RCA | 有，step/workflow 最大 5；workflow 只重跑 failed jobs/被阻断依赖；同原 actor permissions | 无 | 原 step/workflow exit result | 不写代码；同原 actor permissions | 上限、仅 failed、非 manual rerun、pipeline <90 天；无 patch 回退 | 已发布；确定性快环，不纳入根因修复 SH；L3 | 本轮公开一手材料未证明自动 rerun 能辨别产品缺陷与瞬态故障 |
| CircleCI Chunk | 对 failed error/job/workflow 的 Fix button；读取 failure context 与 build history/repo | prompt 明确预授权才自动 rerun；否则需对话决策 | 有，生成修复并把候选推到分支 | 官方 Changelog 明示触发 CI Pipeline 验证；失败继续尝试；Draft PR 状态取决于 Validation Pipeline | GitHub App 安装与 Repo Setup；环境可携带 Project Variable/Selected Context Secret | 受保护分支创建 Draft PR；验证失败关闭；Review/Merge/Revert 仍在外部 | Beta；代码修复 SH3/L2；预授权重跑另计确定性快环/L3 | Validation Pipeline 是否覆盖全部 Required Checks 取决于客户配置；本轮材料未证明自动 Merge 或生产回退 |
| Harness Failure Strategy | step/stage/step-group error types；优先级和 scope 明确 | 有，retry count/interval/post-retry action | 无 | step/stage execution result | 不写代码；pipeline 身份 | manual intervention timeout + fallback；retry exhausted 可 rollback/abort；deployment stage 可 StageRollback | 已发布；确定性快环，不纳入代码根因修复 SH；L3 | 本轮公开一手材料未证明 Failure Strategy 提供语义 RCA 或代码根因修复 |
| Harness Code Quality Autofix | failed `executionId` / logs；可接 CI failure notification | 文档明示 agent 的 50 次 coding iterations，**未明示每次都重跑 original build** | remediation agent 诊断，coding agent patch；推 `ai-autofix` branch 并开 PR | 本文档没有明示独立完整 CI/Required Checks Oracle | branch/PR；依赖 `harnessKey`、LLM connector、Git connector/SCM token | PR review/merge 与 repo branch protection 外置；最多 50 iterations 是 coding-agent 上限 | 已发布文档、未标统一阶段；SH2，L2 | **本轮公开一手材料未证明完整 Required Checks/原 build 重跑、自动 merge、production 写回或修复后的 rollback** |
| Harness Worker Agents | Pipeline step 或独立调用；每个 agent 可组合条件/矩阵等 pipeline 编排 | 可用 pipeline 逻辑配置；产品公告不等于每个 agent 自带 retry | 容器内 Agent，可用 Knowledge Graph/MCP；CI Autofix 产品描述称可重触发 Build 至通过/Max Turns | approval gates/OPA 是授权门，不等于软件正确 Oracle；所接 pipeline test/scan 才可能成为外部 Oracle | container on Delegate/customer infra；effective token 是 agent permission 与 invoking user RBAC 的交集，短期删除 | OPA、RBAC、approval gate、audit、max-turns、network/file sandbox；实际 rollback 取决于所编排 stage | 已发布/可用、无统一子能力阶段标签；平台承载可至 L3，特定代理 SH/L 取决于 template | 本轮公开一手材料未证明 Worker Agents 对所有 CI 修复都运行完整 Required Checks，亦未证明通用自动 merge/production rollback |
| Nx flaky retry | Nx Cloud 判定 flaky PR task | 有，自动重跑；实现为推空 commit 到 PR branch | 无代码修复 | 重跑 task 的结果；只说明稳定性恢复 | 向 PR branch 推空 commit；隐含其 VCS 集成授权 | 特性只对 PR；保护 branch 不生成 fix；无代码变更回退问题 | 已发布、官方未标统一阶段；确定性快环，不纳入根因修复 SH；L3 | 本轮公开一手材料未证明“flaky”分类精度、独立 root-cause 修复或长期不复发 |
| Nx Self-Healing CI | PR CI failed task；`fix-ci` 放在 always-run 末尾；project graph/log/task metadata | flaky retry 与 code-fix 分层；非通用 retry | agent 生成 patch；eligible/never-fix glob 控制 | **明确重跑原 failed task**；只对该 task 的通过作验证 | 默认 suggestion/本地 apply；auto-apply 同时要求 task allowlist、高置信、explicit verification，才 commit 到 PR branch | 默认/main/master/trunk/dev/stable/canary 不生成 fix；exclude pattern；可 revert；PR full checks/merge 外置 | 已发布、官方未标统一阶段；默认 SH3/L2；allowlist auto-apply 为 PR 域局部 SH4/L2 | 本轮公开一手材料未证明 auto-apply 后完整 Required Checks、merge、deploy、业务/性能 oracle 或长期无复发；不得以“GA”表述 |
| Buildkite retry | job exit status/signal/signal reason，是可配置结构化输入 | 有，default limit 2，最多 10；支持 agent-lost/stack-error 等条件 | 无 | job result | 不写代码；pipeline/agent 执行身份 | limit 0 可排除高风险条件；immutable retry history；部署可禁 manual retry | `official-docs-no-unified-feature-stage`；确定性快环，不纳入根因修复 SH；L3 | 本轮公开一手材料未证明 retry 有通用 RCA/patch，也不能从 wildcard retry 推断“只处理瞬态” |
| Buildkite Test Engine quarantine | Test health/monitor/workflow state；自动或人工把 test 转 mute/skip | bktec 有 retry support；不是根因修复 | 无 | mute 时 test 仍执行但 soft fail；skip 时不执行且无数据 | test state API 需 suite permission/scope；workflows 可自动 transition | skip/mute 会改变 gate 语义；建议 label 与 scheduled rerun；release/unquarantine policy 由团队配置 | `official-docs-no-unified-feature-stage`；隔离处置，不纳入代码修复 SH；L3 | quarantine 是吞吐/隔离，不证明被隔离 test 已修复；mute/skip 不能当作产品正确 Oracle |
| Buildkite plugins / Agent-MCP substrate | MCP 可读 build/job/log/artifact/test；plugins 可 annotate failure analysis；agent step 可执行模型调用 | MCP 有 retry build/job 的 `write_builds` 工具；是否调用由部署 agent 决定 | 有 failure summarization、RCA/suggested fix annotation；Buildkite docs 没有声明平台原生 agent 自动代码 patch + PR loop | 日志/状态可被 agent 读取；没有在该平台页看到固定的 patch verifier | MCP token scopes 区分 `read_*`/`write_*`；agent step 使用 existing agent access token 或 BYOT；plugins 是 job hooks | scope/token 与 pipeline policy取决于客户配置；无统一 max-turn/rollback/merge gate 声明 | `official-docs-no-unified-feature-stage`；默认诊断 SH1/L1；若客户给 retry write scope则 L3；不判为通用 SH3 | **本轮公开一手材料未证明 Buildkite 有原生通用“补丁—独立复验—受限 PR 写回”闭环，不能把 MCP/插件/Agent substrate 写成该闭环** |
| Buildkite 官方 PR Build Fixer 参考编排 | 人工给失败 PR 添加 `buildkite-fix` 标签，GitHub Webhook 触发客户 Pipeline | Build 失败后由 Agent 在限权容器中迭代，不是平台默认重试策略 | MCP 读 Build Log，克隆仓库、创建修复分支和新 PR | 新 PR 触发 Build；失败则继续迭代，通过后转人工评审 | GitHub PAT、Buildkite API Token、客户 Pipeline Trigger；人工决定 Merge | 触发、Token 与迭代策略由参考仓库配置；没有平台级统一预算或效果承诺 | 官方参考实现 SH3/L2；不改变平台内建 SH1 判断 | 不能由单个示例外推默认身份边界、完整 Required Checks、通用成功率或原生产品阶段 |

## 3. 公司机制详解

### 3.1 GitHub：平台重跑、可组合诊断、安全修复是三条不同链

1. **Actions rerun** 是确定性的执行恢复，而非 Agent 修复。官方明确重跑保留原始 `GITHUB_SHA`、`GITHUB_REF` 与最初触发者的 privileges；因此它不会自动把新 patch 带入同一次 rerun，也不产生代码 diff。[GitHub Docs](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/re-run-workflows-and-jobs)
2. **Agentic Workflows / CI Doctor** 是可编排的慢环诊断。GitHub 2026-06-11 将 Agentic Workflows 标为 public preview，说明它编译为标准 Actions YAML、复用 runner group/policy，agent 默认只读且其 output 要经过 safe-output 与 threat-detection 流程。[GitHub Changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/) 因而 CI Doctor 默认评为 `SH1/L1`；配置受限 Safe Output 后可至 `SH2/L2`，但不能从“可写 safe output”推断为自动改主分支。
3. **Code Scanning Agentic Autofix** 的 Oracle 更强但范围更窄：CodeQL re-run 是官方明示的验证，输出为 draft PR 供 review；官方同时限定其对 custom/security-extended query 的确认能力与第三方 tool fix quality。因此它是“Analyzer → Agent → Analyzer → draft PR”的 `SH3/L2`，不是“所有 CI 已绿”的结论。[GitHub Docs](https://docs.github.com/en/enterprise-cloud@latest/code-security/concepts/code-scanning/autofix-for-code-scanning)

### 3.2 GitLab：`retry` 与 Duo Flow 不能合并成“自动复验修复”

- `retry:when` 与 `retry:exit_codes` 是 job 级的、可配置上限的确定性重试，后者的 GA 状态在 YAML reference 中被单独标出；它没有生成 patch 的机制。[GitLab YAML reference](https://docs.gitlab.com/ci/yaml/#retry)
- Fix CI/CD Pipeline Flow 在 18.8 GA，但它的可见输出是 MR inline suggestion、新 MR 或 failure comment；对低上下文、安全敏感、不可行动的 failure 会停止在 comment。Flow 仅摄入 job log 尾部 150 KiB，并明确承认 sandbox 未必能验证 package installation。[GitLab Flow docs](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)
- 因此本轮只能记为 `SH2/L2`。**本轮公开一手材料未证明** Flow 写回建议后会自动执行完整 Pipeline、以完整 Pipeline 为 Oracle，或自动 merge。建议/新 MR 的存在不能补足该缺口。

### 3.3 CircleCI：真正已闭环的是有界 rerun；Chunk 仍是 Beta 的 Agent 工作台

- Automatic reruns 是机制清晰的快环：step 先重试，仍失败才 workflow rerun；只重跑失败 jobs 与被前次阻断的依赖，最多 5 次，且重跑继承 original actor permissions。它可以降低瞬态故障的 TTG，但不能证明“发现了根因”。[CircleCI Docs](https://circleci.com/docs/guides/orchestrate/automatic-reruns/)
- Chunk 是 Beta，能在失败 step/job/workflow 放入上下文后启动修复任务，使用其专用 environment；agent 的 secrets/context 由组织配置。官方 Changelog 说明候选会推到分支并触发现有 Pipeline，失败后继续尝试；受保护分支会创建 Draft PR，Validation Pipeline 失败则关闭。[Pipeline validation](https://circleci.com/changelog/chunk-now-validates-changes-by-running-your-ci-pipeline/) [Draft PR](https://circleci.com/changelog/chunk-now-auto-creates-prs-on-protected-branches/)
- 因此代码路径可判 `SH3/L2`；Validation Pipeline 的具体检查集合仍取决于客户配置，不能自动等同仓库全部 Required Checks。Prompt 预授权的 Rerun 另记确定性快环，不替代根因修复。

### 3.4 Harness：Failure Strategy、Autofix 与 Worker Agent 的 Oracle 分别不同

- **Failure Strategy** 是确定性控制面：允许对 error type 设 Retry、Manual Intervention、Abort、Ignore、Rollback；retry exhausted 可落到 rollback/abort。它能做有界快环，但无诊断/patch。[Harness Docs](https://developer.harness.io/docs/platform/pipelines/failure-handling/define-a-failure-strategy-on-stages-and-steps/)
- **Code Quality Autofix** 明确是“log → remediation diagnosis → coding agent → `ai-autofix` branch/PR”。它可由 pipeline failure notification 自动触发，coding agent 最多 50 iterations，但该页面没有明示原 build/完整 Required Checks 的自动独立复验。结论严格停在 `SH2/L2`。[Harness Docs](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)
- **Worker Agents** 是执行与授权底座，不是一个统一修复产品：官方已发布并说明 Agent 在 Pipeline 中以 Container/Delegate 执行，Token 是 Agent 权限与触发用户 RBAC 的交集，另受 OPA、Approval Gate、Audit 与 Network/File Sandbox 约束；当前产品文档未给全部子能力统一 GA/Preview 标签。[Harness blog](https://www.harness.io/blog/introducing-autonomous-worker-agents) 这些控制可约束 L3 行为，却不自动成为软件正确性 Oracle。**本轮公开一手材料未证明**“所有 Worker Agent 修复均跑完整 Required Checks”。

### 3.5 Nx：唯一明确写出“原失败 Task 复验 + 白名单自动写 PR”的产品链

- Flaky retry 与代码修复应分开：前者仅自动重跑被判为 flaky 的 task，并用空 commit 触发 PR branch 的新 CI；它是吞吐恢复而非 root-cause fix。[Nx Docs](https://nx.dev/docs/features/ci-features/self-healing-ci)
- Self-Healing CI 由 `nx fix-ci` 在失败后运行；对 PR failed task 生成候选。默认交给人 apply/reject；auto-apply 必须同时满足 Task pattern、agent 高置信和**显式验证该 failed task 已修复**，才向 PR branch commit。`nx format:check`、`nx sync:check`、`nx conformance:check` 还有专门 deterministic preset。[Nx Docs](https://nx.dev/docs/features/ci-features/self-healing-ci)
- 这是本轮六家中最明确的代码修复 SH3 链，并可在受限 pattern 的 PR 域达到局部 `SH4/L2`；不过 default/main 等保护 branch 不生成 fix，完整 Required Checks、merge、deploy 和业务 Oracle 仍在其外。页面未标统一 GA/Preview，故生命周期只能写“已发布、官方未标统一阶段”，不能写 GA。

### 3.6 Buildkite：可组合的恢复/诊断基础设施，不是已证实的原生通用补丁闭环

- Retry 允许按 `exit_status`、signal 与 `signal_reason` 匹配并有 `limit`；重试历史不可变。默认 wildcard 并不等于仅瞬态，因此建议显式排除不应 retry 的 condition。[Buildkite Retry docs](https://buildkite.com/docs/pipelines/configure/retry)
- Test Engine quarantine 可自动或人工将 test 转为 `mute`/`skip`。`mute` 仍运行但失败 soft-fail；`skip` 不运行且不采集数据。这是 test-gate 的状态管理/隔离，不是测试或产品代码已经修复。[Test state and quarantine](https://buildkite.com/docs/pipelines/configure/tests/test-suites/test-state-and-quarantine)
- AI agents 文档证明 Buildkite 提供 agent-in-CI、模型 provider、failure-analysis plugins 和远程 MCP；MCP 的 read/write scope 明确区分，能读取 logs、retry job/build、创建 annotation。它给部署者组成“诊断 → 受 scope 限制的 action”的部件，但没有把一个通用 patch、独立复验、PR 写回链声明为原生产品闭环。[Buildkite AI agents docs](https://buildkite.com/docs/platform/ai-agents) [MCP tools docs](https://buildkite.com/docs/apis/mcp-server/tools)
- [官方 PR Build Fixer 参考实现](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)补充证明客户可把这些部件组合为 SH3：人工标签触发、容器内 Agent 修复、新 PR、Build 复验与失败迭代，最终由人合并。该证据只支持参考编排，不把 Buildkite 平台内建能力升级为通用 SH3。
- 因而 Buildkite 默认判 `SH1/L1`（诊断）；在客户将 `write_builds` scope 交给 agent 并调用 retry 时是 `L3` 的快环。**本轮公开一手材料未证明**其有原生通用代码补丁闭环，不能与 Nx 或 GitHub Agentic Autofix 并列为已证实 SH3。

## 4. 跨公司比较：闭环终点而非市场标签

| 闭环终点（证据支持） | 公司 / 子能力 | 可安全陈述 | 不可替代的外部职责 |
|---|---|---|---|
| 有界确定性重跑 / 状态动作 | GitHub Actions、GitLab retry、CircleCI reruns、Harness Failure Strategy、Nx flaky retry、Buildkite retry/Test Engine | 在配置的失败信号、次数、身份和（部分产品的）回退范围内恢复一次执行或降低 flaky 阻塞 | failure 是否真是瞬态、业务正确性、代码根因、长期复发 |
| 诊断 / 候选建议 | GitHub CI Doctor、GitLab Duo Flow、CircleCI Chunk、Harness Autofix、Buildkite plugins/MCP | 给出 RCA/patch/suggestion/PR/MR 或解释，并且可能拒绝低置信/敏感情形 | 独立原检查、PR/MR review、merge、发布与回滚 |
| 原始/专用 Oracle 复验后写 PR | GitHub Code Scanning Agentic Autofix、Nx Self-Healing CI | GitHub 对 CodeQL query suite、Nx 对原 failed task 有明确复验；均写到 branch/draft PR 而非自动 merge | 完整 Required Checks、保护分支、人工/策略 merge，及业务/性能/生产观察 |
| 受治理的通用执行承载 | Harness Worker Agents、Buildkite Agent/MCP substrate | 可将可用工具与 token scope、policy/approval/审计编排进 Pipeline | 模板所连接的测试/scan/approval/rollback；承载层本身不证明任何特定修复已验证 |

> [!important] 统一推断
> 六家公司没有证据支持“红灯 → 任意代码修复 → 自动合并/生产发布”的通用 SH4。已发布的高确定性自动化集中在 retry、retry-after-failure、quarantine 或按策略 rollback；Agent 更常停在诊断、候选 patch 或 PR/MR。Nx 的白名单 auto-apply 和 GitHub 的 CodeQL 重跑最接近受限 SH3/局部 SH4，但都没有取代完整 PR 门禁与合并权限。

## 5. 需要保留在后续报告与页面中的证据缺口

1. **GitLab**：本轮公开一手材料未证明 Fix CI/CD Pipeline Flow 会完整重跑 Pipeline、把完整 Pipeline 作为自动 Oracle，或自动 merge；只证明建议/新 MR 与 sandbox 局限。
2. **Harness**：本轮公开一手材料未证明 Code Quality Autofix 或 Worker Agents 对每次修复都跑完整 Required Checks；OPA、RBAC、approval gate 是授权控制，不是完整软件验证。
3. **Buildkite**：本轮公开一手材料未证明原生通用“生成 patch → 独立复验 → 受限 PR 写回”产品闭环；MCP、model provider 和 plugins 是可组合 substrate，默认不能升级为 SH3。
4. **Nx 与 Buildkite 生命周期**：两者官方当前文档提供功能，但本轮未找到各功能的统一 GA 标签；Nx 应写“已发布、官方未标统一阶段”，Buildkite 应写 `official-docs-no-unified-feature-stage`，而不是补写 GA。
5. **指标与效果**：本轮没有采用厂商成功率、TTG 或修复率来横向排序。若后续引用厂商数字，必须单列“厂商自述”、样本、失败定义、Oracle 与观察窗；否则保持 `unverified`。

## 6. 给企业的控制翻译（分析建议，非厂商事实）

1. **快慢环分离**：把产品原生 retry/rollback 配成低歧义、限次数、可观察的快环；把 Agent patch 固定在慢环，不以 rerun 成功冒充根因已修复。
2. **Oracle 外置**：Agent 可读取日志和提出 patch，但不能改写 original task、CodeQL query suite、Required Checks、policy 或 SLO；尤其禁止以 mute/skip、ignore、降阈值换取绿灯。
3. **授权按交叉单元细化**：不要授予“CI 自愈”这一笼统权限；按 `故障类别 × Task × 分支/环境 × 可执行动作` 定义，分别设置 retry count、token scope、PR 写、merge、deploy、rollback 与人工接管。

## 7. 复核清单

- [x] 每家公司按子能力拆分；未把 retry、诊断、PR/MR、Agent substrate 聚合成单一“自愈能力”。
- [x] 每项写明来源状态、发布时间/更新时间（可得时）与访问日 2026-08-09。
- [x] GitLab 完整 Pipeline 复验、Harness 完整 Required Checks、Buildkite 通用补丁闭环均保留为证据缺口。
- [x] Nx 未标为 GA；Buildkite 采用 `official-docs-no-unified-feature-stage`。
- [x] 所有 `SH/L` 均标为分析判级；厂商明示与未证实事项分开。

---
title: 六家公司 CI 自愈机制审计
tags:
  - research/agentic-cicd
  - research/case-audit
  - scenario/self-healing
status: complete
as_of: 2026-08-09
confidence: high-for-mechanisms-medium-for-outcomes
---

# 六家公司 CI 自愈机制审计

## 审计目标

用同一状态机比较 GitHub、GitLab、CircleCI、Harness、Nx、Buildkite：哪些能力只是重跑或诊断，哪些能生成候选变更，哪个 Oracle 负责复验，写回和最终接受由谁授权。产品名、营销中的 “Autonomous/Self-Healing” 和平台整体生命周期均不作为闭环证据。

## 统一审计字段

| 字段 | 判定问题 |
|---|---|
| 触发/分类 | 失败是否绑定 Run、Commit、Job、Exit Code 与 Failure Reason？是否保留 `unknown`？ |
| 确定性重试 | 重试粒度、上限、身份和停止条件是什么？再次成功是否只计流水线恢复？ |
| Agent 诊断 | Agent 读取哪些日志、Diff、历史、图结构和环境证据？ |
| 候选修复 | 输出是解释、Suggestion、Commit、Draft PR 还是直接动作？ |
| 验证 Oracle | 复验原失败 Task、Scanner、Validation Pipeline 还是全部 Required Checks？ |
| 写回目标 | 写入评论、MR/PR、PR Branch、默认分支还是环境？ |
| 身份权限 | Agent、写 Job、Service Account、Triggering User 与 Token Scope 如何分离？ |
| 外部门禁 | Review、Required Check、Approval、Protected Branch/Environment 是否仍持有接受权？ |
| 停止回退 | 最大轮次、失败关闭、Reject/Revert、Abort/Rollback 和人工接管是否明确？ |
| 生命周期 | 平台、Flow、Agent、Endpoint 和插件分别处于 GA、Preview、Beta 或“阶段未标”？ |
| SH/L 判级 | 闭环完整度和行动权限分别到哪里？不能互相代替。 |
| 证据缺口 | 只写“本轮公开一手材料未证明”，不将检索缺口写成产品不存在。 |

## 六家公司总表

| 公司 | 快环：分类/重试 | 慢环：诊断/修复 | Oracle 与写回 | 权限与接受边界 | 生命周期（截至 2026-08-09） | SH / L 与证据缺口 |
|---|---|---|---|---|---|---|
| GitHub | Actions 可重跑 Workflow/失败 Job，但不做通用根因分类 | CI Doctor 调查失败并输出 Issue/建议；Agentic Autofix 在 Code Scanning 微域生成补丁 | Autofix 使用 CodeQL 反馈并创建 Draft PR；完整 PR CI、Review、Merge 由 Ruleset 决定 | Agentic Workflows 默认只读，Safe Output 由隔离写 Job 执行 | Agentic Workflows、Agentic Autofix 为 Public Preview；CI Doctor 是官方参考 Workflow | 通用 CI Doctor SH1/L1；安全微域 Autofix SH3/L2。不能外推为 Actions 通用自愈 |
| GitLab | `retry:when/exit_codes` 对结构化 Failure Reason/Exit Code 有限重试 | Fix CI/CD Pipeline Flow 读取日志、Exit Code、MR Diff 和仓库，输出 Suggestion/MR | 当前文档未直接证明候选变更前自动重跑原完整 Pipeline | Remote Flow 使用 Runner、Service Account/Composite Identity；MR Approval、Pipeline、保护规则外置 | Duo Platform 18.8 GA；Fix Flow 18.8 GA，MR Suggestion 19.2 GA；其他平台子能力混合状态 | SH2/L2；完整 Pipeline 复验 `unverified` |
| CircleCI | Step/Workflow/Test 级有限重跑；Chunk 可把瞬态/基础设施失败路由到 Pipeline Rerun | Chunk 使用 Build History、测试、配置和仓库生成修复 | 候选推到分支并运行 Validation Pipeline；受保护分支创建 Draft PR，失败可关闭 | GitHub App、Project、Context Secret 与 Prompt 预授权共同限定动作；Review/Required Checks 外置 | Chunk Beta；自动重跑是独立的正式 CI 能力 | 代码路径 SH3/L2；重跑只计恢复。Validation Pipeline 是否覆盖全部 Required Checks 取决于配置 |
| Harness | Failure Strategy/Custom Error Categorization 可按规则 Retry、Abort、Rollback | Code Quality AutoFix 从失败日志生成修复/PR；Managed Worker Autofix 可写 PR Branch、重触发 CI 并受 Max Turns 限制 | 官方描述 Build Validation/重触发 Build；本轮材料未证明自动执行仓库全部 Required Checks | 旧 AutoFix Run Step 使用 PAT/Git Connector；Worker Agent 使用 RBAC×Grant、OPA、Approval、Audit。两种执行面不能互相补全 | Code Quality `mixed`；Worker Agents 已文档化但无统一生命周期标签 | AutoFix SH2—SH3/L2；批准后的 Worker 动作可 L3。完整 Required Checks `unverified` |
| Nx | Flaky Task 与代码失败分流；Flaky 可通过空提交重触发 PR CI | `nx fix-ci` 用 Project Graph、Task Metadata 与规则生成最小修复 | 明确重跑原失败 Task；人工 Apply 或对白名单、高置信、已验证任务 Auto-apply 到 PR Branch | Eligible/Never-fix Pattern、Protected Branch Prefix、Reject/Revert；Merge/Deploy 外置 | 官方称各计划可用，但页面未给整套能力统一 GA/Preview 标签 | 默认 SH3/L2；白名单 PR 微域局部 SH4，但不代表完整 CI/主干/生产 L4 |
| Buildkite | Command Step 有限 Retry；Test Engine 可识别/隔离 Flaky Test | 官方插件可总结失败；Model Provider/MCP 为 Agent 提供日志、状态和触发能力 | 平台内建能力停在分析/重跑；[官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)参考编排可等待 CI 并迭代，但不是默认产品闭环 | Job Token、OAuth/API Token Scope 控制读写；参考实现另需 GitHub PAT、人工标签与人工合并 | AI Agents 页面未标统一阶段；Remote MCP Direct Endpoint 单独为 Preview | 平台内建 SH1/L1；参考编排 SH3/L2，Token 授权 Run 可 L3。通用补丁/PR/复验闭环未获产品级证明 |

## GitHub：把三条路径分开

1. **Actions Rerun 是执行快环。** 它沿用原 `SHA/ref` 和触发者权限重新执行 Workflow/Job，不生成补丁，也不是通用 RCA。
2. **CI Doctor 是 Preview 框架中的官方参考 Workflow。** 它读取失败日志和历史模式，主要输出诊断 Issue/建议；GitHub 内部早期提案合并数字是接受度，不是修复正确率。
3. **Agentic Autofix 是安全微域闭环。** Code Scanning Alert 作为确定性 Finding，Agent 生成补丁并使用 CodeQL 反馈迭代，最终创建 Draft PR。
4. **Safe Output 只解决写入隔离。** Agent Job 默认只读，结构化输出交给独立写 Job；输出正确性仍由 Scanner、Required Checks 和 Review 判断。

证据入口：[[00_sources/briefs/2026-github-agentic-workflows|Agentic Workflows]]、[[00_sources/briefs/2026-github-agentic-autofix-code-scanning|Agentic Autofix]]。

## GitLab：结构化 Retry 与候选 MR 是两层能力

1. `retry:when`/`retry:exit_codes` 以 Failure Reason 和 Exit Code 做有限重试，适合 Runner、网络、外部依赖等已知瞬态问题；配置过宽会掩盖真实代码失败。
2. Fix Flow 读取 Pipeline Log、失败 Job、Exit Code、MR Diff 和仓库；证据不足、安全敏感或不可行动时允许只输出解释。
3. 修复出口是 Inline Suggestion 或新 MR，而不是默认分支直接写入。
4. 本轮公开一手材料未证明 Flow 在创建候选变更前自动完成原完整 Pipeline 复验；因此判为 SH2，而不是 SH3。

证据入口：[[00_sources/briefs/2026-gitlab-duo-agent-platform|Duo Agent Platform]]、[[00_sources/briefs/2026-gitlab-fix-cicd-pipeline-flow|Fix CI/CD Pipeline Flow]]。

## CircleCI：先分瞬态重跑与代码修复

1. Automatic Rerun 和 Failed-test Rerun 是确定性快环；再次通过只能计“执行恢复”，不能计根因消除。
2. Chunk 利用跨运行 Build History、测试、配置、依赖行为和代码上下文诊断失败。
3. 代码路径可推分支、触发现有 Pipeline，并在受保护分支创建 Draft PR；验证失败可继续尝试或关闭 PR。
4. 瞬态/基础设施路径在 Prompt 预授权时可自动重跑，否则询问用户。
5. Chunk 仍为 Beta；Context Secret、GitHub App 权限、Validation Pipeline 覆盖和循环预算需要部署侧审计。

证据入口：[[00_sources/briefs/2026-circleci-chunk-agent|CircleCI Chunk]]、[[00_sources/briefs/2026-circleci-agentic-validation-infrastructure|Agentic Validation Infrastructure]]。

## Harness：不要把旧 AutoFix 与 Worker Agent 权限合并

1. Failure Strategy 负责 Retry、Abort、Rollback；Custom Error Categorization 负责结构化错误标签，二者均不是代码修复 Agent。
2. Code Quality AutoFix Run Step/API 读取日志和近期变更，使用 PAT、模型密钥和 Git Connector 生成 `ai-autofix` PR。
3. Managed Worker Autofix 的官方产品描述包含 PR Branch 提交、重触发 CI 和 Max Turns；它运行在受治理 Pipeline 中。
4. Worker Agent 的 RBAC×Grant、Scoped Credential、OPA、Approval 与 Audit 不能反向证明旧 AutoFix Run Step 拥有同样身份边界。
5. “Build 通过”是当前厂商文档支持的验证终点；本轮公开一手材料未证明自动覆盖仓库全部 Required Checks、自动合并或发布。

证据入口：[[00_sources/briefs/2026-harness-code-quality-agents|Code Quality Agents]]、[[00_sources/briefs/2026-harness-worker-agents|Worker Agents]]。

## Nx：自治被缩小到 Task × PR Branch

1. `nx fix-ci` 在前序 Task 失败后仍运行，使用 Project Graph、Task Metadata、CI Run 与 `.nx/SELF_HEALING.md` 形成上下文。
2. Eligible/Never-fix Pattern 和 Protected Branch Prefix 限定可修 Task 与分支。
3. Agent 生成候选后明确重跑原失败 Task；这不能证明其他 Required Checks、全仓影响和业务语义均正确。
4. Auto-apply 只对白名单 Task，且需要 Pattern Match、High Confidence、Explicit Verification；写入目标仍是 PR Branch。
5. 历史记录只生成 Auto-apply 建议，不应自动扩大权限。

证据入口：[[00_sources/briefs/2026-nx-self-healing-ci|Nx Self-Healing CI]]。

## Buildkite：自愈底座不等于修复产品

1. Retry 可按 Exit Status、Signal 和 Signal Reason 限次执行；Test Engine 可隔离 Flaky Test，但 Muted/Skip 可能使 Pipeline 变绿，不能计为代码修复。
2. 官方失败分析插件可以读取日志并写 Annotation；它们没有产品级自动补丁/PR/复验证据。
3. Model Provider、Skills 与 MCP 让 Agent 读取 Build State、Log、Artifact 并触发 Run；副作用继承 Token Scope。
4. [Buildkite 官方 PR Build Fixer](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)展示客户可组合出 SH3 流程：人工标签触发，容器内 Agent 用 MCP 读日志，创建新 PR、等待 Build 并在失败时迭代，最终由人合并；参考实现不等于平台默认内建能力或效果基准。
5. Remote MCP Direct Endpoint 为 Preview；不能由此把整个 Agentic CI 标成 Preview，也不能把“正式文档能力”写成 GA。

证据入口：[[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines|Buildkite AI Agents in Pipelines]]、[官方 PR Build Fixer 参考实现](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)、[[50_deepdives/buildkite/README|Buildkite Deep Dive]]。

## 跨公司结论

1. **闭环终点逐级收窄。** Buildkite 强在重试与 Agent 底座；GitHub CI Doctor 强在调查；GitLab 强在 Suggestion/MR；CircleCI、Harness、Nx 把候选接回不同粒度的 Pipeline/Task 验证。
2. **产品状态与闭环成熟度不相关。** GitLab Flow GA 不自动成为 SH3；CircleCI Chunk Beta 也不妨碍其公开机制比某些 GA 平台更接近验证循环。
3. **最强公开微闭环来自专用 Oracle 或窄 Task。** GitHub Agentic Autofix 用 CodeQL，Nx 用失败 Task；覆盖面越窄，越容易证明候选确实消除了可观察失败。
4. **完整 Required Checks 是共同缺口。** GitLab 与 Harness 没有直接公开证据；Nx 明确只复跑失败 Task；CircleCI 取决于 Validation Pipeline 配置；Buildkite 没有产品级补丁闭环。
5. **最终接受权仍在外部。** 六家公司都没有用 Agent Confidence 取代 Review、Required Check、Protected Branch/Environment、Approval 或 Deployment Policy 的充分证据。

## 仍需补证

- 按故障类别统计的 Repair@1、错误修复率、完整 Required Checks 通过率和人工拒绝率；
- 自动写回后 30/90 天缺陷逃逸、复发和回滚率；
- 每个最终保留修复的 Token、Runner、Queue 和 Review 总成本；
- GitLab Fix Flow 的补丁后完整 Pipeline Trace；
- Harness AutoFix/Worker Autofix 的执行面迁移、身份映射和 Required Checks 覆盖；
- CircleCI Validation Pipeline 与 GitHub Required Checks 的竞态和覆盖；
- Nx VCS 写身份和 High Confidence 校准方法；
- Buildkite 原生产品与[官方 PR Build Fixer 参考实现](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)的明确支持边界。

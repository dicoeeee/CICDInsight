---
title: CI/CD 问题自愈证据矩阵
tags:
  - research/agentic-cicd
  - research/evidence-map
  - scenario/self-healing
status: complete
as_of: 2026-08-09
---

# CI/CD 问题自愈证据矩阵

## Claim—Evidence—Gap

| Claim | 一手证据 | 证据支持到哪里 | Gap / 反例 | 置信度 |
|---|---|---|---|---|
| 六家公司公开能力停在不同闭环终点，不能按产品名横向排名 | [[50_deepdives/cicd-self-healing/35_company-mechanism-audit|六家公司机制审计]] | Buildkite 强在 Retry/Agent 底座；GitHub CI Doctor 强在调查；GitLab 输出 Suggestion/MR；CircleCI、Harness、Nx 接回不同粒度验证 | 生命周期与 SH 完整度不是同一轴；效果数据不可比 | 高（机制），中（效果） |
| 通用 CI 诊断与候选 PR/MR 已产品化，但能力形态和状态不同 | [GitHub CI Doctor](https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-quality-hygiene/)、[GitLab Fix Pipeline](https://docs.gitlab.com/user/duo_agent_platform/flows/foundational_flows/fix_pipeline/)、[Harness AutoFix](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)、[CircleCI Chunk](https://circleci.com/docs/guides/toolkit/chunk-setup-and-overview/) | 分别输出诊断 Issue、Suggestion/MR、PR 或验证后 Draft PR | GitHub 是 Preview 框架中的参考 Workflow；GitLab Flow GA；CircleCI Beta；Harness 子能力阶段混合 | 高 |
| GitHub Agentic Autofix 在 Code Scanning 微域形成 Analyzer → Agent → Analyzer → Draft PR | [[00_sources/briefs/2026-github-agentic-autofix-code-scanning|Agentic Autofix Brief]] | Code Scanning Alert 触发、CodeQL 反馈迭代、Draft PR | Scanner 复验不代表完整 PR CI 或业务正确；Public Preview | 高（机制），中（效果） |
| 自动验证与 PR 分支写回已经出现，但验证粒度不同 | [Nx Self-Healing CI](https://nx.dev/docs/features/ci-features/self-healing-ci)、[CircleCI Chunk Validation](https://circleci.com/changelog/chunk-now-validates-changes-by-running-your-ci-pipeline/)、[Harness Worker Agents](https://www.harness.io/blog/introducing-autonomous-worker-agents) | Nx 明确重跑失败 Task；CircleCI 运行 Validation Pipeline；Harness 描述重触发 Build 至成功或 Max Turns | GitLab/Harness 完整 Required Checks 未证明；CircleCI 取决于 Pipeline 配置；Nx 明确不是完整 PR CI | 高（能力），中（覆盖） |
| 瞬态/基础设施失败应走有限重试，不应让 Agent 猜代码修复 | [GitLab Retry](https://docs.gitlab.com/ci/yaml/#retry)、[CircleCI Automatic Reruns](https://circleci.com/docs/guides/orchestrate/automatic-reruns/)、[Buildkite Retry](https://buildkite.com/docs/pipelines/configure/retry)、[Nx Flaky Tasks](https://21.nx.dev/docs/features/ci-features/flaky-tasks) | 按 Failure Reason、Exit Code、Step/Workflow、Signal 或 Flaky 证据有限重试 | 再次成功只计执行恢复；配置过宽会制造 Retry Storm 或掩盖真实缺陷 | 高 |
| Buildkite 当前是 Agent 的 CI 证据/执行底座，不是产品级通用修复闭环 | [[00_sources/briefs/2026-buildkite-ai-agents-in-pipelines|Buildkite Agentic CI Brief]]、[官方 PR Build Fixer 参考实现](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/) | 平台提供 Retry、Test Engine、失败分析插件、Model Provider 与 MCP；官方参考编排可创建修复 PR、等待 Build 并迭代 | 参考实现由客户配置 Pipeline/Token/标签且人工合并；本轮材料未证明平台内建通用补丁/PR/复验 | 高 |
| 安全修复需要 Analyzer 作为外部 Oracle | [Snyk Agent Fix Architecture](https://snyk.io/blog/snyk-agent-fix-agentic-architecture/)、[[00_sources/briefs/2026-github-agentic-autofix-code-scanning|GitHub Agentic Autofix]]、[Dependabot Agent Remediation](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation/) | 确定性 Finding 驱动 Agent；同类 Analyzer 复验目标告警 | Scanner 或 LLM Judge 不是充分的功能 Oracle；自动消除告警可能产生替代性缺陷 | 中高 |
| 测试“自愈”通常只修 Locator，不等于产品问题恢复 | [BrowserStack Self-Heal](https://www.browserstack.com/docs/automate/selenium/self-healing)、[Low Code Self-Heal](https://www.browserstack.com/docs/low-code-automation/test-recording/browserstack-ai/ai-self-heal) | Locator 失效时寻找替代元素、记录原因并可生成 PR | 文档明确限制于 Locator；“可能掩盖语义变化”是基于机制的风险推断 | 高（机制），中高（风险推断） |
| GitOps 可把自愈约束到 Scope、Runbook、Approval 和 Audit | [Akuity Agents](https://akuity.io/blog/beyond-dashboards-ai-agents-for-gitops-operations)、[Tool Policies](https://docs.akuity.io/intelligence/capabilities/) | 非生产可自动执行，生产可请求批准；按 Tool/Argument/Cluster/Namespace/Runbook 定义策略 | 成效数字为厂商自述；缺少公开故障注入与错误处置数据 | 高（机制），中低（效果） |
| 开源 SRE Agent 已有持续调查、PR 和可选 Remediation Tool | [HolmesGPT Repository](https://github.com/HolmesGPT/holmesgpt) | Operator Mode 持续检查并可开修复 PR；可接 Kubernetes Remediation MCP | 具体自治级别取决于部署者开放的工具和权限，不等于默认安全 | 中高 |
| AWS DevOps Agent 当前不是代执行的生产自愈 | [AWS Multi-agent Reasoning](https://aws.amazon.com/blogs/devops/how-aws-devops-agent-uses-multi-agent-reasoning-to-find-root-causes/)、[Production Operations](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-production-operations-index.html) | 自动调查、根因、缓解计划、预防建议和 Agent-ready Spec | 官方明确写明不代操作员执行 Remediation，写能力限于 Ticket/Support Case | 高 |
| 通用 CI 修复能力仍远未解决 | [CI-Repair-Bench](https://arxiv.org/abs/2604.27148) | 567 个真实故障、103 个仓、12 类错误，以原始完整 CI 复验；最佳受测模型修复 18.9% | arXiv 预印本；开源仓样本不能直接外推企业标准路径 | 中高 |
| 当次合并/绿灯不能代表长期质量 | [Agent-generated PR Post-merge Study](https://arxiv.org/abs/2601.20109)、[Failed Agentic PR Study](https://arxiv.org/abs/2601.15195) | 2026 原始研究提示 Merge Outcome 与后续质量、任务类型和工作流约束并不等价 | 研究对象为开源 Agent PR，不能直接外推到受控企业内部流水线 | 中高 |

## 证据校准规则

1. 官方文档可证明“机制存在”，不能单独证明“生产效果可靠”。
2. 厂商客户指标和内部 Merge Rate 标记为第一方结果，不作为跨组织基准。
3. `GA` 只表示对应产品/Flow 可用；某场景是否达到 SH3/SH4 必须逐项验证权限、Oracle、回退和停止条件。
4. 开源 Star、Release 活跃度和 CNCF 状态用于判断关注度与可检查性，不证明自愈正确率。
5. 任何缺少独立验证或能让 Agent 修改成功判据的案例，都不能被评为完整闭环。
6. 本轮未检索到的能力只记录“公开一手材料未证明”，不写成产品不存在。

## 仍需补证

- 同一失败分类下，不同产品的首次修复成功率、错误修复率和平均 Token/Runner 成本；
- 自动写回 PR 分支后的缺陷逃逸率与 30/90 天复发率；
- 生产 Runbook 自动执行的误操作、人工接管和回退成功率；
- 跨语言、跨平台、硬件相关和多仓回归的公开真实数据；
- Agent 修改测试或配置后，独立 Oracle 能否发现“通过弱化检查获得绿灯”。
- GitLab Fix Flow、Harness AutoFix/Worker Autofix 是否自动覆盖仓库完整 Required Checks；
- CircleCI Validation Pipeline 与 Required Checks 的覆盖和竞态；Nx VCS 写身份与 High Confidence 校准；
- Buildkite 原生产品与[官方 PR Build Fixer 参考实现](https://buildkite.com/resources/blog/building-ai-powered-ci-workflows-three-practical-examples/)的支持边界。

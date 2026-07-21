---
title: GitHub Agentic Workflows Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/github
status: complete
as_of: 2026-07-21
---

# GitHub Agentic Workflows Claim—Evidence—Gap Matrix

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 |
|---|---|---|---|---|
| GAW-C01 | Agentic Workflows 已进入 Public Preview | [GitHub 2026-06-11 Changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview/) | 仍可能显著变化，不是 GA | high |
| GAW-C02 | Markdown 编译为标准 Actions `.lock.yml` | [GitHub Docs](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows)、[Compilation](https://github.github.com/gh-aw/reference/compilation-process/) | 正文运行时加载，不代表行为确定 | high |
| GAW-C03 | Agent Job 默认只读，写动作由 Safe Outputs 外置 | [Security Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 配置、Token 或 Safe Output 过宽仍可扩大风险 | high |
| GAW-C04 | 编译期做 Schema、表达式、依赖和 Action Pin 校验 | [Compilation](https://github.github.com/gh-aw/reference/compilation-process/) | 编译器不能证明自然语言任务正确 | high |
| GAW-C05 | Agent 运行在容器与网络防火墙后 | [Security Architecture](https://github.github.com/gh-aw/introduction/architecture/)、[Network](https://github.github.com/gh-aw/reference/network/) | 仍信任 Runner、Kernel、Firewall、Gateway 等底层 | high |
| GAW-C06 | Safe Output 在写入前可经过独立 Threat Detection | [Threat Detection](https://github.github.com/gh-aw/reference/threat-detection/) | 默认检测本身是模型判断，不是完备安全证明 | high for mechanism, medium for outcome |
| GAW-C07 | 支持 Copilot、Claude、Codex、Gemini | [GitHub Docs](https://docs.github.com/en/copilot/how-tos/github-agentic-workflows/creating-github-agentic-workflows) | 模型、认证、功能和成本不完全等价 | high |
| GAW-C08 | GitHub Tools/MCP 可以按 Tool 和 Repo 收窄 | [GitHub Tools](https://github.github.com/gh-aw/reference/github-tools/)、[Tools](https://github.github.com/gh-aw/reference/tools/) | Registry 字段目前只提供信息，不强制信任 | high |
| GAW-C09 | 可把确定性 Steps/Jobs 与 Agent 推理组合 | [DeterministicOps](https://github.github.com/gh-aw/patterns/deterministic-ops/) | 自定义 Job/Secret 设计不当会绕开隔离意图 | high |
| GAW-C10 | 多仓和复杂任务可用 Orchestrator/Worker 拆分 | [OrchestratorOps](https://github.github.com/gh-aw/patterns/orchestrator-ops/)、[MultiRepoOps](https://github.github.com/gh-aw/patterns/side-repo-ops/) | Fan-out、跨仓 Token、失败补偿和成本更复杂 | high |
| GAW-C11 | 共享 Workflow 可通过 Import/Source/Lock 固定 | [Sharing Workflows](https://github.github.com/gh-aw/practices/sharing-workflows/)、[Imports](https://github.github.com/gh-aw/reference/imports/) | 本地定制和上游更新仍可能冲突 | high |
| GAW-C12 | 平台提供运行/日预算、超时与审计 | [Cost Management](https://github.github.com/gh-aw/reference/cost-management/)、[CLI Logs](https://github.github.com/gh-aw/setup/cli/) | AI Credits 不是跨 Provider 的真实货币成本等价物 | high |
| GAW-C13 | CI Doctor 可产生真实修复 PR | [GitHub 官方 Fault Investigation](https://github.github.com/gh-aw/blog/2026-01-13-meet-the-workflows-quality-hygiene/) | 9/13 合并率为 GitHub 自身样本，非行业 Benchmark | medium-high |
| GAW-C14 | PR Safe Output 可继续进入现有 CI，但触发行为取决于 Token、事件和审批策略 | [Triggering CI](https://github.github.com/gh-aw/reference/triggering-ci/)、[GitHub Actions `GITHUB_TOKEN`](https://docs.github.com/en/actions/concepts/security/github_token#when-github_token-triggers-workflow-runs) | gh-aw 文档称默认不触发；当前 Actions 文档对 PR `opened`/`synchronize`/`reopened` 增加“生成待批准 Run”的例外。无需批准的闭环仍需 App/PAT，并应实测 | high for documented discrepancy |
| GAW-C15 | Lock File 可因已撤回/过旧编译器版本被阻止 | [Releases and Versioning](https://github.github.com/gh-aw/reference/releases/) | 快速版本撤回增加平台运营负担 | high |
| GAW-C16 | 公共仓和私有/内部仓的默认 Integrity Guard 不同 | [Integrity Filtering](https://github.github.com/gh-aw/reference/integrity/) | 私有内容不等于可信内容，企业需显式配置 | high |
| GAW-C17 | 日 AIC 默认值在官方页面之间存在表述矛盾 | [AIC Specification](https://github.github.com/gh-aw/specs/ai-credits-specification/)、[Cost Management](https://github.github.com/gh-aw/reference/cost-management/) | 应以固定版本生成 Lock 和外部预算验证，不能依赖文档默认 | high for discrepancy |
| GAW-C18 | 省略 Safe Outputs 不等于零写能力 | [Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs/) 默认注入保守 `create-issue` | 行为可能随 Preview 版本调整，需检查 Lock | high |
| GAW-C19 | Agentic Workflow 不是 Actions 的替代 Pipeline，而是编译成传统 Actions `.lock.yml`、增强既有确定性 CI/CD | [GitHub Agentic Workflows 首页](https://github.github.com/gh-aw/)、[Compilation Process](https://github.github.com/gh-aw/reference/compilation-process/) | 编译后执行结构确定，不代表 Agent 的语义判断和输出确定 | high |
| GAW-C20 | 同一 Agentic Workflow 可组合确定性 `steps:`、`jobs:` 与 Agent 推理 | [DeterministicOps](https://github.github.com/gh-aw/patterns/deterministic-ops/)、[Custom Steps and Jobs](https://github.github.com/gh-aw/reference/steps-jobs/) | 自定义 Step/Job 在 Agent Firewall 外按标准 Actions 安全模型运行，不能假设自动继承 Agent 沙箱 | high |
| GAW-C21 | 固定 CI 与 Agentic Workflow 可形成双向接力：`workflow_run` 把 CI 结果交给 Agent，PR/Dispatch 把候选结果交回固定 Workflow | [Triggers](https://github.github.com/gh-aw/reference/triggers/)、[Safe Outputs](https://github.github.com/gh-aw/reference/safe-outputs/)、[Triggering CI](https://github.github.com/gh-aw/reference/triggering-ci/) | PR 创建不等于 CI 已闭环；必须显式设计 Safe Output 身份、事件类型、是否需人工批准和下游 Gate | high |
| GAW-C22 | 编译器固定外层 Job 拓扑，Agent 只在受限 Job 内动态选择分析路径和工具 | [Compilation Process](https://github.github.com/gh-aw/reference/compilation-process/)、[Security Architecture](https://github.github.com/gh-aw/introduction/architecture/) | 这是对官方编译图和分层信任模型的架构归纳；正文可运行时变化，Safe Output 也可触发另一个受控 Workflow | high for mechanism, medium-high for synthesis |

## 关键证据缺口

- 缺少跨企业、跨模型、相同任务集的独立成功率和成本基准；
- 缺少 Prompt Injection、Safe Output 绕过和恶意 MCP 的第三方红队结果；
- Public Preview 的兼容性、SLO、支持和未来计费仍可能变化；
- GitHub 自报案例未给出完整失败样本、人工投入和总运行成本；
- 尚无关键生产部署由 Agentic Workflows 普遍闭环执行的可信证据。

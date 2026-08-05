---
title: Agent 生成验证到 CI/CD Gate 的技术事实审计
date: 2026-08-03
as_of: 2026-08-03
status: research-complete
confidence: high
scope: 审计 AWS DevOps Agent Release Management、Meta Catching JiTTests 与 GitHub/GitLab 平台机制，区分厂商事实、跨系统设计推断和无依据抽象；不评价测试用例质量或测试类型优劣。
---

# Agent 生成验证到 CI/CD Gate 的技术事实审计

## 结论先行

1. **“动态生成验证”已被两条一手证据链证实，但它们止于不同位置。**AWS Preview 产品已公开“变更/意图 → 生成 test plan → 对已部署应用执行 → 报告/Check Run”；Meta 已公开“Diff → 推断 intent/risk → 生成并运行 catching tests → rule/LLM assessor → 工程师确认”。两者都不是公开的端到端“Agent 自动准入发布”系统。
2. **Gate 不是 Agent verdict 的同义词。**AWS 对 release-readiness code review 直接提供 `BLOCK` 建议，并明确可在 GitHub 配为 required status check、在 GitLab 配为 approval rule；其 release-testing GitHub Action 则写回 Check Run。GitHub/GitLab 的原生保护机制可把相应状态变成合并门禁，但 AWS 没有公开声明“release test result 默认就是 required gate”，Meta 也没有公开同步或强制门禁证据。
3. **原“五段链”可作为本页的参考架构，但必须标为分析抽象。**唯一可以无歧义放在产品事实层的节点是：`变更输入 → AWS/Meta 生成或推导验证 → 在各自公开范围内执行 → 结果/评估输出 → 平台可配置的合并 Gate`。`Change Envelope`、`Verification Planner`、`Execution Broker`、`Evidence Evaluator`、`Gate Adapter`都不是 AWS 或 Meta 公布的原生命名或统一接口。
4. **三个“合同”不得写成现有产品 schema。**AWS/Meta 都没有公开 `VerificationPlan`、`EvidenceBundle`、`GatePolicy` 的字段定义、签名格式或 API schema。它们只能以“企业若要把动态验证接入 Gate，应另行设计的控制面对象”出现；其中预算、oracle、plan hash、签名、Agent 禁改 policy 都是企业设计建议，不是本次对象的厂商事实。

## 口径、来源与状态

- **访问时间：**全部链接于 2026-08-03 访问。
- **来源等级：**仅使用 AWS User Guide / What’s New、Meta Engineering、作者的原始论文、GitHub Docs、GitLab Docs、SLSA 规范。没有用二手报道支撑关键结论。
- **对象状态：**AWS Release Management 为 Preview（公开公告 2026-06-17，文档仍记录区域 Preview 限制）；Meta JiTTesting 是 Meta 内部生产 workflow 的公开研究报告，论文 v1 于 2026-01-30 提交，非对外产品。
- **否定性结论口径：**“未找到证据”只指列出的、于本次访问日可得的一手材料；不等于未来或内部永远不存在该能力。

## 一、AWS DevOps Agent Release Management：事实链与边界

| 环节 | 可核验的一手事实 | 产品状态 / 边界 | 对 Gate 叙事的含义 |
|---|---|---|---|
| 触发 | 自动 release-readiness review 可在 GitHub PR 或 GitLab MR 打开/更新时运行；release testing 可作为 GitHub Actions 与 GitLab CI pipeline stage 触发。 | Release Management 是 Preview；总览页对 GitLab CI 只给出“可作为 stage”这一能力描述。 | 证明 Agent 验证可进入流水线运行时，而不是只能在聊天中建议。 |
| 规划 | Release testing 会基于代码改动或用户 test intent 生成 test plan；从 PR/branch 触发时目标是受影响功能。 | AWS 未公开 test-plan 的 JSON/schema、字段、持久化位置或审批 API。 | 可称“生成计划”；不可称“有公开的 `VerificationPlan` 契约”。 |
| 执行 | Agent 对 target URL 的运行中 Web/API 应用执行生成测试；可能发出 `POST`、`PUT`、`DELETE`。官方建议使用 staging，生产仅限写操作安全的应用。 | 真实请求可能改动数据、触发通知或支付；这是公开的副作用边界。 | 这是将 Agent 验证看作受控执行工作负载的强事实依据。 |
| 环境与权限 | 自动 verification testing 在 AWS 管理的 verification environment 构建、运行、测试；默认没有内部网络。企业可通过 private connection/VPC 开通内部访问，并配置运行时 IAM role 访问内部服务/Secrets Manager。 | 这些事实直接描述 AWS 的 build/verification environment；不等同于 release testing 的 target application 权限模型全部公开。 | 证明网络与身份是部署控制项，而不是 Agent 自动拥有的能力。 |
| 输出 | Release test 返回 failures、受影响功能、复现步骤、建议修复；在 CI 触发时页面称会给 GitHub Check Run 写入 pass/fail 和详细摘要。Release-readiness report 还含 `BLOCK / Proceed with Caution / Safe to Release`、severity、execution journal。 | 执行日志/报告是 AWS 产品输出；没有公开可导出的统一、签名的 evidence schema。 | 证明“结果可被报告并关联 commit/PR”；不证明“报告本身天然可审计为政策证据”。 |
| Gate | AWS 明确说：GitHub code review 可配为 required status check 以在存在 blocking findings 时阻断 merge；GitLab code review 可配 approval rule 要求处理 blocking findings。 | 这是**readiness review**的直接机制说明。Release testing 页面只说明 GitHub Check Run；将该 Check Run 设为 required check 是 GitHub 的额外仓库保护配置。GitLab release-testing 的 status 回写接口未公开。 | AWS 是本页“从 Agent 输出接到宿主平台 Gate”的可验证锚点，但须精确区分 readiness-review enforcement 与 release-test result。 |

### AWS 精确来源

| 来源 | 发布时间 / 页面状态 | 关键原文所证明的范围 |
|---|---|---|
| [AWS Release Management announcement](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) | 2026-06-17；Preview | 发布 release-readiness review 与 release testing；测试计划在 customer-provisioned environment 中生成和运行；Preview 限 `us-east-1`。 |
| [Release management overview](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html) | 文档页未给统一发布日期；访问 2026-08-03 | 变更感知的 test plan、已部署实例执行、GitHub Actions/GitLab CI stage、PR/MR 自动触发及 `BLOCK` 级建议。 |
| [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) | 文档页未给统一发布日期；访问 2026-08-03 | 生成 test plan、target URL、真实写请求及副作用、GitHub Action 创建 `in_progress` Check Run 并回传 pass/fail 摘要、测试结果内容。 |
| [Release-readiness code reviews](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | 文档页未给统一发布日期；访问 2026-08-03 | PR/MR 自动触发、managed verification environment、VPC/ENI、IAM runtime role、allowlist、execution journal，以及 GitHub required check / GitLab approval rule 的配置语义。 |
| [AWS 官方 GitHub：devops-agent-qa](https://github.com/aws-actions/devops-agent-qa) | `aws-actions` 组织的公开仓库；访问 2026-08-03 | README 标题为 “AWS DevOps Agent Release Testing Action”，其可复制的 workflow 使用 `aws-actions/devops-agent-qa@v1`，并说明通过 webhook 触发 Agent Space、回报 GitHub Check Run。 |
| [AWS DevOps Agent What’s New](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) | 2026-07-09 条目；访问 2026-08-03 | 仍记录 Release Management Preview 限制为 US East (N. Virginia)。 |

### AWS 的来源冲突与 Gate 区分

#### 1. Action 名称：来源冲突已记录，页面不使用具体名称

- **AWS User Guide 的段落文字：**称 GitHub Action 为 `aws-actions/devops-agent-release-testing@v1`。
- **同一页的 YAML 示例：**使用 `aws-actions/devops-agent-qa@v1`。
- **官方 GitHub 仓库核验：**`aws-actions/devops-agent-qa` 是公开仓库；README 标题为 “AWS DevOps Agent Release Testing Action”，其示例同样使用 `aws-actions/devops-agent-qa@v1`。
- **审计结论：**前者是未被本次官方 GitHub 仓库核验的文档标识，后者是经 AWS 官方 GitHub 仓库核验的当前公开 action 标识。本研究不能仅凭无法加载的 URL 证明 `devops-agent-release-testing` 仓库不存在，因此将此保留为**来源冲突**，而非“已证明前者不存在”。技术洞察页不写任一具体 Action 名；若实施文档需要示例，仅可引用已核验的 `devops-agent-qa@v1` 并在上线前重新核验 tag/README。

还有一个实现归属的不一致：User Guide 的流程称“Action 创建 Check Run”，而官方仓库 README 称“Agent Space 创建 Check Run”。两边都一致于“对 commit/PR 出现 `in_progress`，最终回传带详细摘要的 pass/fail Check Run”；不应在页面中声称具体由 workflow runner 还是 Agent Space API 写入。

#### 2. 官方 Action 源码暴露的流水线接入机制

AWS 官方 `aws-actions/devops-agent-qa` 仓库不仅给出使用说明，也公开了 Action 源码。当前公开实现可以直接证明以下技术链：

1. Action 从 GitHub 运行上下文读取当前 `headSha`；若是 PR，则优先取 PR head SHA，并尝试根据 commit 反查 `prNumber`。
2. Action 将 `eventType=deployment_completed`、`testProfileId`、可选 `testRequirement`、`repository`、`headSha` 和 `prNumber` 组成 Webhook 载荷，提交给 Agent Space。
3. Action 在 Webhook 返回 2xx 后结束；它不在 GitHub Runner 内执行生成的测试，也不轮询 Agent 的完整执行过程。
4. 官方 README 说明 Agent Space 随后创建关联当前 commit 的 `in_progress` Check Run，Release Testing Agent 对应用运行测试，最终把 Check Run 更新为 pass/fail 并附详细摘要。

这形成了一个有直接实现依据的**异步外部检查模式**：流水线适配器只负责把 revision 与运行意图提交给 Agent 服务；Agent 服务独立执行；GitHub Check Run 承担跨系统状态回写。`testProfileId` 与可选 `testRequirement` 还体现了“稳定执行档案 + 本次运行关注点”的分离，但 AWS 没有公开 test plan 的字段 schema。

来源：[官方 README](https://github.com/aws-actions/devops-agent-qa/blob/main/README.md)、[官方 `action.yml`](https://github.com/aws-actions/devops-agent-qa/blob/main/action.yml)、[官方 `src/index.ts`](https://github.com/aws-actions/devops-agent-qa/blob/main/src/index.ts)（均访问 2026-08-03）。

#### 3. Readiness review 的 Gate 与 release testing 的 Check Run 不是同一件事

| 维度 | Release-readiness code review | Release testing（GitHub Actions 路径） |
|---|---|---|
| 输入 / 触发 | 已连接 GitHub PR 或 GitLab MR 打开、更新时可自动审查。 | workflow 触发；AWS 文档以 staging deployment 后为例。 |
| Agent 产出 | PR/MR inline comments 与 overall status；report 包含 `BLOCK / Proceed with Caution / Safe to Release`、severity 和 execution journal。 | target application 的动态 test plan 与实际执行结果；GitHub Check Run 从 `in_progress` 到 pass/fail detailed summary。 |
| AWS 直接公布的 Gate 接法 | **GitHub：**“Configure as a required status check to block merges when blocking findings exist”。**GitLab：**“Configure as a merge request approval rule to require resolution of blocking findings”。 | AWS 只公布 Check Run 回写；**没有**说它默认是 required check，也没有给出 GitLab external status check / approval-rule 回写的 release-testing 接法。 |
| 宿主平台的实际执法实体 | GitHub protected branch 的 required status check；GitLab merge-request approval rule。 | 若要阻断 GitHub merge，必须由仓库把该 Check Run context 配进 required status checks；GitLab 路径在 AWS 公共材料中未证明。 |
| 页面可写结论 | “AWS readiness review 已公开产品化接入 merge governance。” | “AWS release testing 已公开产生 commit/PR 关联的动态验证状态；是否成为 Gate 由 GitHub 保护规则另行配置。” |

GitLab 的这两类机制也不能混写：[approval rules](https://docs.gitlab.com/user/project/merge_requests/approvals/rules/) 要求由指定用户/组完成所需审批；[external status checks](https://docs.gitlab.com/user/project/merge_requests/status_checks/) 则由外部系统按 HEAD SHA 回写状态，且默认不阻断，需另启用 `Status checks must succeed`。AWS readiness 文档说的是前者；AWS 没有公开表明 release testing 使用后者。

## 二、Meta Catching JiTTests：事实链与边界

| 环节 | 可核验的一手事实 | 研究状态 / 边界 | 对 Gate 叙事的含义 |
|---|---|---|---|
| 输入与规划 | JiTTest 论文的 intent-aware workflow 对被测 Diff 推断实现 intent 可能引入的 risks，然后据风险构造 mutants；Meta Engineering 也以“PR submitted 时”描述按变更生成 bespoke test。 | 论文公开的是研究/workflow，而不是一个命名为 plan 的服务接口。公开材料不能证明全量 PR 都同步执行。 | 有力证明“验证选择可由当前 Diff 驱动”，但不应写作已发布计划对象。 |
| 对照执行 | Catching test 的目标是：在 proposed Diff 失败、在 parent revision 通过；论文披露的两条 diff-aware workflow 自 2025-09 属于 production deployment。 | 论文没有公开 CI runner、环境隔离、执行身份、网络范围、超时或预算。 | 证明它不是仅文本评分，而是以 diff/parent 行为对照形成候选 signal；不能填补执行控制面的细节。 |
| 证据评估 | 规则和 LLM assessor 用于减少 false positive 和识别可能 true positive；论文明确说 true-positive rule 不是保证，结果用于判断是否值得联系工程师。 | assessor 是候选判断，论文明确保留人类确认；不是安全策略引擎。 | 证明“原始失败需要被评估为工程师可消费信号”，不证明自动授权。 |
| 输出与处置 | Meta Engineering 称工程师得到清晰、相关的报告；论文披露 41 次联系工程师以确认信号，工程师确认/修复/放弃 Diff 的例子。 | 公开结果是 human-in-the-loop，非 Required Check、非 GitHub/GitLab 集成、非自动审批。 | 这条链最稳妥的终点是“人工处置的候选证据”，不是 Gate decision。 |

### Meta 精确来源

| 来源 | 发布时间 / 研究状态 | 关键原文所证明的范围 |
|---|---|---|
| [Just-in-Time Catching Test Generation at Meta](https://arxiv.org/abs/2601.22832) / [PDF](https://arxiv.org/pdf/2601.22832) | arXiv v1 2026-01-30；作者称投稿 FSE 2026 industry track；访问 2026-08-03 | Diff-aware workflows 在生产部署自 2025-09；Diff/parent 通过-失败对照；intent/risk workflow；rule/LLM assessor；联系工程师；研究结果和局限。 |
| [Meta Engineering: JiTTesting](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/) | 2026-02-11；工程实践说明 | PR 提交时自动生成、intent → mutant → tests → rule/LLM assessors → engineer reports 的公开概括。 |

### Meta 的否定性审计

下列内容在上述论文和工程博客中**未找到充分一手证据**，不可写进技术方案的“已发生事实”层：

- 全部 PR、全部仓库、全部语言的覆盖率或同步调度范围；
- 强制 merge block、Required Check、GitHub Check Run、GitLab status check 或 release approval 集成；
- sandbox/VPC、执行身份、Secret、网络、写副作用、timeout、成本预算；
- 可导出的 test-plan/evidence schema、签名、hash、长时存储或可重放性；
- Meta 产品/API/GA/Preview 状态。

## 三、五个抽象的逐项判定

下表中的“直接事实”指厂商在一手材料中明确表述；“跨系统设计推断”指由厂商事实与平台规范一起支持、但不是厂商公开架构；“删除/仅建议”指当前没有足够证据成为本页技术事实。

| 抽象 | 判定 | 可保留的事实依据 | 必须降级或删除的内容 | 页面可安全表述 |
|---|---|---|---|---|
| `Change Envelope` | **跨系统设计推断** | AWS 接收 PR/MR、branch/commit 或 test intent，并关联组织标准、跨仓依赖、访问控制；Meta 接收 Diff 并推断 intent/risk；GitLab external status response 必须指向 MR 当前 HEAD SHA。 | 不是 AWS/Meta 对象名；“依赖、Policy、运行上下文”不存在两家通用公开 schema。 | “以 revision/Diff 为锚，按变更引入验证上下文”——标注为本页抽象。 |
| `Verification Planner` | **AWS 直接事实 + Meta 机制事实；名称为抽象** | AWS 明说生成 change-specific test plan；Meta 明说/论文展示按 Diff intent/risk 生成测试。 | Meta 未公开叫作 planner 或输出可保存计划；AWS 未公开 plan fields。 | “AWS 已产品化生成 test plan；Meta 证明按 Diff 推导验证的机制”。 |
| `Execution Broker` | **部分 AWS 直接事实；整体为跨系统设计推断** | AWS managed verification environment、VPC private connection、ENI、runtime IAM role、网络 allowlist，以及对 target application 的真实请求。 | Meta 没公开同类 broker；“白名单、身份、环境、超时”不是两个案例共同实现；`timeout`无证据。 | “执行需要独立的环境/网络/身份控制；AWS 是已公开例子。” |
| `Evidence Evaluator` | **两家都有事实；名称为抽象** | AWS 有 report、severity、execution journal、GitHub Check Run summary；Meta 有 rule/LLM assessor 和 engineer-facing signal。 | 不存在跨厂商的“Evidence Evaluator”组件或一致 confidence schema；assessor 不是保证真阳性的 policy oracle。 | “执行结果须经报告或评估后再被人/平台消费。” |
| `Gate Adapter` | **跨系统设计推断，具有平台机制支持** | AWS 明确连接 GitHub required check / GitLab approval rule；GitHub required status checks、GitLab external status checks 都能按当前 revision 写状态并可配置阻断。 | 不是 AWS/Meta 产品组件；Meta 无该集成证据；不可声称 AWS release testing 自动带有它。 | “由宿主 SCM/CI 的独立规则把特定状态转成 merge gate。” |

## 四、三个“合同”的逐项判定

| 合同 | 判定 | 事实能支撑到哪里 | 不可当成事实的字段或保证 | 处理建议 |
|---|---|---|---|---|
| `VerificationPlan` | **仅企业设计建议** | AWS 会生成 test plan；AWS workflow 可配置 test-profile ID 和 optional test-requirement；Meta 的生成逻辑使用 Diff intent/risk。 | `revision`、风险假设、步骤、目标环境、预算、oracle、timeout 的统一字段集合；plan hash；先审后跑的 gate。 | 页面可将“计划”画为概念性输出，但不画 JSON/schema，不列字段，不称 AWS/Meta contract。 |
| `EvidenceBundle` | **仅企业设计建议；其组成部分有事实** | AWS report / execution journal / Check Run summary；Meta assessor 结果和工程师报告；GitHub Checks 可承载 logs、test results、annotations、links。 | “统一 Bundle”、`plan hash`、运行身份、provenance、剩余风险、签名/可验证证明均非 AWS/Meta 公开 schema。 | 可画“结果与评估”一格，并只列已披露输出（findings、journal、Check Run、assessor signal）。若要引入结构化 bundle，显式标为企业控制面对象。 |
| `GatePolicy` | **部分平台/产品事实；作为统一合同仍是企业设计建议** | AWS 客户可指定 blocking change 与 warning；AWS code review 有 required-check/approval-rule 接法；GitHub/GitLab 有独立保护/状态配置。 | required evidence、阈值、置信度、fail-open/fail-closed、例外审批、以及“Agent 无权修改 policy”的完整通用 policy schema。 | 将 GatePolicy 改名为“宿主平台保护规则 / 企业 Gate 配置（建议）”；不要把它放进 AWS/Meta 原生流程框。 |

## 五、把“证据 → Gate”补足为平台事实

### GitHub：可将 Check/Status 转为合并条件，但要绑定可信来源

- [GitHub Status checks](https://docs.github.com/en/enterprise-cloud@latest/pull-requests/reference/status-checks) 说明外部系统、GitHub Apps 和 GitHub Actions 可创建 checks/statuses；若它们是 protected branch 的 required status checks，必须通过才能 merge。Checks 可带 logs、test results、annotations 和链接。
- [GitHub branch protection API](https://docs.github.com/en/rest/branches/branch-protection) 说明 required contexts 可配置，且可以要求由特定 GitHub App 提供该状态。
- [GitHub Check Runs API](https://docs.github.com/en/rest/checks/runs) 说明创建 check run 需要 GitHub App 和 `checks: write` 权限。

**能证明：**一个外部/CI 产出的、绑定 commit 的 Check Run，可被 GitHub 的独立 branch-protection 规则要求成功，从而成为 merge gate。

**不能证明：**任何名为“AI testing”的 Check 都应该被要求；Check Run 的详细报告天然可信、完整或不可篡改；它能直接阻止部署或发布。GitHub 文档还提示 skipped 状态可按 success 处理，平台配置与 workflow trigger 必须单独审计。

### GitLab：异步 external status check 可选配为阻断

- [GitLab external status checks](https://docs.gitlab.com/user/project/merge_requests/status_checks/) 说明 MR 事件可 webhook 给外部服务；外部服务运行过程后用 REST API 按当前 HEAD SHA 回写 `pending` / `passed` / `failed`，旧 SHA 会被拒绝。
- 同一文档明确：**默认即使 external check 失败也可 merge**；只有启用 `Status checks must succeed` 后，才会阻断 merge。

**能证明：**外部 Agent/runner 完全可以作为异步验证服务，把某个 revision 的结果写回 GitLab，并由项目配置决定其是否为 merge gate。

**不能证明：**AWS Release Testing 已用此 API；Meta JiTTesting 已用此 API；任何 `failed` 都等价于正确的工程风险判断。

### SLSA：只适合补“制品 provenance 的验证”边界，不是测试证据规范

[SLSA Build: Verifying artifacts v1.2](https://slsa.dev/spec/v1.2/verifying-artifacts) 是 Approved 规范。它要求 verifier 检查 builder identity、provenance envelope 签名以及 buildType/externalParameters 是否符合预期。

**能证明：**若页面需要把“验证的是哪个制品、由哪个 builder 产生”纳入发布门禁，可用 SLSA provenance 作为独立的制品身份/来源证据，并由 verifier 对预配置 expectation 判断。

**不能证明：**SLSA 不定义 Agent test plan、test result、LLM assessor 置信度或“测试通过”的语义；不应把它画成 Meta/AWS 动态验证的 EvidenceBundle 格式。若页面空间有限，应省略 SLSA，避免把供应链 provenance 与运行时测试信号混淆。

## 六、可安全进入技术洞察页的最小参考方案

### 页面主张（可证据化）

> Agent 的新能力不是直接“批准发布”，而是把**当前变更**转成一次性验证，并把执行结果交给 SCM/CI 的既有 Gate 机制；AWS 已公开产品化了前半段并提供 GitHub 接点，Meta 已公开验证了按 Diff 生成与评估信号，但 Meta 的 Gate 仍保留在人类/宿主流程侧。

### 最小技术链

```text
revision / Diff
  → 变更感知验证生成
  → 受控执行与结果评估
  → SCM/CI 状态回写
  → 宿主平台保护规则决定 merge
```

| 节点 | 页面上的对象名 | 已有事实锚点 | 必须标出的控制边界 |
|---|---|---|---|
| 1 | `Revision / Diff` | AWS 的 PR/MR、branch、commit / Meta 的 Diff；GitLab status response 绑定 current HEAD SHA。 | 不是通用 Change Envelope schema。 |
| 2 | `Change-aware verification generation` | AWS 的 change-specific test plan；Meta 的 Diff intent/risk → catching test generation。 | “生成”不等于批准，也不等于覆盖所有风险。 |
| 3 | `Execution + assessment` | AWS target application 上真实执行、可有 VPC/IAM/allowlist；Meta diff/parent 对照和 rule/LLM assessor。 | Meta 的运行环境控制未公开；AWS 的真实请求可有写副作用。 |
| 4 | `Result/status` | AWS report、execution journal、GitHub Check Run pass/fail；Meta engineer-facing assessed signal。 | 不把它泛化为签名 EvidenceBundle。 |
| 5 | `Host gate` | AWS readiness review 的 required check / approval-rule 接法；GitHub required checks；GitLab “status checks must succeed”。 | “Host gate”是本页抽象；Meta 没有公开接入；release gate 也不等于 merge gate。 |

### 两个案例在此链中的精确落点

- **AWS（产品事实）：**`Revision/PR → change-specific test plan → deployed target 上执行 → GitHub Check Run`，以及独立的 `readiness report → GitHub required check / GitLab approval rule`。前一条是动态验证结果回写，后一条才是 AWS 明文说明的 merge governance 接法；图中必须用两条分支表示，不能合并成“release testing 默认阻断”。
- **Meta（研究/内部生产事实）：**`Diff → intent/risk-aware test generation → Diff/parent 对照运行 → rule/LLM assessor → 工程师确认`。链路止于工程师确认；图中不应在 Meta 箭头后画 GitHub/GitLab Gate 图标。

### 页面中必须删去或改写的原文

| 原表述 | 审计结果 | 安全替换 |
|---|---|---|
| “VerificationPlan 包含 revision、风险假设、验证步骤、目标环境、预算、Oracle、timeout。” | 无 AWS/Meta 公开字段依据。 | “AWS 公开称生成 test plan；Meta 公开展示按 Diff intent/risk 生成验证。” |
| “EvidenceBundle 记录 plan hash、运行身份、失败分类、provenance、剩余风险。” | 仅部分概念可从 AWS report/journal、Meta assessor 推出；统一 schema 无依据。 | “AWS 产出报告/执行 journal/Check Run；Meta 产出 assessor 过滤后的工程师信号。” |
| “Agent 无权修改 GatePolicy。” | 合理的企业治理建议，但不是对象事实。 | “Gate 应由宿主 SCM/CI 的独立保护规则配置；是否限制 Agent 权限需要企业另行设计。” |
| “AWS/Meta 都证明 Gate Adapter。” | Meta 无 Gate 集成证据；AWS 的 readiness review 才有直接 Gate 接法，release testing 仅明确写回 GitHub Check Run。 | “AWS readiness review 证明 Gate 接点；AWS release testing 和 Meta 分别证明动态验证结果/候选信号。” |
| “SLSA 为 Agent 测试证据背书。” | 不成立。 | “SLSA 只在需要制品 provenance 时补充，不替代 test-result semantics。” |

## 七、适合汇报的技术启发（分析，不冒充厂商事实）

1. **把“验证生成”与“Gate 决策”拆开。**事实基础是 AWS 的测试生成/Check Run、Meta 的生成/assessor/人工确认，以及 GitHub/GitLab 的独立保护规则；“拆开”是由这些事实得出的架构判断。
2. **动态验证的最小可治理单位应锚定 revision。**GitHub required check 和 GitLab external status check 都围绕具体 commit/HEAD 生效；这给“Agent 输出不是泛化建议，而是针对 revision 的一次运行”提供了平台层支点。
3. **先用现有宿主 Gate 承接状态，再讨论新协议。**在现有证据下，最小可行路径是把 Agent/runner 的结果写成 Check/Status 并由 branch/MR 规则决定是否阻断；公开材料不足以支持先自创一套跨厂商 EvidenceBundle/GatePolicy 协议。

这些是面向企业的技术洞察，不是 AWS 或 Meta 的承诺。若后续要给出可实施的企业 schema、权限模型、fail-open/fail-closed 或例外流程，应另起设计专题并以所选 SCM/CI、身份系统和发布平台的官方规范为依据。

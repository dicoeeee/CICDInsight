---
contract_version: 2
status: superseded-by-object-change
superseded_by: "./07-dagger-develocity.md"
slide: 7
slide_title: "CI 的执行计划，正在从静态配置变成程序输出"
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-02"

evidence:
  deep_dive: "[[50_deepdives/dagger/README|Dagger Deep Dive]]；[[50_deepdives/buildkite/README|Buildkite Deep Dive]]；[[50_deepdives/dagger-buildkite-decision-guide|分层选型指南]]"
  presentation_ready: true
  blocker_state: clear

claim:
  bounded_claim: "传统 CI 预先写好步骤再执行；Dagger 根据类型化函数与内容依赖计算 Job 内的执行图，Buildkite 根据代码变化、资源策略与测试历史生成跨 Job 的任务图。两者共同表明，CI 的执行计划开始成为程序根据上下文计算出的结果。"
  unsupported_stronger_claim: "不把两者写成同层直接竞品、必然组合方案或已被共同客户普遍验证的新一代 CI 标准，也不宣称任一产品必然带来普遍提速或降本。"
  evidence_boundary: "页面只使用截至 2026-08-02 已完成事实审计的产品机制；共同趋势与组合边界属于基于机制的架构推断。公开证据未确认可署名的 Dagger 与 Buildkite 共同客户。"

regions:
  - name: "共同变化"
    question: "两者共同把传统 CI 的什么能力软件化？"
    contribution: "建立‘CI 从固定配置走向编译系统’的共同趋势，但不抹平产品层级。"
    budget: "1 条短引导语；不超过 2 行"
  - name: "Dagger：任务内部"
    question: "Dagger 编程的对象是什么，独特能力从哪里产生？"
    contribution: "用 Module / Function、内容 DAG、缓存与本地/CI 复用证明 Job 内执行语义可编程。"
    budget: "1 条主链 + 3 个机制点 + 1 条适用条件"
  - name: "Buildkite：任务之间"
    question: "Buildkite 编程的对象是什么，独特能力从哪里产生？"
    contribution: "用 Dynamic Pipeline、Queue / Stack / Agent 与 Test Engine 证明跨 Job 图、算力图和反馈图可编程。"
    budget: "1 条主链 + 3 个机制点 + 1 条适用条件"
  - name: "企业启示"
    question: "企业应该优先借鉴哪一层能力？"
    contribution: "按主要瓶颈选择层级，并给出组合时的 DAG 与缓存所有权边界。"
    budget: "2 条选择规则 + 1 条组合原则"

object_policy:
  diagrams: png_allowed
  tables: native_editable
  editable_objects: full_style_editable
  grouping: optional
  grouping_targets: []

component_reuse:
  title: style-reuse
  table: unconstrained
  bottom_insight: style-reuse

production:
  native_review_output: "/private/tmp/cicdinsight-dagger-buildkite-20260802/dagger-buildkite-insight.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/dagger-buildkite-programmable-ci-insight.pptx"
  group_manifest: ""

visual_baseline:
  review_file: ""
  review_sha256: ""
  rendered_preview: ""

locks:
  semantic:
    state: proposed
    approval_quote: ""
  structural:
    state: open
    approval_quote: ""
  visual:
    state: open
    approval_quote: ""

active_decisions: []

decision_history: "./history/07-dagger-buildkite-decisions.md"

source_map:
  - claim_id: C1
    deep_dive_ref: "[[50_deepdives/dagger/20_evidence-map#核心 Claim|DAG-C01、DAG-C02、DAG-C06]]"
    primary_source: "https://docs.dagger.io/；https://docs.dagger.io/reference/api/internals/；https://docs.dagger.io/features/caching/"
    state: verified
    as_of: "2026-08-02"
  - claim_id: C2
    deep_dive_ref: "[[50_deepdives/dagger/20_evidence-map#核心 Claim|DAG-C03、DAG-C04、DAG-C12]]"
    primary_source: "https://docs.dagger.io/getting-started/ci-integrations/github-actions/；https://docs.dagger.io/features/reusability/"
    state: verified
    as_of: "2026-08-02"
  - claim_id: C3
    deep_dive_ref: "[[50_deepdives/buildkite/20_evidence-map#核心 Claim|BK-C05、BK-C06、BK-C07]]"
    primary_source: "https://buildkite.com/docs/pipelines/configure/dynamic-pipelines；https://buildkite.com/docs/pipelines/configure/dynamic-pipelines/sdk"
    state: verified
    as_of: "2026-08-02"
  - claim_id: C4
    deep_dive_ref: "[[50_deepdives/buildkite/20_evidence-map#核心 Claim|BK-C04、BK-C09、BK-C10]]"
    primary_source: "https://buildkite.com/docs/agent/queues；https://buildkite.com/docs/apis/agent-api/stacks；https://buildkite.com/docs/agent/buildkite-hosted"
    state: verified
    as_of: "2026-08-02"
  - claim_id: C5
    deep_dive_ref: "[[50_deepdives/buildkite/20_evidence-map#核心 Claim|BK-C11、BK-C12、BK-C13]]"
    primary_source: "https://buildkite.com/docs/pipelines/configure/tests；https://buildkite.com/docs/pipelines/speed-up-builds-with-bktec"
    state: verified
    as_of: "2026-08-02"
  - claim_id: C6
    deep_dive_ref: "[[50_deepdives/dagger-buildkite-decision-guide#二、最深层共同趋势：CI 从配置系统变成编译系统|共同趋势]]"
    primary_source: "Derived from C1-C5"
    state: corroborated-inference
    as_of: "2026-08-02"
  - claim_id: C7
    deep_dive_ref: "[[50_deepdives/dagger-buildkite-decision-guide#四、选择决策|分层采用判断]]"
    primary_source: "Derived from C1-C5 and audited adoption boundaries"
    state: conditional-recommendation
    as_of: "2026-08-02"
---

# CI 的执行计划，正在从静态配置变成程序输出

## 页面沟通任务

读完这一页，受众应理解：Dagger 与 Buildkite 不是同一层的 CI 替代品；两者分别把交付动作与 CI 调度基础设施变成可编程对象。企业可以据此判断自己的主要瓶颈在哪一层，以及是否值得组合。

## 语义提案

### 标题

**CI 的执行计划，正在从静态配置变成程序输出**

### 页面唯一主张

**传统 CI 预先写好步骤再执行；Dagger 根据类型化函数与内容依赖计算 Job 内的执行图，Buildkite 根据代码变化、资源策略与测试历史生成跨 Job 的任务图。两者共同表明，CI 的执行计划开始成为程序根据上下文计算出的结果。**

### 共同趋势

可借鉴的不是“用代码代替 YAML”，而是让执行系统在每次运行时重新计算三个问题：**怎么执行、执行什么、在哪里执行。** Dagger 主要计算“Job 内怎么执行和复用”，Buildkite 主要计算“本次执行什么 Job、在哪里运行”。

### 企业启示

- **交付逻辑产品化：** 将跨仓重复的 Build、Test、Package 和集成环境组合沉淀为类型化、可组合、可缓存的交付函数。
- **执行计划上下文化：** 将代码变化、资源条件和测试历史转成当次运行的任务图与算力路由，而不是维护不断膨胀的固定矩阵。
- **借鉴能力，不预设采购组合：** 本页证明两层能力的技术价值，不把“同时采用 Dagger 与 Buildkite”作为默认结论。

### 与前页的差异

GitHub Agentic Workflows 主要编程 Agent Task 与仓库事件上下文，Harness 主要编排 Pipeline / Stage / Step 和更宽交付平台；本页解释的是这些上层任务真正落地时，Job 内执行语义与跨 Job 算力调度如何成为可编程底座。

## Claim Ledger

| Claim | 证据事实 | Speaker explanation | 观众可见文案 | 类型 / 状态 | 区域 |
|---|---|---|---|---|---|
| C1 | Dagger Engine 把类型化 Function 调用形成惰性内容 DAG，并提供 Layer、Volume 与 Function Cache | 代码表达的是数据与对象依赖；Engine 在求值时并行执行可并行节点，并按内容与调用身份复用结果。它编程的是 Job 内部的执行语义。 | `Module / Function → Content DAG → Container / Artifact` | 事实 / verified | Dagger |
| C2 | Dagger Module / Function 可由本地 CLI 与现有 CI 调用 | 可移植的是交付逻辑和类型化接口，不是身份、网络、架构与全部运行环境。 | `一次定义，本地与 CI 复用` | 事实 / verified | Dagger |
| C3 | Buildkite Dynamic Pipeline 可在 Build 运行时生成并上传新 Step，SDK 提供类型化生成 API | Generator 把代码变化、依赖和矩阵策略编译为“本次真正需要的 Job”，而非仅在静态图中跳过步骤。 | `Change / Policy → Runtime Job Graph` | 事实 / verified | Buildkite |
| C4 | Queue、Agent 与 Stack 将 Job 映射到 Hosted / Self-hosted、VM / Kubernetes、macOS / Linux / GPU / 私网资源 | Buildkite 编程的是跨 Job 的调度与算力组合；它保留 SaaS Control Plane，同时把执行资源作为可设计的能力队列。 | `Job → Queue → Agent / Stack → Compute` | 事实 / verified | Buildkite |
| C5 | Test Engine 与 `bktec` 使用测试历史进行分片和状态管理 | 测试不再只是末端结果，而会影响下一轮执行图与分片，因此形成反馈图。 | `Test History ↺ Next Split / State` | 事实 / verified | Buildkite |
| C6 | C1-C5 分别体现内容图与任务/算力/反馈图的程序化 | 两者共同趋势不是“代码替代 YAML”本身，而是执行计划从预先写好的静态配置变成系统依据内容和上下文计算出的结果。 | `执行计划：静态配置 → 程序输出` | 推断 / corroborated | 共同变化 |
| C7 | 两者主要解决不同层级问题，且公开资料未确认共同客户 | 是否组合取决于两层痛点是否同时显著，以及图、分片、缓存所有权能否明确；不能默认全量叠加。 | `先选主要瓶颈，再决定是否叠加` | 建议 / conditional | 企业启示 |

## 洞察质量检查

- **Agent counterfactual：repaired。** 页面不把 Agent 当主角；保留的 Agent-specific 负载是：Agent 产生更动态、多样的交付任务时，需要可调用的交付函数与按上下文生成的执行资源图作为确定性执行底座。
- **Company differentiation：pass。** Dagger 的 Function / Content DAG 与 Buildkite 的 Dynamic Pipeline / Queue / Test Feedback 不能互换公司名。
- **Region uniqueness：pass。** 共同变化回答趋势，左右两区回答各自编程对象，页底回答企业选择与组合边界。

## 事实与推断边界

- “CI 从配置系统走向编译系统”是基于两组机制的架构推断，不是厂商原话或市场份额结论。
- 页面不使用客户提速、降本数字，因为公开案例无法把结果单因素归因给 Dagger 或 Buildkite。
- 页面不以 Dagger Cloud Checks / Cloud Engines 或 Buildkite Agentic CI 作为主叙事，避免将 Early Access / Preview 邻接能力误写成成熟核心。
- 公开证据只支持技术分层与组合可行性，不支持“同一客户已成功联合部署”这一事实主张。

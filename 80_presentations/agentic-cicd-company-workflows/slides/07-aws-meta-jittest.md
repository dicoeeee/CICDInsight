---
contract_version: 3
status: aws-region-scope-reassessment
slide: 7
slide_title: ""
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-03"

evidence:
  deep_dive: "[[50_deepdives/runtime-generated-verification-gates/README|Agent 生成验证的运行时 Gate Deep Dive]]"
  presentation_ready: true
  blocker_state: clear

insight_alignment:
  route: bottom_up
  state: user_selected
  selection_quote: "bottom-up"

claim:
  bounded_claim: ""
  unsupported_stronger_claim: ""
  evidence_boundary: ""

regions:
  - name: "左栏｜AWS DevOps Agent Release Management"
    task: "解释 AWS Release Management 如何在发布前后形成两条独立验证链，并分别接入宿主 Gate。"
    viewpoint: "AWS 的可借鉴点不是单个 Action，而是把发布前的就绪判断与部署后的运行时验证做成两种状态接口；宿主 SCM/CI 再决定各自的 Gate 强度。"
    content_evidence:
      - RVG-C03
      - RVG-C04
      - RVG-C11
      - RVG-C13
    representation:
      form: "上部技术架构图 + 下部文字解释"
      source: user_explicit
      state: user_approved
      approval_quote: "分为上下两块，上边可以画图，下面文字解释"
      detail_asset: "../assets/aws-release-testing-async-check-example.png"
    budget: "上部 2 条发布前后验证链 + 1 个 Release Testing 接入放大点；下部 2 句机制解释；不展开测试类型、安全、权限、网络或审批"
  - name: "右栏｜Meta JiTTesting"
    question: "Meta 如何针对 Diff 临时生成、对照运行并筛选验证信号，最终交给谁决策？"
    contribution: "完整呈现 Diff-aware 生成、Diff/parent 对照、assessor 与工程师确认终点。"
    budget: "1 条五步链 + 1 个边界条；可见正文约 70—90 个中文字"
  - name: "底部｜共同技术洞察"
    question: "生成式验证从报告变成 Gate，缺少的控制动作是什么？"
    contribution: "指出结果绑定 revision、Check/Status 绑定 required rule；区分 AWS Gate 接点与 Meta 人工终点。"
    budget: "1 条主句 + 1 条技术公式；不超过 45 个中文字"

object_policy:
  diagrams: open
  tables: open
  editable_objects: open
  grouping: open
  grouping_targets: []

component_reuse:
  title: open
  table: open
  bottom_insight: open

production:
  native_review_output: "/private/tmp/cicdinsight-aws-meta-runtime-verification-gates-20260803/aws-meta-runtime-verification-gates.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/aws-meta-runtime-verification-gate-insight.pptx"
  group_manifest: ""

visual_baseline:
  review_file: ""
  review_sha256: ""
  rendered_preview: ""

locks:
  semantic:
    state: open
    approval_quote: ""
  structural:
    state: open
    approval_quote: ""
  visual:
    state: open
    approval_quote: ""

active_decisions:
  - id: O1
    gate: object-selection
    proposal: "本页使用 AWS DevOps Agent Release Management 与 Meta JiTTesting 两个案例，并以 CI/CD 专家关心的运行时规划、执行、证据与 Gate 转换为观察框架。"
    state: user_approved
    approval_quote: "需要写AWS DevOps Agent Release Management和Meta JiTTesting的ppt；页面应该洞察的是：Agent 生成的验证，如何在流水线运行时被规划、执行并转化为 Gate这类cicd专家应该关注的而不是mutant、测试用例质量、UI/API 测试类型这类测试专家关注的"
  - id: R1
    gate: insight-alignment-route
    proposal: "在证据就绪后，由用户在 top_down 与 bottom_up 中明确选择页面分析顺序。"
    state: user_approved
    approval_quote: "bottom-up"
  - id: S0
    gate: structural-direction
    proposal: "页面主体采用左右双栏：左侧讲 AWS，右侧讲 Meta。"
    state: user_approved
    approval_quote: "我的建议是整体ppt分为左右两块，左边讲aws 右边讲meta"
  - id: B1
    gate: semantic-region-content
    proposal: "AWS 左栏主图提升到 Release Management 的两条独立证据链：PR/MR 更新触发 Release Readiness，输出就绪状态并可接 Required Check / MR approval；部署完成触发 Release Testing，生成变更特定测试计划、在目标应用执行并回写 GitHub Check Run。当前三层 Action 示例降为 Release Testing 分支的局部放大图，不再承担 AWS 左栏主观点。下部结论为：Agent 不是增加一个测试步骤，而是在发布前后生产不同状态；宿主 SCM/CI 分别决定这些状态是否成为 Gate。"
    state: proposed
    approval_quote: ""
  - id: S1
    gate: region-representation
    proposal: "AWS 左栏内部纵向分为上下两块：上部画流程图，下部用文字解释机制。"
    state: user_approved
    approval_quote: "分为上下两块，上边可以画图，下面文字解释"
  - id: LANG1
    gate: copy-language
    proposal: "页面可见文案默认使用中文；产品正式名称、平台原生对象或无法准确替代的术语首次出现时保留英文括注，之后使用中文简称。"
    state: user_approved
    approval_quote: "尽量都输出中文，除非这个英文单词比较普遍"

decision_history: "./history/07-aws-meta-jittest-decisions.md"

source_map:
  - claim_id: RVG-C03
    deep_dive_ref: "[[50_deepdives/runtime-generated-verification-gates/20_evidence-map#核心 Claim|AWS change-specific release testing]]"
    primary_source: "https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: RVG-C04
    deep_dive_ref: "[[50_deepdives/runtime-generated-verification-gates/20_evidence-map#核心 Claim|AWS CI/CD integration]]"
    primary_source: "https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: RVG-C07
    deep_dive_ref: "[[50_deepdives/runtime-generated-verification-gates/20_evidence-map#核心 Claim|Meta JiTTesting workflow]]"
    primary_source: "https://arxiv.org/pdf/2601.22832；https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/"
    state: verified
    as_of: "2026-08-03"
  - claim_id: RVG-C11
    deep_dive_ref: "[[50_deepdives/runtime-generated-verification-gates/50_findings#F3：Check Run 只有被设为 Required 才成为 Gate|Native status to host gate]]"
    primary_source: "https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html；https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html；https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches"
    state: verified-synthesis
    as_of: "2026-08-03"
  - claim_id: RVG-C13
    deep_dive_ref: "[[50_deepdives/runtime-generated-verification-gates/50_findings#F8：AWS 暴露了一个可复用的异步外部检查模式|AWS asynchronous external check pattern]]"
    primary_source: "https://github.com/aws-actions/devops-agent-qa/blob/main/README.md；https://github.com/aws-actions/devops-agent-qa/blob/main/action.yml；https://github.com/aws-actions/devops-agent-qa/blob/main/src/index.ts"
    state: verified
    as_of: "2026-08-03"
  - claim_id: TECH-AUDIT
    deep_dive_ref: "[[00_sources/research-agent-generated-verification-gate-technical-basis-2026-08-03|生成验证到 Gate 技术事实审计]]"
    primary_source: "AWS User Guide；Meta JiTTest paper；GitHub Docs；GitLab Docs"
    state: verified
    as_of: "2026-08-03"
---

# AWS + Meta 运行时生成验证 Gate 候选洞察页

> [!info] 当前门
> 用户要求所有技术方案必须有事实依据。新增研究与主 Agent 复核已经完成；无公开 schema 的五段组件名和三个合同已撤回。当前提出的最小技术链只使用 AWS、Meta、GitHub、GitLab 一手来源可证明的节点，语义、结构和视觉锁仍为 `open`。

## 已批准的范围

- 对象：AWS DevOps Agent Release Management、Meta JiTTesting；
- 受众：CTO、研发效能负责人、平台工程负责人；
- 关注：Agent 生成的验证如何在流水线运行时被规划、执行、形成证据并转化为 Gate；
- 非主轴：mutant、测试用例质量、UI/API 测试类型。

## 已选择的页面分析路径

- **Bottom-up：**先对齐两个案例的事实、机制、差异与未证实边界，再综合页面主张与企业启示。

用户原话：`bottom-up`。

## 支撑技术方案的 Claim ledger

| ID | Evidence fact | Speaker explanation | 技术方案锚点 | 类型 / 状态 | 来源 |
|---|---|---|---|---|---|
| C1 | AWS Release Management 可从 PR/MR、branch/commit 或 CI/CD stage 触发 | 生成式验证以具体 revision/变更为运行入口 | **Revision trigger** | Fact / verified | RVG-C02、C04 |
| C2 | AWS 根据代码变更或 test intent 生成 test plan；在 managed verification environment 或客户提供的已部署目标执行 | AWS 直接证明“变更感知生成 → 受控执行” | **Generate + execute** | Fact / verified | RVG-C03；技术审计 §一 |
| C3 | AWS release testing 产出 GitHub Check Run；readiness review 产出 report/journal，并可配 required status check 或 GitLab approval rule | AWS 直接证明两条不同的结果与 Gate 接入路径，不能合并成默认阻断 | **Two result paths** | Fact / verified | RVG-C11 |
| C4 | Meta JiTTesting 根据选定 Diff 的 intent/risk 生成临时 catching tests | Meta 证明验证内容可以按 Diff 在运行时生成 | **Diff-aware generation** | Fact / verified | RVG-C07、C08 |
| C5 | Meta 以 Diff/parent 行为对照运行，并用 rule/LLM assessor 压缩信号 | Meta 证明结果在进入工程处置前还需评估 | **Execute + assess** | Fact / verified | RVG-C07；技术审计 §二 |
| C6 | Meta 的公开链路止于工程师确认，没有 Required Check 或自动阻断证据 | Meta 不能被画入 SCM 原生 Gate | **Human endpoint / boundary** | Fact + boundary / verified | RVG-C06、C09 |

## 一手事实增量审计（完成）

### AWS 已直接证明的技术链

1. **输入与触发：**PR/MR 新建或更新可自动触发 release readiness；release testing 还可由部署后的 GitHub Actions stage 触发。
2. **计划：**Agent 根据代码变更或用户提供的 test intent 生成 test plan；AGENTS.md 可指定测试命令、passing build 标准和要验证的范围。
3. **运行环境：**release readiness 的 automated verification 在 AWS-managed verification environment 中 clone/build/run/test，默认内部网络不可达，可配置 VPC、Runtime role 和受限网络；release testing 则对客户提供的已部署应用发起真实请求。
4. **结果：**readiness report 包含 `BLOCK / Proceed with Caution / Safe to Release`、风险、建议、严重度与 execution journal；release testing 结果可回写 GitHub Check Run 的 pass/fail 与详细摘要。
5. **Gate：**release readiness 文档明确说明 GitHub 可配置 required status check、GitLab 可配置 MR approval rule；release testing 只明确回写 GitHub Check Run。GitHub 官方分支保护要求被设为 required 的 checks 成功后才能 merge。

### Meta 已直接证明的技术链

1. PR/Diff 进入 JiTTesting 工作流；
2. 系统推断变更意图并生成针对本次变更的临时验证；
3. 运行生成的验证；
4. rule-based 与 LLM-based assessors 压缩信号；
5. 工程师收到候选报告。

当前仍没有一手证据证明 Meta 把结果写入 Required Check、覆盖所有 PR 或形成自动阻断 Gate。

### 已撤回或降级的名称

- `Change Envelope`、`Execution Broker`、`EvidenceBundle`、`GatePolicy` 不是 AWS/Meta 公布的组件或 schema；不得作为厂商事实。
- `VerificationPlan` 只能降级为对 AWS 官方 `test plan` 的通用描述，不能虚构字段结构。
- `Gate Adapter` 可以改写为已存在的具体接口：GitHub Check Run → required status check，或 GitLab review findings → MR approval rule。
- 若最终保留跨系统参考架构，将明确标注为“基于一手事实的企业设计推断”，并只使用来源已证明的节点。

## 左栏内容提案｜AWS DevOps Agent 发布管理

### 上部图草案

![[80_presentations/agentic-cicd-company-workflows/assets/aws-release-testing-async-check-example.png]]

### 左栏观点

> **把 Agent 验证实现为与当前提交版本（commit SHA）绑定的异步外部检查，而不是把 Agent 塞进 Runner。**

### 上部｜三层技术架构图

**流水线适配器**

`部署完成 → 封装作业上下文〔当前提交版本（commit SHA）/ PR + 测试档案 + 本次测试要求〕 → Webhook 提交`

↓

**Agent 执行面**

`生成变更特定测试计划 → 对目标应用执行`

↓

**SCM 状态面**

`GitHub Check Run：进行中 → 通过 / 失败 ──设为必需检查──→ Gate`

### 下部｜文字解释

- **配置与计划解耦：**稳定测试档案固定执行目标，本次测试要求注入变更关注点；Pipeline YAML 不需要预写完整测试计划。
- **Runner 与 Agent 解耦：**Action 只提交作业；Agent Space 在外部执行，并通过 Check Run 状态机把结果关联回当前提交版本。

是否成为阻断式 Gate，仍由宿主仓库把对应检查配置为 Required Check；这不是 Agent 自己的结论。

页面不展开测试类型，也不讨论安全、权限、网络或审批治理。

## 后续板块

Meta、共同洞察和标题继续暂停；当前只重新确认增强后的 B1。

## 当前语义质量检查

- **Agent counterfactual：pass。** 删除 Agent 后，“按本次变更生成原先未预写的验证计划/工件”这一增量消失，不退化为普通固定测试执行。
- **Company differentiation：pass。** AWS 的差异是产品化 Pipeline 入口与客户环境执行；Meta 的差异是内部 Diff-aware 临时验证与 assessor 候选反馈，互换公司名后不成立。
- **Comparison gate：pass。** 两者只按相同控制问题比较，不作为同层产品或成熟度排名；AWS 证明产品化阶段，Meta 证明动态证据生产。
- **Region uniqueness：pending。** 当前只重新确认 AWS；Meta 尚未按同一四问框架展开。
- **Title coverage：pending。** Bottom-up 路径尚未进入主张与标题综合阶段。

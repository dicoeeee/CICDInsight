---
contract_version: 3
status: blocked-by-deep-dive
slide: 7
slide_title: "从运行固定测试，到为每次变更生成测试"
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-03"

evidence:
  deep_dive: "[[50_deepdives/dagger/README|Dagger Deep Dive]]；Meta JiTTesting Deep Dive 待建立；[[00_sources/research-large-company-llm-build-test-practices-2026-08-03|大公司 LLM CI 候选研究]]；[[00_sources/research-llm-native-cicd-platform-candidates-2026-08-03|LLM 原生平台横评]]"
  presentation_ready: false
  blocker_state: blocked-by-deep-dive

insight_alignment:
  route: ""
  state: open
  selection_quote: ""

claim:
  bounded_claim: "候选主张：Agentic Development 提高代码变更速度后，CI 的验证对象开始从长期维护的固定测试集，扩展为随 Diff 即时生成的临时验证。Dagger 将 LLM 可调用的 Function 与 Env 放入可组合执行图；Meta JiTTest 根据 Diff 意图与风险生成 mutants 和 catching tests，并在 parent / Diff 对照中运行。"
  unsupported_stronger_claim: "不宣称 Dagger 与 Meta 存在集成或共同客户；不宣称 JiTTest 替代完整回归测试、覆盖每个 Diff、自动批准代码或普遍达到 Meta 的内部效果；不把 Dagger Experimental LLM 能力写成稳定 GA。"
  evidence_boundary: "Dagger 已有 presentation-ready Deep Dive；Meta 当前只有截至 2026-08-03 的一手论文、工程博客与候选研究，正式页面仍需独立 Deep Dive、产品/研究状态核对和逐主张事实审计。"

regions:
  - name: "Agentic Development 带来的验证缺口"
    question: "为什么更快生成代码，会使固定测试资产跟不上？"
    contribution: "说明变更速度、未知回归与测试维护负担共同推动按 Diff 生成验证。"
    budget: "候选；1 条因果链，不超过 3 行"
  - name: "Dagger：交付动作成为 LLM 可调用的执行图"
    question: "LLM 如何在与构建测试一致的环境中调用交付能力？"
    contribution: "用 LLM、Env、Function、容器与 Trace 解释运行时执行能力。"
    budget: "候选；1 条机制链 + 3 个对象"
  - name: "Meta：验证工件随 Diff 即时生成"
    question: "固定测试没有覆盖某次变更的新风险时，怎样生成新的失败信号？"
    contribution: "用 intent/risk → mutant → catching test → parent/Diff 对照 → assessor 解释 JiTTesting。"
    budget: "候选；1 条六步链 + 2 个内部效果证据"
  - name: "企业启示"
    question: "面向 Agentic Development，CI 平台应该新增什么能力？"
    contribution: "把交付动作与验证假设都提升为可在运行期生成、执行和评估的对象。"
    budget: "候选；1 条底部主张"

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
  native_review_output: "/private/tmp/cicdinsight-dagger-meta-jittest-20260803/dagger-meta-jittest.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/dagger-meta-jittest-insight.pptx"
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
    proposal: "Slide 07 保留 Dagger，并以 Meta JiTTesting 替换 Develocity。Meta 的 LLM 在 Diff 提交验证中推断意图与风险、生成 mutants 和临时 catching tests；它既满足明确 LLM 参与，也形成与现有 GitHub、Harness、CI 自愈、基础设施、CLI/MCP 页面不同的‘验证工件即时生成’能力。"
    state: proposed
    approval_quote: ""

decision_history: "./history/07-dagger-meta-jittest-decisions.md"

source_map:
  - claim_id: D1
    deep_dive_ref: "[[50_deepdives/dagger/20_evidence-map|Dagger Evidence Map]]"
    primary_source: "https://docs.dagger.io/features/llm/；https://dagger.io/blog/llm/"
    state: verified
    as_of: "2026-08-03"
  - claim_id: M1
    deep_dive_ref: "[[00_sources/research-large-company-llm-build-test-practices-2026-08-03#第一选择：Meta JiTTest|候选研究]]"
    primary_source: "https://arxiv.org/pdf/2601.22832；https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/"
    state: research-input-needs-deep-dive
    as_of: "2026-08-03"
  - claim_id: M2
    deep_dive_ref: "[[00_sources/research-large-company-llm-build-test-practices-2026-08-03#第一选择：Meta JiTTest|候选研究]]"
    primary_source: "https://arxiv.org/abs/2501.12862；https://engineering.fb.com/2025/02/05/security/revolutionizing-software-testing-llm-powered-bug-catchers-meta-ach/"
    state: research-input-needs-deep-dive
    as_of: "2026-08-03"
---

# Dagger + Meta JiTTesting 候选洞察页

> [!warning] 对象待批准，页面仍被 Deep Dive 阻塞
>
> 本页只记录当前最强对象与主张候选。Meta JiTTesting 尚未建立完整 Deep Dive，页面分析路径、语义、结构和视觉均未锁定。

## 对象选择提案

建议保留 Dagger，并以 **Meta Just-in-Time Catching Test Generation** 作为第二对象。

它满足用户新增的硬条件：LLM 不是外围聊天界面，而是参与推断 Diff 风险、生成 mutants、生成测试以及评估失败信号；同时测试资产本身也为 Agentic Development 改变——针对一次变更临时生成、在 CI 中运行，不要求长期进入代码库维护。

## 候选页面主张

> **Agentic Development 正在把 CI 从“运行固定测试”，推向“为每次变更生成测试”。**

Dagger 与 Meta 分别说明两个运行时对象正在出现：

1. **交付动作运行时化：** Dagger 把 Function、Env 与容器能力提供给 LLM，并在执行图中运行；
2. **验证假设运行时化：** Meta 从 Diff 意图和风险生成 mutants 与 catching tests；
3. **验证信号可计算：** 测试必须在 parent 上通过、在 Diff 或 mutant 上失败，再经过规则与 LLM assessor 压缩为候选。

## 当前阻塞

Meta JiTTesting 尚无 `presentation_ready: true` 的 Deep Dive。对象获批后，应先完成 Charter、Question Tree、Evidence Map、Findings、Report 与逐主张事实审计；之后再向用户提供 top-down / bottom-up 两条页面分析路径，不能直接进入版式或 PPTX。

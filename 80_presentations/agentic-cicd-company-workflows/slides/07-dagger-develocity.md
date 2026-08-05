---
contract_version: 2
status: superseded-by-llm-requirement
superseded_by: "./07-dagger-meta-jittest.md"
slide: 7
slide_title: "CI 提速的核心，正在从增加算力转向减少无效计算"
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-03"

evidence:
  deep_dive: "[[50_deepdives/dagger/README|Dagger Deep Dive]]；Develocity Deep Dive 待建立；[[00_sources/research-dagger-alternative-platform-candidates-2026-08-03|候选横评研究]]"
  presentation_ready: false
  blocker_state: blocked-by-deep-dive

claim:
  bounded_claim: "候选主张：Dagger 通过类型化 Function 与内容 DAG 复用未变化的执行结果；Develocity Predictive Test Selection 根据变更指纹与测试历史选择当前最相关的测试，Test Distribution 再并行适合分布式执行的测试。两者共同指向先减少重复计算和无关验证，再扩展剩余工作。"
  unsupported_stronger_claim: "不宣称 Dagger 与 Develocity 存在原生集成或共同客户，不宣称 PTS 零漏测或可替代 remaining/full-suite，也不把厂商案例效果外推为普遍 ROI。"
  evidence_boundary: "Dagger 已完成 presentation-ready Deep Dive；Develocity 当前只有截至 2026-08-03 的候选研究和主 Agent 一手资料复核，正式页面仍需独立 Deep Dive 与逐主张事实审计。"

regions:
  - name: "效率问题重定义"
    question: "为什么增加 Runner 不是 CI 提速的第一答案？"
    contribution: "把优化对象从资源扩容转向减少不必执行的工作量。"
    budget: "候选；1 条主张，不超过 2 行"
  - name: "Dagger：消除重复计算"
    question: "哪些交付动作可以直接复用而无需重算？"
    contribution: "用 Function、内容 DAG 与缓存解释确定性复用。"
    budget: "候选；1 条机制链 + 2 个能力点"
  - name: "Develocity：消除无关验证"
    question: "哪些测试对本次变更最可能提供有效反馈？"
    contribution: "用变更指纹、测试历史、selection profile、relevant / remaining tests 和 Test Distribution 解释验证选择。"
    budget: "候选；1 条反馈链 + 3 个能力点"
  - name: "企业启示"
    question: "平台应该以什么顺序优化 CI？"
    contribution: "先复用不变结果，再跳过低相关测试，最后并行必须执行的验证。"
    budget: "候选；3 步优化顺序"

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
  native_review_output: "/private/tmp/cicdinsight-dagger-develocity-20260803/dagger-develocity-insight.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/dagger-develocity-ci-efficiency-insight.pptx"
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

active_decisions: []

decision_history: "./history/07-dagger-develocity-decisions.md"

source_map:
  - claim_id: D1
    deep_dive_ref: "[[50_deepdives/dagger/20_evidence-map#核心 Claim|DAG-C01、DAG-C02、DAG-C06]]"
    primary_source: "https://docs.dagger.io/core-concepts/functions/；https://docs.dagger.io/reference/api/internals/；https://docs.dagger.io/features/caching/"
    state: verified
    as_of: "2026-08-03"
  - claim_id: V1
    deep_dive_ref: "[[00_sources/research-dagger-alternative-platform-candidates-2026-08-03#首选详解：Dagger + Develocity（PTS）|候选研究]]"
    primary_source: "https://docs.develocity.ai/predictive-test-selection/"
    state: research-input-needs-deep-dive
    as_of: "2026-08-03"
  - claim_id: V2
    deep_dive_ref: "[[00_sources/research-dagger-alternative-platform-candidates-2026-08-03#首选详解：Dagger + Develocity（PTS）|候选研究]]"
    primary_source: "https://docs.develocity.ai/current/administration/build-acceleration/test-distribution/"
    state: research-input-needs-deep-dive
    as_of: "2026-08-03"
  - claim_id: V3
    deep_dive_ref: "[[00_sources/research-dagger-alternative-platform-candidates-2026-08-03#来源台账与状态审计|状态审计]]"
    primary_source: "https://develocity.ai/releases/；https://develocity.ai/blog/gradle-technologies-is-now-develocity/"
    state: research-input-needs-deep-dive
    as_of: "2026-08-03"
---

# Dagger + Develocity 候选洞察页

> [!warning] 已被新的 LLM 对象要求取代
>
> 用户明确要求第二对象必须有 LLM 参与，或体现平台为 LLM 时代发生的结构改变。PTS 属于传统 ML，原候选不再控制 Slide 07；新的候选见 [[07-dagger-meta-jittest]]。

## 对象选择提案

**建议用 Develocity 的 Predictive Test Selection 与 Test Distribution 替换 Buildkite。**

Develocity 的技术辨识度在于，它不只是让测试跑得更并行，而是先用当前变更的输入指纹与历史测试结果判断“哪些测试最可能提供有效反馈”，再将被选中的测试按历史耗时分布到执行资源。Dagger 则从另一端减少工作量：相同 Function 输入和内容依赖无需重复执行。

## 候选页面主张

> **CI 提速的核心，正在从增加算力转向减少无效计算：Dagger 复用未变化的执行结果，Develocity 跳过低相关测试并并行剩余验证。**

这不是两款产品的集成方案，而是两个可借鉴的效率机制：

1. **重复工作不重算：** 用类型化输入、内容依赖与缓存识别相同计算；
2. **低价值验证不先跑：** 用变更—测试历史选择当前最相关的测试；
3. **必须完成的验证再并行：** 用历史耗时和执行能力分布剩余测试，并在后续阶段补齐 remaining / full suite。

## 与现有页面的差异

- GitHub Agentic Workflows：解释 Agent 任务如何进入仓库事件与 Actions；
- Harness CI：解释 Agent 与 Pipeline / Stage / Step 如何协作；
- CI 自愈：解释失败后的诊断、修复和复验；
- 基础设施：解释 Agent 长程任务推动上下文、动态运行、快速验证与供应链事实演进；
- CLI / MCP：解释 Agent 如何发现和调用工具；
- **本候选页：解释大规模验证如何通过“复用 + 预测选择 + 分布执行”主动减少工作量。**

## 当前阻塞

Develocity 尚无 `presentation_ready: true` 的 Deep Dive。对象获批后，需要补齐 Charter、Question Tree、Evidence Map、Findings、Report 与逐主张事实审计，才能提出正式语义锁或进入页面结构。

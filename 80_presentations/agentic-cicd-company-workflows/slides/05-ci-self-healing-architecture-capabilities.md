---
contract_version: 7
status: content-design-proposed
slide_title: "（无可见标题）"
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-04"

evidence:
  deep_dive: "../../../50_deepdives/cicd-self-healing/README.md"
  presentation_ready: true
  blocker_state: clear

text_policy:
  primary_language: zh-CN
  chinese_font: "Microsoft YaHei"
  standalone_english_font: "Arial"
  mixed_text_font: "Microsoft YaHei"
  english_scope: key_terms_and_proper_names
  copy_mode: synthesized_not_translated

typography:
  sizing_rule: maximize_within_layout
  body_typical_pt: [11, 10]
  dense_body_rule: flexible_with_readability
  diagram_primary_min_pt: 8

color_policy:
  normal_text: black
  important_text: red
  gray_scope: structural_only
  emphasis_rule: selective

insight_alignment:
  route: bottom_up
  state: user_selected
  selection_quote: "同意"

layout_frame:
  state: user_approved
  proposal: "16:9 单页；左侧流程图约 55%，右侧能力描述约 45%；无页面标题和底部洞察区；右侧三层六张纵向能力卡；左右比例可在不改变阅读逻辑的范围内微调。"
  approval_quote: "均同意"

claim:
  bounded_claim: "CI 自愈的架构课题不是单次生成补丁，而是把故障对象、分类路由、诊断修复、独立验证、受控写回与反馈治理组成有边界的控制系统。"
  unsupported_stronger_claim: "该架构已经构成适用于所有 CI/CD 场景的无人值守自愈标准。"
  evidence_boundary: "覆盖 CI 构建、测试、PR 修复及受限 Runbook；不外推为自动合并、自动部署或生产开放式修复。"

regions:
  - name: "左侧架构流程"
    task: "CI 失败如何在有限权限与有限循环内完成分诊、处理、验证和退出？"
    viewpoint: "有状态控制面先分诊，再将问题送入快环、慢环或安全退出，并由独立 Oracle 决定是否允许写回。"
    content_evidence: ["C1", "C2", "C3"]
    representation:
      form: "native editable diagram"
      source: user_explicit
      state: user_approved
      approval_quote: "均同意"
    budget: "现有左侧对象、标签与连接线全部保留"
  - name: "右侧架构课题"
    task: "要让左侧受控闭环成立，平台需要建设哪些互不重叠的架构能力？"
    viewpoint: "六项课题依次解决故障对象、路径决策、根因诊断、有界修复、验证写回和规则治理。"
    content_evidence: ["C1", "C2", "C3", "C4", "C5", "C6"]
    representation:
      form: "six native editable capability cards"
      source: user_explicit
      state: user_approved
      approval_quote: "均同意"
    budget: "三层六卡；每项标题一行、正文原则上两行；不改变卡片几何"

object_policy:
  diagrams: native_editable
  tables: native_editable
  editable_objects: full_style_editable
  grouping: optional
  grouping_targets: []

component_reuse:
  title: unconstrained
  table: unconstrained
  bottom_insight: unconstrained
  source_deck: "../../../outputs/ci-self-healing-architecture-capabilities-editable.pptx"
  left_architecture: exact-component
  right_cards: exact-component
  visible_copy: rewrite-approved-targets-only

production:
  native_review_output: "/private/tmp/ci-self-healing-rightcopy.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/ci-self-healing-architecture-capabilities-editable.pptx"
  group_manifest: ""

visual_baseline:
  source_file: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/ci-self-healing-architecture-capabilities-editable.pptx"
  source_sha256: "5c2c2ea9c1090615c23668824cf902f0d160431d9c60eaf96936e1eb5cda23ec"
  source_mtime: "2026-07-30 21:06:18 +0800"
  review_file: ""
  review_sha256: ""
  rendered_preview: ""

locks:
  content_design:
    state: proposed
    approval_quote: ""
  visual:
    state: open
    approval_quote: ""

active_decisions:
  - id: "RC-1"
    gate: content_design
    proposal: "按中文优先、主体—机制—结果—边界和两行预算重写右侧六项文案。"
    state: proposed
    approval_quote: ""

decision_history: ""

source_map:
  - claim_id: "C1"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/15_architecture-design.md#十一、层级能力视图（课题-能力-边界）"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "C2"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/90_report.md#四、实践架构：先分类，再选择闭环"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "C3"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/50_findings.md#F4：失败分类器比修复-Agent-更值得优先建设"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "C4"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/15_architecture-design.md#四、失败分类路由（第②层展开）"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "C5"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/15_architecture-design.md#九、独立-Oracle-验收清单"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "C6"
    deep_dive_ref: "../../../50_deepdives/cicd-self-healing/15_architecture-design.md#十一、层级能力视图（课题-能力-边界）"
    state: verified
    as_of: "2026-07-15"
---

# CI 自愈架构能力页

现有 PPTX 是视觉与版式权威。本轮只修改右侧六项可见文案；任何标题、卡片数量、区域比例、对象类型或左侧流程变化都需要重新打开相应门禁。

## 右侧内容设计提案

### 控制决策层

1. **统一故障事件与状态机**
   - 读者问题：如何把一次失败变成可去重、可恢复、可审计的控制对象？
   - 区域判断：状态机必须围绕绑定运行、提交、环境与证据的统一故障事件推进。
   - 可见描述：把运行、提交、环境与证据绑定为统一故障事件；状态机管理去重、租约、预算和可恢复流转。
   - 预算：标题一行；正文原则上两行。

2. **分层分类与策略路由**
   - 读者问题：系统依据什么选择快环、慢环或安全退出？
   - 区域判断：结构化信号和产品规则优先，语义分析只补足复杂或未知问题，分类结果用于路由而不是替代根因判断。
   - 可见描述：结构化信号和产品规则先行，语言、组件规则可插拔；语义分析补足复杂问题，并输出路径或 Unknown。
   - 预算：标题一行；正文原则上两行。

### 诊断执行层

3. **诊断上下文与根因收敛**
   - 读者问题：不同问题路径如何获得相应证据和诊断方法？
   - 区域判断：路由结果负责装配上下文和选择专业诊断单元，复现与假设实验负责收敛根因。
   - 可见描述：按路由装配代码、环境与历史证据，调用专业诊断 Skill/Tool；以复现和假设实验收敛根因。
   - 预算：标题一行；正文原则上两行。

4. **有界修复与隔离执行**
   - 读者问题：根因明确后，怎样限制修复对象、权限和循环次数？
   - 区域判断：只有证据达标才生成最小修复，执行范围同时受动作、身份、作用域和预算约束。
   - 可见描述：根因证据达标后才生成最小修复；白名单、作用域锁、临时身份和轮次预算限制修改对象与尝试次数。
   - 预算：标题一行；正文原则上两行。

### 可信验证层

5. **独立验证与写回门禁**
   - 读者问题：谁证明修复有效，什么条件下才允许写回？
   - 区域判断：成功判据由修复器之外的 Oracle 执行，有效凭证必须绑定被验证的同一对象。
   - 可见描述：独立 Oracle 复跑原任务和必过门禁；只有绑定同一提交、制品与环境的有效凭证才允许写回。
   - 预算：标题一行；正文原则上两行。

6. **运行评估与规则治理**
   - 读者问题：运行经验如何转化为可靠规则，并避免过期知识继续生效？
   - 区域判断：只把经过持续评估和审核的稳定模式固化，指标漂移或模式失效时必须降级。
   - 可见描述：统计分类准确率、修复通过率、复发与回退；稳定模式经审核固化为规则或 Runbook，失效即降级。
   - 预算：标题一行；正文原则上两行。

## 右侧综合判断

六项课题依次回答“控制什么、如何分流、怎样诊断、如何修复、凭什么写回、如何持续治理”，共同支撑左侧有状态、自限权、可验证的 CI 自愈闭环。

---
contract_version: 7
status: native-review-proposed
slide_title: ""
audience: "CTO、研发效能负责人和平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-05"

evidence:
  deep_dive: "50_deepdives/cicd-self-healing"
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
  route: top_down
  state: user_selected
  selection_quote: "top-down"

layout_frame:
  state: proposed
  proposal: "适配 16:9 PPT 右侧上半区的 1200×640 PNG；三层控制流：上层为 PR CI、恢复重跑与结束状态，中层为 fix-ci 上下文、Flaky 分流和可修范围判定，下层为候选修复与 Task 复验、自动提交条件和 PR 分支写回；Flaky 恢复重跑和写回后的 PR CI 复验分别形成可见反馈环；未授权、未验证或条件不足汇入人工/停止出口；无内置标题。"
  approval_quote: ""

claim:
  bounded_claim: ""
  unsupported_stronger_claim: "CI 自愈能够无边界地自动处理所有失败"
  evidence_boundary: "仅复用 presentation-ready CI/CD 自愈专题及指定最终版 PPTX 中已核验的 Nx 流程，不新增外部产品事实。"

regions:
  - name: "PPT 上半区流程图"
    task: "让读者不依赖产品术语，也能沿顺序看懂 Nx CI 自愈如何从失败信息进入受控提交。"
    viewpoint: "流程的关键不是直接生成代码，而是分流、限定范围、验证条件和受控提交。"
    content_evidence: ["E2", "E3"]
    representation:
      form: "deterministic PNG process diagram"
      source: user_explicit
      state: user_approved
      approval_quote: "基于这个ppt上版本部分的流程图，进行修改生成一张新的png，我要放到一页ppt的😔部分"
    budget: "六个编号机制节点；一个 PR CI 外层检查节点；一个 Flaky 恢复重跑节点；一个结束状态；一个人工/停止出口；两个反馈环。"

object_policy:
  diagrams: png_allowed
  tables: not_applicable
  editable_objects: reproducible_source_retained
  grouping: not_applicable
  grouping_targets: []

component_reuse:
  title: unconstrained
  process_diagram: style-reuse
  bottom_insight: not_applicable

production:
  native_review_output: "/private/tmp/ci-self-healing-process-draft.C5ofQC/ci-self-healing-nx-loop.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/ci-self-healing-process-upper-simplified.png"
  group_manifest: ""

visual_baseline:
  review_file: "/private/tmp/ci-self-healing-process-draft.C5ofQC/ci-self-healing-nx-loop.review.pptx"
  review_sha256: "141b02acb045acfbf07c40123fbcc2bd461d1d088ec3468c03b42b5f62542f5f"
  rendered_preview: "/private/tmp/ci-self-healing-process-draft.C5ofQC/ci-self-healing-nx-loop-draft.png"

locks:
  content_design:
    state: proposed
    approval_quote: ""
  visual:
    state: proposed
    approval_quote: ""

active_decisions:
  - id: "R1"
    gate: route
    proposal: "在 top-down 与 bottom-up 两条内容对齐路径中选择一条"
    state: user_approved
    approval_quote: "top-down"
  - id: "L1"
    gate: layout_frame
    proposal: "1200×640 三层控制流；上层 PR CI 与重跑/结束，中层上下文与分流/范围，下层修复复验与提交；两个反馈环和一个人工/停止出口"
    state: proposed
    approval_quote: ""
  - id: "CD3"
    gate: content_design
    proposal: "Nx 机制按 PR CI → 失败进入 fix-ci → Flaky 分流或可修范围判断 → 生成 Reasoning/Diff 并重跑原失败 Task → Pattern/Confidence/Verified 三条件或人工审批 → 写回 PR 分支 → PR CI 重新复验呈现；公开证据不支持画成失败后无限自动修复。"
    state: proposed
    approval_quote: ""
  - id: "V2"
    gate: visual
    proposal: "1200×640 三层流程与反馈环；浅色背景、绿色执行节点、黄色判断节点、红色反馈循环和例外强调；微软雅黑中文与混排；放入 PPT 右上区域后主标题约 10 pt、说明约 8 pt。"
    state: proposed
    approval_quote: ""

decision_history: "./history/05-ci-self-healing-process-upper-png-decisions.md"

source_map:
  - claim_id: "E1"
    deep_dive_ref: "50_deepdives/cicd-self-healing/README.md"
    primary_source: "专题判断与 presentation_ready 状态"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "E2"
    deep_dive_ref: "50_deepdives/cicd-self-healing/15_architecture-design.md"
    primary_source: "证据、分类、处理、验证、写回、观察的有界闭环"
    state: verified
    as_of: "2026-07-15"
  - claim_id: "E3"
    deep_dive_ref: "80_presentations/agentic-cicd-company-workflows/slides/05-ci-self-healing.md"
    primary_source: "指定 PPTX 上半区六阶段 Nx Self-Healing 流程的现有页面契约"
    state: verified
    as_of: "2026-07-27"
---

# CI 自愈流程图上半区 PNG

## 当前源图盘点

- 视觉基线：`outputs/ci-self-healing-six-dimension-company-comparison-final.pptx` 第一页上半区。
- 当前主线：失败证据与上下文装配 → Flaky 分流与重跑路由 → 修复资格与作用域约束 → 根因诊断与候选修复 → Auto-apply 三条件门禁 → PR 分支写回、外层检查与回退。
- 当前可读性问题：单个步骤混合动作、英文机制名和控制边界，读者需要先理解术语，才能看懂流程。
- 本次目标：保留事实边界与关键控制点，用更直观的中文动作描述重写，并控制为适配 16:9 页面上半区的横向 PNG。

## 待用户选择的内容对齐路径

- Top-down：先确定读者看完流程图应理解的一句话，再决定保留哪些步骤与边界。
- Bottom-up：先逐框简化现有六阶段文字，再从改写结果归纳整张图的核心信息。

## 当前版式框架提案

```text
[结束] ← [PR CI / Required Checks] ← [Flaky 恢复重跑]
             │失败                       ↑
             ↓                           │
[fix-ci 上下文] → [Flaky 分流] → [可修范围判断]
                                        │允许修复
                                        ↓
[候选修复 + Task 复验] → [提交条件/人工审批] → [写回 PR 分支]
          └──────── 未验证/条件不足 ────────→ [人工或停止]
                                        └── 写回后重新运行 PR CI ──┘
```

- 输出比例：`1200 × 640`，适配 16:9 PPT 右侧上半区。
- 主流程：三层控制流，区分外层 PR CI、失败分流、修复与写回。
- 反馈：Flaky 的 Empty Commit 恢复重跑，以及 PR 分支写回后的 Required Checks 复验分别形成可见循环。
- 例外：不在范围、未验证或提交条件不足时，转人工或停止；不画成无限 Agent 自动循环。
- 文本容量：中文动作标题与解释优先，英文机制名降为灰色辅助信息。
- 固定位置：PNG 内不放页面标题、洞察总结或来源脚注；由最终 PPT 页面统一承载。
- 容差：节点宽度、间距和例外出口位置可在 `±8%` 内微调，不改变阅读顺序。

## 已锁定的视觉约束

- 中文及中英混排：Microsoft YaHei；纯英文：Arial。
- 正常信息使用黑色；关键控制点选择性使用红色；灰色只用于结构辅助。
- 主节点标题不低于 10 pt，解释文字不低于 8 pt；在右上区域实际显示尺寸下仍需可读。
- 延续指定最终版 PPTX 的浅色背景、绿色主流程框和红色关键强调，不引入新的装饰色。

## 当前可视化草图

- 机制主线：PR CI → fix-ci 上下文 → Flaky 分流/可修范围 → 候选修复与原 Task 复验 → 三条件或人工审批 → PR 分支写回 → PR CI 外层复验。
- 两个循环：Flaky 的 Empty Commit 恢复重跑；写回新提交后的 Required Checks 复验。
- 边界：任务复验失败、不可修或条件不足时转人工/停止；不声称 Nx 会无限自动迭代修复。
- 草图 PNG：`/private/tmp/ci-self-healing-process-draft.C5ofQC/ci-self-healing-nx-loop-draft.png`。
- 原生评审 PPTX：`/private/tmp/ci-self-healing-process-draft.C5ofQC/ci-self-healing-nx-loop.review.pptx`。
- 自动检查：`1200 × 640`，无画布溢出。

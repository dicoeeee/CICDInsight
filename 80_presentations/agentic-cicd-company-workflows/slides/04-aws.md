---
title: Slide 04 - AWS AgentCore 与 DevOps Agent
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
status: production-complete
slide: 4
contract_version: 7
content_status: user-approved-v4
visual_status: final-pptx-verified
page_genre: technical-insight
audience: CTO、研发效能负责人、平台工程负责人
as_of: "2026-08-04"
slide_title: "AWS AgentCore：把执行轨迹转成质量证据，把历史交互转成可复用记忆"

evidence:
  deep_dive: "[[50_deepdives/amazon-bedrock-agentcore/README|Amazon Bedrock AgentCore Deep Dive]]；[[50_deepdives/aws-devops-agent/README|AWS DevOps Agent Deep Dive]]"
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
  state: user_approved
  proposal: "14% 顶部标题带；70% 中部正文区，整体左 50% / 右 50%；左侧为 AgentCore Evaluations 主机制图与三行评测落地说明；右侧为 AgentCore Memory 到 DevOps Agent Memories 的上下机制；12% 底部洞察启示带；4% 来源与 as_of 页脚。"
  approval_quote: "同意，直接讨论图怎么画，内容怎么写吧；左50%；右50%"

claim:
  bounded_claim: "AgentCore 把历史执行转成两类派生资产：Evaluations 将轨迹转成质量证据，Memory 将历史交互提炼成下一轮可检索的上下文；二者都不替代 CI/CD 的事实系统与发布 Gate。"
  unsupported_stronger_claim: "AgentCore Evaluation 分数或 Memory Record 本身等于确定性事实、业务正确性证明或自动发布 Gate。"
  evidence_boundary: "Online Evaluation 没有 Ground Truth；programmatic trajectory 只核工具名称和顺序；Memory Record 与语义检索结果是派生上下文；AWS 未公开 DevOps Agent Memories 与 AgentCore Memory API 的逐对象映射；外部 Test、制品系统、Policy、SCM Gate、业务 Oracle 与人工审批持有最终权威。"

regions:
  - name: 左侧 AgentCore Evaluations
    task: "AWS AgentCore 如何基于执行轨迹评估 Agent 的回答与执行过程？"
    viewpoint: "三种运行合同复用同一评估契约，把上线前已知场景与上线后生产采样转成可消费的质量证据。"
    content_evidence:
      - S04-E1
      - S04-E2
      - S04-E3
      - S04-E4
      - S04-E5
    representation:
      form: "native editable mechanism diagram"
      source: user_explicit
      state: user_approved
      approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
    budget: "1 张三列机制图、1 条生命周期带、3 行评测落地说明。"
  - name: 右上 AgentCore Memory
    task: "AgentCore Memory 如何把交互记录提炼成可检索的长期记忆？"
    viewpoint: "短期 Event 经 Strategy 异步提炼形成长期 Record，再按 namespace 检索并装配进下一轮 Agent 上下文。"
    content_evidence:
      - S04-M1
      - S04-M2
      - S04-M3
    representation:
      form: "native editable six-step mechanism diagram"
      source: user_explicit
      state: user_approved
      approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
    budget: "4 个 Memory 节点、2 个编排节点、1 条回流、1 条红色汇总与 1 条删除边界。"
  - name: 右下 DevOps Agent Memories
    task: "AWS DevOps Agent 如何把调查经验与团队约定用于后续排障？"
    viewpoint: "近期调查和团队长期指令沉淀为 Agent Space 共享 Memory，后续调查按需读取并重新核验当前状态。"
    content_evidence:
      - S04-M4
      - S04-M5
    representation:
      form: "native editable four-step application flow"
      source: user_explicit
      state: user_approved
      approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
    budget: "1 条平台到场景箭头、4 个应用节点、1 条红色汇总与 1 条证据边界。"
  - name: 底部洞察启示
    task: "Evaluation 与 Memory 出现后，Agent CI/CD 新增了哪些需要治理的对象？"
    viewpoint: "Agent CI/CD 不只管理代码，还要管理“如何评估”与“如何记忆”；评估器与 Memory Strategy 随版本回归，长期 Record 独立管理权限、隔离和删除。"
    content_evidence:
      - S04-E5
      - S04-M4
      - S04-M5
    representation:
      form: "native editable two-line insight band"
      source: user_explicit
      state: user_approved
      approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
    budget: "1 行黑色治理范围、1 行红色工程动作。"

object_policy:
  diagrams: native_editable
  tables: not_used
  editable_objects: full_style_editable
  grouping: optional
  grouping_targets: []

component_reuse:
  title: exact-component
  table: unconstrained
  bottom_insight: exact-component

production:
  native_review_output: "/private/tmp/cicdinsight-aws-agentcore-20260804/aws-agentcore-devops-agent-native-v1.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/aws-agentcore-devops-agent-insight.pptx"
  group_manifest: ""

visual_baseline:
  review_file: "/private/tmp/cicdinsight-aws-agentcore-20260804/aws-agentcore-devops-agent-native-v1.review.pptx"
  review_sha256: "5d23e182f6ddbaf2e6e533c5ee7e03199d7c09afab1343041b0c57c25773a612"
  rendered_preview: "/private/tmp/cicdinsight-aws-agentcore-20260804/canonical-render/slide-1.png"
  rendered_preview_sha256: "88f6a7ce66c5774299e6f5fe157837f9ce0a0d261dbc2d25610cf3b50131d43d"
  canonical_sha256: "5d23e182f6ddbaf2e6e533c5ee7e03199d7c09afab1343041b0c57c25773a612"

full_page_reference:
  state: user_approved
  approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
  png_path: "/Users/zhujiayi/.codex/visualizations/2026/08/03/019fc76e-5ba0-7cb3-8167-468e752a0fa3/aws-agentcore-full-evaluation-memory-devops-agent-v4-title-insight.png"
  png_sha256: "23d2db7e8397ad466896506551ec8fe03c0b959f5ab4c43b2525bd62394439ce"

locks:
  content_design:
    state: user_approved
    approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
  visual:
    state: user_approved
    approval_quote: "将该ppt放到output中，并commit and push"

active_decisions:
  - id: CD-FULL-V4
    gate: content_design
    proposal: "v4 PNG 的全部可见文案、50/50 版式与左右机制关系作为完整区域内容方案。"
    state: user_approved
    approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
  - id: CD-INSIGHT3
    gate: content_design
    proposal: "底部保持两行：Agent CI/CD 不只管理代码，还要管理“如何评估”与“如何记忆”；评估器与 Memory Strategy 随版本回归，长期 Record 独立管理权限、隔离和删除。"
    state: user_approved
    approval_quote: "同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。"
  - id: VS-NATIVE-V1
    gate: visual
    proposal: "全原生 native review V1：沿用 v4 黑灰红视觉、50/50 主体、左右机制密度与底部两行洞察；PowerPoint 渲染预览绑定 SHA-256 5d23e182f6ddbaf2e6e533c5ee7e03199d7c09afab1343041b0c57c25773a612。"
    state: user_approved
    approval_quote: "将该ppt放到output中，并commit and push"

decision_history: "./history/04-aws-agentcore-devops-agent-decisions.md"

source_map:
  - claim_id: S04-E1
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Evaluations 补充 Claim|AC-E02—E04]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations-types.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: S04-E2
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Evaluations 补充 Claim|AC-E02—E05]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/ground-truth-evaluations.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: S04-E3
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Evaluations 补充 Claim|AC-E05、AC-E08]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/ground-truth-evaluations.html ; https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluators.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: S04-E4
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Evaluations 补充 Claim|AC-E11]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_Evaluate.html"
    state: verified
    as_of: "2026-08-03"
  - claim_id: S04-E5
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Evaluations 补充 Claim|AC-E12]]"
    primary_source: "[[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|Evaluations 与 CI/CD 证据研究]]"
    state: verified
    as_of: "2026-08-03"
  - claim_id: S04-M1
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Memory 补充 Claim|AC-M01—M02]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-terminology.html ; https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-strategies.html"
    state: verified
    as_of: "2026-08-04"
  - claim_id: S04-M2
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Memory 补充 Claim|AC-M03]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_RetrieveMemoryRecords.html ; https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-memory.html"
    state: verified
    as_of: "2026-08-04"
  - claim_id: S04-M3
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/20_evidence-map#Memory 补充 Claim|AC-M04]]"
    primary_source: "https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-delete-event.html ; https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-delete-memory-records.html"
    state: verified
    as_of: "2026-08-04"
  - claim_id: S04-M4
    deep_dive_ref: "[[50_deepdives/amazon-bedrock-agentcore/56_memory-cicd-insight#三、给 CI/CD 的三条启发|CI/CD 启发]]"
    primary_source: "[[00_sources/research-amazon-bedrock-agentcore-memory-cicd-2026-08-04|Memory 与 CI/CD 一手研究]]"
    state: verified-as-analysis
    as_of: "2026-08-04"
  - claim_id: S04-M5
    deep_dive_ref: "[[00_sources/research-aws-devops-agent-agentcore-memory-2026-08-04|DevOps Agent Memories 产品化研究]]"
    primary_source: "https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html ; https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/"
    state: verified-with-implementation-gap
    as_of: "2026-08-04"
---


# AWS AgentCore 单页契约（v4 内容已锁定）

> [!info] 当前状态
> AgentCore Deep Dive、Evaluations 与 Memory 补充洞察均已通过受限的 Presentation-ready 门禁。用户已明确批准 v4 PNG 的全部可见文案、50/50 版式与底部两行洞察；当前进入全原生 native review PPTX 制作，视觉锁仍待对渲染结果批准。

## 写作提纲

1. 先确定页面是从行业主张向机制展开，还是从两组证据向上归纳；
2. 再固定单页区域、阅读顺序、比例与容量；
3. 在固定画布内对齐唯一主张、AgentCore Evaluations、AgentCore Memory 与外部发布权边界；
4. 最后生成原生可编辑评审稿，视觉批准后才形成正式 PPTX。

## 证据就绪判断

- [[50_deepdives/amazon-bedrock-agentcore/README|AgentCore Deep Dive]]：`presentation_ready: true`，支持“通用 Agent 生产控制面、行动与质量双闭环、外部业务 Gate 保留最终权威”的受限判断；
- [[50_deepdives/amazon-bedrock-agentcore/56_memory-cicd-insight|AgentCore Memory 补充洞察]]：`presentation_ready: true`，支持“Memory 是 Event → strategy → Record → retrieval / injection 的外部上下文管道，并且不是发布事实源”的受限判断；
- 截止日：`2026-08-04`；阻塞状态：`clear`。

## 当前待决策

- 洞察对齐路径：`top_down / user_selected`，用户原话：`top-down`；
- 页面版式框架：`LF3 / user_approved`，用户原话：`同意，直接讨论图怎么画，内容怎么写吧`；
- 区域内容设计：`user_approved`，用户原话：`同意按 v4 PNG 的全部可见文案与版式制作，底部两行洞察保持不变。`；
- 视觉基线：`proposed / native review 制作中`。

## 页面版式框架提案 LF3

```text
┌──────────────────────────────────────────────────────────────┐
│ 标题（左）＋状态标签（右）                         约 14% │
├────────────────────────────┬─────────────────────────────────┤
│ 左上主图区（左区约 66%）   │                                 │
│ 1 张图：≤6 节点 / 8 关系   │ 右侧详细说明区                  │
│ ─────────────────────────  │ 约 50%，贯穿正文全高           │
│ 左下能力介绍（左区约 34%） │ 最多 3 个内容小节               │
│ 最多 2 节，每节标题＋2 条  │ 每节标题＋3 条短说明     约 70% │
├────────────────────────────┴─────────────────────────────────┤
│ 底部洞察启示：1 条核心洞察＋1 条边界/启示           约 12% │
├──────────────────────────────────────────────────────────────┤
│ 来源与 as_of（右下固定）                              约 4% │
└──────────────────────────────────────────────────────────────┘
```

- 正文整体比例：左 `50%` / 右 `50%`；左区内部上 `66%` / 下 `34%`；
- 阅读顺序：上 → 左上图 → 左下能力 → 右侧详细说明 → 底部洞察；
- 标题最多两行；右上保留状态标签；右区不再分出第三列；
- 后续允许左右宽度 `±5%`、左侧上下比例 `±6%`、整体纵向高度 `±4%` 的内容适配；
- 新增区域、改变阅读顺序或超出比例容差，需要重新批准页面版式框架；
- 用户已明确左上用图、左下做能力介绍、右侧做详细介绍；具体图形结构与可见文案仍在后续区域内容设计中决定。

## 禁止升级的表述

- AgentCore Evaluation 分数与 AWS DevOps Agent 结果已经闭合端到端自主发布；
- Release Management 已 GA 或可跨区域稳定用于强制门禁；
- Online Evaluation 具有 Ground Truth，或 expected response / assertions 都是确定性断言；
- Evaluation、readiness result、Check Run 或 mitigation plan 本身拥有部署与生产恢复授权；
- AWS 已通过独立客户数据证明普遍降低 MTTR、提高缺陷捕获率或 ROI。

## 左侧 50% 机制与三句解释提案 LEFT-S13

### 左侧小标题提案

推荐：**AgentCore Evaluations｜基于执行轨迹评估 Agent 的回答与执行过程**

- “执行轨迹”是评估输入；“回答与执行过程”是被检查的对象；
- 避免把“评估结果”误读为只检查最终业务结果，也没有把 Evaluation 升级成正确性证明或发布门禁；
- 原机制图内的黑色标签建议缩短为“评估机制”，避免与小标题重复。

批准记录：用户回复 `可以，请补充进png中`；本轮只在草图中增加小标题，不改动已确认的机制图与左下三条说明。

更新草图：`/Users/zhujiayi/.codex/visualizations/2026/08/03/019fc76e-5ba0-7cb3-8167-468e752a0fa3/aws-agentcore-50-50-left-evaluation-with-heading.png`；SHA-256：`b3ce6eeb89cb60737f202f5ca6352d2661354d9901f5a2fca03ad6d4bbcafc59`。

### 左侧唯一判断

> **AgentCore Evaluations 用三种运行合同接入不同轨迹来源，再复用同一套评估对象与评估方法，把 Agent 行为转成质量证据；上线前真值回归与上线后生产采样互补，但发布权仍在外部 Gate。**

### 左上图描述什么

左上回答：**AWS AgentCore 如何把不同来源的 Agent 轨迹，通过三种评测运行合同转成质量证据？**

```mermaid
flowchart TB
  subgraph AC["AgentCore Evaluations"]
    direction LR
    MODES["① 三种运行合同<br/>按需：OTel 会话轨迹｜同步｜可带真值<br/>批量：CloudWatch 会话｜异步｜可带真值<br/>在线：生产轨迹采样｜持续｜无真值"]
    CONTRACT["② 评估契约<br/>层级：会话｜轨迹｜工具调用<br/>模型评审：语义 / 质量信号<br/>轨迹规则：工具名称 / 顺序<br/>Lambda：自定义规则 / 外部检查"]
    OUTPUT["③ 对应证据输出<br/>按需：分数｜标签｜解释<br/>批量：聚合均值｜逐会话明细<br/>在线：指标｜结果日志｜低分会话"]
    MODES --> CONTRACT --> OUTPUT
  end
```

图形规则：

- 用一个明确外框标记 **AgentCore Evaluations 产品能力**；
- 三列分别解释运行合同、评估契约与对应输出，并保持按需/批量/在线的纵向映射；
- 评估契约成为中间视觉焦点，突出 AWS 评测层级和三类评估方法；
- 图下用一条生命周期带连接变更前参考真值回归与生产在线采样；低分轨迹回流明确标注为企业侧编排；
- 主图只使用中文，保留 OTel、CloudWatch、Lambda、CI/CD 等不可替代的产品或技术名词。

### 左下能力介绍写什么

左下不复述图，而是补充“怎样配置成可回归方案”和“怎样形成生命周期闭环”。

**01｜在不同阶段取不同的数据**

- 按需和批量用于发布前测试，检查 Agent 的实际执行是否符合预期；在线评测用于上线后监测真实请求。

**02｜先确定检查对象，再选择检查方式**

- 可以评整个会话、一段轨迹或一次工具调用，再选择模型评审、轨迹规则或**自定义代码检查（Lambda）**。

**03｜AgentCore 给结果，外部流程做决定**

- AgentCore 输出分数、标签、解释、聚合结果与低分会话；CI/CD、测试和审批流程据此决定放行或回滚。

### 左侧证据范围

| Claim | 证明内容 | 可见投影 |
|---|---|---|
| S04-E1 | On-demand / Batch / Online 的 trigger、session source、target 与输出不同 | Data Sources、Execution Surfaces、Evidence Outputs |
| S04-E2 | On-demand / Batch 支持 Ground Truth；Online 无 Ground Truth | Evaluator Contract |
| S04-E3 | LLM、trajectory 与 Lambda evaluator 的确定性强度不同 | Evaluator Contract、配置机制 |
| S04-E4 | Evaluation API 输出不含发布授权语义 | `evidence input` 虚线、Consumer |
| S04-E5 | 可回归发布证据需要把场景、trace、evaluation 与外部 Gate 关联 | 生命周期闭环 |

### 当前边界

- 暂不讨论页面标题、右侧详细内容与底部洞察；
- 暂不展示 Dataset Evaluation Preview、Batch 500-session 限制、成本或 evaluator 数量冲突；这些内容不影响左侧主机制，后续只在有容量时进入右侧或演讲备注；
- 左下三行文字与说明面板已获批准，用户原话：`没啥问题，生成吧`；
- 本轮评审图：`/Users/zhujiayi/.codex/visualizations/2026/08/03/019fc76e-5ba0-7cb3-8167-468e752a0fa3/aws-agentcore-50-50-left-evaluation-three-steps.png`，SHA-256：`035a27f61fa23a3be695d4a3e74c954491eb4c1a3b43be9598c99435ea21fe0c`；
- 左上 L-DIAG9 的精确设计仍待通过 PNG 草图评审；右上 R-MEM3 已批准并生成草图，右下三条启发与底部洞察仍保持 open。

## 右侧 50% Memory 机制与 CI/CD 启发提案 R-MEM3

### 右侧回答什么

**AgentCore Memory 如何把历史交互变成下一轮可用的上下文；为什么 CI/CD 仍要重新核对当前事实？**

推荐小标题：

**AgentCore Memory｜把历史交互提炼成下一轮可用的上下文**

### 上半部机制图

```mermaid
flowchart TB
  subgraph MEMORY["AgentCore Memory"]
    direction LR
    EVENT["① 短期 Event<br/>对话消息 / 工具调用记录<br/>actorId +（可选）sessionId<br/>长期提炼仅处理对话消息"]
    STRATEGY["② 托管 Strategy<br/>异步提炼 / 合并<br/>无 Strategy 或 SKIP 不生成 Record<br/>自管 Strategy 由客户管道处理"]
    RECORD["③ 长期 Record<br/>托管 Strategy 新增或更新<br/>namespace 组织与检索过滤<br/>IAM 限定访问"]
    RETRIEVE["④ 分域检索<br/>namespace + query + topK"]
    EVENT --> STRATEGY --> RECORD --> RETRIEVE
  end
  subgraph ORCH["Agent 编排层"]
    direction LR
    CONTEXT["⑤ 装配推理上下文<br/>Harness（启用 Memory）自动<br/>Runtime / Framework 通过集成或 API 接入"]
    RUN["⑥ 新一轮 Agent 执行<br/>推理 / 工具调用"]
    CONTEXT --> RUN
  end
  RETRIEVE -->|返回相关 Record| CONTEXT
  RUN -. "Harness 自动持久化 / 其他用法由集成或 CreateEvent 写入" .-> EVENT
```

机制图只保留两条边界：

- `actorId / sessionId / namespace` 控制事件与长期记录的组织、检索和访问范围；
- **删除 Event ≠ 删除长期 Record**，原始层与派生层要分别治理；
- 自动检索和注入是 Harness 行为，不能泛化为所有 Runtime / framework 的默认能力。

### 下半部三句说明

**01｜记住经验，但重新核对当前状态**

- 可以复用历史诊断线索；测试、制品、审批和部署健康仍从事实系统查询。

**02｜记忆配置也要随 Agent 版本一起测试**

- 抽取规则、namespace、检索参数和保留期变化，都会改变 Agent 看到的上下文。

**03｜专门测试串线、过期和删除**

- 按项目、环境和租户隔离，并分别验证 Event 与 Record 的删除结果。

### 右侧唯一判断

> **Memory 让 Agent 复用历史经验，但派生 Record 与语义检索结果不是当前发布事实；CI/CD 应把记忆策略纳入版本和回归，同时继续从事实系统核验测试、制品、审批与运行状态。**

### 右侧证据范围

| Claim | 证明内容 | 可见投影 |
|---|---|---|
| S04-M1 | Event、strategy 与 Record 的双层结构 | 短期 Event → 提炼 / 合并 → 长期 Record |
| S04-M2 | namespace、topK 与 Harness / 普通 Memory API 的注入边界 | 分域检索 / 上下文注入 |
| S04-M3 | Event 与 Record 分别删除；保留期前向生效 | 红色删除边界 |
| S04-M4 | Memory 是候选上下文；当前发布事实由外部系统核验 | 三条 CI/CD 启发 |

### 当前状态

- `CD-RT-MEM3` 已获批准，用户原话：`同意，请重新生成`；
- 右上机制图草稿：`/Users/zhujiayi/.codex/visualizations/2026/08/03/019fc76e-5ba0-7cb3-8167-468e752a0fa3/aws-agentcore-memory-right-top-r-mem3.png`；SHA-256：`c2a7da7d67f0873f7afddfee9ea2b433bdf164b9e8ff93eb933910936a5266d1`；
- 右下三条 CI/CD 启发仍为 `CD-RB-MEM1 / proposed`；
- 页面精确标题与底部洞察仍保持 open，待左右两侧内容共同锁定后再讨论。

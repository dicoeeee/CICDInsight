---
contract_version: 7
status: layout-frame-v2-proposed
slide_title: ""
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-05"

evidence:
  deep_dive: "[[50_deepdives/aws-devops-agent/README|AWS DevOps Agent Deep Dive]]；[[50_deepdives/github-agentic-workflows/README|GitHub Agentic Workflows Deep Dive]]；[[50_deepdives/llm-era-cicd-infrastructure/README|LLM-era CI/CD Infrastructure Deep Dive]]"
  supplementary: "[[00_sources/research-aws-microsoft-intelligent-cicd-capabilities-2026-08-05|AWS 与 Microsoft 智能化 CI/CD 能力核验]]；[[00_sources/research-aws-intelligent-cicd-expanded-capabilities-2026-08-05|AWS 扩展能力候选池]]；[[00_sources/research-microsoft-intelligent-cicd-expanded-capabilities-2026-08-05|Microsoft 扩展能力候选池]]；[[00_sources/briefs/2026-azure-devops-mcp-open-source|Azure DevOps MCP Source Brief]]"
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
  selection_quote: "bottom-up"

layout_frame:
  state: proposed
  proposal: "16:9 单页；顶部 15% 为横跨全页的标题与一行综合判断占位；中部 78% 为左右双区，左 AWS、右 Microsoft，正文净宽各约 49%，中间 2% 留白或细分隔线。每个公司区顶部固定公司标识，主体分为三个纵向堆叠的能力层；每层最多承载 2 个主功能点，因此每侧最多 6 个主功能点。每个主功能点容量为 1 行功能标签、1 行机制说明和 1 个紧邻的状态/边界短句；层标题与最终功能名称均保持开放。底部 7% 固定为来源、产品状态口径与 as_of。阅读顺序为顶部标题与综合判断，再 AWS 三层自上而下，再 Microsoft 三层自上而下，最后页脚。后续左右宽度可各浮动不超过 2 个百分点，三个能力层高度可各浮动不超过 3 个百分点；不得新增第四层、超过每侧 6 个主功能点、交换左右顺序或改变顶部/页脚职责。"
  approval_quote: ""
  carried_user_constraint: "整体左右两分，左边为aws，右边为微软"
  reopen_reason: "用户要求功能点不一定只留 4 个，超出已批准框架的每侧最多 4 个信息位容量；v2 已将容量提升到每侧最多 6 个主功能点。"

claim:
  bounded_claim: ""
  unsupported_stronger_claim: "AWS 或 Microsoft 已经实现端到端自治 CI/CD，或其中一家在智能化 CI/CD 上整体领先。"
  evidence_boundary: "只比较官方已公开的机制与产品状态；结果性效果均为厂商自述；MCP、Agent、Check Run 和 PR 不等于合并、部署或恢复授权。"

regions:
  - name: "AWS 左侧区域"
    task: ""
    viewpoint: ""
    content_evidence: []
    representation:
      form: ""
      source: proposed
      state: open
      approval_quote: ""
    budget: "开放；等待扩展研究后按新页面框架确定。"
  - name: "Microsoft 右侧区域"
    task: ""
    viewpoint: ""
    content_evidence:
      - MS-1
      - MS-2
    representation:
      form: ""
      source: proposed
      state: open
      approval_quote: ""
    budget: "开放；MS-1 与 MS-2 已确认保留，其余等待替换研究与新页面框架。"

object_policy:
  diagrams: native_editable
  tables: native_editable
  editable_objects: full_style_editable
  grouping: optional
  grouping_targets: []

component_reuse:
  title: style-reuse
  table: unconstrained
  bottom_insight: style-reuse

production:
  native_review_output: "/private/tmp/cicdinsight-aws-microsoft-20260805/aws-microsoft-intelligent-cicd.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/aws-microsoft-intelligent-cicd-capabilities.pptx"
  group_manifest: ""

visual_baseline:
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
  - id: CD2-KEEP-1
    gate: content_design
    proposal: "Microsoft 右侧保留异步编码 Agent。"
    state: user_approved
    approval_quote: "CD2的1，2可以保留"
  - id: CD2-KEEP-2
    gate: content_design
    proposal: "Microsoft 右侧保留 PR 与安全反馈 / 候选修复。"
    state: user_approved
    approval_quote: "CD2的1，2可以保留"

decision_history: "./history/08-aws-microsoft-intelligent-cicd-decisions.md"

source_map: []
---

# AWS 与 Microsoft 智能化 CI/CD 能力对比

## 当前沟通任务

到本页结束时，CTO、研发效能负责人和平台工程负责人应理解：AWS 与 Microsoft 分别把 Agent / AI 放进了哪些交付阶段、这些能力通过什么控制面工作，以及哪些产品状态和外部门禁限制了当前可用边界。

## 证据就绪说明

- AWS 主入口：AWS DevOps Agent Deep Dive，`presentation_ready: true`，截至 2026-08-03；
- Microsoft 主入口：GitHub Agentic Workflows Deep Dive 与 LLM-era CI/CD Infrastructure Deep Dive，均为 `presentation_ready: true`；
- 2026-08-05 已补充核验 Copilot coding agent、Agentic Workflows、Azure DevOps MCP、AWS DevOps Agent GA / Preview 状态；
- 页面不写整体领先、成熟度排名、普遍 ROI 或端到端自治。

## 已选择的对齐路线

- Bottom-up；用户原文：`bottom-up`。

## 已失效并重新打开的页面版式框架

~~~text
┌──────────────────────────────────────────────────────────┐
│ [页面标题占位]                                            │
│ [一行综合判断占位]                                        │
├───────────────────────────┬──────────────────────────────┤
│ AWS                       │ Microsoft                    │
│ [信息位 1]                │ [信息位 1]                   │
│ [信息位 2]                │ [信息位 2]                   │
│ [信息位 3]                │ [信息位 3]                   │
│ [信息位 4]                │ [信息位 4]                   │
├───────────────────────────┴──────────────────────────────┤
│ [来源 / 产品状态说明 / as_of]                            │
└──────────────────────────────────────────────────────────┘
~~~

- 画布：16:9；顶部 16%，中部 76%，页脚 8%；
- 中部：AWS 左、Microsoft 右；净宽各约 48%，中间 4% 留白或细分隔线；
- 容量：每侧最多 4 个等高信息位；单个信息位最多 1 行标签加 2 行说明；
- 阅读：顶部 → AWS 自上而下 → Microsoft 自上而下 → 页脚；
- 容差：左右净宽各可浮动不超过 2 个百分点；槽位高度和间距可微调，但不得新增区域、交换左右、增加第五个信息位或改变顶部与页脚职责。

用户曾批准原文：`同意`。随后用户要求“功能点不一定只留4个，可以多个”，超出每侧最多 4 个信息位的容量，因此该框架已重新打开，等待扩展候选池收敛后提出新版本。

## 已撤回的证据簇提案

### 原 CD1：AWS 左侧

1. 发布就绪审查；
2. 变更驱动的发布测试；
3. 上线后事件调查；
4. 事故回流与预防建议。

选择理由：四项都属于 AWS DevOps Agent 的产品化 CI/CD / DevOps 能力，能够形成“发布前判断 → 上线后调查 → 经验回流”的连续范围；不把通用 AgentCore 或传统 CodePipeline 规则误写为 AI 功能。

### 原 CD2：Microsoft 右侧

1. 异步编码 Agent；
2. PR 与安全反馈 / 候选修复；
3. GitHub Agentic Workflows；
4. Azure DevOps / Azure MCP 工具接入。

选择理由：四项覆盖“代码生成 → 评审与修复 → 仓库自动化 → 存量交付与云资源接入”，同时保留 GitHub 与 Azure DevOps 的不同产品状态和授权边界。

## 当前研究刷新

- 用户反馈原文：`CD1改一下，看看还有其他能力不；CD2的1，2可以保留，34看看能不能换；同时功能点不一定只留4个，可以多个`；
- AWS：重新建立扩展能力候选池，不预设保留原四项；
- Microsoft：保留“异步编码 Agent”和“PR 与安全反馈 / 候选修复”，重新寻找更合适的后续能力；
- 页面容量：候选点可以超过 4 个，但须在研究收敛后重新批准页面版式框架；
- 本阶段不确定标题、整页洞察、表示形式或最终可见文案。

### 扩展候选池结果

- AWS：9 个候选；新增高价值方向包括交付—运行上下文地图、按需 SRE 任务、可调度自定义 SRE Agent、IDE / Transform 内发布反馈以及 MCP / A2A 扩展；
- Microsoft：8 个候选；推荐重点考察 Dependabot 告警驱动 Agent 修复、GitHub Code Quality、Azure Repos Copilot Review、Azure SRE Agent 与 GitHub Copilot for Azure；
- 主 Agent 已复核 Kiro headless CI、AWS Transform continuous modernization、Dependabot Agentic Remediation、GitHub Code Quality 和 Azure SRE Agent 的官方机制与生命周期；
- 上述内容仍是研究候选，不构成区域内容批准。

## 待批准的页面版式框架 v2

~~~text
┌──────────────────────────────────────────────────────────┐
│ [页面标题占位]                                            │
│ [一行综合判断占位]                                        │
├────────────────────────────┬─────────────────────────────┤
│ AWS                        │ Microsoft                   │
│ [能力层 A：最多 2 项]      │ [能力层 A：最多 2 项]       │
│ [能力层 B：最多 2 项]      │ [能力层 B：最多 2 项]       │
│ [能力层 C：最多 2 项]      │ [能力层 C：最多 2 项]       │
├────────────────────────────┴─────────────────────────────┤
│ [来源 / 产品状态口径 / as_of]                            │
└──────────────────────────────────────────────────────────┘
~~~

- 画布：16:9；顶部 15%，正文 78%，页脚 7%；
- 中部：AWS 左、Microsoft 右；净宽各约 49%，中间 2% 间隔；
- 容量：每侧三个能力层，每层最多 2 个主功能点，即每侧最多 6 项；
- 单项容量：1 行功能标签、1 行机制说明、1 个紧邻的状态 / 边界短句；
- 容差：栏宽各可浮动不超过 2 个百分点，层高各可浮动不超过 3 个百分点；不得增加第四层或第七个主功能点。

---
contract_version: 7
status: complete
slide: 7
slide_title: "Agent 自主执行后，不应只交付代码，同时交付运行环境"
audience: "CTO、研发效能负责人、平台工程负责人"
page_genre: technical-insight
as_of: "2026-08-03"

evidence:
  deep_dive: "[[50_deepdives/dagger/README|Dagger Deep Dive]]；[[50_deepdives/container-use/README|Container Use Standalone Deep Dive]]"
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

user_visual_overrides:
  mixed_latin_font: "Arial"
  body_typical_pt: 10
  diagram_primary_min_pt: 9
  approval_quote: "中文字体为微软雅黑，英文字体为Arial；说错了，正文通常在10，图中主要元素字号不得小于9"

insight_alignment:
  route: top_down
  state: user_selected
  selection_quote: "Top-down"

layout_frame:
  state: user_approved
  proposal: "全页上方为单一技术洞察标题；主体采用左右双区，左侧 Dagger、右侧 Container Use，左右宽度近似相等并以单向能力承接关系连接；每侧均使用上方完整技术模型、图下提示条、下方四项能力的结构；底部保留一条整页洞察。标题、主体、底部高度可各微调约 4%，左右栏宽可各微调约 5%。"
  approval_quote: "同意"

claim:
  bounded_claim: "Agent 的多轮工程验证，正在推动交付基础设施从一次性任务，转向可复用的执行能力与可继续的任务环境。Dagger 提供可反复调用、可增量复用的程序化执行图；Container Use 将执行结果归档为可继续和处置的工程环境。"
  unsupported_stronger_claim: "不宣称 Container Use 会保存完整活体运行环境、自动把任意 Agent 会话迁移到 CI、解决全部并行资源与外部副作用冲突，或已经达到企业规模成熟采用。"
  evidence_boundary: "Dagger 的 Function、Operation Graph 与缓存机制由当前官方文档支撑；Container Use 的 Environment 对象和生命周期由当前官方源码与文档支撑，但产品仍处 experimental / early development。"

regions:
  - name: "Dagger 左半区"
    task: "Dagger 为什么适合 Agent 反复执行构建、测试和环境操作？"
    viewpoint: "Dagger 用可调用 Module Functions、显式执行对象和 Operation 级增量复用，为 Agent 多轮工程验证提供程序化执行层。"
    content_evidence:
      - DAG-C01
      - DAG-C02
      - DAG-C03
      - DAG-C06
      - DAG-C12
    representation:
      form: "PowerPoint 原生可编辑技术图：上方完整技术模型，图下多轮工程验证提示条，下方四项核心能力；全部文字、形状和连线可单独编辑。左区红色只标记 Agent 入口、当前 Module Function 调用、缓存复用路径和多轮验证结论，Engine、普通节点、辅助标题与非关键能力使用黑灰色。"
      source: user_explicit
      state: user_approved
      approval_quote: "所有元素可编辑；左半的图中非重要部分不标红色"
      visual_state: user_approved
      visual_review_quote: "同意 Review V4，仅提交并推送 Dagger + Container Use 这一页相关文件"
    budget: "1 张完整技术模型图、1 条多轮工程验证说明、4 项能力卡片。"
  - name: "Container Use 右半区"
    task: "Container Use 如何把一次 Agent 任务变成可查看、可继续、可接受或丢弃的工程环境？"
    viewpoint: "Container Use 以一个 Environment ID 关联 Git branch/worktree/notes 与 Dagger ContainerID：Agent 经 MCP 驱动执行，结果归档为独立 Git 候选环境，工程师再通过 CLI/Git 审阅和处置。"
    content_evidence:
      - CU-C04
      - CU-C05
      - CU-C06
      - CU-C10
      - CU-C11
    representation:
      form: "PowerPoint 原生可编辑双状态架构图：Container Use 协调进程居上，Environment ID 关联 Git state plane 与 Dagger execution state plane，Engineer CLI 独立承担 inspect / merge / apply / delete；图下展示执行结果归档，能力列表按执行、归档、配置和人工处置展开；全部文字、形状和连线可单独编辑。"
      source: user_explicit
      state: user_approved
      approval_quote: "同意，请按照 build-insight-pptx 要求的标题和洞察启示的格式进行最终ppt绘制；所有元素可编辑"
      visual_state: user_approved
      visual_review_quote: "同意 Review V4，仅提交并推送 Dagger + Container Use 这一页相关文件"
    budget: "1 张双状态架构图、1 条执行结果归档说明、4 项能力卡片。"

object_policy:
  diagrams: native_powerpoint_shapes
  tables: none
  editable_objects: all_slide_elements
  grouping: optional
  grouping_targets: []

component_reuse:
  title: style-reuse
  table: unconstrained
  bottom_insight: style-reuse

production:
  native_review_output: "/private/tmp/cicdinsight-dagger-container-use-20260804-v4/dagger-container-use-native-v4.review.pptx"
  canonical_output: "/Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/dagger-container-use-agent-environment-insight.pptx"
  canonical_sha256: "330f636f81992d1efd8d21a924ed3ac6b4ef6cdbfd4e41e32c299077e4c0d1ca"
  final_qa_report: "/private/tmp/cicdinsight-dagger-container-use-20260804-v4/final-qa-final/final-qa-report.json"
  group_manifest: ""

visual_baseline:
  review_file: "/private/tmp/cicdinsight-dagger-container-use-20260804-v4/dagger-container-use-native-v4.review.pptx"
  review_sha256: "330f636f81992d1efd8d21a924ed3ac6b4ef6cdbfd4e41e32c299077e4c0d1ca"
  rendered_preview: "/private/tmp/cicdinsight-dagger-container-use-20260804-v4/dagger-container-use-native-v4.review/slide-1.png"

locks:
  content_design:
    state: user_approved
    approval_quote: "同意，请按照 build-insight-pptx 要求的标题和洞察启示的格式进行最终ppt绘制"
  visual:
    state: user_approved
    approval_quote: "同意 Review V4，仅提交并推送 Dagger + Container Use 这一页相关文件"

active_decisions:
  - id: CD-L-REGION
    gate: content_design
    proposal: "Dagger 左区使用已批准 V5：完整技术模型、多轮工程验证提示条和四项能力卡片，删除 Gradle 对照。"
    state: user_approved
    approval_quote: "同意左侧v5"
  - id: CD-R-OBJECT
    gate: content_design
    proposal: "Container Use 右区以 Environment ID 为绑定点，但不把 branch/worktree、ContainerID、notes 与 environment.json 画成四个平级 runtime 状态；分别放入 Git 持久化面、Dagger 执行状态面与项目配置基线。"
    state: user_approved
    approval_quote: "左上图别和左上的图那么像啊，而且一定要按实际情况画，画完之后检查是否符合实际的技术方案"
  - id: CD-R-LIFECYCLE
    gate: content_design
    proposal: "右区区分 Agent MCP 执行与 Engineer CLI/Git 处置：create/run/config/file 进入环境执行，log/diff/terminal/checkout 与 merge/apply/delete 由工程师检查和决定。"
    state: user_approved
    approval_quote: "左上图别和左上的图那么像啊，而且一定要按实际情况画，画完之后检查是否符合实际的技术方案"
  - id: CD-R-REPRESENTATION
    gate: content_design
    proposal: "右区只沿用整页的上图下列表框架；主图使用 Environment ID + Git / Dagger 双状态面与 Engineer CLI，不复刻左区的 Engine → API → Operation Graph 拓扑。"
    state: user_approved
    approval_quote: "左上图别和左上的图那么像啊，而且一定要按实际情况画，画完之后检查是否符合实际的技术方案"
  - id: VIS-PAGE-01
    gate: visual
    proposal: "Review V4 使用白底、黑色正文与选择性红色焦点，灰色仅用于分隔线、边框和辅助结构：标题按技能样例使用红色，底部洞察启示使用红底白字；右区红色只标记 Environment ID 双状态绑定与执行结果归档，左区红色只标记 Agent 入口、当前 Function 调用、缓存复用路径与多轮验证结论。本页按用户指令覆盖混排默认值：中文 glyph 使用 Microsoft YaHei，英文与数字 glyph 使用 Arial；正文通常使用 10pt，技术图主要元素不低于 9pt。整页不使用 PNG，全部为 PowerPoint 原生可编辑对象。"
    state: user_approved
    approval_quote: "同意 Review V4，仅提交并推送 Dagger + Container Use 这一页相关文件"
  - id: VIS-FONT-02
    gate: visual
    proposal: "中文 glyph 使用 Microsoft YaHei；英文与数字 glyph 使用 Arial，包括中英混排文本中的英文 run。"
    state: user_approved
    approval_quote: "中文字体为微软雅黑，英文字体为Arial"
  - id: VIS-COMPONENT-03
    gate: visual
    proposal: "顶部标题参考技能样例使用红色；底部洞察启示参考技能样例使用红底白字。"
    state: user_approved
    approval_quote: "顶部标题和底部洞察启示参考 build-insight-pptx 的样例，标题使用红色，底部是红底白字"
  - id: VIS-SIZE-04
    gate: visual
    proposal: "在美观前提下尽量放大字号；本页正文通常使用 10pt，技术图主要元素不低于 9pt。"
    state: user_approved
    approval_quote: "说错了，正文通常在10，图中主要元素字号不得小于9"
  - id: VIS-COLOR-05
    gate: visual
    proposal: "红色只用于重要观点；其他内容使用黑色或结构性灰色。"
    state: user_approved
    approval_quote: "这里红色的标准是什么，只有重要的观点才用红色"
  - id: CD-BOTTOM-INSIGHT
    gate: content_design
    proposal: "底部洞察启示使用约 50 字的已批准结论：Agent 的多轮工程验证，正在推动交付基础设施从一次性任务，转向可复用的执行能力与可继续的任务环境。"
    state: user_approved
    approval_quote: "同意，请按照 build-insight-pptx 要求的标题和洞察启示的格式进行最终ppt绘制"
  - id: PROD-ALL-NATIVE
    gate: content_design
    proposal: "整页所有可见元素均使用 PowerPoint 原生文本、形状与连线，不使用左右区域 PNG；所有元素可单独编辑。"
    state: user_approved
    approval_quote: "所有元素可编辑"

decision_history: "./history/07-dagger-container-use-decisions.md"

source_map:
  - claim_id: DAG-LEFT
    deep_dive_ref: "[[50_deepdives/dagger/20_evidence-map#核心 Claim|DAG-C01、DAG-C02、DAG-C03、DAG-C06、DAG-C12]]"
    primary_source: "https://docs.dagger.io/reference/api/internals/；https://docs.dagger.io/extending/function-caching/；https://docs.dagger.io/extending/cache-volumes/；https://docs.dagger.io/core-concepts/functions/"
    state: verified-with-conditional-cache-boundary
    as_of: "2026-08-03"
  - claim_id: CU-RIGHT
    deep_dive_ref: "[[50_deepdives/container-use/20_evidence-map#核心 Claim|CU-C04、CU-C05、CU-C06、CU-C10、CU-C11]]"
    primary_source: "https://github.com/dagger/container-use/blob/main/cmd/container-use/stdio.go；https://github.com/dagger/container-use/blob/main/environment/environment.go；https://github.com/dagger/container-use/blob/main/environment/state.go；https://github.com/dagger/container-use/blob/main/environment/config.go；https://github.com/dagger/container-use/blob/main/repository/repository.go；https://github.com/dagger/container-use/blob/main/repository/git.go"
    state: verified-with-state-boundary
    as_of: "2026-08-03"
---

# Dagger + Container Use 洞察页

## Evidence readiness

- **主要事实源：** [[50_deepdives/container-use/README|Container Use Standalone Deep Dive]]，当前 `presentation_ready: true`，只支撑受限的“候选 Environment 与 CI 交接机制”主张；
- **比较证据：** [[50_deepdives/claude-code-container-use/README|Claude Code × Dagger Container Use Deep Dive]]、[[50_deepdives/dagger/README|Dagger Deep Dive]]、[[00_sources/research-container-use-adoption-endorsement-2026-08-03|公开采用与生态背书审计]]；
- **页面位置：** Slide 07；正式输出目标为 `outputs/dagger-container-use-agent-environment-insight.pptx`；
- **证据时间：** 2026-08-03；
- **阻塞状态：** 事实、路线、页面布局框架、Review V4 视觉基线、canonical 生成与归档 QA 均已通过；最终文件与批准基线 SHA-256 一致。

## 路线门禁 R1

### Top-down

先对齐这页要让管理者相信的行业变化与企业启示，再用 Dagger / Container Use 的机制、证据和边界检验它。适合当前已有较强候选观点、希望快速收敛页面主张的情况。

### Bottom-up

先逐项对齐 `Environment = branch + container + history`、Dagger Function / cache、CI 重放边界和生态成熟度，再从这些证据归纳页面主题。适合希望先挑战技术事实、再决定是否值得形成主张的情况。

Standalone Deep Dive 已完成并通过受限的 `presentation_ready` 审计，路线门禁重新开放。

## 页面布局框架 L1

### 页面类型

- **定位：** 技术洞察页；展示 high-level 技术方案，不下沉到 CLI 命令、源码字段和采用名单；
- **关系：** 左右两侧不是平级产品对比，而是“执行底座 → Agent 环境能力”的承接关系；
- **信息密度：** 每侧最多三个机制对象，中间只保留一条关系箭头，底部只保留一条结论。

### 低保真框架

```text
┌──────────────────────────────────────────────────────────────┐
│ [技术洞察标题]                         [一行高层判断]          │ 16%
├──────────────────────────┬────────┬──────────────────────────┤
│ Dagger                   │        │ Container Use            │
│ 可编程执行底座            │   →    │ Agent 开发环境            │ 66%
│ [Functions]              │ 能力   │ [branch]                 │
│ [内容寻址 DAG / cache]    │ 承接   │ [container state]        │
│ [Engine / container]     │        │ [history + lifecycle]    │
├──────────────────────────────────────────────────────────────┤
│ [技术趋势 / 企业启示：一句结论]                               │ 18%
└──────────────────────────────────────────────────────────────┘
```

### 区域职责与对象策略

1. **顶部标题带（约 16%）：** 只回答“这页要证明什么”，具体标题在语义门禁确定；
2. **左侧 Dagger（主体约 46%）：** 用三层原生机制图回答“Agent 的任务为何能够被程序化定义和复用”；
3. **中央连接带（主体约 8%）：** 用单向箭头说明 Container Use 建立在 Dagger 执行能力之上，不制造横向优劣比较；
4. **右侧 Container Use（主体约 46%）：** 用环境对象与生命周期图回答“Agent 执行后留下什么，以及人如何继续”；
5. **底部洞察带（约 18%）：** 将左右机制收束为一条技术趋势或企业启示，不再堆叠第二套流程；
6. **对象类型：** 核心图形和文字使用 PowerPoint 原生可编辑对象；Logo 可使用小型图片；不使用整页截图、复杂表格或扁平化信息图。

### 容差与重开条件

- 标题、主体、底部高度可各微调约 4%；左右栏宽可各微调约 5%；中央连接带可在 6%—10% 之间调整；
- 文案换行、图标位置和连接线细节属于后续视觉调整；
- 增删区域、交换 Dagger / Container Use 左右顺序、改为上下布局，均需重新打开布局框架门禁。

> L1 页面布局框架已由用户明确批准；S1R4 标题与完整语义包恢复为当前待审提案。

## Top-down 方向修订 S1R4

### 修订原因

用户先追问“和并行有什么关系？”，随后指出“Container Use 将执行现场与历史组合为候选环境”看不懂，最终提出标题“Agent 自主执行后，不应只交付代码，同时交付运行环境”。该标题直接表达交付物变化，成为当前提案。

### 当前候选主题

> **Agent 自主执行后，不应只交付代码，同时交付运行环境。**

解释行：**Container Use 同时保留代码分支、容器状态与命令记录；人可进入原任务继续调试，CI 可按环境配置与 Dagger Function 重新执行。**

### 当前三项含义

1. **留下完整任务：** 每次 Agent 任务都保留代码分支、运行环境和执行记录；多个任务可以同时存在，但并行不是主张前提；
2. **人能直接接手：** 失败后可以查看 log/diff、进入 terminal，并从原任务继续调试或选择 merge/apply/delete；
3. **CI 能按同一定义重跑：** setup/build/test 沉淀为环境配置和 Dagger Function 后，CI 可以复用执行定义与缓存并重新验证。

### 方向边界

- 不把运行中的 service、Host Tunnel、外部数据库状态写成可自动跨 Engine 迁移；
- 不把 Container Use 写成托管 Agent runner fleet；
- 不把 Claude Code / Codex 等客户端兼容写成厂商联合方案；
- 页面突出能力，不以安全、可信或权限治理为主叙事。

## Claim ledger

| Claim ID | Evidence fact | Speaker explanation | Visible slide copy | Type / state | Source | 候选区域 |
|---|---|---|---|---|---|---|
| C1 | Agent 通过 Container Use 的 MCP tools 创建环境、执行命令、修改文件和配置服务 | 操作者不再亲历每一步，单独的代码 diff 无法说明依赖怎么装、命令怎么跑、失败发生在哪里 | `Agent 写代码 + 装依赖 + 跑命令` | inference / corroborated | [[50_deepdives/container-use/20_evidence-map#核心 Claim|CU-C01、CU-C05、CU-C06]] | 驱动变化 |
| C2 | Environment 由 branch、worktree、ContainerID state、notes 和 config 组成 | 这些层共同保留 Agent 的代码、运行环境和执行记录 | `代码分支 + 运行环境 + 执行记录` | fact / verified | CU-C04—C06 | 保留什么 |
| C3 | 非零退出后仍保存新的 Container state，并记录 command、exit code、stdout/stderr | 失败后人仍能查看和进入原任务继续处理 | `log / diff / terminal / continue` | fact / verified | CU-C05、CU-C10 | 人如何接手 |
| C4 | merge、apply、delete、prune 对应接受、定制、丢弃和清理任务 | 人可以决定保留代码、调整后提交或放弃本次尝试 | `merge / apply / delete` | fact / verified | CU-C11 | 人如何处置 |
| C5 | Dagger Function/cache 可在本地和 CI 复用被声明的执行定义；checkpoint 可显式发布当前容器 | CI 能重用的是环境配置和 Dagger Function，不是运行中进程的直接搬迁 | `同一环境配置与 Function → CI 重跑` | inference / verified with boundary | CU-C07、CU-C12、CU-C13 | CI 如何复验 |
| C6 | 项目仍为 experimental / early development，暂无具名企业生产采用证据 | 适合展示设计方向，不足以证明成熟平台标准 | `early development｜方向性机制` | fact / verified with gap | CU-C14、CU-C15 | 成熟度边界 |

## 机制与反例审计

### Agent counterfactual：pass

去掉“Agent 写完代码”后，页面退化为普通开发环境介绍：这里的变化是 Agent 在后台完成代码修改、依赖安装和命令执行，操作者收到结果时没有亲历过程。因此产品价值必须落到“Agent 完成后，人还能接手，CI 还能重跑”。

### Company differentiation：pass

把 Container Use 换成通用 sandbox 后，`local Git remote/worktree + ContainerID state + Git notes + merge/apply/delete + Dagger Function/cache` 这条完整机制不再成立。页面必须显示 Dagger / Container Use 的具体对象和操作，不使用泛化的“容器隔离”。

### Comparison gate：pass with bounded scope

- **比较对象：** 裸代码 diff / branch 与候选 Environment，属于工程交接单元的前后状态；
- **比较维度：** 是否携带执行上下文、历史和生命周期操作；
- **因果关系：** Agent 自主执行使过程上下文与操作者分离，推动交接对象从结果 diff 升级为带现场的候选 Environment；
- **反转条件：** 已有 `worktree + devcontainer/Nix + 稳定 CI + 远程调试/历史` 时，Container Use 的新增价值可能不足。

### 关键边界

- ContainerID 是 Dagger 状态引用，不是无限期、跨 Engine、跨架构的 VM snapshot；
- service 配置可重建，不等于运行中的 service、Host Tunnel、数据库内存态无缝恢复；
- Container Use 不自动把 shell history 编译为 Dagger Function 或 CI pipeline；
- CI 仍独立执行 test / scan / policy / artifact / approval；
- 项目处于 experimental / early development，页面只讲方向性机制。

## Title coverage map

| 标题含义单元 | 候选可见区域 | Claim IDs | 贡献 |
|---|---|---|---|
| `Agent 自主执行后` | 驱动变化 | C1 | 说明操作者未亲历 Agent 的完整执行过程 |
| `不应只交付代码` | 保留什么 | C2 | 展开代码分支、容器状态和执行记录 |
| `同时交付运行环境` | 人接手 + CI 复验 | C3、C4、C5 | 分开说明人进入原任务与 CI 按执行定义重跑 |
| 解释行：`方便人继续调试` | 人如何接手/处置 | C3、C4 | 说明查看、进入、继续、合并或丢弃 |
| 解释行：`CI 按同一定义重跑` | CI 如何复验 | C5 | 限定为按环境配置与 Dagger Function 重新执行 |
| 方向适用到什么程度 | 成熟度边界 | C6 | 防止把早期机制升级为成熟标准 |

所有候选区域均直接证明或限制标题；无孤立公司清单、MCP 清单或安全治理模块。

## 修订语义包 S1R4

### 精确标题

> **Agent 自主执行后，不应只交付代码，同时交付运行环境**

解释行：**Dagger 将环境与任务变成可编程、可缓存的执行图；Container Use 进一步将代码分支、容器状态与执行历史封装为 Agent 可持续操作的任务环境。**

### 唯一主张

Dagger 解决“任务如何被程序化定义、执行和复用”，Container Use 解决“Agent 执行后，任务环境如何被保留、继续和处置”；两者共同把工程交付单元从单独的代码结果扩展为带运行上下文的任务环境。

### 行业趋势

`工程抽象：一次性的 Pipeline Run → 可创建、观察、继续和处置的 Agent Environment`

### 左侧：Dagger 的 high-level 技术方案

1. **可组合的 Module API：** 构建、测试与发布逻辑成为可发现、可调用、可跨语言组合的 Functions，而不是重复复制的 Pipeline Step；
2. **多粒度缓存与增量计算：** Engine 以内容寻址 DAG 复用整个 Function 或单次操作的结果，并通过 Cache Volume 保留跨 Session 的工具数据；
3. **可移植的执行定义：** Container、Directory、Service、Secret 与 Function 一同进入执行图，使同一交付逻辑可以在本地、现有 CI 与远程 Engine 调用。

### 右侧：Container Use 的 high-level 技术方案

1. **Agent 接入层：** 通过 MCP / CLI 将环境创建、命令执行、文件修改和环境处置暴露给不同 Coding Agent；
2. **Environment 复合对象：** 一个任务环境同时绑定 Git branch/worktree、Dagger Container 状态与执行历史；
3. **环境生命周期：** 环境可以被创建、运行、观察、继续，并最终 merge、apply 或 delete；命令失败后仍保留新的容器状态与记录。

### 中央能力承接

Container Use 不是另一套 CI 编排器；它把 Dagger 的容器执行与状态能力封装成 Agent 可以直接操作的任务环境生命周期。

### 底部洞察

> **Agent 平台的高阶能力，不只是调用更多工具，而是把每次自主执行沉淀为可接手、可重跑、可处置的任务环境。**

### 比较范围

页面不做 Dagger 与 Container Use 的横向优劣对比，而是解释“执行底座 → Agent 任务环境”的技术分层；不做 Claude Code、Codex、Goose 等 Agent 厂商排名，也不把兼容清单当作行业采用。

### 证据边界

Container Use 为 experimental / early development；“可重跑”仅指环境配置和 Dagger Function 等已声明执行定义可以重新执行，不代表运行中的 service、Host Tunnel、外部数据库内存态或任意交互会话能够无损跨 Engine 迁移。

## 当前未锁定

- S1R4 high-level 语义包已获批；
- L1 页面区域与阅读顺序已锁定；S2 区域内部对象结构与内容预算待批准；
- 正式 PPTX 归档后的最终 QA 与版本同步。

## 左半区内容重开 CD-L-R

用户指出当前 Monorepo 示例过于简单，并要求参考“可组合执行模型、内容寻址增量计算、可移植执行环境”三个关键特性。原整合稿不再作为当前视觉提案；纵向三段结构继续有效，但三段内容重新进入区域内容设计。

### 候选区域主张

> **Dagger｜可组合、可增量、可移植的交付执行层**

解释行：**Module API 组织交付能力，Engine 以内容寻址 DAG 求值，并让同一执行定义跨运行位置复用。**

这里使用“可移植的执行定义”，不使用“环境完全一致”：可移植的是 Module / Function 图和大部分容器化工具链定义；身份、网络、架构、Cache 拓扑与外部状态仍随运行位置变化。

### CD-L-R1｜可组合的 Module API

**可见文案：**

> **可组合的 Module API**
> 构建、测试与发布能力从复制脚本变成可发现、可调用、可组合的 Functions。

**候选机制图：**

```text
Developer ─┐
CI ────────┼──▶ Delivery Module ──▶ build() · test() · publish()
Agent ─────┤         API                       │
Module ────┘                                   └──▶ Container · File · Directory
```

图的任务不是证明某段示例代码，而是说明同一个 Module API 可以被不同调用方和其他 Module 复用。

### CD-L-R2｜多粒度缓存与增量计算

**可见文案：**

> **多粒度缓存与增量计算**
> 复用粒度从整个 Function 下沉到单次 Container / File 操作；Cache Volume 保留跨 Session 的工具数据。

**候选机制图：**

```text
┌──────────────────── build(source) Function ────────────────────┐
│                    FUNCTION CACHE · 整体调用                    │
│                                                                │
│  Base image ──▶ Add source ──▶ Install deps ──▶ Build artifact │
│  LAYER CACHE    LAYER CACHE     LAYER CACHE      LAYER CACHE   │
│                                     ▲                          │
└─────────────────────────────────────┼──────────────────────────┘
                                      │
                         Package / Tool Cache Volume
                          VOLUME CACHE · 跨 Session

Function hit → 跳过整张执行图
Function miss → 继续复用命中的中间 Layer
Cache Volume → 保留 npm / pip / Go 等工具数据
```

**图下短句：** `Dagger 不只缓存最终制品，而是按 Function、操作 Layer 与工具数据三种粒度减少重复工作。`

**Speaker notes：**

- 普通 CI Step 会修改共享 Runner，输入可能藏在工作目录、环境变量、网络和时间中，系统无法可靠判断两次执行是否等价；
- Dagger Function 接收显式对象，链式操作产生新的对象状态，Engine 因而可以识别并复用中间节点；
- Function Cache Key 的官方边界包括 Module 源码、Function 参数与父对象状态；Container/File 操作由其父对象和操作输入形成 Layer 身份；不同缓存层不能被简化成一条完全相同的公式；
- Cache Volume 是跨 Session 保存工具或包管理器数据的显式状态通道，不应与内容寻址的 Layer / Function Cache 混写；
- 外部读取与副作用 Function 不能仅凭摘要相同就永久跳过，需要 TTL、Session 或 `never` 策略。

**备注中的三类失败模式：**

`漏掉输入 → 错误命中`；`输入边界过宽 → 大面积失效`；`缓存副作用 → 业务语义错误`。

### CD-L-R3｜可移植的执行定义

**可见文案：**

> **可移植的执行定义**
> Container、Directory、Service、Secret 与 Function 一同进入执行图，使同一交付逻辑跨运行位置调用。

**候选机制图：**

```text
Container · Directory · Service · Secret
                    │
                    ▼
          Module / Function Graph
                    │
               Dagger Engine
          ┌─────────┼─────────┐
        Local   Existing CI   Remote Engine
```

图下保留一条灰色边界：`同一执行逻辑 ≠ 身份、网络与外部状态完全一致`。

### CD-L-COMPARE-GRADLE｜底部对照带

**可见文案：**

| | 核心执行图 | 缓存粒度 | 主要作用范围 |
|---|---|---|---|
| Gradle | Project / Task Graph | Task outputs · Build Cache | 项目内部构建 |
| Dagger | Module / Typed Object Graph | Function / Layer + 工具状态 | 构建、测试、环境与制品 |

比较意图是说明二者的**核心抽象层级不同**，不是宣称 Dagger 替代 Gradle。Gradle 仍可在 Dagger Container 内承担项目构建；Dagger 则把它与测试服务、制品和执行环境一起纳入更外层的交付执行图。

### 左半区 V2 评审资产

- PNG：`../assets/dagger-left-region-core-features-v2.png`（1800 × 1680）
- SVG：`../assets/dagger-left-region-core-features-v2.svg`（900 × 840，可编辑源）
- 来源与边界：`../assets/dagger-left-region-core-features-v2-sources.md`
- 当前为内容与视觉提案；“先生成一版看看”属于生成授权，不构成 CD-L-R1—R3、Gradle 对照带或 VIS-L2 的批准。

### 左半区 V3 评审资产

- 用户已将区域结构改为“左上完整图、左下功能列表”；旧 V2 的三段纵向结构被替代。
- PNG：`../assets/dagger-left-region-architecture-features-v3.png`（1800 × 1680）
- SVG：`../assets/dagger-left-region-architecture-features-v3.svg`（900 × 840，可编辑源）
- 来源与边界：`../assets/dagger-left-region-architecture-features-v3-sources.md`
- 当前为视觉提案；结构已获批，但完整图的对象密度、四项功能列表文案和 VIS-L3 仍待评审。

**核心能力卡片候选文案：**

1. **可复用的 Module Functions**：构建、测试、发布封装为 Functions，供本地、CI 和 Agent 统一调用。
2. **显式建模执行依赖**：Directory、Container、Service、Secret 作为类型化输入进入执行图。
3. **分层缓存与增量执行**：Function 命中跳过整个调用；未命中时，Layer 仍按操作复用，Cache Volume 保留工具数据。
4. **跨环境复用执行定义**：同一 Module Graph 可在本地、现有 CI 和 Remote Engine 上执行。

**Function / Layer 层级：**

```text
Module
└─ Function Call：ci(source)       ← Function Cache：复用完整调用结果
   └─ Engine Operation DAG
      ├─ FROM image               ← Layer Cache
      ├─ WITH source              ← Layer Cache
      ├─ RUN install              ← Layer Cache
      ├─ RUN build                ← Layer Cache
      └─ RUN test                 ← Layer Cache

Cache Volume                      ← 挂载到具体操作的工具状态，不属于上下层
```

Function Cache 命中时，Function Body 整体不再执行；Function Cache 未命中或设置为 `never` 时，Function 内部的 Container、File、Directory 操作仍可分别命中 Layer Cache。

图中的 `RUN build`、`RUN test` 表示 `ci()` Function Body 内部的容器操作，不表示名为 `build()`、`test()` 的 Module Functions。若 `build()`、`test()` 被建模为独立 Module Functions，应与 `ci()` 同处 Function 层，并分别拥有各自的操作 DAG。

### 左半区 V4 事实审计版

- V3 经逐对象事实审计后未通过正式页面门禁，不再作为当前候选图；
- 审计报告：`[[00_sources/research-dagger-diagram-fact-audit-2026-08-03|Dagger 左区技术图事实审计]]`；
- PNG：`../assets/dagger-left-region-fact-audited-v4.png`（1800 × 1680）
- SVG：`../assets/dagger-left-region-fact-audited-v4.svg`（900 × 840，可编辑源）
- 来源与边界：`../assets/dagger-left-region-fact-audited-v4-sources.md`
- V4 使用当前官方 Cache Volume 示例的 API 形态：`build(source: Directory) → Container`；内部精确对应 `from(node:21) → withDirectory → withWorkdir → withMountedCache → withExec(npm install)`，并区分 base image、可复用 operation、挂载与 Cache Volume；
- Local shell、CI script、Agent tool 改为调用上下文；Dagger Engine 只画当前官方稳定证据直接支持的本地/CI runtime，不再把需额外配置且状态不同的 remote endpoint 与其并列；
- 当前为事实核验通过后的视觉提案，VIS-L4 仍待用户评审。

### AI 适配逻辑增强提案

用户明确要求保留 V4 已审计的层级图，不新增 Agent 迭代回路。AI 适配逻辑只通过图下的一行说明和核心能力文案表达。

**图下说明候选：**

> **适合 Agent 多轮工程验证：工程 Functions 可反复调用；输入变化后，Engine 重新求值并复用未受影响的 operation。**

这里不把 Dagger 原生 `LLM` / `Env` / Module-as-MCP 作为主图中心。相关能力确实存在，但按当前 Deep Dive 证据仍应视为 Experimental / 未稳定；可放入备注或右侧 Container Use 的接口说明。

对应核心能力文案建议调整为：

1. **可反复调用的工程 Functions**：构建、测试和环境操作形成稳定接口，Agent 每轮修改后直接重复调用，不必生成或改写流水线脚本。
2. **显式输入与执行环境**：Directory、Container、Service 等对象明确每轮验证使用的代码和环境，减少隐含的宿主机状态。
3. **Operation 级增量复用**：Engine 按输入与上游对象识别 operation；变化沿依赖关系传播，未受影响的节点继续复用。
4. **同一执行定义贯穿 Agent 与 CI**：Agent、本地开发和 CI 调用同一 Module，多轮验证逻辑进入流水线时无需改写为另一套定义。

### Container Use 右区内容提案 R1

**区域主张：**

> **Container Use 为每个 Agent 任务建立一个可查看、可继续、可接受或丢弃的工程环境。**

**上半区完整技术模型：**

```text
Coding Agent
Claude Code / Codex / Goose
            │ MCP / CLI
            ▼
┌──────────────────────────────────────┐
│ Environment · env-42                 │
│                                      │
│ Git branch + worktree                │ 独立代码与工作目录
│ Dagger Container state               │ 命令后的文件系统状态
│ Command notes                        │ command / exit / stdout / stderr
│ Environment config                   │ base / setup / install / services
└──────────────────────────────────────┘
            │
            ▼
create → run / modify → inspect / continue → merge / apply / delete
```

对象层只表达不同状态的组合关系，不把 Environment 画成一个长期运行的容器，也不暗示 Service、Host Tunnel 或外部副作用随 Container state 完整恢复。

**图下说明候选：**

> **Agent 命令即使失败，代码、容器状态与操作记录仍被保留，工程师可以查看现场后继续或处置。**

**核心能力卡片候选：**

1. **每个任务独立建环境**：branch、worktree 与 Container state 按 Environment 隔离，减少多个 Agent 直接覆盖同一工作目录。
2. **失败后保留处理现场**：非零退出仍记录新的 Container state、命令、退出码与输出，不只留下失败日志。
3. **人可以观察并接管**：通过 log、diff、terminal、checkout 查看变化、进入环境并继续处理。
4. **结果可以接受或丢弃**：merge、apply、delete、prune 把 Agent 尝试变成有明确结局的生命周期对象。

**表达边界：**

- 不写“完整运行环境永久保存”；Container state、Git 数据、notes 与运行中 Service 的持久性不同；
- 不写“自动进入 CI”；只有声明为 Dagger Function / 配置的执行语义才能在 CI 独立重跑；
- 不写“解决所有并行冲突”；branch/worktree/container 主要隔离工作区和文件系统，外部系统仍需独立命名空间。

### 区域预算与旧资产状态

- 继续使用“标题 → high-level 机制图 → 一句解释”的三段纵向结构；
- 每张图只保留 4—6 个对象与一条核心关系，避免代码截图占据主要空间；
- 原 `dagger-left-region-integrated.png` 与三张 Monorepo 子图保留为历史设计资产，不再作为当前视觉提案；
- CD-L-R1—R3 当前为 `proposed`；左区 V2 PNG 已按用户要求生成，等待逐项内容与视觉评审。

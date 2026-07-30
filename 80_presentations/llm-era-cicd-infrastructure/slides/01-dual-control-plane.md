---
title: Slide 01 - Agent 时代的 CI/CD 基础设施演进
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
  - technology/infrastructure
status: production-complete
slide: 1
content_status: fact-audited
visual_status: final-pptx-verified
primary_deep_dive: "[[50_deepdives/llm-era-cicd-infrastructure/README]]"
as_of: 2026-07-30
audience: CTO、研发效能负责人、平台工程负责人
page_genre: technical-insight
claim:
  bounded_claim: Agent 把研发任务变成高频、并行、异步且跨工具的机器工作负载，驱动 CI/CD 基础设施沿四个方向演进；代码仓承载任务与上下文，流水线约束 Agent 执行，构建系统加速验证反馈，制品仓提供供应链事实与交付证据。
  causal_mechanism: Agent 的多轮试错、并行会话、长时间异步运行、跨工具调用和可产生副作用，使传统面向人类提交与固定脚本的接口、反馈、身份和证据模型不足。
  evidence_boundary: 页面只需证明跨厂商机制共同指向这些演进方向，不要求证明行业已普遍完成转型、成熟度一致或已经形成统一 ROI。
  unsupported_stronger_claim: Agent 已替代传统 CI/CD，或行业已普遍实现无人值守的合并、制品晋级与生产发布。
  implications:
    - 跨系统 Agent 的约束不只在模型能力，更在上下文能否复用；将规则、事件、任务图和制品事实转为可查询、可版本化接口。
    - Agent 将一次提交放大为多轮并行尝试；应以影响分析、缓存和可复用环境增强快速验证，并按资源利用率弹性调度算力。
object_policy:
  diagrams: left_png_right_native_shapes
  tables: native_editable
  editable_objects: full_style_editable
  grouping: required
  grouping_scope: all_non_placeholder_slide_local
  grouping_targets:
    - right_layer_code_repository
    - right_layer_pipeline
    - right_layer_build_and_resources
    - right_layer_artifact_and_supply_chain
    - bottom_insight_summary
    - left_company_paths_png
  region_policy:
    left_company_paths: approved_png_image
    right_synthesis: native_powerpoint_text_and_shapes
    title: native_powerpoint_text
    bottom_insight: native_powerpoint_text_and_shapes
locks:
  semantic:
    state: user_approved
    approval_quote: 批准
  structural:
    state: user_approved
    approval_quote: 直接进入ppt构建
  visual:
    state: user_approved
    approval_quote: 直接进入ppt构建
decision_log:
  - id: S1
    gate: semantic
    proposal: 面向 CTO、研发效能负责人和平台工程负责人，采用技术洞察加决策启示的单页类型。
    owner: user
    state: user_approved
    approval_quote: S1批准
  - id: S2-v6
    gate: semantic
    proposal: Agent 把研发任务变成高频、并行、异步且跨工具的机器工作负载，驱动 CI/CD 基础设施演进；代码仓承载任务与上下文，流水线约束执行，构建系统加速反馈，制品仓提供供应链事实与交付证据。
    owner: user
    state: user_approved
    approval_quote: 批准，后面再看看是否需要进行调整
    superseded_by: S2-v7-one-line-title
  - id: S2-v7-one-line-title
    gate: semantic
    proposal: 单行标题压缩为“Agent 新负载推动 CI/CD 基础设施沿上下文、动态运行、快速验证与供应链事实四个方向演进”。该标题保留 Agent 新负载的因果驱动和四类已批准演进方向，删除标题中对代码仓、流水线、构建系统与制品仓角色的重复展开；证据边界仍为只证明演进方向，不证明行业普遍完成转型。
    owner: user
    state: not_approved
    approval_quote: agent新负载感觉比较奇怪
    superseded_by: S2-v8-natural-one-line-title
  - id: S2-v8-natural-one-line-title
    gate: semantic
    proposal: 单行标题改为“长时、多轮、跨工具的 Agent 任务，正推动 CI/CD 基础设施向上下文协作、动态运行、快速验证与供应链事实演进”。用具体任务特征替代抽象的“Agent 新负载”，保留 Agent 与基础设施演进之间的因果关系及四类已批准方向；证据边界仍为只证明演进方向，不证明行业普遍完成转型。
    owner: user
    state: not_approved
    approval_quote: 改成Agent长程任务
    superseded_by: S2-v9-agent-long-horizon-task-title
  - id: S2-v9-agent-long-horizon-task-title
    gate: semantic
    proposal: 单行标题改为“Agent 长程任务正推动 CI/CD 基础设施向上下文协作、动态运行、快速验证与供应链事实演进”。使用用户指定的“Agent 长程任务”，其余因果关系、四类演进方向和证据边界保持不变。
    owner: user
    state: user_approved
    approval_quote: 批准
  - id: S2-evidence-boundary
    gate: semantic
    proposal: 页面只需证明演进方向，不需要证明行业已经普遍完成转型。
    owner: user
    state: user_approved
    approval_quote: 不需要证明普遍完成转型，只要证明演进方向就够了
  - id: S3-a-v3
    gate: semantic
    proposal: 阅读逻辑以 Agent 负载变化驱动四类价值演进为主线，即代码仓增强上下文与协作、流水线增强编排与运行、构建系统增强反馈吞吐与经济性、制品仓增强供应链智能与新资产管理；身份、安全、策略、审计和证据作为每类价值演进的必要边界，不作为页面主线。
    owner: user
    state: user_approved
    approval_quote: 批准
    superseded_by: S3-a-v4
  - id: S3-b-v2
    gate: semantic
    proposal: 归纳四条价值演进趋势，即代码仓从代码存放与人工协作扩展为 Agent 可消费的任务、上下文和候选变更协作面；流水线从固定 Job 编排扩展为 Agent Session、事件驱动 Tool 调用和可观测运行时；构建系统从单次全量验证扩展为面向高频并发的 Graph、Affected、Cache、可复用环境和反馈经济性；制品仓从包与二进制存放扩展为实时依赖情报、供应链事实以及 Model、Skill、MCP 等新型 Agent 资产的受限分发。身份、权限、Sandbox、Policy、Oracle、审计和 Lineage 作为四条趋势的控制边界；只证明演进方向，不外推普及率和统一 ROI。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: S3-b-v3
  - id: S3-a-v4
    gate: semantic
    proposal: 阅读逻辑以 Agent 负载变化驱动四类价值演进为主线，即代码仓增强上下文与协作、流水线增强编排与运行、构建系统增强反馈吞吐与经济性、制品仓增强供应链智能与交付证据；身份、安全、策略、审计和证据作为每类价值演进的必要边界，不作为页面主线。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: S3-a-v5
  - id: S3-b-v3
    gate: semantic
    proposal: 归纳四条价值演进趋势，即代码仓从代码存放与人工协作扩展为 Agent 可消费的任务、上下文和候选变更协作面；流水线从固定 Job 编排扩展为 Agent Session、事件驱动 Tool 调用和可观测运行时；构建系统从单次全量验证扩展为面向高频并发的 Graph、Affected、Cache、可复用环境和反馈经济性；制品仓从包与二进制存放扩展为 Agent 可查询的实时依赖情报、供应链事实与交付证据。身份、权限、Sandbox、Policy、Oracle、审计和 Lineage 作为四条趋势的控制边界；只证明演进方向，不外推普及率和统一 ROI。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: S3-b-v4
  - id: S3-a-v5
    gate: semantic
    proposal: 多个 CI/CD 厂商已开始针对 Agent 长时、并发、迭代且跨工具的任务模式扩展基础设施：代码仓增强上下文与协作；流水线增加 Agent 编排与可观测运行；构建系统强化任务图、增量计算、缓存、远程执行与底层计算资源调度，以支撑高频反馈和成本控制；制品仓及其相邻供应链平台提供可查询的供应链事实与可验证交付证据。身份、隔离、策略、审计与可追溯性构成共同边界。
    owner: user
    state: user_approved
    approval_quote: Q1 同意 Q2不用 Q3 不用可以突出
  - id: S3-b-v4
    gate: semantic
    proposal: 四条趋势均表述为在既有角色上增加 Agent-ready 能力：代码仓增加机器可消费的任务上下文、仓库规则和候选变更协作面；流水线增加 Agent Session、事件驱动 Tool 调用、动态执行路径和可观测运行时，并继续负责触发、依赖、门禁与发布编排；构建系统增加基于 Graph/Affected 的任务选择、Remote Execution、Runner/Worker Pool 调度、Cache、Snapshot/可复用隔离环境和按负载伸缩，形成高频并发验证的执行资源面；制品仓及其相邻供应链平台增加 Agent 可查询的实时依赖情报、SBOM、Provenance、Evidence 和可验证交付证据。只证明多个厂商已开始沿这些方向演进，不表示旧模式被替代、行业普遍采用或存在统一 ROI。
    owner: user
    state: user_approved
    approval_quote: Q1 批准；继续
  - id: S3-c
    gate: semantic
    proposal: 企业启示为不要只给现有工具增加 AI 接口，而要同步升级上下文、受控执行、反馈和证据；先补机器可读上下文与快速验证，再建立任务级身份和证据链，最后按可回退动作逐级放权。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: S3-c-v2
  - id: S3-c-v2
    gate: semantic
    proposal: 企业启示分为三项：第一，平台目标不是给每个工具增加一个 AI 入口，而是把代码仓、流水线、构建执行资源和制品/供应链平台建设成共同的 Agent-ready 底座，使上下文可读取、任务可编排、验证可快速调度、供应链事实可查询；第二，投资重点应按实际瓶颈补齐任务上下文、Agent Session、Graph/Affected/Cache/Remote Execution、Runner/Worker 资源以及依赖与制品证据，并以反馈时延、排队、资源利用率和 Verified Outcome 成本衡量成效；第三，安全治理不占页面主线，但决定自治上限，只有动作具备独立 Oracle、任务级身份、可追溯证据和回退路径时，才扩大到自动合并、制品晋级或发布。
    owner: user
    state: user_approved
    approval_quote: Q1：同意；Q2:这块需要给出业界的洞察证据支撑，如果有就突出；Q3:还好
    superseded_by: S3-c-v3
  - id: S3-c-v3
    gate: semantic
    proposal: 企业启示分为三项：第一，平台目标不是给每个工具增加一个 AI 入口，而是把代码仓、流水线、构建执行资源和制品/供应链平台建设成共同的 Agent-ready 底座，使上下文可读取、任务可编排、验证可快速调度、供应链事实可查询；第二，把反馈经济性提升为平台 KPI，Agent 任务同时消耗执行算力与推理额度，平台应观测反馈时延、Queue Time、Cache Hit、CPU/内存利用率、Runner/Core Minutes、AI Credits/Token，并以“经独立验证后被接受的结果成本”作为企业自定义复合指标，再通过 Affected、Cache、Snapshot/可复用隔离环境、Remote Execution、动态 Worker 与 Run Budget 优化；第三，安全治理不占页面主线，但决定自治上限，只有动作具备独立 Oracle、任务级身份、可追溯证据和回退路径时，才扩大到自动合并、制品晋级或发布。只证明厂商已经把资源和成本观测产品化，不外推统一 ROI。
    owner: user
    state: user_approved
    approval_quote: Q1:批准；Q2: 复合成本指标，你准备怎么展示呢
  - id: T2-page-architecture
    gate: structural
    proposal: 采用混合证据结构，阅读顺序为“标题主张 → 四类基础设施因果表格 → 共同控制边界窄横带 → 三项企业启示”；不设置复合成本公式或独立指标仪表盘。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T2-v2-diagram-architecture
  - id: T3-main-table
    gate: structural
    proposal: 中央主体使用原生可编辑四行四列表格；列为“基础设施、Agent 带来的负载压力、Agent-ready 能力扩展、代表性产业信号与状态”，行为“代码仓、流水线、构建系统与执行资源、制品仓及相邻供应链平台”。每行只保留一个因果机制和一至两个代表性厂商/规范信号，不做公司横向排名。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v2-diagram-content
  - id: T4-space-budget
    gate: structural
    proposal: 页面纵向空间约为标题15%、主体表格60%、共同控制边界6%、企业启示19%；标题最多三行，表格正文每格最多三行，产业信号列每行最多两个厂商或规范及其状态，底部三项启示每项最多两行，不展示复合成本公式。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T4-v2-diagram-budget
  - id: T2-v2-diagram-architecture
    gate: structural
    proposal: 采用一张“Agent 新负载驱动基础设施演进图”作为页面主体，阅读顺序为“标题主张 → 左侧 Agent 负载驱动块 → 向右展开四类基础设施演进模块 → 贯穿四类模块的共同控制边界 → 三项企业启示”；不再使用中央因果表格。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T2-v3-company-trend-diagram
  - id: T3-v2-diagram-content
    gate: structural
    proposal: 主图以“既有角色 + Agent-ready 新增能力”表达演进而非替代。左侧驱动块只保留长时、并发、迭代、跨工具四个负载特征；右侧四个并列模块分别为代码仓、流水线、构建系统与执行资源、制品仓及相邻供应链平台。每个模块包含一行既有角色、最多三项新增能力，并嵌入一至两个产业证据标签或产品状态；四个模块底部共享身份与隔离、Policy 与 Oracle、审计与 Lineage 三组控制边界。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v3-company-trend-content
  - id: T4-v2-diagram-budget
    gate: structural
    proposal: 页面纵向空间约为标题15%、主图及共同控制边界66%、企业启示19%。主图使用原生可编辑形状和连接线；每个基础设施模块最多一行既有角色、三项新增能力和两个证据标签，底部三项启示每项最多两行；不展示复合成本公式、厂商自测倍数、公司排名、全行业普及率或统一 ROI。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T4-v3-supporting-region
  - id: T2-v3-company-trend-diagram
    gate: structural
    proposal: 主图只承担“基础设施对象 + 代表公司 + 变化趋势说明”。采用四条并行演进轨道，每条从左到右依次为基础设施对象、代表公司或产品信号、Agent 时代的变化趋势；不在主图中承载 Agent 负载清单、共同控制边界或企业启示。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T2-v4-left-right-architecture
  - id: T3-v3-company-trend-content
    gate: structural
    proposal: 四条轨道分别为：代码仓—GitHub/GitLab—任务上下文、仓库规则与候选变更协作；流水线—GitHub/GitLab/Buildkite/Harness—Agent Session、动态 Tool 调用与可观测运行；构建系统与执行资源—CircleCI/Nx/Bazel—Graph/Affected、Cache/RBE/Snapshot 与动态资源调度；制品仓及相邻供应链平台—Sonatype/Cloudsmith/GitHub Attestations—依赖情报、SBOM/Provenance 与可验证交付证据。公司只作为产业演进信号，不做排名。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v4-left-diagram-and-right-explanation
  - id: T4-v3-supporting-region
    gate: structural
    proposal: 阅读顺序为“标题 → 公司与趋势主图 → 关键结论及证据边界说明 → 三项企业启示”。纵向空间约为标题15%、主图52%、结论及证据说明18%、企业启示15%。主图使用原生可编辑形状与连接线；结论及证据说明使用一张原生可编辑的三行小表，分别说明演进方向而非普遍转型、反馈经济性的产业信号、自治扩张的控制条件；企业启示保持已批准的 Agent-ready 底座、反馈经济性、自治扩张条件三项，不展示复合成本公式。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T4-v4-space-budget
  - id: T2-v4-left-right-architecture
    gate: structural
    proposal: 整页采用“上标题—中间左右—下启示”的四区结构。中间左侧为公司与趋势主图，中间右侧为结论和证据说明；下方用贯穿全宽的启示区收束。阅读顺序为“标题主张 → 左图识别产业变化 → 右侧理解 Agent 因果与证据边界 → 下方形成企业动作”。
    owner: user
    state: user_approved
    approval_quote: Q1 同意
  - id: T3-v4-left-diagram-and-right-explanation
    gate: structural
    proposal: 左图使用四条纵向堆叠的演进轨道，每条从左到右呈现“基础设施对象 → 代表公司 / 产品信号 → Agent-ready 变化趋势”，继续沿用已提出的四类对象及公司范围。右侧不用窄表格，改用三个纵向说明块：Agent 负载为何不同、跨厂商信号能够证明什么、身份与隔离 / Policy 与 Oracle / 审计与 Lineage 构成什么边界。
    owner: user
    state: user_approved
    approval_quote: Q2:同意
    superseded_by: T3-v5-company-specific-paths
  - id: T3-v5-company-specific-paths
    gate: structural
    proposal: 左图从“公司名单共同指向一个趋势”改为“每类基础设施内展示公司差异化路径，再归纳共同趋势”。代码仓分别展示 GitHub 的 Issue/评论到临时环境再到 PR，以及 GitLab 的事件到 Custom Flow/Runner 再到 MR/CI Log；流水线分别展示 GitHub 的 Markdown 到 Actions YAML、GitLab 的 Custom Flow/Runner、Buildkite 的 MCP/Agent Step、Harness 的 Autofix/Worker Agent；构建与执行资源分别展示 CircleCI 的 Microbuild/Sidecar、Nx 的 Graph/Affected/Cache/Dynamic Agents/Self-Healing、Bazel 的 Hermetic RBE/Remote Cache 既有底座升值；制品与供应链分别展示 Sonatype 的依赖选择情报、Cloudsmith 的受限制品操作、JFrog 的 Evidence/Release 与 Agent Package 分发，并把 GitHub Artifact Attestations/SLSA 作为相邻交付证据放到右侧说明。每类最后只保留一句共同趋势，不把公司画成同质能力。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v6-representative-company-set
  - id: T3-v5a-company-path-structure
    gate: structural
    proposal: 左图改为“每类基础设施内展示公司差异化路径，再归纳共同趋势”，不再把公司画成同质能力。
    owner: user
    state: user_approved
    approval_quote: Q1 同意
  - id: T3-v5b-artifact-company-boundary
    gate: structural
    proposal: 制品与供应链主图加入 JFrog，并把 GitHub Artifact Attestations / SLSA 移到右侧交付证据说明。
    owner: user
    state: user_approved
    approval_quote: Q2同意
  - id: T3-v6-representative-company-set
    gate: structural
    proposal: 每类基础设施最多展示三家公司，并制作以下结构评审候选：代码仓保留 GitHub、GitLab；流水线保留 GitHub、Buildkite、Harness，GitLab 的 Flow / Runner 证据由代码仓轨道和右侧说明承接；构建与执行资源保留 CircleCI、Nx、Bazel；制品与供应链保留 Sonatype、Cloudsmith、JFrog。每家公司只展示一个最具区分度的机制短语，每类最后归纳一句共同趋势。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v7-detailed-company-paths
  - id: T3-v7-region-responsibility
    gate: structural
    proposal: 左图只展示“基础设施 → 公司差异化路径”，不再展示共同演进趋势；代码仓、流水线、构建与执行资源、制品与供应链的四条共同趋势统一移到页面右侧说明。
    owner: user
    state: user_approved
    approval_quote: 共同演进趋势放在右边说明里吧
  - id: T3-v7-detailed-company-paths
    gate: structural
    proposal: 左图公司路径由单个机制标签扩展为“入口或触发 → 核心运行机制 → 输出或控制边界”的可讲解短路径。代表集暂定为代码仓 GitHub/GitLab；流水线 GitHub/Buildkite/Harness；构建 CircleCI/Nx/Bazel；制品与供应链 Sonatype/Cloudsmith/JFrog。每家公司占一行，保留足以区分实现路径的两到三个机制节点，不在左图重复共同趋势。
    owner: user
    state: proposed
    approval_quote:
  - id: T3-v8-right-trend-expression
    gate: structural
    proposal: 右侧四条共同趋势均采用“结论句 → Agent 负载为何推动该变化 → 必须保留的证据或控制边界”三层表达。代码仓强调仓库成为任务上下文与候选变更协作面，边界为 Review / Required Checks；流水线强调受控 Agent 运行时，边界为 Tool / Secret / Network / Budget；构建强调反馈经济性底座，边界为既有能力升值而非 Agent 新发明；制品与供应链强调可查询事实与交付证据面，边界为查询不等于授权、高风险动作需独立审批。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v10-right-evidence-sequence
  - id: T3-v10-right-evidence-sequence
    gate: structural
    proposal: 右侧按代码仓、流水线、构建与资源、制品与供应链四项与左图逐项对应；每项固定采用“演进结论 → 左侧公司的详细说明及其如何支撑结论 → Agent 驱动原因 → 控制边界”的阅读顺序。先形成较详细的完整文字版，再由用户筛选单页可见文案。
    owner: user
    state: user_approved
    approval_quote: Q1 同意；Q2 每项按照演进结论-》左侧公司的详细说明（能支撑演进结论）-〉驱动原因-》控制边界；先给出较为详细的文字版，然后我们再进行筛选
    detailed_draft: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-detailed-v1.md]]"
    superseded_by: T3-v12-right-compressed-visible-copy
  - id: T3-v11-right-no-duplicate-branding
    gate: structural
    proposal: 正式页面右侧不重复公司图标或另建公司名单；详细审核稿暂时保留公司名作为证据锚点，筛选为页面文案时再通过与左图对齐的位置和机制短语减少品牌重复。
    owner: user
    state: user_approved
    approval_quote: Q3:同意
  - id: T3-v12-right-compressed-visible-copy
    gate: structural
    proposal: 右侧继续与左图四类基础设施逐项对应；每项页面可见内容压缩为“演进结论 → 各公司差异化变化短句 → Agent 驱动原因”。完整公司证据、产品状态和来源回链进入演讲备注；本页右侧不展示控制边界。
    owner: user
    state: user_approved
    approval_quote: Q1 批准，Q2：保留；Q3: 可以
    compressed_draft: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-explanation-compressed-v1.md]]"
    superseded_by: T3-v13-right-synthesis-matrix
  - id: T3-v13-right-synthesis-matrix
    gate: structural
    proposal: 拉开左右信息职责：左图继续回答“哪些公司通过什么差异化机制演进”；右侧改为“Agent 新负载 → 四类共同演进”的综合判断区。右侧顶部只放一次长时、并行、迭代、跨工具的 Agent 负载，主体采用二乘二演进矩阵；每格以演进结论和基础设施响应为主，公司变化仅作为底部小型证据锚点，不再逐家公司占据独立正文行。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T3-v14-right-detailed-synthesis-cards
  - id: T3-v14-right-detailed-synthesis-cards
    gate: structural
    proposal: 左图保持不变；右侧采用“顶部 Agent 新负载条 + 二乘二综合洞察卡片”。每张卡依次呈现共同演进结论、公司证据的机制解释和基础设施具体响应。公司证据不再只是小型标签，而用一至两句话解释公司做了什么以及为何能够支撑结论；产品状态、来源和控制边界仍进入演讲备注。
    owner: user
    state: not_approved
    approval_quote: 依然是从上到下分四层展示
    user_direction_quote: 左边不变，右边的格式和内容调整一下，尤其是对公司证据的说明可以详细一些，让大家好理解
    detailed_draft: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-synthesis-detailed-v2.md]]"
    superseded_by: T3-v15-right-four-layer-synthesis
  - id: T3-v15-right-four-layer-synthesis
    gate: structural
    proposal: 左图保持不变；右侧仍按代码仓、流水线、构建系统与底层资源、制品仓与供应链从上到下分四层，并与左侧层级对齐。每层内部依次呈现共同演进结论、公司证据的机制解释和基础设施具体响应；顶部只补充一次 Agent 新负载，不改变四层主体结构。
    owner: user
    state: user_approved
    approval_quote: 整体ok，流水线的描述中确定性外壳比较奇怪
    user_direction_quote: 依然是从上到下分四层展示
    detailed_draft: "[[80_presentations/llm-era-cicd-infrastructure/slides/01-right-synthesis-detailed-v2.md]]"
  - id: T3-v16-pipeline-dynamic-runtime-wording
    gate: structural
    proposal: 流水线共同演进结论使用“固定 Job / DAG → 承载 Agent Session 与 Tool 调用的动态运行时”，不再使用“确定性外壳”或“Agent 执行与编排层”。
    owner: user
    state: user_approved
    approval_quote: Q1 批准；Q2 不用
  - id: T5-v1-bottom-enterprise-implications
    gate: structural
    proposal: 底部启示区不重复四类基础设施结论，而把上方证据转成三项企业动作：一是把仓库上下文、流水线事件、构建任务图和供应链事实建设成共同的 Agent-ready 底座；二是把反馈效率作为平台能力，用 Queue Time、Cache Hit、资源利用率和有效结果成本引导增量验证、缓存、远程执行与弹性 Worker 投资；三是按独立验证、任务级身份、证据链和回退能力逐级扩大到自动合并、制品晋级与发布。底部不展示复合成本公式。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T5-v2-two-enterprise-implications
  - id: T5-v2-two-enterprise-implications
    gate: structural
    proposal: 底部启示区收敛为两项。第一，先统一机器可消费的交付上下文，把仓库规则、流水线事件、构建任务图、依赖与制品事实标准化为 Agent 可读、可调用的接口。第二，用反馈速度与资源利用率检验平台改造成效，观察 Queue Time、任务吞吐、Cache Hit、Runner/Core、CPU/内存利用率和 AI Credits/Token，以判断瓶颈位于任务选择、缓存、排队还是算力调度，再决定 Affected、Remote Execution、Cache 或动态 Worker 投资。这些指标用于诊断与校准投资，不定义为统一 KPI，也不展示复合成本公式。原第三项自治放权从本页底部移除。
    owner: user
    state: proposed
    approval_quote:
    partial_approval_quote: 第一项可以，2提出的资源利用率，速率是一个合适的点，但是表述要改一下，不能说成kpi，3去除吧
    superseded_by: T5-v3-conditional-insight-implications
  - id: T5-v3-conditional-insight-implications
    gate: semantic
    proposal: 底部只保留两条条件化洞察。第一，“跨系统 Agent 的首要约束不是模型能力，而是上下文能否稳定复用”：当任务跨 Repo、CI 和 Artifact 且需要多轮接续时，先把规则、事件、任务图和供应链事实标准化为可查询、可版本化接口，再扩大 Agent 覆盖面。第二，“Agent 验证变慢时，先区分重复工作、调度低效与真实算力不足，再决定是否扩容”：低 Cache Hit 或重复执行高时优先投入 Graph/Affected/Cache；排队高但资源利用率低时先修调度、冷启动与资源碎片；排队高且资源利用率持续高时再增加 RBE 或动态 Worker。指标只作为本地诊断信号，不定义统一 KPI 或行业 ROI；原自治放权启示继续从本页移除。
    owner: user
    state: proposed
    approval_quote:
    evidence_basis:
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F3：流水线正分化为低延迟验证内环和完整交付外环]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F5：制品仓从存储面扩展为供应链信任面与受限行动面]]"
    superseded_by: T5-v4-two-compact-insights
  - id: T5-v4-two-compact-insights
    gate: semantic
    proposal: 底部保留两条约50—60字的条件化洞察。第一，“跨系统 Agent 的约束不只在模型能力，更在上下文能否复用；将规则、事件、任务图和制品事实转为可查询、可版本化接口。”第二，“Agent 将一次提交放大为多轮并行尝试；应以影响分析、缓存和可复用环境增强快速验证，并按资源利用率弹性调度算力。”第一条延续已认可的上下文观点；第二条以增强快速验证能力为主，资源利用率只用于指导弹性调度，不定义为 KPI。原自治放权启示继续移除。
    owner: user
    state: user_approved
    approval_quote: Q1Q2Q3同意
    partial_approval_quote: 第一条的观点可以，整体提升到50-60字；第二条应该提示的是增强快速验证能力吧
    visible_copy_length:
      implication_1_codepoints: 59
      implication_2_codepoints: 57
    evidence_basis:
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F3：流水线正分化为低延迟验证内环和完整交付外环]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制]]"
      - "[[50_deepdives/llm-era-cicd-infrastructure/50_findings#F5：制品仓从存储面扩展为供应链信任面与受限行动面]]"
  - id: T5-v5-template-bottom-insight-format
    gate: structural
    proposal: 底部严格复用 insight-process-table-template.pptx 的洞察总结格式：左侧为红底白字“洞察总结”标签，右侧为同高红色底栏；栏内仅放两条白色粗体编号洞察“1.”和“2.”，每条保持完整可读并按一行处理。不新增“企业启示”标题，不拆分为卡片，不另设指标、说明或第三条洞察。
    owner: user
    state: user_approved
    approval_quote: 这个skill中不是有底部洞察的格式么，不要自由发挥！
    visual_reference: /Users/zhujiayi/.codex/skills/build-insight-pptx/assets/insight-process-table-template.pptx
  - id: T6-v1-right-native-editable-left-png
    gate: structural
    proposal: 最终 PPTX 中左侧公司路径允许使用已批准 PNG；右侧四层综合洞察必须由 PowerPoint 原生文本框、矩形、边框和色块构成，结论、公司证据与基础设施响应均可单独编辑。顶部标题和底部洞察总结栏同样使用原生可编辑对象。当前右侧 HTML/PNG 仅作为视觉参考，不作为最终嵌入对象。
    owner: user
    state: user_approved
    approval_quote: 右侧的图需要是可编辑的ppt对象，左侧可以用图
  - id: T6-v2-region-grouping-policy
    gate: structural
    proposal: 右侧代码仓、流水线、构建与资源、制品与供应链四层分别组成四个独立对象组；底部洞察总结栏组成一个独立对象组；左侧 PNG 保持一个独立图片对象。组内文本和形状保持可编辑。
    owner: user
    state: user_approved
    approval_quote: 同意
  - id: T7-v1-two-line-approved-title-treatment
    gate: structural
    proposal: 保留已批准标题原文并使用模板红色结论标题样式，固定分为两行；第一行结束于“驱动 CI/CD 基础设施演进：”，第二行呈现代码仓、流水线、构建系统和制品仓的四项结果，不缩小到模板可读字号以下。
    owner: user
    state: not_approved
    approval_quote: 标题就一行
    superseded_by: S2-v7-one-line-title
  - id: T7-v2-middle-composition-and-left-crop
    gate: structural
    proposal: 中间主体继续保持左右50:50。左侧仍使用已批准 PNG，但在 PowerPoint 图片框中只裁掉 PNG 自带的顶部表头和外围纯白留白，四层公司路径内容完整保留；在图片上方用原生文本补回“代表公司的差异化路径”列标题。右侧使用原生对象重建四层，并设置“共同演进与公司证据解释”列标题。
    owner: user
    state: user_approved
    approval_quote: Q1 同意
  - id: T7-v3-vertical-space-budget
    gate: structural
    proposal: 整页纵向空间按顶部标题约13%、中间左右主体约75%、底部模板洞察总结栏约12%分配；允许制作时在不改变阅读顺序和最低字号的前提下对三块各调整不超过2个百分点。
    owner: user
    state: not_approved
    approval_quote: Q2：不一定要进准占比，美观就行
    superseded_by: T7-v4-adaptive-template-space-rule
  - id: T7-v4-adaptive-template-space-rule
    gate: structural
    proposal: 不固定标题、主体和底部的百分比。标题区域和底部洞察总结栏继承参考模板的原始高度、位置、字体层级与内边距；中间主体占满两者之间的剩余空间。左右区域保持50:50、等高且上下边缘对齐；只在保证模板字号、文字不裁切、内容完整和视觉平衡的条件下调整间距，不改变已批准内容或区域顺序。
    owner: user
    state: user_approved
    approval_quote: 同意
  - id: T7-v5-left-final-fit-aspect-reflow
    gate: structural
    proposal: 现有4:5左图继续保留为已批准的内容与视觉参考，但不直接压缩进最终16:9页面。最终左侧仍使用PNG，并保持代码仓、流水线、构建与资源、制品与供应链从上到下四层、相同公司、Logo和路径机制不变；仅在维护源中把每层重排为适配最终50%半页框的横向比例，再导出新的最终左图PNG。最终比例由模板标题、底部洞察栏和剩余主体框实际尺寸推导，不预设固定数值。
    owner: user
    state: user_approved
    approval_quote: 同意
    downstream_conflict: 4:5竖图在16:9页面的半宽主体框内无法同时满足完整显示、50:50占位和文字可读性。
    superseded_by: T8-v1-original-layout-natural-width
  - id: T8-v1-original-layout-natural-width
    gate: structural
    proposal: 恢复已批准4:5左图内部的原始版式、节点宽度与箭头节奏，仅裁去图片自带表头和外围纯白留白；不再把路径拉伸铺满。中间主体取消50:50，按左图自然高度推导为左700px、右1066px，约39:61，右侧获得其余宽度。标题、底部洞察栏、四层顺序和所有内容保持不变。
    owner: user
    state: user_approved
    approval_quote: 直接进入ppt构建
    user_direction_quote: 左边比较奇怪，期望能恢复成一开始的版式，如果有留白，就把左右两边的比例做一些调整
  - id: T3-v9-left-asset-content
    gate: structural
    proposal: 左侧 PNG 只展示“基础设施对象 → 代表公司的差异化路径”，不在图内重复任何共同趋势、Agent 驱动或控制边界；这些内容全部由页面右侧说明承接。
    owner: user
    state: user_approved
    approval_quote: 选A；但是图中不用加共同趋势，该图是占据ppt左侧的图，所以 长:宽=4:5
  - id: V1-three-style-exploration
    gate: visual
    proposal: 在不锁定整页纵向比例的前提下生成三张中间主体区视觉候选：A 编辑型泳道，暖白背景、弱边框、以排版和色条建立层次；B 技术蓝图，深色背景、高对比连线、强调架构与运行机制；C 高管洞察，白底高留白、左侧公司路径克制、右侧趋势结论更强。三版均保持左右50:50、相同公司与主张，不使用公司 Logo，不作为最终视觉基线，待用户选择后再冻结。
    owner: user
    state: review_ready
    approval_quote:
    selected_variant: A
    superseded_by: V2-style-a-left-asset
    review_assets:
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/body-style-a-editorial-v1.png]]"
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/body-style-b-blueprint-v1.png]]"
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/body-style-c-executive-v1.png]]"
    qa_note: 三张 PNG 均已完成逐图视觉检查；未发现文字重叠或裁切。候选只用于选择信息密度、色彩和层级方式，未冻结最终视觉基线。
  - id: V2-style-a-left-asset
    gate: visual
    proposal: 左图采用 A 版编辑型泳道视觉语言：暖白背景、低饱和四色分类、弱边框圆角卡片、灰色短箭头和无公司 Logo 的文字路径。
    owner: user
    state: user_approved
    approval_quote: 选A
  - id: V3-style-a-left-asset-4x5
    gate: visual
    proposal: 将 A 版收敛为只含基础设施和公司路径的左侧 PNG，并按横向宽度:纵向高度=4:5 输出。
    owner: user
    state: review_ready
    approval_quote:
    review_asset: "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v1.png]]"
    qa_note: PNG 为 2000×2500；已完成逐图检查，未发现裁切、重叠或比例变形。
    superseded_by: V4-style-a-white-logo-path-cards
  - id: V4-style-a-white-logo-path-cards
    gate: visual
    proposal: 在保持 A 版、4:5 和既有公司范围的前提下，将画布背景改为纯白；在每家公司名称前加入保持原比例和品牌色的公司图标；公司路径改为“图标与公司名 + 三段路径节点 + 类别色关键机制”的对齐卡片，提高可扫描性和演讲可读性。
    owner: user
    state: user_approved
    approval_quote: Q1批准；Q2. 合适，Q3.保留
    user_direction_quote: 1. 背景纯白；2. 增加公司图标；3. 排版美观
    review_asset: "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.png]]"
    maintainable_source: "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.html]]"
    asset_provenance: "[[80_presentations/llm-era-cicd-infrastructure/assets/company-icons/PROVENANCE.md]]"
    qa_note: PNG 为 2000×2500，背景为纯白；已逐项检查图标比例、公司名称分离、卡片对齐、文字换行、裁切和重叠，未发现阻塞问题。
  - id: V5-style-a-right-explanation-4x5
    gate: visual
    proposal: 右侧延续左图的纯白背景、四类强调色、圆角卡片和弱阴影；每类按“演进结论标题 → 公司变化说明 → Agent 驱动色块”组织，不重复公司图标，不展示控制边界。
    owner: user
    state: not_approved
    approval_quote: 这样的话，左边和右边好像区别不大
    review_asset: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-evolution-explanation-style-a-4x5-v1.png]]"
    maintainable_source: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-evolution-explanation-style-a-4x5-v1.html]]"
    qa_note: PNG 为 2000×2500，宽高比4:5；已检查四组类别、结论、公司变化和 Agent 驱动块，未发现文字裁切、重叠或对象溢出。
    superseded_by: V6-right-synthesis-matrix
  - id: V6-right-synthesis-matrix
    gate: visual
    proposal: 右侧采用与左图纵向公司路径明显不同的“顶部 Agent 负载条 + 二乘二共同演进矩阵”；四格使用同一组类别色，但以结论和因果响应为视觉主角，公司变化缩为每格底部的一行证据锚点。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: V7-right-detailed-synthesis-cards
  - id: V7-right-detailed-synthesis-cards
    gate: visual
    proposal: 保持左图不变；右侧采用顶部 Agent 新负载条和二乘二卡片，以结论为卡片标题、公司机制解释为主体、基础设施响应为底部色块。视觉上不重复左图的公司 Logo、路径节点和横向箭头。
    owner: user
    state: not_approved
    approval_quote: 依然是从上到下分四层展示
    review_asset: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-synthesis-matrix-style-a-4x5-v2.png]]"
    maintainable_source: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-synthesis-matrix-style-a-4x5-v2.html]]"
    qa_note: PNG 为 2000×2500，宽高比4:5；已检查顶部负载条、四张卡片、公司证据段落和底部响应块，未发现文字裁切、对象重叠或页面溢出。
    superseded_by: V8-right-four-layer-synthesis
  - id: V8-right-four-layer-synthesis
    gate: visual
    proposal: 右侧保持从上到下四层并与左图逐层对齐；每层使用“演进结论标题带 + 公司证据解释正文 + 基础设施响应色块”，不使用公司 Logo、路径节点和2×2卡片。
    owner: user
    state: user_approved
    approval_quote: 整体ok，流水线的描述中确定性外壳比较奇怪
    review_asset: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-synthesis-vertical-style-a-4x5-v3.png]]"
    maintainable_source: "[[80_presentations/llm-era-cicd-infrastructure/assets/right-synthesis-vertical-style-a-4x5-v3.html]]"
    qa_note: PNG 为 2000×2500，宽高比4:5；已检查从上到下四层的类别、共同结论、公司证据解释和基础设施响应，未发现文字裁切、对象重叠或页面溢出。
  - id: V9-final-fit-left-and-full-slide-preview
    gate: visual
    proposal: 左图按最终半页框重排为横向等距三段路径，保留四层、公司、Logo 与机制不变；整页使用模板单行红色标题、左右等分主体和底部红色“洞察总结”栏。右侧继续按四层呈现共同演进、公司证据和基础设施响应，最终 PPTX 将以原生可编辑对象重建。
    owner: user
    state: not_approved
    approval_quote: 左边比较奇怪，期望能恢复成一开始的版式，如果有留白，就把左右两边的比例做一些调整
    superseded_by: V10-original-left-layout-variable-split
    review_assets:
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-final-fit-v1.png]]"
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/slide-01-full-visual-preview-v1.png]]"
    maintainable_sources:
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-final-fit-v1.html]]"
    qa_note: 左图 PNG 为1728×1480，11条路线均使用完整可用宽度且无裁切、重叠或溢出；整页 PNG 为1920×1080，标题保持单行，左右主体等高，底部两条洞察完整，DOM溢出检查为零。
  - id: V10-original-left-layout-variable-split
    gate: visual
    proposal: 左图恢复V4已批准的原始短节点链条版式，并从原维护源直接裁取四层主体；整页主体采用约39:61的非等分结构，左图保持自然比例，右侧继续使用四层综合说明。标题与底部洞察总结栏保持模板样式不变。
    owner: user
    state: user_approved
    approval_quote: 直接进入ppt构建
    review_assets:
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-original-layout-cropped-v1.png]]"
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/slide-01-full-visual-preview-v2.png]]"
    maintainable_sources:
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.html]]"
      - "[[80_presentations/llm-era-cicd-infrastructure/assets/slide-01-full-visual-preview-v1.html]]"
    qa_note: 左图由已批准原始HTML的.stack主体直接裁取，PNG为1456×1588；整页PNG为1920×1080，主体列宽700px与1066px，左图实际显示678×740px，所有标题、证据、响应和底部洞察均无裁切、重叠或DOM溢出。
  - id: P1-final-pptx-build
    gate: production
    proposal: 按已批准的原始左图版式和自适应左右比例生成最终单页 PPTX；左侧为一个 PNG 对象，右侧四层、标题与底部洞察均为 PowerPoint 原生对象。右侧四层分别分组，底部洞察单独分组；公司变化、产品状态、证据边界与来源写入演讲备注。
    owner: user
    state: user_approved
    approval_quote: 直接进入ppt构建
    output: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/outputs/llm-era-cicd-infrastructure-agent-evolution.pptx
    qa_note: 已在 Microsoft PowerPoint 中完成五个原生对象组，保存后关闭并重开验证；独立渲染无裁切或重叠，slides_test 无越界，editability 检查通过，noGrp/noTextEdit/noSelect/noMove/noResize 均为0。
  - id: T4-v4-space-budget
    gate: structural
    proposal: 页面纵向约为标题15%、中间主体67%、底部启示18%；中间主体横向约为左图64%、右侧说明36%。左图每条轨道最多四家公司或产品信号和一条趋势说明；右侧每个说明块最多三行；底部保持 Agent-ready 底座、反馈经济性、自治扩张条件三项，每项最多两行。主图与说明使用原生可编辑形状和文本，允许区域比例在制作时上下浮动2个百分点，文字字号不得低于批准的视觉基线。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T4-v5-equal-split
  - id: T4-v5-equal-split
    gate: structural
    proposal: 中间主体横向采用左图50%、右侧说明50%的等分结构。
    owner: user
    state: user_approved
    approval_quote: 左右占比一半一半
    superseded_by: T8-v1-original-layout-natural-width
  - id: T4-v6-vertical-budget-and-object-policy
    gate: structural
    proposal: 页面纵向约为标题15%、中间主体67%、底部启示18%。左图每条轨道最多四家公司或产品信号和一条趋势说明；右侧每个说明块最多三行；底部保持 Agent-ready 底座、反馈经济性、自治扩张条件三项，每项最多两行。左图先生成可审核 PNG，最终页面中的说明和启示保持原生可编辑文本；允许区域比例在制作时上下浮动2个百分点，文字字号不得低于批准的视觉基线。
    owner: user
    state: proposed
    approval_quote:
    superseded_by: T4-v7-left-asset-review-first
  - id: T4-v7-left-asset-review-first
    gate: structural
    proposal: 暂不规定标题、主体和底部启示的纵向占比；先按已批准的左图内容生成一张独立低保真 PNG，仅用于审核四条演进轨道的结构和信息组织。整页纵向比例、最终字体、颜色和品牌呈现均在左图审核后另行决策。
    owner: user
    state: user_approved
    approval_quote: Q3：先不要规定占比，直接看左图
  - id: T4-v8-left-asset-aspect
    gate: structural
    proposal: 左侧 PNG 采用横向宽度:纵向高度=4:5 的固定比例，用于占据 PPT 中间主体的左半区；嵌入时保持原比例，不拉伸。
    owner: user
    state: user_approved
    approval_quote: 该图是占据ppt左侧的图，所以 长:宽=4:5
feedback_log:
  - id: V10-restore-original-left-layout-and-adjust-ratio
    response_quote: 左边比较奇怪，期望能恢复成一开始的版式，如果有留白，就把左右两边的比例做一些调整
    outcome: equal_spread_left_rejected_original_short_node_chain_restored_and_middle_split_changed_from_equal_to_natural_width_39_61_candidate
  - id: V9-left-right-whitespace
    response_quote: 新左图的右边留白有点多啊
    outcome: company_route_changed_from_content_width_layout_to_full_width_equal_spread_three_stage_layout_without_content_change
  - id: T7-adaptive-space-not-fixed-percentage
    response_quote: Q1 同意；Q2：不一定要进准占比，美观就行
    outcome: left_crop_and_native_column_header_approved_fixed_percentage_budget_replaced_with_template_anchored_adaptive_space_rule
  - id: S2-use-agent-long-horizon-task
    response_quote: 改成Agent长程任务
    outcome: one_line_title_revised_to_user_specified_agent_long_horizon_task_phrase
  - id: S2-agent-new-load-wording-rejected
    response_quote: agent新负载感觉比较奇怪
    outcome: abstract_agent_new_load_phrase_replaced_with_long_running_multi_turn_cross_tool_agent_task_description
  - id: T7-title-must-be-one-line
    response_quote: 标题就一行
    outcome: two_line_treatment_rejected_semantic_gate_reopened_with_compact_single_line_title
  - id: T6-region-grouping-approved
    response_quote: 同意
    outcome: four_independent_right_layer_groups_one_bottom_insight_group_and_one_left_png_object_locked
  - id: T6-right-editable-object-policy
    response_quote: 右侧的图需要是可编辑的ppt对象，左侧可以用图
    outcome: left_png_allowed_right_title_and_bottom_required_as_native_editable_powerpoint_objects
  - id: T5-restore-template-bottom-insight-format
    response_quote: 这个skill中不是有底部洞察的格式么，不要自由发挥！
    outcome: freeform_bottom_titles_and_cards_withdrawn_template_red_insight_summary_bar_with_two_numbered_lines_locked
  - id: T5-compact-context-and-fast-validation-insights
    response_quote: 第一条的观点可以，整体提升到50-60字；第二条应该提示的是增强快速验证能力吧
    outcome: first_insight_retained_and_compacted_second_recentered_on_fast_validation_with_resource_aware_elastic_scheduling
  - id: T5-reduce-to-context-and-feedback-resource-efficiency
    response_quote: 第一项可以，2提出的资源利用率，速率是一个合适的点，但是表述要改一下，不能说成kpi，3去除吧
    outcome: first_implication_retained_second_reframed_as_diagnostic_feedback_speed_and_resource_utilization_third_removed
  - id: T3-pipeline-wording-approved-bottom-implications-next
    response_quote: Q1 批准；Q2 不用；Q3:先讨论下面的启示
    outcome: pipeline_dynamic_runtime_wording_locked_full_body_composition_deferred_bottom_implications_reopened
  - id: V8-pipeline-wording-clarification
    response_quote: 整体ok，流水线的描述中确定性外壳比较奇怪
    outcome: four_layer_right_asset_approved_pipeline_conclusion_reworded_to_agent_session_and_tool_runtime
  - id: V7-return-to-four-vertical-layers
    response_quote: 依然是从上到下分四层展示
    outcome: two_by_two_layout_rejected_right_returned_to_four_vertically_aligned_synthesis_layers
  - id: V6-keep-left-expand-right-company-evidence
    response_quote: 左边不变，右边的格式和内容调整一下，尤其是对公司证据的说明可以详细一些，让大家好理解
    outcome: left_asset_frozen_right_reframed_as_agent_load_plus_two_by_two_synthesis_cards_with_explanatory_company_evidence
  - id: V5-left-right-role-duplication
    response_quote: 这样的话，左边和右边好像区别不大
    outcome: right_company_rows_rejected_returned_to_structural_gate_with_agent_load_to_common_evolution_synthesis_matrix_proposed
  - id: T3-right-compression-approved-and-visual-authorized
    response_quote: Q1 批准，Q2：保留；Q3: 可以
    outcome: right_visible_structure_locked_english_mechanism_terms_retained_and_style_a_right_review_png_generated
  - id: T3-right-detailed-evidence-sequence
    response_quote: Q1 同意；Q2 每项按照演进结论-》左侧公司的详细说明（能支撑演进结论）-〉驱动原因-》控制边界；先给出较为详细的文字版，然后我们再进行筛选；Q3:同意
    outcome: four_right_side_sections_aligned_to_left_asset_with_detailed_company_evidence_driver_and_boundary_draft_created
  - id: V3-white-background-company-icons-layout
    response_quote: 1. 背景纯白；2. 增加公司图标；3. 排版美观
    outcome: pure_white_logo_enhanced_4x5_left_asset_generated_with_aligned_path_nodes
  - id: V2-style-a-left-only-4x5
    response_quote: 选A；但是图中不用加共同趋势，该图是占据ppt左侧的图，所以 长:宽=4:5
    outcome: style_a_selected_common_trends_removed_from_left_asset_and_exact_4x5_review_png_generated
  - id: V1-right-expression-and-style-redesign
    response_quote: 基于详细说明，再优化一下右图的表达，同时重新设计几版样式，现在的样式有点丑
    outcome: three_full_body_visual_candidates_generated_with_trend_driver_boundary_expression_and_awaiting_user_selection
  - id: T3-move-trends-and-expand-paths
    response_quote: 共同演进趋势放在右边说明里吧，公司差异化路径再详细一些，能够让大家看懂
    outcome: common_trends_moved_to_right_detailed_company_paths_v3_requested
  - id: T3-company-count-guidance
    response_quote: Q1 同意 Q2同意，Q3 小于等于三家公司吧，看排版和重要程度
    outcome: company_specific_structure_and_artifact_boundary_approved_exact_representative_set_to_be_reviewed_in_v2_png
  - id: T3-company-specific-evolution-question
    response_quote: 这里不同公司 是不是会有不同的演讲方式
    outcome: verified_company_mechanisms_differ_current_aggregated_png_requires_structural_reconsideration
  - id: T4-equal-split-and-left-png-request
    response_quote: 左右占比一半一半，然后先生成一个左图的png给我审核一下
    outcome: equal_split_user_approved_png_pending_remaining_structural_approval
  - id: T2-left-right-layout-request
    response_quote: 先看下结构，整体分为左右结构吧，左图，右边说明，上面标题、下面启示
    outcome: proposed_top_title_middle_left_right_bottom_implication_architecture
  - id: T2-diagram-scope-refinement
    response_quote: 这张图，主要体现基础设施+公司+变化趋势说明，其余达成的一些结论或者证据，通过后面的表格或者文字进行叙述
    outcome: proposed_main_diagram_as_infrastructure_company_trend_tracks_and_moved_cross_cutting_conclusions_to_supporting_regions
  - id: T2-diagram-request
    response_quote: 希望能够有一张图
    outcome: incorporated_as_single_main_evolution_diagram
  - id: T1-feedback-economics-display
    proposal: 在底部企业启示区使用“单位有效结果成本”公式与驱动指标模块。
    response_quote: Q1 不需要展示
    outcome: not_approved_keep_as_text_and_source_note
  - id: S2-v1
    proposal: 大模型没有替代 CI/CD，而是把它推向双层控制；Agent 负责理解、尝试和生成候选，基础设施负责验证、授权和发布。
    response_quote: S2不批准，更多的体现的是agent时代这些基础设施要有哪些演进变化，当然增强验证和授权能力也是变化之一
    outcome: not_approved
  - id: S3-hold
    response_quote: S3不批准，先决策S2吧，再看S3
    outcome: deferred_until_S2
  - id: S2-v2
    proposal: Agent 时代，CI/CD 基础设施正从面向人的工具链走向机器可读、受控调用、快速反馈、可验证交付。
    response_quote: S2-v2的四类核心演进没有囊括“包括不限于代码仓、制品仓、流水线，构建系统”吧
    outcome: not_approved
  - id: S2-v3
    proposal: Agent 时代，代码仓扩展任务与上下文，流水线承载受控 Agent 编排，构建系统提供高频验证，制品仓承载供应链事实与信任。
    response_quote: 持保留态度，但是我希望能接着往下，看下面的内容是否可以证明该标题
    outcome: narrowed_after_evidence_audit
  - id: S2-v4
    proposal: Agent 时代，代码仓正扩展为任务与上下文入口，流水线开始承载受控 Agent 运行，构建系统强化高频验证，制品仓向可查询的供应链信任面扩展。
    response_quote: 不需要证明普遍完成转型，只要证明演进方向就够了
    outcome: reframed_as_direction_claim
  - id: S2-v5
    proposal: Agent 时代，CI/CD 基础设施沿四个方向演进；代码仓承载任务与上下文，流水线承载受控 Agent 运行，构建系统支撑高频验证，制品仓转向可查询的供应链信任面。
    response_quote: 感觉好像和agent的关系又不大
    outcome: rejected_missing_causal_link
  - id: S3-a-v1
    proposal: Agent 负载变化到现有基础设施压力到四类对象的演进响应到横切控制条件。
    response_quote: S3-a这个逻辑是已经有证据证明了么
    outcome: revised_after_evidence_audit
  - id: S3-a-v2
    proposal: Agent 负载变化驱动四类基础设施分别暴露压力并产生演进响应；身份、Sandbox、Policy、审计和成本贯穿所有响应。
    response_quote: 看上去演进响应全是关于安全的？
    outcome: rejected_overweighted_governance
  - id: S3-b-v2
    proposal: 四条价值演进趋势，其中制品仓明确纳入 Model、Skill、MCP 等新型 Agent 资产。
    response_quote: Q1批准；Q3 不需要
    outcome: approve_four_trend_structure_remove_new_agent_asset_scope
  - id: S3-evidence-narrowing
    proposal: 将四类演进限定为多个厂商已开始产品化的方向，并将旧角色被替代改为在既有角色上增加 Agent-ready 能力；制品仓及其相邻供应链平台提供供应链事实与可验证交付证据。
    response_quote: Q1 接受，Q2:同意，Q3 不用；补充说明：构建系统还包括底层资源管理
    outcome: accepted_with_build_execution_resource_plane_added
  - id: S3-a-v5-scope
    proposal: 保留“制品仓及其相邻供应链平台”，并在构建系统内部突出底层资源管理。
    response_quote: Q1 同意 Q2不用 Q3 不用可以突出
    outcome: approved_keep_artifact_scope_and_emphasize_build_resources
  - id: S3-c-v2-cost-evidence
    proposal: 若有产业证据，则在投资重点中突出底层算力利用率和成本。
    response_quote: Q1：同意；Q2:这块需要给出业界的洞察证据支撑，如果有就突出；Q3:还好
    outcome: evidence_confirmed_reopen_as_S3-c-v3
visual_baseline:
  file: /Users/zhujiayi/.codex/skills/build-insight-pptx/assets/insight-process-table-template.pptx
  origin: reference
  reuse_mode: style_reuse
  approved_left_asset:
    file: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.png
    origin: generated
    aspect_ratio: "4:5"
    maintainable_source: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.html
    approval_quote: Q1批准；Q2. 合适，Q3.保留
  final_fit_left_candidate:
    file: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-final-fit-v1.png
    origin: generated
    aspect_ratio: "216:185"
    maintainable_source: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-final-fit-v1.html
    approval_quote:
    superseded_by: original_layout_cropped_candidate
  original_layout_cropped_candidate:
    file: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-original-layout-cropped-v1.png
    origin: crop_from_approved_generated_asset_source
    aspect_ratio: "364:397"
    maintainable_source: /Users/zhujiayi/personal/02_knowledge/03_cicdInsight/80_presentations/llm-era-cicd-infrastructure/assets/left-company-paths-style-a-4x5-v2.html
    approval_quote: 直接进入ppt构建
---

# Agent 长程任务正推动 CI/CD 基础设施向上下文协作、动态运行、快速验证与供应链事实演进

> [!abstract] 页面任务
> 用一页回答代码仓、流水线、构建系统和制品仓在大模型到来后发生了什么结构性变化，以及企业应该如何放大 Agent 能力而不让概率性判断直接获得确定性副作用。

## 语义门提案（等待用户明确批准）

1. **S1｜受众与页面类型：已批准。** 面向 CTO、研发效能负责人和平台工程负责人，采用“技术洞察 + 决策启示”的单页。批准原文：“S1批准”。
2. **S2-v6 有界主张与 S2-v9 精确标题：已批准。**
   - **标题：** “Agent 长程任务正推动 CI/CD 基础设施向上下文协作、动态运行、快速验证与供应链事实演进。”
   - **有界主张：**
     - **代码仓：** 从代码与协作事实源，扩展为 Agent 的任务入口、上下文系统、配置载体和候选变更出口；
     - **流水线：** 在固定 DAG 和 Runner 之外，开始承载确定性外壳中的受控 Agent 运行、会话执行和审计；
     - **构建系统：** 既有 Graph、Affected、Cache、Remote Execution 和隔离复现能力，因 Agent 高频试错而被强化为反馈与成本 Backpressure；
     - **制品仓：** 从包与二进制存储，向 Agent 可查询的依赖情报、SBOM / Provenance / Evidence 和受限供应链行动面扩展。
   - **已批准的证据边界：** 只需证明这些是可观察、可交叉验证的演进方向，不要求证明行业已经普遍完成转型、成熟度一致或存在统一 ROI。
   - **横切关系：** 身份、权限、Sandbox、Policy、审计、Lineage 和成本不是第五个并列仓库，而是贯穿四类基础设施的共同演进要求；其呈现逻辑由 S3 决定。
   - **批准原文：** “批准，后面再看看是否需要进行调整”。如后续正文与标题冲突，再重新打开 S2。

## S3-v5｜阅读逻辑、趋势与企业启示（分项审批中）

### S3-a-v5｜页面阅读逻辑与范围（已批准）

主线是 **多个 CI/CD 厂商针对 Agent 新任务模式已经开始的能力扩展**：

`Agent 长时、并发、迭代、跨工具任务 → 上下文与协作 / 编排与运行 / 反馈及执行资源 / 供应链智能与交付证据`

安全与治理是护栏：

`身份 / 权限 + Sandbox / Network + Policy / Oracle + 审计 / Lineage`

- **分析对象：** 代码仓、流水线、构建系统、制品仓；四者并行响应 Agent 负载变化；
- **每个对象优先回答：** Agent 带来什么新负载、基础设施新增什么价值、现有能力为什么升值；
- **控制问题后置为边界：** 哪些身份、执行、Oracle 和证据不能交给模型自行决定；
- **厂商角色：** GitHub、GitLab、CircleCI、Nx、Buildkite、JFrog、Cloudsmith、Sonatype 等只作为机制与状态证据，不作为页面比较对象。

| 基础设施 | Agent 带来的核心压力 | 主要价值演进 | 必要但非主线的控制边界 |
|---|---|---|---|
| 代码仓 | Agent 需要持续理解任务、代码、历史、规则并接续长任务 | 从代码协作面扩展为任务、上下文、Agent 配置和候选变更协作面 | Ruleset、CODEOWNERS、Context Scope、Agent Config Review |
| 流水线 | Agent 需要异步运行、动态规划、调用多个 Tool 并传递中间结果 | 从固定 Job 编排扩展为 Agent Session、事件驱动执行、Tool Orchestration 和可观测运行时 | 身份、Tool / Network Scope、预算、Safe Output、独立 Gate |
| 构建系统 | Agent 高频、多轮、并行试错放大启动、排队、日志、计算容量与成本压力 | 任务图、Affected、Cache、Remote Execution、Runner / Worker Pool、Snapshot / 可复用隔离环境和动态伸缩共同形成反馈与执行资源面 | 隔离、可复现、可信 Cache Writer、Task Oracle、容量与成本预算 |
| 制品仓及相邻供应链平台 | Agent 需要在选择依赖、判断风险和准备发布时获得实时、可追溯的供应链事实 | 在二进制存储之上增加依赖情报、供应链知识和可验证交付证据 | Digest、Provenance、Policy、Promotion / Waiver / Delete 权限 |

**流水线与构建系统的边界：** 流水线负责触发、步骤依赖、门禁、审批与发布编排；构建系统的执行资源面负责依据任务图和受影响范围，把已经选定的 Build / Test 分配到远程执行节点或 Runner / Worker Pool，并通过 Cache、Snapshot、可复用隔离环境与按负载伸缩控制反馈延迟和计算成本。

#### S3-a 证据审计

| 逻辑环节 | 直接证据 | 证据类型 | 审计判断 |
|---|---|---|---|
| Agent 形成不同于人工提交的负载 | GitHub / GitLab 已提供 Issue、评论、事件触发的异步 Agent Session；CircleCI 明确讨论多 Agent、多轮验证带来的启动、Fan-out、日志 Token 和计算成本；GitHub Agentic Workflows 同时计量 Actions Minutes 与 AI Credits | 官方产品机制 + 第一方工程观察 | **事实成立，high** |
| 新负载对既有基础设施形成压力 | 现有资料分别记录上下文获取、动态 Tool 副作用、低延迟反馈、短期身份、审计与 Provenance 需求 | 跨来源机制推断；没有单一实验同时验证整条链 | **方向成立，medium-high；不是严格因果实验** |
| 四类基础设施出现对应响应 | 代码仓任务 / Context / Agent Config，Runner 内 Agent Flow，Graph / Cache / Remote Execution / 动态 Agent 分配 / Microbuild，制品情报 / SBOM / Evidence 均有跨厂商实现 | 多个官方产品和规范样本 | **演进方向成立，high；成熟度不均** |
| 横切控制必须覆盖这些响应 | GitLab Composite Identity、GitHub / Harness Sandbox 与权限约束、SLSA / Artifact Attestation、Agent Audit、Run Budget 分别覆盖身份、执行、证据、审计和成本 | 官方产品机制 + 正式规范 | **控制需求成立，high；不支持把它们放在线性链条末端** |

**结论：** 多个厂商的一手产品机制共同支持四类能力扩展方向。S3-a-v5 将页面主线放在上下文与协作、编排与运行、反馈及执行资源、供应链智能与交付证据；安全与治理只承担“允许这些能力可靠扩张”的边界角色。四分类及 Agent 因果关系仍是跨来源机制推断，而不是某个来源直接给出的统一框架。

### S3-b-v4｜四条价值演进趋势与证据边界

1. **上下文与协作：** 在代码存放、Issue 与人工 Review 之上，增加 Agent 可消费的任务上下文、仓库规则、Agent Config 与候选变更协作面；
2. **编排与运行：** 在固定 Job 与预定义步骤之上，增加 Agent Session、事件驱动 Tool 调用、动态执行路径与可观测运行时；流水线继续负责触发、依赖、门禁与发布编排；
3. **反馈及执行资源：** 既有 Graph / Affected / Cache / Remote Execution 与 Runner / Worker Pool 被强化为高频并发验证的执行资源面，并增加 Snapshot / 可复用隔离环境、动态伸缩以及延迟和计算成本治理；
4. **供应链智能与交付证据：** 在包与二进制存放之上，增加 Agent 可查询的实时依赖情报、SBOM / Provenance / Evidence 和可验证交付证据。

证据只需证明多个厂商已开始沿这些方向演进；不表示旧模式被替代，也不外推普及率、成熟度一致性、统一 ROI 或全链路自治。身份、权限、Sandbox、Network、Policy、Oracle、审计与 Lineage 是四条趋势的控制边界，不单列为页面主线。

### S3-c-v3｜企业启示（已批准）

1. **平台目标：** 不要只给每个工具增加一个 AI 入口；应把代码仓、流水线、构建执行资源和制品 / 供应链平台建设成共同的 Agent-ready 底座，使上下文可读取、任务可编排、验证可快速调度、供应链事实可查询。
2. **反馈经济性：** Agent 任务同时消耗执行算力与推理额度。平台应观测反馈时延、Queue Time、Cache Hit、CPU / 内存利用率、Runner / Core Minutes、AI Credits / Token，并以“经独立验证后被接受的结果成本”作为企业自定义复合指标；再通过 Affected、Cache、Snapshot / 可复用隔离环境、Remote Execution、动态 Worker 与 Run Budget 优化。
3. **自治扩张条件：** 安全治理不占页面主线，但决定自治上限；只有动作具备独立 Oracle、任务级身份、可追溯证据和回退路径时，才扩大到自动合并、制品晋级或发布。

#### S3-c-v3 反馈经济性证据审计

| 证据 | 可直接证明 | 必须保留的边界 |
|---|---|---|
| [CircleCI Agentic Validation（2026-06-05）](https://circleci.com/blog/agentic-validation-needs-different-infrastructure/) | 迭代式 Agent 会放大完整 CI 的冷启动、Fan-out、日志 Token 与计算成本；Cache、Incremental、可复用 MicroVM / Snapshot 已用于缩短反馈和控制成本 | 3x Token Efficiency、10–20x Core Cost 为 CircleCI 自测，不外推为行业基准 |
| [GitHub Agentic Workflows](https://docs.github.com/en/enterprise-cloud@latest/copilot/concepts/agents/about-github-agentic-workflows) | 总成本拆为 Actions Minutes 与推理成本；Run 可查看 Duration、Token、AIC 估算并设置 `max-ai-credits` | 当前为 Public Preview；AIC 是估算，不等于最终账单 |
| [Nx Dynamic Agents](https://nx.dev/docs/features/ci-features/dynamic-agents)、[Nx Resource Usage](https://nx.dev/docs/features/ci-features/resource-usage) | 按 PR 规模动态分配执行节点以平衡成本和速度，并产品化 CPU / 内存平均值、峰值、容量和 Task 级归因 | Nx Agent 指执行 Worker，不是 LLM Agent；资源观测本身不证明 Agent 因果或统一 ROI |

**审计结论：** 可以突出“反馈经济性”与资源利用率。反馈时延、Queue Time、Cache Hit 和 CPU / 内存利用率属于可操作的运营指标；“经独立验证后被接受的结果成本”是企业自定义复合指标，不是现成的跨平台行业标准。

## T2-v4—T4-v6｜整页结构提案（低保真）

```text
┌──────────────────────────────────────────────────────────────┐
│ 标题主张：Agent 新负载驱动四类 CI/CD 基础设施演进            │
├───────────────────────────────────────┬──────────────────────┤
│ 左图 50%：基础设施 × 公司差异化路径   │ 右侧说明 50%         │
│                                       │                      │
│ [代码仓] → GitHub：Issue → 环境 → PR  │ ① 代码仓共同趋势     │
│          → GitLab：Event → Flow → MR  │ 任务上下文与候选协作  │
│                                       │ 仓库规则与变更出口    │
│ [流水线] → GitHub / Buildkite /       │ ② 流水线共同趋势     │
│          Harness 的差异化运行路径      │ Agent 编排与可观测运行│
│                                       │                      │
│ [构建与资源] → CircleCI / Nx / Bazel  │ ③ 构建共同趋势       │
│          的反馈与资源路径              │ 高频验证与反馈经济性  │
│                                       │                      │
│ [制品与供应链] → Sonatype / Cloudsmith│ ④ 制品共同趋势       │
│          / JFrog 的事实与行动路径      │ 供应链事实与交付证据  │
│                                       │                      │
│                                       │ 证据及共同控制边界    │
├───────────────────────────────────────┴──────────────────────┤
│ 企业启示：Agent-ready 底座｜反馈经济性｜自治扩张条件          │
└──────────────────────────────────────────────────────────────┘
```

### 区域契约

| 区域 | 回答的问题 | 对主张的贡献 | 内容预算 |
|---|---|---|---|
| 标题 | 为什么 Agent 会改变 CI/CD 基础设施？ | 给出已批准的有界主张 | 最多三行 |
| 左侧公司差异化路径主图 | 同类基础设施中，不同公司的 Agent-ready 实现路径有什么差异？ | 用公司级路径证明产业变化不是同质功能清单 | 中间宽度50%；每类最多三家公司；每家公司一条“入口 / 触发 → 机制 → 输出 / 边界”短路径 |
| 右侧共同趋势说明 | 从公司差异化路径中能够归纳出哪些共同方向，证据能够证明到哪里？ | 归纳四类共同演进趋势，并补足 Agent 因果与证据边界 | 中间宽度50%；四条趋势说明，底部一条证据与控制边界 |
| 企业启示 | 企业应该建设什么、优化什么、何时扩大自治？ | 把行业方向转化为平台决策 | 三项，每项最多两行 |

### 左图内容骨架

| 基础设施轨道 | 代表公司 | 左图呈现的差异化路径 |
|---|---|---|
| 代码仓 | GitHub、GitLab | GitHub：Issue / 评论 → 临时 Actions 环境 → Branch / Draft PR；GitLab：Issue / MR / Event → Custom Flow / Runner → MR + CI Log |
| 流水线 | GitHub、Buildkite、Harness | GitHub：Markdown 意图 → Actions YAML → Runner / Policy；Buildkite：Build / Log / Trigger MCP → Agent Step → 分析或触发；Harness：Autofix / Worker Agent → 受控凭据与网络 → 修复 / 复验 |
| 构建系统与执行资源 | CircleCI、Nx、Bazel | CircleCI：预热 Snapshot → Sidecar / Microbuild → 增量反馈；Nx：Graph / Affected / 历史耗时 → Cache / 动态 Worker → Task 复验；Bazel：Hermetic Action Graph → RBE / Remote Cache → 确定性结果复用 |
| 制品仓及相邻供应链平台 | Sonatype、Cloudsmith、JFrog | Sonatype：依赖请求 → 版本 / 漏洞 / License / 维护情报 → 推荐版本；Cloudsmith：MCP 查询 → 非破坏性制品操作 → Policy 激活仍人工；JFrog：Repository / Xray / SBOM / Evidence / Release → Agent Package 分发与治理操作 |

左图中的公司只表示差异化实现样本，不承担厂商能力排名；共同趋势、产品状态和证据边界放到右侧说明。

### 右侧说明骨架

| 共同趋势 | 可见说明 |
|---|---|
| 代码仓 | 从代码存放和人工协作，扩展为 Agent 的任务上下文、仓库规则和候选变更协作面 |
| 流水线 | 从固定 Job 编排，扩展为 Agent Session、动态 Tool 调用与可观测运行 |
| 构建与执行资源 | 任务图、影响分析、缓存、远程执行和动态资源调度升级为高频验证与反馈经济性底座 |
| 制品与供应链 | 从包与二进制存放，扩展为可查询的依赖 / 供应链事实和可验证交付证据 |
| 证据与控制边界 | 多厂商信号只证明方向与机制；身份、隔离、Policy、Oracle、审计和 Lineage 决定自治上限 |

**明确不展示：** 复合成本公式、厂商自测倍数、公司排名、全行业普及率或统一 ROI。

### 左图 PNG 结构评审稿

- 文件：`../assets/left-infrastructure-company-trends-v1.png`
- 可维护源：`../assets/left-infrastructure-company-trends-v1.dot`
- 尺寸：1800 × 1307
- 状态：`superseded-by-company-path-v2-candidate`
- 用途：只审核四类基础设施、代表公司和变化趋势的组织方式；不作为最终配色、字体、品牌 Logo 或整页比例基线。

### 左图 PNG 公司差异化路径评审稿

- 文件：`../assets/left-infrastructure-company-paths-v2.png`
- 可维护源：`../assets/left-infrastructure-company-paths-v2.dot`
- 尺寸：1800 × 1240
- 状态：`superseded-by-detailed-path-v3-candidate`
- 代表集：代码仓 GitHub / GitLab；流水线 GitHub / Buildkite / Harness；构建 CircleCI / Nx / Bazel；制品与供应链 Sonatype / Cloudsmith / JFrog。
- 用途：审核“公司差异化路径 → 共同趋势”的结构、公司取舍与机制短语；不作为最终视觉基线。

### 左图 PNG 详细公司路径评审稿

- 文件：`../assets/left-infrastructure-company-paths-v3.png`
- 可维护源：`../assets/left-infrastructure-company-paths-v3.dot`
- 尺寸：1800 × 1662
- 状态：`awaiting-user-detailed-path-review`
- 代表集：代码仓 GitHub / GitLab；流水线 GitHub / Buildkite / Harness；构建 CircleCI / Nx / Bazel；制品与供应链 Sonatype / Cloudsmith / JFrog。
- 用途：审核“基础设施 → 公司差异化路径”的信息充分性和可讲解性；共同趋势已移出左图。

### T3-v5｜公司差异化路径候选

| 基础设施 | 公司 / 产品 | 差异化路径 | 共同趋势 |
|---|---|---|---|
| 代码仓 | GitHub | Issue / PR 评论 → 临时 Actions 环境 → Branch / Draft PR | 代码仓扩展为 Agent 的任务上下文与候选变更协作面 |
| 代码仓 | GitLab | Issue / MR / Pipeline Event → Custom Flow / Runner → MR / CI Log | 同上 |
| 流水线 | GitHub | 自然语言 Markdown → 编译为 Actions YAML → Runner / Policy 执行 | 流水线增加 Agent 编排、动态工具调用和可观测运行 |
| 流水线 | GitLab | 事件触发 → Custom Flow → Runner / Composite Identity | 同上 |
| 流水线 | Buildkite | MCP 读取 Build / Log / Trigger + Agent Step 嵌入 Pipeline | 同上 |
| 流水线 | Harness | CI Autofix / Worker Agent → 受限修复与复验循环 | 同上 |
| 构建与执行资源 | CircleCI | Chunk Sidecar / Microbuild → Snapshot / 增量同步 / 精简反馈 | 构建执行资源强化为 Agent 高频验证与反馈经济性底座 |
| 构建与执行资源 | Nx | Project Graph / Affected / Cache / Dynamic Agents / Self-Healing | 同上 |
| 构建与执行资源 | Bazel | Hermetic RBE / Remote Cache 既有底座升值，并非 Agent 专项产品 | 同上 |
| 制品与供应链 | Sonatype | 在 Agent 选择依赖时提供版本、漏洞、许可证和推荐版本 | 制品及相邻供应链平台扩展为可查询的事实与交付证据面 |
| 制品与供应链 | Cloudsmith | MCP 查询漏洞 / 版本并执行有限的非破坏性制品操作 | 同上 |
| 制品与供应链 | JFrog | MCP 暴露 Repository / Xray / SBOM / Evidence / Release，并分发 Agent Package | 同上 |
| 相邻交付证据 | GitHub Artifact Attestations / SLSA | 将 Workflow / Commit / Builder / Environment 与 Artifact Digest / Provenance 绑定 | 作为右侧证据说明，不与制品仓厂商并列 |

## S2 的 Agent 因果链（低保真语义草图）

| Agent 带来的负载变化 | 为什么原有基础设施不够 | 对应的基础设施演进 |
|---|---|---|
| Agent 需要理解代码、Issue、历史、规则和运行结果，并以 Branch / PR / MR 交付候选 | 仅给人看的页面和非结构化日志难以稳定供机器检索、引用和持续接续任务 | **代码仓：** 扩展为任务入口、机器可读上下文、Agent 配置和候选变更出口 |
| Agent 会长时间异步运行、跨工具调用并产生写入副作用 | 固定 DAG 能执行命令，但不能单靠 Prompt 约束动态计划、身份、Tool、网络、预算和安全输出 | **流水线：** 在确定性 Runner / Policy 外壳内承载并约束 Agent 执行 |
| Agent 会多轮试错、并行生成候选，每轮都需要可消费的失败证据 | 把完整 CI 原样用作每轮内环会放大冷启动、Fan-out、排队、日志 Token 和计算成本 | **构建系统：** 以 Graph、Affected、Cache、Remote Execution、隔离与 Task Oracle 加速验证反馈 |
| Agent 会选择依赖、调查风险，并可能触发上传、晋级、豁免或删除等动作 | Tag、单次 Pass / Fail 和模型摘要不足以证明输入、Builder、Digest、策略与动作授权 | **制品仓：** 向 Agent 提供实时供应链事实、SBOM / Provenance / Evidence，并把高风险动作交给独立授权 |

## S2 标题证明审计（低保真语义草图）

| 标题分句 | 可核验事实 | 推理桥 | 必须保留的边界 | 审计结论 |
|---|---|---|---|---|
| 代码仓正扩展为任务与上下文入口 | GitHub Cloud Agent 可由 Issue、Agents UI、PR 评论发起并交付 Branch / Draft PR；GitLab Custom Flows 可由 Issue、MR、评论和 Pipeline Event 触发；仓库开始版本化 `AGENTS.md`、Setup、Agent / MCP 配置 | 任务、上下文、Agent 配置、候选变更和接受规则在 Repository 汇合，使其角色超出代码存储与人类协作 | Repository 不是唯一上下文系统，也不拥有外部云和生产授权；部分第三方 Agent 能力仍为 Preview | **可以证明，high**；对应 [[50_deepdives/llm-era-cicd-infrastructure/50_findings#F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面|F2]] |
| 流水线开始承载受控 Agent 运行 | GitHub Agentic Workflows 在 Actions Runner / Policy 内运行；Buildkite 提供 Pipeline Agent Step 与 Build / Log / Trigger Tool；GitLab Flow 在 Runner 上执行并使用服务身份或组合身份 | 动态推理被嵌入既有 Runner、权限、网络和输出边界，而不是用 Prompt 整体替代 Pipeline | GitHub Agentic Workflows 仍为 Public Preview；不同平台的 Tool Governance 和审计成熟度不一致 | **只支持“开始承载”，不支持普遍完成，medium-high**；对应 [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C05、C06]] |
| 构建系统强化高频验证 | Nx 以 Project Graph、Affected、Remote Cache 和任务复验缩小反馈范围；CircleCI 以快照、增量同步和 Microbuild 探索 Agent 内环；Bazel RBE / Cache 提供既有远程执行与复用基础 | Agent 的多轮试错放大反馈延迟、并发和日志成本，使影响分析、缓存、隔离与 Task Oracle 从提速项升为运行前提 | Graph、Cache、RBE 不是大模型新发明；缺少跨行业数据证明所有团队都已出现相同负载变化 | **支持“强化”，不支持“Agent 发明了新构建系统”，medium-high**；对应 [[50_deepdives/llm-era-cicd-infrastructure/50_findings#F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制|F4]] |
| 制品仓向可查询的供应链信任面扩展 | Sonatype、Cloudsmith、JFrog 已把版本、漏洞、许可证、SBOM、Evidence、Release 等能力暴露为 Agent Tool；GitHub Artifact Attestations 把 Workflow、Commit、Environment 与 Artifact Digest 绑定；JFrog 开始管理 Skill / MCP 等 Agent 资产 | Agent 在选择依赖、调查风险或准备发布动作时，需要实时供应链事实和机器可验证证据，制品仓由被动存储转向可查询的事实与信任入口 | JFrog MCP / Skills 等状态包含 Beta / Open Beta；Attestation 不证明安全或业务正确；高风险写入仍需独立授权 | **可以证明方向，high；自治程度受限**；对应 [[50_deepdives/llm-era-cicd-infrastructure/50_findings#F5：制品仓从存储面扩展为供应链信任面与受限行动面|F5]] |

### 审计后的判断

正文能够以跨厂商事实和机制推断证明四个演进方向。根据用户批准的证据边界，页面不承担“行业已普遍完成转型”的成熟度命题；标题因此改为显式的“四个演进方向”，产品状态和采用边界留在各方向的证据说明中。

> [!warning] 旧方案暂停
> 以下从“页面分析框架”开始的双层矩阵与结构草稿属于未批准的旧 S3，不构成当前提案，也不会据此推进页面制作。S2 批准后再重新设计。

## 页面分析框架

`基础设施对象 × Agent 上下文/执行面 × 确定性控制面`，再用身份、证据和成本解释横切变化。

## 区域契约

| 区域 | 回答的问题 | 对主张的贡献 | 内容预算 |
|---|---|---|---|
| 结论标题 | 变化的本质是什么？ | 明确“角色分层，不是替代” | 1 行 |
| 双层矩阵 | 四类基础设施分别新增或强化了什么？ | 证明同一个双层结构贯穿代码、验证、构建与制品 | 5 列 × 3 行 |
| 横切变化 | 为什么身份、证据和成本成为主约束？ | 解释规模化采用 Agent 的新增治理压力 | 3 项 |
| 企业启示 | 应该按什么顺序建设与放权？ | 将架构判断转成条件化建议 | 2 条 |

## 主矩阵文案

| 控制层 | 代码仓 | 流水线 | 构建系统 | 制品仓 |
|---|---|---|---|---|
| Agent 上下文与执行面 | Issue / Session / Agent 配置；Branch / PR / MR 候选 | Agent Step；日志归因；低延迟验证内环 | Graph / Affected 上下文；增量、缓存、远程执行 | 漏洞 / SBOM / Evidence 查询；受限动作；Agent 行为资产 |
| 确定性接受与发布面 | Ruleset、CODEOWNERS、Required Checks、Review | 完整 Build / Test / Scan / Policy；Environment Gate | 干净隔离、可复现、Trusted Builder、Task Oracle | Digest、Provenance、Attestation、Promotion Policy |

## 横切变化

1. **身份：** 共享 Bot Token → 任务级短期委托与权限求交；
2. **证据：** Pass / Fail → Session—Commit—Builder—Digest—Policy Lineage；
3. **成本：** Runner 分钟 → 计算 + 推理 + 尝试次数 + 反馈延迟 + Verified Outcome。

## 页底洞察

1. **先补底座：** 机器可读上下文、隔离运行时、低延迟验证、任务级身份与全链路证据。
2. **再逐级放权：** Agent 先生成候选；Merge、Promotion、Deploy 必须由独立 Gate 按 Commit、Digest 或 Plan 接受。

## Claim Ledger

| Claim ID | 页面表述 | 类型 | 证据 | 推理桥 | 边界 | 状态 | 区域 |
|---|---|---|---|---|---|---|---|
| P01 | 大模型没有替代 CI/CD，而是形成双层控制 | 分析推断 | [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map#核心矩阵|C01]] | 多个独立平台把 Agent 接入现有 Repository、Runner、Pipeline 和 Artifact 基础设施，同时保留固定 Gate | 不代表全链路自治成熟 | corroborated | 标题 |
| P02—P05 | 四类基础设施分别形成 Agent 面与控制面 | 事实 + 推断 | [[80_presentations/llm-era-cicd-infrastructure/source-map|P02—P05]] | 产品机制显示新增接口与运行时，规则、测试、构建和制品证据仍决定接受 | 产品状态不均衡，构建基本功多为既有能力升值 | verified / corroborated | 主矩阵 |
| P06 | 身份、证据和成本成为规模化主约束 | 分析推断 | [[80_presentations/llm-era-cicd-infrastructure/source-map|P06]] | Agent 增加多轮、并行、跨工具调用和副作用，传统 Bot Token 与 Run Pass/Fail 不足以归因和控权 | 身份与成本口径尚未跨平台统一 | corroborated | 横切变化 |
| P07 | 先补底座，再逐级放权 | 企业建议 | [[50_deepdives/llm-era-cicd-infrastructure/60_playbook#八、推荐的近期优先级|Playbook]] | 每扩大一级写权限，都先建立相应的外部 Oracle、短期身份、证据绑定和回退 | 仅适用于 Agent 获得写权限或触达发布链路的场景 | conditional | 页底洞察 |

## 来源

- [[80_presentations/llm-era-cicd-infrastructure/source-map|页面 Source Map]]
- [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map|证据矩阵]]
- [[50_deepdives/llm-era-cicd-infrastructure/50_findings|Findings]]
- [[50_deepdives/llm-era-cicd-infrastructure/60_playbook|企业演进手册]]

---
title: Slide 02 - Harness CI Agent Pipeline
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
  - company/harness
status: draft
slide: 2
content_status: archived
visual_status: pending
as_of: 2026-07-21
---

# Harness 把 Agent 插入 CI Pipeline，但把成功标准留给原始 Gate

> [!abstract] 页面唯一主张
> Harness CI 的关键机制不是让一个聊天 Agent 接管流水线，而是把开放式诊断、测试生成和修复放进受治理的 Pipeline Step；触发、轮次、工具、权限和失败策略由 Pipeline 约束，构建、测试、扫描与人工评审继续证明结果是否可信。

## 一、页面沟通任务

读完这一页，CTO、研发效能负责人和平台工程负责人应理解 Harness 如何把 Agent 的概率性判断嵌入确定性 CI，以及为什么“Agent 能运行”与“修复可以合并”仍是两道不同的门。

本页只聚焦 PR、测试和构建失败链路，不展开 Harness 的 CD、GitOps、AI SRE、成本优化或完整 After-Code 产品地图。

## 二、候选观众可见文案

### 标题

Harness 把 Agent 插入 CI Pipeline，但把成功标准留给原始 Gate

### 副标题

Agent 读取 Diff、日志和测试结果，生成诊断、测试或修复；原构建、测试、扫描、Policy 与人工评审决定能否进入主分支。

### 状态信息

- **产品组合：** Worker Agents、Code Quality Agents、DevOps Agent 与 Harness CI Pipeline
- **覆盖阶段：** 代码评审、测试与质量门禁、编译构建，以及跨阶段治理
- **典型输出：** PR Comment、Coverage Report、候选测试、修复分支或 Draft PR
- **自治口径：** 诊断属于 L1；生成可审查修改属于 L2；在沙箱中调用工具并复验可达到受限 L3；不把自动 Merge 或 Release 视为当前可信基线
- **状态口径：** Worker Agents 与 Code Quality Agents 已有正式产品文档；Worker 账户入口、细粒度权限和部分能力仍需管理员、Support 或 Feature Flag 核验

## 三、页面论证链

`PR / CI Failure`
→ Pipeline 收集 Diff、日志、测试与策略上下文
→ Agent Step 在轮次、模型和 Tool Allowlist 内诊断或生成候选修改
→ 原始 Build / Test / Scan 重新运行
→ 未通过则在预算内重试或停止升级
→ 通过后只外化为 Comment、Report、Branch 或 Draft PR
→ Human Review、Branch Protection 与原 Pipeline Gate 决定是否合并

这条链同时证明标题的两部分：Agent 已成为 CI 运行图中的一等 Step，但成功标准和最终写入权仍由 Agent 外部系统持有。

## 四、主作业流：把开放式修复夹在两段确定性 CI 之间

~~~text
PR / CI Failure
        ↓
确定性证据收集
Diff · Build Log · Test Result · Coverage · Scan Finding · Policy Context
        ↓
Agent Step
Instructions · Model Connector · Scoped MCP Tools · Max Turns
        ↓
候选结果
Diagnosis · Suggested Test · Patch · Fix Branch
        ↓
原始 Oracle 复验
Build · Test · Coverage · Scan · OPA
        ↓
失败 ──→ 预算内回到 Agent；超限则停止并升级给人
通过 ──→ Comment / Report / Draft PR
        ↓
Human Review · Branch Protection · Merge Policy
~~~

### 六个关键问题的回答

| 问题 | Harness CI 中的答案 |
|---|---|
| 什么触发 | PR、CI 失败、人工或 API 发起的 Pipeline Run；事件 Trigger 必须单独核验身份 |
| Agent 看什么 | 代码 Diff、Pipeline Execution、Build/Test/Scan 结果，以及通过表达式或 MCP 暴露的批准上下文 |
| Agent 决定什么 | 失败归因、下一步工具选择、候选测试或修复，以及是否在轮次预算内继续 |
| 谁真正执行 | Harness Pipeline、Cloud/Kubernetes Runtime、CI Build/Test/Scan Step 与受限 MCP/API Tool |
| 谁证明成功 | 原始构建、测试、Coverage、Scanner、OPA、Branch Protection 与人工 Review |
| 自治停在哪里 | 可生成并复验候选修改；默认停在可审查输出，不自动获得 Merge 或 Release 权 |

## 五、三个 Agent 入口分别改变 CI 的不同阶段

| 入口 | 生效阶段 | 在 CI 中的职责 | 不能误解为 |
|---|---|---|---|
| DevOps Agent | CI 设计时 | 创建或修改 Pipeline、Stage、Step 与 OPA Policy 草案 | 聊天结果直接拥有运行或生产权限 |
| Worker Agent | CI 运行时 | 作为 Agent Step 读取上下文、多轮调用允许的 Tool、输出诊断或候选修改 | 用 Prompt 替代 Pipeline DAG、RBAC 或 Gate |
| Code Quality Agents | PR / 测试 / 构建链路 | Code Review 发布意见，Coverage 生成测试，AutoFix 诊断失败、验证构建并交付 PR | 三者必然共享同一 Worker Runtime、模型或权限实现 |

页面应把 DevOps Agent 放在主流程左上方作为“设计时入口”，把 Worker/专项 Agent 放进 Pipeline 主链，避免把生成 Pipeline 和运行 Pipeline 画成同一个连续 Agent。

## 六、Agent Definition 决定能力，Pipeline 决定运行边界

Worker Agent 把以下对象组合成可复用定义：

| 组件 | 配置对象 | 页面要表达的职责 |
|---|---|---|
| 工作方法 | Instructions / Task | 说明目标、证据、禁止动作和输出格式；属于软约束 |
| 推理引擎 | Model Connector | 选择 Harness Managed 或客户连接的受支持模型 |
| 工具入口 | MCP Server Connector / Harness Tool | 暴露查询或动作能力；可调用不等于已授权 |
| 运行预算 | Max Turns、Timeout、Inputs / Outputs | 限制循环次数、时延、成本和可传递结果 |
| 执行环境 | Harness Cloud Runtime 或客户 Kubernetes | 隔离 Agent 进程、网络和工作目录 |
| 流程控制 | Pipeline Condition、Matrix、Retry、Failure Strategy | 决定何时运行、如何组合、失败后停止还是回退 |

CI、STO、SCS 和 IaCM Stage 可使用 Harness Cloud Runtime；企业也可以通过 Delegate 在客户 Kubernetes 中运行。运行位置改变数据、网络和运维责任，但不改变“Agent Step 仍受 Pipeline 编排”的基本关系。

## 七、权限边界是逐次求交集，不是一次性给 Agent 一个 Bot Token

### 运行时授权链

~~~text
触发 Principal 的 Harness RBAC
        ∩
Agent 声明的 Resource / Verb Grant
        =
单次 Run 的 Scoped Ephemeral Token

Connector Allowed Tools
        ∩
Agent Allowed Tools
        =
本次 Run 可调用的第三方 Tool
~~~

- Agent、Run、触发 Principal、Tool、参数和结果应进入逐次调用审计。
- Worker Runtime 采用硬化镜像、非特权进程、Credential Broker、Secret Placeholder 与默认拒绝的 Egress Proxy，目标是即使 Agent 进程被攻陷也限制 Blast Radius。
- AI Rules 只能影响模型生成；OPA 才能在 Save/Run 阶段 Warn 或 Block；Build/Test/Scan 等外部 Oracle 才能证明行为结果。
- 当前文档指出，Webhook、Schedule、Artifact、Manifest 等 Trigger 发起的 Worker Run 不能注入某个触发人的 Scoped Token。事件驱动 Agent 在身份与审批模型完成验证前，不应获得高风险写权限。
- 细粒度 Permission / Token Injection 仍需在目标账户确认 `HARNESS_TOKEN_INJECT` 等 Feature Flag 的实际状态。

## 八、外部 Oracle 把“看起来修好了”变成“有证据的候选修复”

| Agent 候选输出 | 必须由谁复验 | 安全外化方式 |
|---|---|---|
| CI 失败原因 | 原日志、可复现步骤与 Build Result | Comment / Diagnosis Report |
| 新增或修改测试 | 独立测试集、Coverage 与业务断言 | Test PR / Coverage Report |
| 代码修复 Patch | 原 Build、Test、Scan 与 Policy 全量回归 | Fix Branch / Draft PR |
| Pipeline YAML 修改 | Schema、OPA、Save Review 与非生产执行 | Pipeline Diff / Review Request |

必须保持两个不变量：Agent 不能修改或跳过用来证明自身成功的原始 Gate；同一个 Agent 不应同时重写关键测试和把该测试作为唯一成功证据。

## 九、页底三条洞察

- **核心差异化：** Harness 没有把 Agent 放在 CI 外部做建议，而是把它变成可编排、可限权、可审计的 Pipeline Step，并复用既有 Build、Test、Scan 和 Policy 作为证明系统。
- **企业启示：** CI Agent 的安全起点应是“只读诊断 → 候选 Patch → 原任务复验 → Draft PR”，权限与自治只在可观测的通过率、误修率和回退数据上逐级扩大。
- **当前边界：** Worker Agents 发布较新，跨客户成功率、独立红队和长期经济性证据仍弱；事件 Trigger 身份与 Feature Flag 也必须在目标账户实测，因此自动 Merge、制品晋级和发布不应由本页机制直接外推。

## 十、产品状态、自治与证据

| 对象 | 观察日状态 | 本页采用的自治判断 | 证据边界 |
|---|---|---|---|
| Worker Agents | 平台总览与文档列 GA；账户入口和细粒度权限可能受控 | 读写范围受限时 L1—L3 | 正式文档与第一方技术文章；缺跨客户成功率 |
| Code Quality Agents | 正式文档能力 | Review L1；测试/修复 PR 为 L2；沙箱复验可为受限 L3 | 机制清晰；不同语言和失败类型的独立效果不足 |
| DevOps Agent | 企业版能力，使用 Harness 管理模型 | 生成 Pipeline / OPA 草案为 L1—L2 | 厂商的 50 Stage 验证未公开任务集和成功标准 |
| CI AutoFix 闭环 | 诊断、修改、验证并交付 PR | 最高可信输出停在可审查变更 | 不等于自动 Merge，更不等于 Release 自治 |

### 上游来源

- [[50_deepdives/harness-company/90_report#5.3 Pipeline：概率决策与确定性执行的混合图|Pipeline 混合控制机制]]
- [[50_deepdives/harness-company/90_report#5.4 Worker Runtime：假设 Agent 已被攻陷|Worker Runtime 隔离]]
- [[50_deepdives/harness-company/90_report#5.5 身份与授权：委托身份，不是共享 Bot|委托身份与权限]]
- [[00_sources/briefs/2026-harness-worker-agents|Worker Agents Source Brief]]
- [[00_sources/briefs/2026-harness-worker-agent-security|Worker Agent 安全 Source Brief]]
- [[00_sources/briefs/2026-harness-code-quality-agents|Code Quality Agents Source Brief]]
- [[00_sources/briefs/2026-harness-ai-devops-agent|DevOps Agent Source Brief]]
- [[60_tutorials/harness-worker-agent-config|Worker Agent 配置速查]]

## 十一、视觉结构建议

- **顶部：** 结论型标题、副标题，以及 `GA / Controlled enablement`、`L1—L3 by task` 两个状态标记。
- **中部主画面：** 六步横向作业流；把 Agent Step 作为唯一高亮节点，前后均使用黑白确定性节点。
- **中部下轨：** 一条治理轨显示 `RBAC ∩ Grant`、`Connector Tools ∩ Agent Tools`、`Runtime Isolation`、`OPA / Human Gate`。
- **右侧或节点下方：** 在失败分支标出“预算内重试 / 超限升级”，在成功分支标出“Draft PR，不是 Merge”。
- **底部：** 核心差异化、企业启示、当前边界三句；不要再堆叠产品功能列表。

## 十二、本页明确排除

- 不展开 Harness CD、GitOps、Artifact、FME、AI SRE、CCM 或 Engineering Insights。
- 不把 Knowledge Graph/HQL 画成所有 CI Agent 的必经运行路径，只把已批准的结构化上下文视为可用输入。
- 不使用 AI Test Automation 的厂商量化案例证明通用 Worker Agent 或 AutoFix 的成功率。
- 不假设 Code Quality Agents 与 Worker Agent 共享同一 Runtime、安全实现或 Feature Flag。
- 不把 GA、MCP 可调用或 Runtime 隔离等同于自动 Merge、Release 或 L4 生产自治。

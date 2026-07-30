---
title: 大模型时代的 CI/CD 基础设施 Findings
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/findings
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
confidence: high
---

# Findings

## 总判断

> [!summary] 一句话结论
> 大模型带来的不是“AI 替代 CI/CD”，而是 **CI/CD 基础设施的双控制面化**：一侧是供 Agent 理解、尝试和生成候选的概率性上下文/执行面，另一侧是模型不能自行修改的确定性接受/发布面。基础设施的竞争点从“能否执行固定流水线”扩展为“能否低成本地给 Agent 提供上下文、隔离环境、快速反馈、最小权限与可验证证据”。

## F1：变化的本质是角色分层，不是替代

### 来源事实

- GitHub Agentic Workflows 把自然语言 Markdown 编译为标准 Actions YAML，继续复用 Runner 与 Policy；
- GitHub Cloud Agent 和 GitLab Flow 都在现有 Actions/Runner 上执行，结果回到 PR/MR；
- DORA 2025 将内部平台、版本控制、自动测试和快速反馈视为 AI 收益的基础，并发现 AI 采用仍与交付稳定性负相关；
- Artifact Attestation 与 SLSA 仍要求 Builder 控制面、Digest、Provenance 和验证策略。

### 分析推断

传统 CI/CD 的确定性骨架没有消失。新增的是一层能处理模糊任务、跨日志归因和候选修复的概率性决策层。平台正确的演进方向不是“把 YAML 换成 Prompt”，而是：

```text
自然语言任务 → 受限 Agent 决策 → 候选变更/动作
                         ↓
              固定 Build/Test/Scan/Policy/Review
                         ↓
                 接受、发布或拒绝
```

### 边界

“确定性”不等于门禁永远正确；业务语义、数据迁移和生产风险仍可能需要人工或外部 SLO。结论是 Oracle 必须独立，不是 Oracle 必须全部由脚本实现。

**置信度：high。**

## F2：代码仓从 Source of Truth 扩展为 Agent 的任务与治理控制面

### 来源事实

- GitHub Cloud Agent 已 GA，Issue、Agents UI、PR 评论可成为任务入口，Agent 在临时环境工作后以 Draft PR/Branch 交付；
- GitLab Custom Flows 19.2 GA，Issue、MR、评论提及和 Pipeline Event 可触发 Flow；
- `AGENTS.md`、Setup Steps、Agent Config、MCP Config、Review Instructions 等文件开始版本化 Agent 的环境、上下文与行为；
- GitHub Ruleset/Protected Branch 仍能要求 Review、Checks、签名与部署成功。

### 分析推断

Repository 现在同时承载五种对象：

1. **任务：** Issue、PR/MR、评论与事件；
2. **上下文：** 代码、历史、日志、指令与架构文档；
3. **Agent 配置：** Tool、Skill、MCP、环境和网络规则；
4. **候选变更：** Branch、Patch、Suggestion、PR/MR；
5. **接受规则：** CODEOWNERS、Ruleset、Required Check、Review 和 Deployment Gate。

这使代码仓成为 Agent 协作的天然控制面，但也使 Agent 配置文件本身变成高风险代码：修改一个 Setup Script 或 MCP 配置，可能比修改业务代码更直接地扩大权限与数据面。

### 边界

Repository 不拥有外部云、制品仓或生产环境的授权；它只能承载委托和证据。第三方 Agent 与部分自动化 API 仍是 Preview。

**置信度：high。**

## F3：流水线正分化为低延迟验证内环和完整交付外环

### 来源事实

- CircleCI Chunk Sidecar/Microbuild 通过快照、增量同步与精简反馈支持 Agent 工作中的内循环；
- Harness CI Autofix 与 Nx Self-Healing 形成失败→候选修复→复验的有界循环；
- Buildkite MCP 与 Pipeline Agent Step 使 Agent 能读取 Build State/Log、触发 Run 或在 Step 内分析；
- GitHub Agentic Workflows 同时计量 Actions Minutes 与 AI Credits。

### 分析推断

传统 CI 假设大多数提交已在本地预检，适合一次 Fan-out 后尽可能收集完整反馈。Agent 会反复试错、并行工作，并在每一轮需要更直接的反馈；把完整 CI 原样当作内循环会造成排队、冷启动、日志 Token 和计算成本。

合理结构是：

- **内环：** 受影响任务、快速启动、可复用隔离环境、结构化失败、有限轮次；
- **外环：** 干净环境、完整 Required Checks、扫描、策略、集成/端到端验证和批准；
- **发布环：** 绑定 Artifact Digest、Attestation、Environment Policy 和 SLO。

### 边界

CircleCI 的效率数字是厂商自测；Sidecar 不是完全 Hermetic。没有跨行业证据证明所有团队都应另建一套 Microbuild 平台。

**置信度：medium-high。**

## F4：构建系统的旧能力从“提速”升级为 Agent 的 Backpressure 与成本控制

### 来源事实

- Nx 使用 Project Graph、Affected 范围、历史耗时和 Remote Cache 分发任务；
- Bazel RBE/Remote Cache 早已提供多机执行、统一环境和结果复用；
- Nx Self-Healing 使用 Task Metadata 与原失败 Task 复验；
- SLSA v1.2 要求高等级 Builder 隔离不同 Build、后续 Build 和共享 Cache 污染。

### 分析推断

Agent 时代最关键的构建变化不是“让模型参与编译”，而是把四个既有能力组合成反馈系统：

1. **Graph：** 告诉 Agent 与调度器“什么受影响”；
2. **Cache/Incremental：** 降低每轮尝试成本；
3. **Isolation/Reproducibility：** 防止环境偶然和缓存污染制造伪绿灯；
4. **Task-level Oracle：** 用固定任务验证候选，而不是相信模型摘要。

构建系统由吞吐优化器升级为 Agent 的 Backpressure：它既加速有效尝试，也应尽快拒绝越界或错误尝试。

### 边界

Agent 导致的并发和缓存压力尚无跨平台统一数据；企业必须通过 Queue Time、Cache Hit、重试次数和 Verified Outcome 成本验证投资优先级。

**置信度：medium-high。**

## F5：制品仓从存储面扩展为供应链信任面与受限行动面

### 来源事实

- Sonatype MCP 把版本、漏洞、许可证和推荐版本前移到 Agent 选择依赖的时点；
- Cloudsmith MCP 可查询漏洞/版本并管理部分制品，但当前只允许非破坏性动作，Policy 激活保留人工；
- JFrog MCP 将 Repository、Xray、SBOM、Evidence、Release 与部分 Identity/Governance Tool 暴露给 Agent，当前仍为 Beta；
- GitHub Artifact Attestations 已 GA，可绑定 Workflow、Repo、Commit、Environment 与 Artifact Digest，并关联 SBOM。

### 分析推断

制品仓正在承担三种角色：

- **事实源：** 哪个版本存在、有什么风险、来自何处；
- **信任图：** 哪个 Builder 用何输入产生哪个 Digest，附带何种 SBOM/Attestation；
- **行动面：** 查询、标记、复制、隔离、晋级、删除或豁免。

成熟度从左到右快速下降：查询已普遍出现，受控非破坏性动作开始产品化，Promotion/Delete/Waiver/Identity Admin 仍需要更严格的独立授权。

### 边界

Attestation 只证明来源和过程，不证明没有漏洞、恶意逻辑或业务缺陷；MCP 只暴露后端能力，不提供天然审批。

**置信度：high。**

## F6：Agent 的 Skill、Plugin、Prompt 与 MCP 配置开始成为新的供应链资产

### 来源事实

JFrog Agent Packages Repository 已支持分发 Skills、Plugins、Prompts、Hooks、MCP Servers、Instructions 与 Agents；Skills Repository 和部分扫描能力仍为 Open Beta。

### 分析推断

这类资产可能直接改变 Agent：

- 读取哪些数据；
- 调用哪些工具；
- 执行哪些命令；
- 采用哪些策略；
- 把结果写向何处。

因此它们比普通文档更接近可执行依赖，需要版本、来源、签名、扫描、准入、回滚和 Owner。未来制品仓的对象模型会从 `package/container/model` 扩展到 `agent behavior asset`。

### 边界

目前主要是单一厂商样本，格式与跨平台验证尚未成熟；不能宣称行业已形成统一 Agent Package Standard。

**置信度：medium。**

## F7：身份从“一个 Bot Token”演进为任务级委托与多重求交

### 来源事实

- GitLab Composite Identity 取触发人与 Service Account 的较小权限，并保留两者归因；
- Harness Runtime Token 为触发人 RBAC 与 Agent Grant 的交集，第三方 Tool Scope 再求交；
- GitHub Cloud Agent 使用独立 Agent/MCP Secret 通道；Agentic Workflows 可使用组织 `GITHUB_TOKEN`，不再必须长期 PAT；
- JFrog/Cloudsmith/Sonatype MCP 最终仍继承后端 OAuth、API Key 或 Platform Permission。

### 分析推断

安全的运行授权应至少计算：

```text
Effective Permission
= Initiator 权限
∩ Agent 声明权限
∩ Tool/Connector 权限
∩ Task/Environment Policy
∩ Run TTL / Budget
```

如果一个事件没有可继承的真实 Principal，就不能悄悄退化为高权限共享 Bot。应使用专门服务身份、白名单任务和独立批准。

### 边界

各平台的身份模型不同；部分事件触发、第三方 Agent API 和本地 Client 审计仍有缺口。

**置信度：medium-high。**

## F8：Pass/Fail 正扩展为完整 Lineage，但审计覆盖仍不完整

### 来源事实

- GitHub Audit 可记录 Agent Actor、Session、Initiator 和 Action；
- GitLab Flow Session 关联 CI Job Log，但部分 AI Audit 功能仍在 Beta/Experiment；
- Artifact Attestation 绑定构建与 Digest；
- GitHub Agentic Workflows 可以查看 Run 级 Token/Cost 与编译后 Workflow。

### 分析推断

最低可审计链应回答：

```text
谁发起 → 哪个 Agent/版本 → 读了什么证据 → 调了什么 Tool
→ 修改哪个 Commit → 哪个 Builder 验证 → 产生哪个 Digest
→ 哪个 Policy/人批准 → 部署到哪里 → 结果如何
```

这意味着 Logs 不再只是排障文本，而是 Agent Evaluation、权限审计、成本归因与发布证明的共同数据面。

### 边界

现有平台并不都保存本地 Prompt、全部 MCP 往返、模型版本与完整工具输出。不能把“有 Session Log”写成“可完整重演推理”。

**置信度：high。**

## F9：最大的风险不是模型偶尔答错，而是把概率性结论误接成确定性副作用

### 来源事实

- GitHub Issues 的 Agent Approval 被官方明确称为工作流便利，不是服务器端安全边界；
- Cloudsmith 不允许 MCP 激活 Policy；
- Sonatype Bulk Waiver API 无后续审批流；
- JFrog MCP 暴露 Token/OIDC/Role/Release 等高风险 Tool，但权限仍由平台决定；
- CI-Repair-Bench 中最佳受测模型在真实 CI 故障上的修复成功率仅 18.9%。

### 分析推断

危险组合是：

```text
自然语言输入
+ 高权限长期凭据
+ 不可逆 Tool
+ Agent 自己定义成功
+ 无独立证据与停止条件
```

因此真正需要平台化的不是更多 Prompt，而是 **Typed Action、Dry-run、审批、幂等、TTL、Circuit Breaker、回退和外部 Oracle**。

### 边界

18.9% 来自预印本和特定公开数据集，不能外推某企业；它只足以反驳“通用 CI 修复已可靠解决”的表述。

**置信度：high。**

## F10：行业已跨过“概念验证”，但尚未跨过“普遍自治”

| 能力层 | 当前成熟度 |
|---|---|
| Repository 内异步 Coding Agent、PR/MR 交付 | 已有 GA 样本 |
| Agent Flow、Composite Identity、Runner 执行 | 已有 GA 样本，治理能力不齐 |
| Agentic Pipeline/Workflow、Safe Output | 可用与 Public Preview 并存 |
| 高频验证内环、自愈 Task | 已产品化，效果数据主要为厂商自述 |
| 制品查询与依赖情报 MCP | 已可用 |
| 制品高风险写入、Policy/Waiver/Promotion 自治 | 缺少统一成熟授权与独立效果证据 |
| Agent 行为资产供应链 | Open Beta/早期 |
| 全链路、无人值守生产自治 | 未得到本研究证据支持 |

**置信度：high。**

## 六个变化与四个不变

| 已发生变化 | 仍必须保持不变 |
|---|---|
| 人工 UI/API → 人与 Agent 共用的 Tool/CLI/MCP/Skill 接口 | Git/Repository 与 Artifact Digest 仍是事实锚点 |
| 人类节奏提交 → 并行、多轮、长时 Agent 工作负载 | Build/Test/Scan/Policy 仍必须独立于 Repair Agent |
| 一次性 CI → 验证内环 + 完整外环 | Required Checks、Review、Environment Gate 仍决定接受 |
| 静态 Bot Token → 任务级委托、短期身份与权限求交 | MCP/CLI/API 可调用仍不等于授权 |
| Run Pass/Fail → Session、Commit、Builder、Digest、Policy Lineage | Provenance/Attestation 仍需验证，且不等于安全 |
| Runner 分钟 → 计算、推理、尝试、反馈延迟和 Verified Outcome 成本 | 业务价值、风险偏好和生产批准仍由外部 Owner 决定 |

## 对主问题的直接回答

代码仓、流水线、构建系统和制品仓都在发生变化，但变化深度不同：

- **代码仓：** 变化最明确，已成为 Agent 任务、上下文、Session、配置、PR/MR 和规则的控制面；
- **流水线：** 从执行固定 DAG 扩展为承载 Agent Step、调查/修复循环和低延迟验证，但完整门禁不应弱化；
- **构建系统：** 新增 Agent 上下文和自愈层，核心 Graph/Cache/RBE/Isolation 是旧能力升值；
- **制品仓：** 从被动存储转向实时供应链事实与受限行动面，并开始管理 Agent 资产；高风险写权仍最不成熟；
- **横切治理：** 身份、权限、证据、审计与成本从配套能力上升为能否规模化采用 Agent 的主约束。

完整证据见 [[50_deepdives/llm-era-cicd-infrastructure/20_evidence-map|证据矩阵]]与 [[50_deepdives/llm-era-cicd-infrastructure/30_case-map|案例地图]]。

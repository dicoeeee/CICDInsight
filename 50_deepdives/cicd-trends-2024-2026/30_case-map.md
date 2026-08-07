---
title: 各公司智能化 CI/CD 演进趋势 Case Map
aliases:
  - Intelligent CI/CD Evolution Case Map
tags:
  - research/agentic-cicd
  - research/deep-dive
  - case-map
topic_id: cicd-trends-2024-2026
status: complete
as_of: 2026-08-07
---

# Case Map：各公司智能化 CI/CD 演进趋势（2024—2026）

> 时间线事件均回链至研究底稿：[[00_sources/research-github-microsoft-cicd-trends-2026-08-07|GitHub/Microsoft]]、[[00_sources/research-aws-cicd-trends-2024-2026-2026-08-07|AWS]]、[[00_sources/research-openai-anthropic-cicd-trends-2026-08-07|OpenAI/Anthropic]]、[[00_sources/research-bytedance-cicd-2024-2026-trends-2026-08-07|字节]]、[[50_deepdives/harness-company/fact-table-2024-2026-2026-08-07|Harness 事实表]]。事实编号（F/O/A）对应各底稿事实列；访问时间统一 2026-08-07。

## 一、各公司智能化演进轨迹（时间线）

### GitHub：把 Agent 编译进 Actions 控制面

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2024 | Copilot 助手建议；Artifact Attestations 多 subject（2024-12）；持久 commit 签名 GA（2024-12-10） | GA |
| 2025-04 | Copilot Agent Mode + MCP GA（VS Code） | GA |
| 2026-02-13 | gh-aw 技术预览（GitHub+Microsoft Research+Azure Core 联合开发），Markdown→Actions | Technical Preview |
| 2026-06-11 | gh-aw 进入 Public Preview，默认只读+Safe Outputs+Firewall | Public Preview |
| 2026-07-02 | Copilot CLI 在 Actions 免 PAT | Improvement |
| 2026-07-10 | Agentic Autofix for Code Scanning 进入 Public Preview | Public Preview |
| 2026-07-23 | Copilot cloud agent 修 Actions checks（Mobile 入口） | Improvement |
| 2026-07-29 | Copilot Code Review Agent Skills + MCP GA | GA |

**形态演进**：Copilot 助手 → Agent 编译进 Actions → Copilot 在 CI 内一等执行者。
**自治边界**：gh-aw 官方"PR 永不自动合并"；"增强而非替代确定性 CI/CD"。

### Microsoft：把 GitHub 系智能能力移植进 Azure DevOps

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2025-11 | 与 Defender for Cloud code-to-cloud 风险可见性 | Public Preview |
| 2026-03-31 | Remote Azure DevOps MCP Server | Public Preview（Q3 GA 计划） |
| 2026-06-17 | Copilot Code Reviews for Azure Repos（Sprint 275） | Limited Public Preview |
| 2026-06-17 | Copilot Autofix for Code Scanning（GHAzDO 内） | Limited Public Preview |
| 2026-06-17 | Entra workload identity service connection | Preview |
| 2026-08 | GHAzDO 三件套（Secret/CodeQL/Dependency）已 GA；Advanced Security 分支门禁 GA | GA |

**形态演进**：确定性扫描门禁（先行 GA）→ Copilot 评审/修复（Limited Preview）→ 身份治理（Preview）。
**自治边界**：Copilot Code Reviews 计划 2026 Q3 转公开预览；AI 能力落地速度慢于安全能力。

### AWS：从只读调查向发布前审查/受控执行前移

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2025-10-13 | Amazon Bedrock AgentCore GA（Agent 运行/治理底座） | GA |
| 2026-03-31 | DevOps Agent Production Operations GA（事件调查/预防/按需 SRE） | GA |
| 2026-06-12 | custom agents、MCP/A2A、memories、pipeline topology skill | GA 面增量 |
| 2026-06-17 | Release Management（release readiness + release testing） | Preview（us-east-1） |
| 2026-07-23 | Sandbox（Agent 写/运行代码，Lambda MicroVM，只读代理） | Preview |
| 2026-08-05 | 权限护栏扩展（ReadOnlyAccess 全部动作 + athena/kms） | GA 面 |
| 2026-08-06 | AgentCore Runtime instances GA；temporal policies + rate limiting 同日公告（未给独立 GA/Preview 标签） | GA + 公告 |

**形态演进**：只读调查（GA）→ 发布前审查/受控测试（Preview）→ 沙箱执行（Preview）。
**自治边界**：自动触发仅限私有仓库；评审/修复 PR 不等于自动合并/部署；AWS 代理只读。

### Harness：编排与智能同平台

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2024 | 传统 CI 基础设施优化（Cache/Build Intelligence、Git Clone、OIDC） | GA/EA |
| 2025-2026H1 | Build/Test/Cache Intelligence 转 GA；DAG Pipelines Beta | GA/Beta |
| 2026-06-30 | Autonomous Worker Agents GA（Pipeline Step 作 reasoning agent） | GA |
| 2026-07 | Agent DLC（AI Evals、AI Test、AI Configs、AI Firewall、AgentTrace） | 多项 GA/产品页 |
| 2026-07 | AI 平台总览：Memories、Semantic Code Search、Dashboard Intelligence | 多数 GA |
| 2026 | CLI 3.0 Public Beta、AI SAST Beta、OPA 生成 Limited GA | Beta/Limited GA |

**形态演进**：传统 CI 优化 → 确定性智能（Intelligence）→ Agent 执行（Worker Agents）→ AI 原生 DLC。
**自治边界**：Worker 受 scoped credentials、OPA、审批、审计约束。

### 字节：产品化交付 + 研究资产双轨

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2024-04 | 火山引擎 CP AI 应用部署邀测 | 邀测 |
| 2025-03 | AI 应用部署正式发布（DeepSeek/Qwen/Stable Diffusion 一键部署） | GA |
| 2025-03 | Repo2Run 开源（自动生成可执行环境） | 开源 |
| 2025-06-13 | Trae Agent 开源（SWE-bench Verified SOTA） | 开源 |
| 2025-07-31 | CP V1 下线（迁移 V2） | 下线 |
| 2026-03 | AgentKit 部署/更新任务（Test-Time Agent 运行时）纳入 CP 产品面（逐项状态未单独核验） | GA（产品面） |
| 2026-04 | YAML 流水线、OAM 商用 | GA |
| 2026 | SE Lab 研究转向 LLM Agent（Trae/Repo2Run/AEGIS/MarsCode） | 研究/开源 |

**形态演进**：传统流水线 → AI 应用交付 → Agent 运行时部署；研究从测试工具转向 Agent 软件工程。
**自治边界**：内部 CI 平台 2024+ 无一手披露（G2）；AgentKit 是"部署 Agent 运行时"而非"Agent 编排 CI/CD"。

### OpenAI：任务追踪器即控制平面

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2026-02-11 | harness engineering 内部披露（0 行人工代码、1,500 PR、最小门禁） | 内部实践 |
| 2026-04-27 | Symphony 开源（Linear 变控制平面，WORKFLOW.md 入库） | Experimental |
| 2026-05-13 | Codex Windows sandbox GA（默认无网络） | GA |
| 2026-05-27 | Tax AI（生产 trace→eval 门禁→Codex 修复闭环） | Pilot |

**形态演进**：终端 Codex Agent → 常驻编排器（Symphony）→ 沙箱内受控执行 → 生产 trace 驱动的 eval 门禁。
**自治边界**：未公开"Agent 直接执行生产发布"；Symphony 效果 500% 为厂商自述。

### Anthropic：审批与执行环境自动化

| 时间 | 演进事件 | 状态 |
|---|---|---|
| 2025-10-20 | Claude Code sandbox（双边界）+ Claude Code on the web | Beta/Research Preview / GA |
| 2025-11-26 | 长时运行 harness 研究（initializer/coding agent/context reset） | Research |
| 2026-02-18 | 自主性测量研究（最长运行 turn 的 p99.9 时长近翻倍、平均人工干预 5.4→3.3/会话） | Research |
| 2026-03-24 | 强化 harness 研究（planner/generator/evaluator） | Research |
| 2026-03-25 | auto mode（模型分类器审批，20+ block rules） | GA |
| 2026-04-08 | Managed Agents（cattle 化执行环境，凭据外置） | GA |
| 2026-05-25 | 三产品 containment 架构披露（含多起真实事故） | 内部披露 |

**形态演进**：终端 Claude Code → 沙箱执行 → 分类器审批 → 可恢复 cattle 执行环境。
**自治边界**：auto mode 拦截"直接推 main/绕过 pre-check 部署"；headless 下连续拒绝终止进程（[[00_sources/research-openai-anthropic-cicd-trends-2026-08-07|Anthropic 底稿]] A5）。

## 二、共性演进模式（跨公司，2024-2026）

### 模式 1：能力形态从"建议"走向"执行"再走向"编排"

- 2024：Copilot 助手、Codex/Claude Code 终端补代码（AI 建议）
- 2025-2026H1：Agent 在沙箱内执行（修复 CI、自动评审、生成 PR）
- 2026H2：常驻编排器/编译进流水线（Symphony、gh-aw、Worker Agents、Managed Agents）

**代表证据**：gh-aw 从技术预览到 Public Preview（2026-02→06）；Worker Agents GA（2026-06-30）；Symphony（2026-04-27）。

### 模式 2：环节扩散"检查/评审最先，发布/恢复最后"

- 最先 GA：评审（Copilot Review GA、auto mode）、调查（DevOps Agent production ops GA）
- 中间：门禁/CI 修复（Agentic Autofix Preview、Copilot 修 check、Codex autofix）
- 最后：发布准备（AWS release readiness Preview、Azure Copilot Reviews Limited Preview）、恢复（多处于研究/内部）

**判断**：这是最稳定的跨公司演进事实——智能化沿管道的扩散顺序与"人类最信任的环节呈现"一致。

### 模式 3：自治抬升必须以沙箱/凭据外置为前提

- GitHub：sandbox + Firewall + 零密钥 + Safe Outputs
- AWS：Lambda MicroVM + 只读代理 + 出站 allowlist
- OpenAI：默认无网络 + workspace 内可写
- Anthropic：双边界 + 凭据永不进沙箱 + scoped git token

**判断**：四家独立演化出同一控制模型，说明"边界机制"是智能化 CI/CD 的结构性门槛而非某家特色。

### 模式 4：2026 年密集转正但"发布端自治"仍被边界拦截

- 转正：gh-aw Public Preview、Worker Agents GA、AgentCore GA 系列、Copilot Review GA、auto mode GA、Managed Agents GA
- 拦截：GitHub "PR 永不自动合并"、AWS "审查不等于授权"、Anthropic block 规则、OpenAI 证据止于合并

**判断**：转正集中在"检查/门禁/执行"层；"发布/恢复"层无一家 GA，"发布自治"的边界在 2026 年仍是硬约束。

### 模式 5：工作流策略入库成为共同演进方向

- OpenAI：WORKFLOW.md 版本化进仓库
- GitHub：gh-aw Markdown+frontmatter 编译为锁文件
- Anthropic：AGENTS.md / Rules / Hooks
- Harness：Pipeline YAML Git 化 + OPA 策略生成

**判断**：方向共性明确，但格式互不兼容，无统一标准（不写成互操作标准）。

### 模式 6：治理演进——从"治理流程"到"治理 Agent 身份与持续行为"

- 2024：治理流程（分支保护、required checks、人工审批）
- 2025-2026H1：治理凭据与执行边界（scoped token、凭据外置、只读代理）
- 2026：治理持续行为与状态化授权（AWS temporal policies+rate limiting、Harness Runtime Token=grant∩RBAC+AgentTrace、GitHub Safe Outputs 独立 Job、Anthropic scoped git token 按 session 取用）

**代表证据**：AWS F14（2026-08-06）、[[50_deepdives/harness-company/fact-table-2024-2026-2026-08-07|Harness 事实表]]（Worker 权限模型）、gh-aw（零密钥+独立写 Job）。

**判断**：治理重心从"流程"移向"Agent 行为"，但权威仍分散在外部控制面，无一家把治理权交给 Agent 自管。

### 模式 7：验证机制演进——权威保持确定性，前置验收智能化

- 2024：人工逐个审批 / 确定性 gate（CodeQL、status check）
- 2025-2026：Agent 产出接入 gate（AWS BLOCK 映射 required check、GitHub Ruleset 持有合并权威、Harness OPA 硬门禁）
- 2026：模型分类器 + eval 门禁（Anthropic auto mode、OpenAI Tax AI targeted eval+独立 grader、AWS release readiness 护栏、GitHub Agentic Autofix 修复+重跑）

**贯穿约束**："Agent 不得自证"——自评/eval 分离（Anthropic A8）、grader 独立于被修 Agent（OpenAI O14）、Safe Outputs 独立 Job（GitHub）。

**判断**：智能化演进的是"前置验收"，不是"最终放行权"；最终权威仍由确定性 gate 与人持有。

### 模式 8：生命周期分界——"单点/入口"收窄，"嵌入平台控制面"存活

- 收窄/下线：字节构建加速（2025-09-25）、AWS CodeGuru（2025-11-07 停新）、Q Developer IDE 入口（EoS 2027-04-30）、Harness DLite（实现替换）、字节 CP V1（版本迁移）、WIF issuer（身份迁移）。
- 转正/存活：gh-aw、Worker Agents、Code Quality、auto mode、Managed Agents、AgentCore。

**判断**：改变"调度/治理/验证"的嵌入型能力存活并转正；解决单环节效率的独立工具型能力价值被稀释。样本小（6 收窄 vs 7 转正），且"入口收窄/实现替换/版本迁移"与"能力死亡"须区分。

## 三、差异化演进路线

| 公司 | 演进路线 | 一句话 |
|---|---|---|
| GitHub | 编译进流水线 | 把 Agent 编译进 Actions 执行与治理平面 |
| Microsoft | 移植 | 把 GitHub 系智能能力移植进 Azure DevOps，安全先行 |
| AWS | 旁路前移 | Agent 服务从只读调查前移到发布前审查，不改造 CI 引擎 |
| Harness | 同平台嵌入 | 编排与智能同平台，Worker 作 Pipeline Step |
| 字节 | 产品化+研究 | 对外产品化（AI 应用交付）+ 研究资产（Agent 软件工程） |
| OpenAI | 重定义控制平面 | 让 issue 追踪器成为 CI 的控制平面 |
| Anthropic | 自动化审批与执行 | 分类器审批 + 可恢复执行环境 |

## 四、证据冲突与口径差异（显式保留）

1. **Harness macOS 全局排队**：Release Notes v1.150.0 标"已发布"（CI-23880），Feature Availability 仍标 `GLOBAL_QUEUEING_ENABLED` Beta。口径差异（渐进部署 vs 生命周期状态）须避免混用。
2. **AWS Release Management 日期**：文档历史 2026-06-11 vs 公告 2026-06-17；本次按公告日期为准记录。
3. **"Microsoft 仓库迁移到 GitHub"**：仓库既有 Source Brief 有其证据，本次研究未以一手来源复核；方向性旁证（Aspire 生产用 gh-aw、GitHub-hosted Agents、Copilot 进 Azure Repos）不足以证明"迁移"结论。
4. **字节"无 2024+ CI/CD 编排级开源"**：GitHub 搜索 API 限流 + 关键字检索口径限制，该结论置信度为中（G5）。
5. **"环节密度"衡量**：检查/门禁环节证据数量最多，但这是"证据数量"而非"智能化成效已被独立验证"。

## 五、可采用度排序（按演进成熟度，分析推断）

| 排序 | 演进阶段的智能化能力 | 演进程度 | 可用状态 | 证据强度 | 依据 |
|---|---|---|---|---|---|
| 1 | GitHub 供应链门禁（Attestations/SLSA） | 已成熟，GA 多年演进 | GA | 高 | attest@v4，SLSA L2 默认/L3 可达 |
| 2 | GitHub Copilot Code Review + Agentic Autofix | 已进入执行层 | GA / Preview | 中高 | GA 2026-07-29；autofix Preview |
| 3 | AWS DevOps Agent 事件调查（默认只读） | 已 GA，成熟度最高 | GA | 高 | 2026-03-31 GA |
| 4 | Harness Worker Agents（Pipeline Step Agent） | 刚转 GA，演进早期 | GA | 高 | 2026-06-30 GA |
| 5 | 沙箱/凭据外置的 Agent 执行模型 | 结构性前提，多家一致 | GA/Preview 混合 | 高 | 四家一致机制 |
| 6 | AWS release readiness 审查 | 从调查前移到发布准备 | Preview | 高 | 仅 us-east-1，私有仓库自动触发 |
| 7 | Azure DevOps Copilot Code Reviews | 移植初期 | Limited Preview | 高 | 2026 Q2 落地，Q3 公开预览计划 |
| 8 | Anthropic auto mode 审批门禁 | 审批自动化已 GA | GA | 中（指标厂商自述） | 需 headless CI 端到端数据 |
| 9 | OpenAI Symphony 常驻编排 | 实验阶段 | Experimental | 中（厂商自述指标） | 仓库 openai/symphony |
| 10 | 字节 AgentKit/AI 应用部署 | 产品化初期 | GA | 高（产品）/低（内部） | 字节内部 CI 平台证据缺口 |

**保持阻塞（证据不足，不进入正式采用建议）**：
- 字节内部 Monorepo/Bazel/Rspack CI 平台 2024+ 演进（G2）
- Microsoft 仓库迁移到 GitHub 的官方范围/时间表
- OpenAI/Anthropic "Agent 直接执行生产发布"
- AWS Transform → AgentCore 构建依赖
- 恢复环节的端到端自治案例（发布后恢复闭环证据不足）
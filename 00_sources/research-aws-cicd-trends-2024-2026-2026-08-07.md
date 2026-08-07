---
title: AWS CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/aws
  - trend-update
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
confidence: high-for-status-and-mechanism-from-primary-docs; medium-for-CodePipeline-CodeBuild-AI-evolution-absence-confirmed-only-on-current-feature-pages; low-for-pre-2026-CodePipeline-CodeBuild-AI-announcements-unlocatable
---

# AWS CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）

## 研究范围与口径

- **任务性质**：趋势更新。仓库已有 AWS DevOps Agent、Amazon Bedrock AgentCore、AWS Intelligent CI/CD（项目主题，非 AWS 官方产品名）、AWS Transform 等的大量既有证据（2026-08-03——2026-08-06 核实），本报告补充 2026-08-06——2026-08-07 新变化，并回核既有产品状态是否过期。
- **访问时间**：全部链接于 2026-08-07 实际访问；无法访问或未核验的项显式标记 `unverified`。
- **一手来源**：AWS 官方 What's New / News Blog / User Guide（devopsagent、bedrock-agentcore、codepipeline、codebuild、codedeploy）/ 产品 Features 页。
- **证据分级**：`事实`（来源明确陈述）/ `分析推断`（由证据推导，不写成已发生事实）/ `待验证假设`（无证据支撑的检索方向）。
- **"AWS Intelligent CI/CD"**：经仓库既有研究（`research-aws-official-intelligent-cicd-capability-map-2026-08-06`）确认，这是本项目的研究主题，不是 AWS 官方产品或官方能力全景图。

## 结论先行

1. **既有结论全部成立，无过期项。** AWS DevOps Agent Production Operations **GA（2026-03-31）**、Release Management **Preview（2026-06-17，仅 us-east-1）** 截至 2026-08-07 均未变；GA 与 Release Management 两条 What's New 公告 URL 本次均已实际访问核验（后者由仓库先前标记的 `unverified` 修复为已核验）。
2. **新增（2026-06-12 至 2026-08-05）一批 DevOps Agent 能力增量**，其中 Release Management 面（发布就绪审查、发布测试）仍是 Preview，但 GA 面（production operations、custom agents、MCP/A2A、memories、pipeline topology 等）持续扩张；2026-07-23 新增 **Sandbox（Preview，4 区域）**。
3. **AgentCore 平台层在 2026-08-06 同日新增两项 GA 能力**：Runtime instances（EC2 型持久计算，14 天会话）与 temporal policies + rate limiting（状态化授权与限流）。Managed Harness GA（2026-06-17）本次已核验。AgentCore 是 DevOps Agent 已证实的运行与治理底座（`built on AgentCore`，2026-03-31 博客），但**不**是 CI/CD 编排功能。
4. **传统 CodePipeline / CodeBuild / CodeDeploy 截至 2026-08-07 官方 Features 页无任何 AI / Agent 能力条目**。这与仓库既有"传统 CI/CD 核心仍为确定性机制"的排除结论一致；未能通过 What's New 归档检索到 2024-2026 期间这三项服务的 AI 特性公告（见证据缺口）。
5. **AWS Transform → AgentCore 的构建关系仍未证实**（`unverified`）；Transform 与 DevOps Agent 仅存在可选的 release-readiness review 集成（2026-06-14 文档条目本次已复核）。

---

## 一、事实表

> 访问时间统一为 2026-08-07。证据强度：`强`=官方一手来源直接核验；`中`=官方来源 + 仓库既有交叉核验；`厂商自述`=仅单一厂商来源，不得外推为行业结论。

| # | 事实 | 一手来源 URL | 发布时间 | 产品状态 | 证据强度 |
|---|---|---|---|---|---|
| F1 | AWS DevOps Agent production operations（事件调查、预防、按需 SRE 任务）GA，新增 Azure/on-prem 调查、custom skills、自定义图表报告 | https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/ | 2026-03-31 | **GA** | 强 |
| F2 | AWS DevOps Agent release management 能力（release readiness review + autonomous release testing）以 Preview 发布，仅 us-east-1，预览期免费 | https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/ | 2026-06-17 | **Preview，us-east-1** | 强（本 URL 此前在仓库标记 unverified，本次已核验） |
| F3 | DevOps Agent 产品支持 11 个区域；Production operations / on-demand tasks / custom agents 全部支持区域可用；Release management 仍仅 us-east-1 Preview；Sandbox 为 Preview（us-east-1/us-west-2/ap-northeast-1/eu-west-1） | https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-supported-regions.html | 页面当前；2026-07-09 区域表更新 | GA 面全域 + RM/Sandbox Preview | 强 |
| F4 | Sandbox 沙箱（Agent 写/运行代码，单次调查隔离在 Lambda MicroVM，AWS API 代理为只读，出站 allowlist）2026-07-23 作为 Preview 引入 | https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html | 2026-07-23 | **Preview** | 强 |
| F5 | Custom agents、MCP/A2A remote servers、Memories、Pipeline topology view、Summary report、skills 从仓库导入等一批能力 2026-06-12 加入文档；Release Manager 工具 2026-07-27 允许自定义 Agent 操作 Release Manager 任务；动态子代理委派 2026-07-27 | https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html | 2026-06-12 至 2026-07-27 | 未逐项标 GA/Preview（GA 产品面增量） | 强 |
| F6 | 2026-08-05 权限护栏扩展：agent 现支持 ReadOnlyAccess 托管策略全部动作 + athena:StartQueryExecution/StopQueryExecution/kms:Decrypt | https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html | 2026-08-05 | GA 面 | 强 |
| F7 | 2026-07-30 Chat 支持模型档位选择（Smart/Balanced/Fast） | https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html | 2026-07-30 | GA 面 | 强 |
| F8 | Release readiness 审查：GitHub（Check Run，可配置为 required status check 阻塞合并）与 GitLab（MR approval rule）双支持；自动触发仅限私有仓库；托管验证环境 build/run/test，出站域名 allowlist；内置护栏（凭据暴露阻断、敏感文件外泄检测、mutative AWS 操作阻断、顺序阶段强制）；单次审查 8-10 分钟 | https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html | 2026-06-11 文档加入；2026-07-24 补充私有仓库限制 | **Preview（继承 RM）** | 强（本次全文核验） |
| F9 | Release testing（UAT+回归，UI/API 两类）会对被测应用发送真实请求含写操作（POST/PUT/DELETE），仅公网端点（私有网络端点不支持）；官方提供 `aws-actions/devops-agent-qa` GitHub Action 在 CI/CD 中触发并回写 Check Run；仅 us-east-1 | https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html | 2026-06-11 文档加入 | **Preview** | 强（本次全文核验） |
| F10 | DevOps Agent 明确 built on Amazon Bedrock AgentCore（专用 memory/policies/evaluations/observability 基础设施） | https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/ | 2026-03-31 | — | 强（仓库 2026-08-03 已核验，本次无冲突） |
| F11 | Amazon Bedrock AgentCore 2025-10-13 GA；平台定位为通用 Agent 构建/部署/运行/治理平台，可用任意框架、模型与 MCP/A2A | https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available/ + https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html | 2025-10-13 / 页面当前 | **GA** | 强 |
| F12 | AgentCore Managed Harness GA（托管 agent loop，配置即运行，可导出为 Strands 代码） | https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available/ | 2026-06-17 | **GA** | 强（本次核验） |
| F13 | AgentCore Runtime instances GA：在自有 EC2 实例上运行 agent（覆盖 GPU/内存/计算优化实例族），agent 会话最长 14 天；默认 serverless microVM 运行时上限 8 小时会话；首批 9 区域（US East N.Virginia/Ohio、US West Oregon、AP Mumbai/Singapore/Sydney/Tokyo、EU Frankfurt/Ireland）；按托管计算计费 + 标准 EC2 费用 | https://aws.amazon.com/about-aws/whats-new/2026/08/aws-bedrock-agentcore-runtime-instances-generally-available/ | 2026-08-06 | **GA** | 强（本次核验） |
| F14 | AgentCore 新增 temporal policies（状态化授权：要求工作流顺序、tool 参数恰等于先前调用输出、特权操作前人工批准、数据新鲜度）与 rate limiting（按 OAuth 或 IAM 作用域，对 requests/tokens/concurrent 三类限制） | https://aws.amazon.com/about-aws/whats-new/2026/08/temporal-policies-agentcore/ + https://aws.amazon.com/blogs/machine-learning/control-agent-behaviors-and-cost-beyond-a-single-action-new-capabilities-in-amazon-bedrock-agentcore/ | 2026-08-06 | 公告（未给独立 GA/Preview 标签） | 强（本次核验，两 URL 均 200） |
| F15 | AWS CodePipeline 官方 Features 页：无任何 AI/Agent/生成式能力条目（仅 workflow modeling、integrations、declarative templates、access control） | https://aws.amazon.com/codepipeline/features/ | 页面当前 | — | 强（现状核验） |
| F16 | AWS CodeBuild 官方 Features 页：无任何 AI/Agent/生成式能力条目（仅 build/test、配置、CI/CD 集成） | https://aws.amazon.com/codebuild/features/ | 页面当前 | — | 强（现状核验） |
| F17 | AWS CodeDeploy 官方 Features 页：无任何 AI/Agent 能力条目（仅自动化部署、滚动/蓝绿、健康跟踪、回滚） | https://aws.amazon.com/codedeploy/features/ | 页面当前 | — | 强（现状核验） |
| F18 | AWS Transform 由 Amazon Bedrock 驱动（官方文档表述）；**未**检索到 'Transform built on AgentCore' 的一手表述 | https://docs.aws.amazon.com/transform/latest/userguide/cross-region-processing.html | 页面当前 | — | 强（仓库 2026-08-03 已核验，本次无冲突） |
| F19 | AWS Transform continuous modernization GA（技术债分析 + 自主生成验证过的修复 PR） | https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/ | 2026-08-03 | **GA** | 中（仓库 2026-08-06 核验） |
| F20 | Transform custom 可安装 DevOps Agent release-readiness skill：Transform 生成/修改代码时调用 review，findings 回到 Transform 输出；2026-06-14 文档条目（skill 位于 `aws-samples/aws-transform-custom-samples`）；另有 Kiro Power 与 Claude Code plugin 两种 coding-agent 集成 | https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html | 2026-06-14（文档 What's New 条目） | Preview（继承 RM） | 强（本次全文核验） |
| F21 | Amazon CodeGuru Reviewer 自 2025-11-07 起不能新建仓库关联；Amazon CodeCatalyst 自 2025-11-07 起不接受新客户 | https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/codeguru-reviewer-availability-change.html + https://docs.aws.amazon.com/codecatalyst/latest/adminguide/managing-generative-ai-features.html | 2025-11-07 | 停止新关联/新客户 | 中（仓库 2026-08-06 核验） |
| F22 | Amazon Q Developer IDE 插件与付费订阅计划于 2027-04-30 结束支持；Q Developer 本身未整体下线 | https://aws.amazon.com/blogs/devops/amazon-q-developer-end-of-support-announcement/ | 2026-04-30 | EoS 2027-04-30（IDE/订阅） | 中（仓库 2026-08-03 核验） |

### 状态更新标注

| 既有结论 | 仓库此前记录 | 2026-08-07 核验结果 |
|---|---|---|
| DevOps Agent Production operations GA | GA 2026-03-31 | **仍成立**，公告 URL 本次 200 核验 |
| DevOps Agent Release Management Preview | Preview 2026-06-17，仅 us-east-1；公告 URL 曾标 `unverified` | **仍成立**；公告 URL 本次已核验（Posted Jun 17, 2026），修复 `unverified` |
| AgentCore Managed Harness GA | GA 2026-06-17（仓库 2026-08-07 AgentCore 能力核验） | **仍成立**，本次独立核验公告 URL |
| AgentCore GA | GA 2025-10-13 | **仍成立** |
| 传统 CodePipeline/CodeBuild 为确定性机制（排除项） | 排除于 Agentic 能力池 | **仍成立**，且 CodeDeploy 一并核验（三者 Features 页均无 AI 条目） |
| Transform → AgentCore 构建关系 | `unverified`，禁止画边 | **仍为未证实**，本次无新证据 |
| "AWS Intelligent CI/CD" 名称 | 项目主题，非 AWS 官方产品 | **仍成立**，官方无同名产品/全景图 |

---

## 二、AWS DevOps Agent：GA/Preview 状态与能力边界（问题 4）

截至 2026-08-07 的官方状态（[Supported Regions](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-supported-regions.html)、[What's New](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)）：

**能做的（官方陈述）：**
- **GA 面**：production operations（事件调查、主动预防、按需 SRE 任务）、custom agents（可调度）、MCP/A2A 接入、Memories、Webhook/Asset API/EventBridge、Pipeline topology view、Sandbox 之外的 Agent Space 上下文地图与 learned skills。
- **Release Management（Preview，us-east-1）**：发布就绪审查（BLOCK/CAUTION/SAFE + 托管验证环境 build/run/test + GitHub Check Run 可作 required status check / GitLab MR approval rule）与发布测试（UAT+回归，对客户目标应用发真实读写请求，可经 `aws-actions/devops-agent-qa` GitHub Action 在流水线中触发并回写 Check Run）。

**不能做的（官方边界）：**
- 审查自动触发仅限**私有仓库**（F8）；发布测试仅支持公网端点、会发真实写请求，官方明确警告仅用于可容忍探索性写操作的环境（F9）。
- AWS native 工具默认不突变基础设施/应用（例外：建 ticket/support case）；缓解建议由人审阅后应用；Sandbox 内 AWS API 代理为只读；审查护栏阻断凭据明文调用、敏感文件外泄型 shell、mutative AWS 调用，并强制审查阶段顺序执行。
- `BLOCK`、Check Run、缓解建议、修复 PR 均不等于自动合并/部署/恢复授权——合并、部署、恢复仍由外部确定性门禁与人工流程决定（分析推断，依据仓库 2026-08-06 逐项核验）。
- 具体 foundation model、prompt 编排、planner、重试策略**未公开**。

---

## 三、CodePipeline / CodeBuild / CodeDeploy 2024-2026 的 AI/Agent 能力演进（问题 3）

**核验结论（分析推断，基于现状事实）：**
1. 截至 2026-08-07，三项服务官方 Features 页与欢迎页均**无任何 AI/Agent/生成式能力条目**（F15-F17）。这与仓库既有结论一致：传统 AWS CI/CD 核心保持"确定性机制"定位，AI 能力被 AWS 放在旁路产品线（DevOps Agent / Amazon Q Developer / AWS Transform）而非内建于 CodePipeline/CodeBuild/CodeDeploy。
2. CodePipeline 与智能化的实际连接点是**旁路式**的：DevOps Agent 把 CodePipeline 的 pipeline 结构学习为 `understanding-pipeline-topology` 技能（2026-06-11），供事件调查与发布就绪审查使用，但**不**改写 CodePipeline 的编排执行；CodePipeline 仍由确定性规则执行。
3. **待验证假设**：2024-2025 期间三项服务可能有过 AI 相邻的增量（如控制台向导、Q Developer 辅助生成 pipeline/buildspec），但本次无法通过 What's New 归档检索到可核验的一手公告（见证据缺口 G1）。**不得**把"未检索到"表述为"业界不存在"。

---

## 四、AWS Transform 与 DevOps Agent 的关系（问题 4 补充）

- **已证实**：DevOps Agent built on AgentCore（F10）；Transform powered by Bedrock（F18）；Transform continuous modernization GA（F19）；Transform custom 可集成 DevOps Agent release-readiness skill（F20）。
- **未证实**：Transform → AgentCore 内部构建依赖（`unverified`，禁止画边）。
- **边界**：Transform 与 DevOps Agent 是不同垂直域的独立托管 Agent 服务；集成是可选、定向的 review 工作流，不构成"review = 发布授权"。

---

## 五、与 GitHub / Microsoft / Harness 的差异化定位（问题 5，分析推断）

> 依据仓库既有 Deep Dive（`aws-microsoft-intelligent-cicd`、`harness-company`）与本次核验，标注为**分析推断**，不写成厂商原话或行业定论。

| 维度 | AWS | GitHub/Microsoft | Harness |
|---|---|---|---|
| AI 落点 | 旁路：DevOps Agent（发布前审查 + 发布后调查）+ Q Developer 审查 + Transform | 仓库内闭环：Copilot 审查/修复 PR + Code Quality（Ruleset 门禁）+ Actions | 平台内：CI/CD 编排中嵌入 AI（门槛/流程即产品） |
| 发布前验证 | 托管验证环境 build/run/test + 对客户目标探索式测试（Preview） | coding agent 跑测试 + CodeQL 确定性扫描 + 覆盖率门禁 | 门禁/验证并入交付流程 |
| 运行反馈 | 事件调查/预防/自定义 SRE Agent（GA）回灌交付 | Azure SRE Agent / Defender code-to-cloud | 恢复与交付在单平台内闭环 |
| 门禁权威 | 映射到 GitHub/GitLab 外部 required check/approval | GitHub Ruleset 确定性阈值 | 平台内确定性策略 |
| 差异化主张 | 交付—运行共享上下文（topology/pipeline/依赖）是 AWS 独有底座 | 仓库原生、变更即证据 | 编排与智能同一平台 |

`AWS 的差异化定位在于把 CI/CD 与运行证据收敛为同一份 Agent 上下文，而非改造 CI/CD 编排器本身`——该判断为分析推断，置信度 medium，不支持"整体领先"或"端到端自治"结论。

---

## 六、证据缺口清单

- **G1（重要）**：未能在 2024-2026 What's New 归档中定位 CodePipeline / CodeBuild / CodeDeploy 的 AI 相关公告。What's New JSON 搜索 API 与 RSS 分类过滤本次均不可用（返回空或全量）；月度归档页为 JS 渲染无法枚举。三项服务 2024-2025 的 AI/Agent 增量存在与否**未核验**，标记 `unverified`，不得写死"绝无 AI 增量"。
- **G2**：AgentCore temporal policies 与 rate limiting 的发布公告未给独立 GA/Preview 标签，仅能写"2026-08-06 公告"。
- **G3**：DevOps Agent 2026-06 之后新增的 custom agents/MCP/A2A/memories 等未逐项标注 GA/Preview，本报告只报发布日期，不断言生命周期。
- **G4**：仓库既有记录的两处小冲突（GA 日期文档 03-30 vs 公告 03-31；Release Management 文档历史 06-11 vs 公告 06-17）本次未消除，按公告日期为准记录。
- **G5**：所有效果数值（MTTR 从 hours 到 minutes、Reviews 8-10 分钟、客户证言）均为厂商自述，无独立第三方验证，不进入正式结论。
- **G6**：AgentCore 各组件区域可用性逐能力不同（Runtime/Gateway/Identity/Observability 20 区、Harness/Memory 15 区、Payments 4 区、Web Search 仅 us-east-1），本次未全量重核，引用仓库 2026-08-07 核验记录。

---

## 治理说明

本报告为业界洞察研究成果，按 AGENTS.md 归入 `00_sources/` 供批量洞察与深度洞察复用。全部状态、日期、版本号来自 2026-08-07 实际访问的 AWS 官方页面；`unverified` 项已显式标记；所有效果指标为厂商自述，未外推为行业平均值。下划线结论如需进入 `90_report/` 或 Presentation，须由主 Agent 完成逐主张事实审计并补齐 G1 等证据缺口。
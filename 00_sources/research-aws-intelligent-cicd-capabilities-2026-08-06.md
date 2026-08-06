---
title: AWS 智能化 CI/CD 能力复核与扩展研究报告
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/aws
status: complete
as_of: 2026-08-06
accessed: 2026-08-06
confidence: high-for-status-and-mechanism-from-primary-docs; medium-for-effect-metrics-all-vendor-claimed; low-for-release-management-whats-new-announcement-url-unlocated
---

# AWS 智能化 CI/CD 能力复核与扩展研究报告

## 提纲

1. 研究范围与访问时间
2. 特性清单总表
3. 各项能力详细核验
4. 新发现的特性
5. 相对 2026-08-05 底稿的更新说明
6. 证据缺口与排除项
7. 复核记录（链接可访问性）

## 结论先行

截至 2026-08-06，AWS 在智能化 CI/CD 领域最完整的形态是 **AWS DevOps Agent** 双线结构：**Production Operations 线已于 2026-03-31 达到 GA**（What's New 公告发布时间；文档历史记录为 2026-03-30），**Release Management 线（发布就绪审查、托管验证、发布测试）为 Preview，仅限 us-east-1**。原有九项候选能力（A1–A9）全部获得一手来源复核；另新发现四项（N1 WebUI 外沙箱、N2/N3 Amazon Q Developer 代码审查、N4 AWS Transform continuous modernization GA）。

所有状态、日期、版本号均来自 2026-08-06 实际访问的 AWS 官方页面；无法核验项已显式标记 `unverified`。所有效果数值（MTTR 改善、命中率、客户证言）均为厂商自述，未作为独立结论写入。

## 一、研究范围与访问时间

| 项目 | 说明 |
|---|---|
| 范围 | AWS 在智能化 CI/CD 领域（代码审查→门禁→构建→发布→部署→运行调查→恢复）的 Agent 化能力 |
| 篇幅窗口 | 2025-12（re:Invent 2025 Preview）→ 2026-08-05（文档 What's New 最新条目） |
| 访问时间 | 2026-08-06（全部为当日实际访问） |
| 一手来源类型 | AWS DevOps Agent 用户指南（devopsagent）、AWS 产品页与定价页、What's New 公告、AWS DevOps/News 博客、CodeGuru/CodeCatalyst/Amazon Q Developer 文档、GitHub 官方 Action 仓库 |
| 主要排除项复核 | CodeGuru Reviewer（2025-11-07 起不能新建 repository association）、CodeCatalyst（2025-11-07 起不接受新客户）、Bedrock AgentCore（通用 Agent 平台）、传统 CodePipeline/CodeBuild 规则（确定性机制） |

研究报告起点沿用 2026-08-05 底稿定义的九项候选能力池（A1–A9），本次逐项回链一手来源并核定状态与边界。

## 二、特性清单总表

| ID | 能力 | CI/CD 阶段 | 状态/日期 | 机制 | 用户可见产物 | 授权/副作用边界 | 一手来源链接 |
|---|---|---|---|---|---|---|---|
| A1 | 交付—运行上下文地图（Agent Space 理解 + Code Dependencies + Pipeline Topology + Tool Use 学习技能） | 全部阶段（共享上下文底座） | GA；Production GA 2026-03-31；Code Dep/Pipeline Topology 技能 2026-06-11 加入 | 自动资源发现（CloudFormation 栈 + AWS Resource Explorer 标签资源），结合配置、标签、代码仓、CI/CD 与遥测生成关系；生成结构化学习技能（SKILL.md + 引用文件） | Topology 交互图、Pipeline 视图、版本化 Summary report、"Chat about report" | 只读分析；Agent Space 数据/会话隔离；技能受已接入账户、标签、仓库、Pipeline 与遥测质量限制；地图是派生上下文，不证明因果关系 | [learned-skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)、[topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html)、[about](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html) |
| A2 | 发布就绪审查 + 托管验证环境 build/run/test（BLOCK/CAUTION/SAFE） | 编码/PR/合并前 | **Preview**，仅 us-east-1，2026-06-11 文档加入 | 四透镜审查：自然语言 skills 标准评估、跨仓依赖知识图谱、CloudFormation 访问控制 WAF 最佳实践、AWS 托管验证环境 clone/build/run/test（出站网络 allowlist） | Release readiness report（BLOCK / Proceed with Caution / Safe to Release）、PR/MR 内联评论、GitHub Check Run（可配 required status check） | 审查 Agent 内置护栏：阻断明文凭据、阻断敏感文件+网络外泄、阻断一切 mutative AWS API、强制阶段顺序；自动触发仅限私有仓库；私有依赖需显式授予 runtime role | [release-management-index](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)、[release-readiness-code-review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) |
| A3 | 变更驱动的探索式发布测试 | 部署前验证/发布验证 | **Preview**，仅 us-east-1，2026-06-11 | 基于变更生成测试计划，对客户提供的 production-like 目标 URL（Web UI 浏览器交互 + REST API）执行回归/UAT/用户旅程/集成/边界测试 | GitHub Check Run（pass/fail + 摘要）、web app 测试时间线、UI 截图、复现步骤、建议修复 | **向被测应用发送真实写操作（POST/PUT/DELETE）**，文档明确警告仅用于能容忍探索性写操作的环境；仅公网可达端点，不支持私有网络；CI 触发经 webhook（HMAC-SHA256 或 API key） | [release-testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) |
| A4 | 事件自主分流与根因调查 | 发布后验证、事故调查与恢复准备 | **GA**；GA 2026-03-31；内联缓解建议 2026-07-27 | 从 ServiceNow/ticket、webhook（PagerDuty、Grafana 告警）、手动触发；20 分钟回看窗口内 AI 关联分流 LINKED/SKIPPED/PROCEED；生成根因与缓解计划；缓解建议内联展示 | Investigation timeline、root-cause assessment、mitigation plan、journal、Markdown 导出、AWS Support case 上下文 | AWS native 工具默认只读；缓解建议由人审阅后应用；Sandbox（Preview 2026-07-23）将调查隔离在 Lambda MicroVM 并把 AWS API 代理为只读；Basic Support 不可升级 case | [autonomous-incident-response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html)、[GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/) |
| A5 | 按需 SRE 任务与交互式证据产物 | 运行、发布后复盘、可回看部署 | **GA**；GA 2026-03-31；产物/附件/上下文响应持续演进 | 自然语言查询资源/系统健康/调查洞察/预防建议；按页面上下文响应；生成版本化产物与附件 | Chat artifact（周度运行健康报告、错误趋势报告等）、资源/拓扑问答、调查补充结论、AWS Support case 上下文 | 会话历史保留 90 天且按 Agent Space/用户隔离；IAM 权限控制（aidevops:ListChats/CreateChat/SendMessage）；附件 ≤3.75MB×20 | [on-demand-devops-tasks](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-on-demand-devops-tasks.html) |
| A6 | 历史事故预防与改进待办 | 事故复盘 → 测试/流水线/架构/可观测性改进 | **GA**；GA 2026-03-31；优先级排序 2026-05-13 | 每周自动评估历史调查（可暂停、可手动 Run Now），产出可观测性/基础设施/治理/代码优化四类建议；AI 排序 + 确定性打分；代码类建议附 agent-ready 规格 | Improvements/recommendations backlog、报告；agent-ready 规格（问题陈述→目标仓库→代码变更→测试要求→实施计划） | 建议为顾问性质；Keep/Discard（可附理由供学习）/Implemented 三态；约 6 周自动移除未采纳建议；MTTR/预防价值描述为厂商自述 | [proactive-incident-prevention](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-proactive-incident-prevention.html) |
| A7 | 可调度自定义 SRE Agent | 持续运行治理、发布报告、配置审计、趋势分析 | **GA**；2026-06-12 文档加入；动态子代理委派 2026-07-27 | 用户定义 system prompt + skills + MCP 工具 + 触发器；调度支持 EventBridge 兼容 cron/rate 表达式；Run Now / Run with prompt / Chat 触发 | 每次 invocation trajectory（推理步骤、工具调用、结果）、text/持久 artifact/Proposed recommendation | 单次一个 invocation；只能调用被分配的工具；MCP 工具可能写 ticket/消息/外部系统，需逐 Agent 最小权限与 trigger 治理 | [custom-agents-executing](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-executing-custom-agents.html)、[whats-new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| A8 | IDE/变换 Agent 内的发布反馈回路 | 代码生成、现代化改造、提交前 | GA（MCP 远程访问层 2026-06-12）；Kiro Power 博客 2026-06-19；审查/测试本身继承 Release Management Preview | ① Kiro Power（aws-devops-agent MCP）把发布就绪审查/发布测试/调查带入 IDE并生成修复代码；② Claude Code plugin（aws-agents-for-devsecops）在代码生成期调用审查；③ AWS Transform custom skill（aws-transform-custom-samples 下载）在生成/修改代码后触发 review | IDE 内 findings、review report、Kiro 生成的 remediation code、Transform 输出内 review findings | 经 https://connect.aidevops.{region}.api.aws/mcp 的 access token（read/operate 双作用域、1–60 天、可选 IP allowlist）或 SigV4；支持多 Agent Space 路由；生成的修复仍是候选代码 | [accessing-remote-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)、[Kiro 博客](https://aws.amazon.com/blogs/devops/supercharge-your-cloud-operations-with-the-kiro-power-for-aws-devops-agent/) |
| A9 | MCP/A2A 无界面接入与外部子 Agent 扩展 | 全部阶段（跨工具协作与运行自动化扩展） | **GA**；2026-06-12；500 MCP 工具限额 2026-05-19；scoped access token 2026-06-13 | 入站：外部 MCP 服务器仅 Streamable HTTP（OAuth Client Credentials/3LO、API key、SigV4）作数据源，Agent Space 工具 allowlist；出站：A2A v1.0 远程 Agent 注册后在调查中委派子任务（当前仅限事故调查）；另有 webhook、Asset REST API、EventBridge 事件 | Headless chat/investigation 调用、scoped access token 或 SigV4 身份、外部 Agent 工具调用结果、webhook/事件 | 外部 Agent 强烈建议只读；提示注入风险有安全文档覆盖；每次 token 认证记录 CloudTrail AuthenticateAccessToken 事件（2026-07-21 起）；MCP/A2A 是连接协议不是自动授权 | [mcp-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-mcp-servers.html)、[remote-a2a-agents](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-remote-a2a-agents.html)、[remote-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html) |
| N1 | Sandbox 沙箱（Agent 写/运行代码） | 运行调查 | **Preview**，2026-07-23，区域 us-east-1/us-west-2/ap-northeast-1/eu-west-1 | 每次调查隔离在 Lambda MicroVM；Agent 可写/运行代码；沙箱内 AWS API 调用被代理为只读；出站按显式 allowlist；可预装 pip/npm 包、从沙箱文件系统读技能 | 沙箱会话、代码执行结果并入调查 | 范围限定单次调查；只读 AWS API + 出站 allowlist；不改变生产环境 | [whats-new §2026-07-23](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| N2 | Amazon Q Developer IDE 代码审查（SAST/secrets/IaC/代码质量/部署风险/SCA） | 审查/门禁（IDE 内） | GA（IDE 功能） | 由 Amazon Q Detector Library（源自 CodeGuru）驱动六类问题检测；IDE 内静态分析与建议 | IDE 内 findings、修复建议 | 只读分析；CodeGuru Reviewer 官方替代路径之一 | [code-reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html) |
| N3 | Amazon Q Developer for GitHub 自动 PR 审查 | 审查/门禁（GitHub） | **Preview** | PR 创建/重开时自动审查；threaded findings；可生成修复建议甚至 commit | 整体摘要、线程化 findings、建议修复 | 仅私有仓库；Writing/Maintain/Admin 角色；按月行数限额 | [github-code-reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html) |
| N4 | AWS Transform continuous modernization（技术债分析 + 自主修复 PR） | 代码变更/恢复（相邻，非门禁） | **GA**，2026-08-03 | 连接 GitHub/GitLab/Bitbucket/本地仓库；按需或定时扫描；产出优先级化技术债 findings；按指示自主生成验证过的修复 PR | findings、修复 PR、Web app + atx ct CLI + Kiro Power | 修复 PR 是候选变更，合并/部署仍由人决定；属现代化工具、CI/CD 相邻 | [Transform GA 博客](https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/) |

## 三、各项能力详细核验

### A1 交付—运行上下文地图（环境智能）— GA

来源：[learned-skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)、[topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html)、[about](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html)。

机制：自动发现资源（CloudFormation 栈 + AWS Resource Explorer 标签资源），结合配置、CloudFormation、标签、代码仓、CI/CD 与遥测（CloudWatch Application Signals、Dynatrace）构建关系。生成四类结构化学习技能（SKILL.md + 引用文件）：`understanding-agent-space`（容器架构、关键请求路径、可观测性覆盖）、`understanding-code-dependencies`（服务间/包依赖，2026-06-11 加入）、`understanding-pipeline-topology`（流水线阶段与环境晋升，2026-06-11 加入）、`tool-use-best-practices`（每 30 次调查更新）。

用户可见产物：Topology 页交互图、Pipeline 视图、版本化只读 Summary report、"Chat about report"。

边界：只读分析；Agent Space 数据/会话隔离；Agent Space Understanding 技能每 3 天刷新（活跃空间）；地图是派生上下文，不能证明某次变更的因果关系。

### A2 发布就绪审查 + 托管验证 — Preview（us-east-1）

来源：[release-management-index](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)、[release-readiness-code-review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html)。

机制：四透镜审查——①自然语言 Skills 标准评估；②知识图谱跨仓库依赖分析；③CloudFormation 访问控制 WAF 最佳实践核验；④AWS 托管验证环境中 clone/build/run/test（自动安装依赖、构建、生成并运行测试计划；出站网络为预定义 allowlist，如 npm/pypi/maven 等）。产出 BLOCK / Proceed with Caution / Safe to Release 三档结论。

用户可见产物：Changes 页报告（风险类别 + Blocking/Warning/Informational 严重级 + Execution journal）、PR/MR 内联评论、GitHub Check Run（可配置为 required status check 门禁）。

关键边界：审查 Agent 内置护栏——**阻断明文凭据、阻断敏感文件+网络组合外泄、阻断一切变更性 AWS API（mutative 操作全禁，仅 describe/get/list）、强制阶段顺序**；自动触发仅限私有仓库；每仓库可独立开关；私有依赖需显式授予 runtime role 与 VPC 私网访问；文档称 Reviews 常 8–10 分钟完成（厂商自述）。

### A3 变更驱动的探索式发布测试 — Preview（us-east-1）

来源：[release-testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)。

机制：基于代码变更或测试意图生成测试计划，对客户提供的 production-like 目标 URL（Web UI 浏览器交互 + REST API HTTP 测试）执行探索式回归/UAT/用户旅程/集成/边界测试，含边缘案例、错误处理、schema 校验。

用户可见产物：GitHub Check Run（pass/fail + 摘要）；web app 中测试时间线、UI 截图、复现步骤、建议修复。

关键边界（副作用）：**该测试会向被测应用发送真实写操作（POST/PUT/DELETE），可能创建/修改/删除数据**——文档明确警告仅用于能容忍探索性写操作的环境，建议只对 staging；仅支持公网可达端点，不支持私有网络端点；CI 触发经 webhook（HMAC-SHA256 或 API key）。

### A4 事件自主分流与根因调查 — GA（2026-03-31）

来源：[autonomous-incident-response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html)、[GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)。

机制：从 ServiceNow 工单/监控 webhook（PagerDuty、Grafana 告警）/手动触发；分流阶段在 20 分钟回看窗口内做 AI 关联，决策 LINKED/SKIPPED（由 skip skill 定义）/PROCEED；调查后自动生成根因分析、缓解计划（AWS Support 案例可携完整调查上下文）。2026-07-27 起告警触发调查时内联展示缓解建议（调查与缓解单一流程）；2026-06-15 结构化反馈闭环（正确/错误 RCA 标注 → 准确率指标）。

边界：AWS native production 工具默认不修改基础设施或应用；缓解建议由人审阅后可应用；Sandbox（2026-07-23 Preview）将每次调查隔离在 Lambda MicroVM 中并把沙箱内 AWS CLI/SDK 调用代理为只读；Basic Support 客户不能升级 AWS Support 案例。

### A5 按需 SRE 任务与交互式证据产物 — GA

来源：[on-demand-devops-tasks](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-on-demand-devops-tasks.html)。

机制：面向资源/系统健康/调查洞察/预防建议的自然语言查询；按页面上下文响应；Chat 可生成版本化产物（周度运行健康报告、错误趋势报告等）、支持附件（图片/文档/代码，≤3.75MB×20）。

边界：会话历史保留 90 天且按 Agent Space/用户隔离；IAM 权限控制（aidevops:ListChats/CreateChat/SendMessage）；生成报告或建议不是变更执行。

### A6 历史事故预防与改进待办 — GA

来源：[proactive-incident-prevention](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-proactive-incident-prevention.html)。

机制：每周自动评估历史调查（可暂停、可手动 Run Now），产出四大类建议（可观测性/基础设施/治理/代码优化）；AI 排序 + 确定性打分；代码类建议附带 **agent-ready 规格**（问题陈述→目标仓库→代码变更→测试要求→实施计划），可直接交接给编码 Agent。2026-05-13 起按 Impact、Likelihood、Recovery 排序。

边界：建议为顾问性质；Keep/Discard（可附自然语言理由供学习）/Implemented 三态；未采纳建议约 6 周自动移除；AWS 对 MTTR 与预防价值的描述是厂商自述。

### A7 可调度自定义 SRE Agent — GA（2026-06-12）

来源：[custom-agents-executing-custom-agents.html](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-executing-custom-agents.html)、[whats-new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)。

机制：用户定义系统提示 + 技能 + MCP 工具 + 触发器；调度支持 EventBridge 兼容 **cron 与 rate 表达式**（如 `cron(0 9 ? * MON-FRI *)`、`rate(1 hour)`）；可 Run Now / Run with prompt / Chat 触发；2026-07-27 起支持动态子代理委派（接近上下文上限时自动委派）。

边界：单次一个 invocation；工具调用轨迹可审计；只能调用被分配的工具，但这些 MCP 工具可能写 ticket/消息/外部系统，需逐 Agent 最小权限、输出审阅与 trigger 治理；Release Manager 工具集（2026-07-27）允许自定义 Agent 创建/列出/取消 Release Manager 任务。

### A8 IDE/变换 Agent 内的发布反馈回路 — GA（集成层）

来源：[accessing-remote-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)、[Kiro 博客](https://aws.amazon.com/blogs/devops/supercharge-your-cloud-operations-with-the-kiro-power-for-aws-devops-agent/)。

机制：① **Kiro Power**（`aws-devops-agent`，2026-06-19 博客）经由 MCP 将发布就绪审查、发布测试、调查直接带入 IDE，可基于 investigation finding 生成修复代码；② **Claude Code 插件**（`aws-agents-for-devsecops`）在代码生成期调用审查；③ **AWS Transform custom skill**（从 `aws-samples/aws-transform-custom-samples` 下载，2026-06-14 文档加入）使 Transform 生成/修改代码时触发审查，并在最终变更前处理 findings。

边界：通过 `https://connect.aidevops.{region}.api.aws/mcp` 的 access token（read/operate 双作用域、1–60 天过期、可选 IP allowlist）或 SigV4 认证；支持多 Agent Space 路由；生成的修复仍是候选代码，须经版本控制、测试和合并 Gate。

### A9 MCP/A2A 无界面接入与外部子 Agent 扩展 — GA

来源：[mcp-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-mcp-servers.html)、[remote-a2a-agents](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-remote-a2a-agents.html)、[remote-servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)。

机制：入站——外部 MCP 服务器（仅 Streamable HTTP；OAuth Client Credentials/3LO、API key、SigV4）作为数据源，按 Agent Space 工具 allowlist（上限 500 工具，2026-05-19）；出站——**A2A v1.0** 远程 Agent 注册后，Agent 可在调查中委派子任务并吸收其发现（当前仅限事故调查）；另有 webhook（HMAC/API key）、Asset REST API（2026-06-08）、EventBridge 事件、AWS Support 集成。

边界：外部 Agent 强烈建议只读；提示注入风险由安全文档覆盖；每次 MCP/A2A access token 认证记录 CloudTrail `AuthenticateAccessToken` 事件（2026-07-21 起）；MCP/A2A 是连接协议不是自动授权，各工具权限各自生效。

## 四、新发现的特性（2026-08-05 底稿之外）

1. **N1 — Sandbox 沙箱（Preview，2026-07-23）**：允许 Agent 在隔离的 Lambda MicroVM 中写/运行代码，范围限定单次调查；沙箱内 AWS API 调用被代理为只读；出站按显式 allowlist 控制；可预装 pip/npm 包、从沙箱文件系统读技能。可用区域 us-east-1/us-west-2/ap-northeast-1/eu-west-1。对"运行调查与实验"阶段的新机制。
2. **N2 — Amazon Q Developer IDE 代码审查（GA）**：含 SAST、secrets 检测、IaC 审查、代码质量、**代码部署风险（评估发布/部署风险）**、SCA 六类问题；由 Amazon Q Detector Library（源自 CodeGuru）驱动。是 CodeGuru Reviewer 的官方替代路径之一。来源：[code-reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html)。
3. **N3 — Amazon Q Developer for GitHub（Preview）**：PR 创建/重开时自动代码审查，整体摘要 + 线程化 findings + 建议修复；`/q review` 手动触发。仅限私有代码审查场景中的 Writing/Maintain/Admin 角色。来源：[github-code-reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html)。
4. **N4 — AWS Transform continuous modernization（GA，2026-08-03）**：连接 GitHub/GitLab/Bitbucket/本地仓库，按需或定时扫描，产出优先级化技术债 findings，并按指示**自主生成验证过的修复 PR**；Web app + `atx ct` CLI + Kiro Power。属"变更/恢复"相邻能力（自助修复），非门禁。来源：[Transform GA 博客](https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/)。
5. **N5 — DevOps Agent Memories（2026-06-12）**：指令（directives）与监控（monitors）两类内置记忆存储，记忆告警/指标反复根因，加速调查。来源：[whats-new §2026-06-12](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)。
6. **N6 — Webhook / Asset API / EventBridge 接口族（2026-04-28 至 06-08）**：5 种访问方式（web app、MCP、ACP、webhook、API）已文档化，支持将 Agent 纳入既有事件驱动 CI/CD。来源：[whats-new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)。

## 五、相对 2026-08-05 底稿的更新说明

| 项 | 2026-08-05 底稿 | 2026-08-06 复核结果 |
|---|---|---|
| Release Management 状态 | Preview 2026-06-17 | 文档历史条目为 **2026-06-11**（Preview、us-east-1）。以 2026-06-11 为准，标注原 06-17 为文档不一致 |
| A2 托管验证网络 | — | 新增：出站网络为预定义 allowlist（npm/pypi/maven 等） |
| A3 触发方式 | Check Run 为主 | 补充 webhook（HMAC-SHA256 或 API key）触发 CI 集成 |
| A4 缓解建议 | — | 核实为 2026-07-27，内联缓解建议 + 自定义 Agent 可用 release manager 工具 |
| A4/A5 付费模型 | — | 补充定价页：按 Agent 工作秒计费、无最低承诺；Release Management Preview 免费；新客户 2 个月试用；支持抵扣（Unified Ops 100% / Enterprise 75% / Business+ 30%） |
| A9 工具限额 | — | 核实 500 MCP 工具上限（2026-05-19）、scoped access token（2026-06-13）、CloudTrail AuthenticateAccessToken（2026-07-21） |
| 新增 N1–N6 | 无 | 六项新发现，见上节 |
| CodeGuru/CodeCatalyst | 已知排除 | 复核确认两者同日（2025-11-07）收缩；编码该时间点观察 |
| GitHub Action 名 | 文档不一致 | 实际存在 `aws-actions/devops-agent-qa@v1`（HTTP 200）；文档文字处 `aws-actions/devops-agent-release-testing@v1`（HTTP 404）为文档笔误，引用以实际仓库为准 |

## 六、证据缺口与排除项

### 单一厂商来源支撑（标为厂商自述）
- **全部效果数据均为厂商/客户自述，无独立第三方验证**：WGU "MTTR 从 2 小时降至 28 分钟（-77%）"、Zenchef "20–30 分钟 vs 1–2 小时（-75%）"、Deriv "DB 权限缺口 + 长运行服务内存泄漏被拦截"、Kiro/Quantiphi/Tech Mahindra/AWS Transform 客户证言，均来自 AWS 产品页、第一方博客或客户引用。
- **"Reviews 常 8–10 分钟完成"** 等性能表述来自 AWS 文档，无独立实测。

### 无法核验/缺失
- **Release Management 的 What's New 公告 URL 未定位到**（多次 URL 猜测与 What's New 搜索 API 均失败；What's New 全文 RSS 仅覆盖近两周，JSON API 返回 2024 年缓存数据）。证据以用户指南为准，公告 URL 标记 `unverified`。
- **GA 日期存在两处差异**：文档历史记 2026-03-30，What's New 公告发布于 2026-03-31。建议以 What's New 公告日 2026-03-31 为主，注明文档侧 03-30。
- **文档内部矛盾**：release-testing 页一处文字写 `aws-actions/devops-agent-release-testing@v1`，示例与已核验的 GitHub 仓库实际为 `aws-actions/devops-agent-qa@v1`（后者 200，前者 404）。引用时以实际仓库为准。
- **逐特性 GA/Preview 标注不完整**：Chat 各子功能（产物、附件、inline 缓解、Memories 等）无逐项 GA 日期，仅能依据 What's New 表推断。

### 排除项复核（确认正确）
- **Amazon CodeGuru Reviewer**：文档明确 "As of November 7, 2025, you can't create new repository associations in Amazon CodeGuru Reviewer"，仅存量关联可用；官方替代路径指向 Amazon Q Developer 代码审查与 Amazon Inspector Code Security。✅ 排除成立。
- **Amazon CodeCatalyst**：文档明确 "**will no longer be open to new customers starting on November 7, 2025**"，提供迁移指南。✅ 排除成立（属终止接受新客户状态）。
- **Amazon Bedrock AgentCore**：通用 Agent 构建/连接/优化平台，不属于原生 CI/CD 编排。✅ 排除成立。
- **传统 CodePipeline/CodeBuild/MRA 规则**：确定性机制，非"智能化"。✅ 排除成立。

### 供 90_report 参考的观察（需主 Agent 复核后再上升为观点）
- 2025-11-07 是 AWS 开发者工具收缩的关键节点（CodeGuru 停新关联 + CodeCatalyst 停新客户同日），能力向 **AWS DevOps Agent + Amazon Q Developer + Amazon Inspector Code Security + AWS Transform** 四条线收敛。
- AWS DevOps Agent 定价：按 Agent 工作秒计费、无最低承诺；Release Management 预览期免费；支持客户按月获抵扣（Unified Ops 100% / Enterprise 75% / Business+ 30%）。"支持抵扣"本身是 AWS 的采纳设计，厂商自述，不上升为行业结论。

## 七、复核记录（链接可访问性，2026-08-06）

| 来源 | URL | 状态 | 备注 |
|---|---|---|---|
| 产品页 | aws.amazon.com/devops-agent/ | ✅ 200 | Preview/GA 状态、客户、集成清单 |
| 定价页 | aws.amazon.com/devops-agent/pricing/ | ✅ 200 | 按秒计费、支持抵扣、试用详情 |
| 文档·About | docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html | ✅ 可读 | 双控制台架构、Agent Space |
| 文档·Learned Skills | about-aws-devops-agent-learned-skills.html | ✅ 可读 | 4 项学习技能 |
| 文档·Topology | about-aws-devops-agent-what-is-a-devops-agent-topology.html | ✅ 可读 | CloudFormation + Resource Explorer |
| 文档·Release Mgmt 索引 | working-with-devops-agent-release-management-index.html | ✅ 可读 | Preview + us-east-1 |
| 文档·Code Review | release-management-release-readiness-code-review.html | ✅ 可读 | 护栏、allowlist、Kiro/Claude/Transform |
| 文档·Release Testing | release-management-release-testing.html | ✅ 可读 | 写操作副作用警告、Check Run |
| 文档·Incident Response | production-operations-autonomous-incident-response.html | ✅ 可读 | 分流、内联缓解、AWS Support |
| 文档·Prevention | production-operations-proactive-incident-prevention.html | ✅ 可读 | 4 类建议、agent-ready 规格 |
| 文档·Custom Agents | custom-agents-executing-custom-agents.html | ✅ 可读 | cron/rate 调度 |
| 文档·On-Demand | working-with-devops-agent-on-demand-devops-tasks.html | ✅ 可读 | 产物、附件、上下文响应 |
| 文档·Remote Servers | accessing-...-remote-servers.html | ✅ 可读 | MCP/A2A、token、SigV4 |
| 文档·MCP | connecting-mcp-servers.html | ✅ 可读 | 认证、allowlist、限额 |
| 文档·A2A | connecting-remote-a2a-agents.html | ✅ 可读 | 仅调查委派 |
| 文档·GitHub 集成 | connecting-to-cicd-pipelines-connecting-github.html | ✅ 可读 | Check Run、权限表 |
| 文档·Supported Regions | about-aws-devops-agent-supported-regions.html | ✅ 可读 | Release 仅 us-east-1 |
| 文档·What's New / History | whats-new.html / document-history.html | ✅ 可读 | 2026-08-05 最新条目 |
| What's New·GA 公告 | about-aws/whats-new/2026/03/aws-devops-agent-generally-available/ | ✅ 200 | Posted Mar 31, 2026 |
| 博客·GA（与 Datadog） | blogs/devops/production-ready-...-now-ga-and-datadog-mcp-server/ | ✅ 200 | 2026-06-18 |
| 博客·Kiro Power | blogs/devops/supercharge-...-kiro-power-... | ✅ 200 | 2026-06-19 |
| 博客·Transform GA | blogs/devops/analyze-and-remediate-technical-debt... | ✅ 200 | 2026-08-03 |
| 文档·CodeGuru | codeguru/latest/reviewer-ug/welcome.html + codeguru-reviewer-availability-change.html | ✅ 可读 | 2025-11-07 停新关联 |
| 文档·CodeCatalyst | codecatalyst/latest/adminguide/managing-generative-ai-features.html | ✅ 可读 | 2025-11-07 停新客户 |
| 文档·Amazon Q 审查 | amazonq/.../code-reviews.html + github-code-reviews.html | ✅ 可读 | IDE 版 GA；GitHub 版 Preview |
| GitHub Action | github.com/aws-actions/devops-agent-qa | ✅ 200 | 实际 action 名 |
| ❌ What's New·Release Mgmt 公告 | 多次 URL 猜测 + 搜索 API | 未定位 | 标记 unverified |

**治理说明**：本报告全部状态、日期、版本号均来自实际访问页面；`unverified` 项已显式标记。所有效果指标为厂商自述，未外推为行业平均值。本文件为业界洞察研究成果，按 AGENTS.md 归入 `00_sources/` 供批量洞察与深层洞察复用。
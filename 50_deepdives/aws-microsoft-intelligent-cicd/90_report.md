---
title: AWS 与 Microsoft 智能化 CI/CD 能力全景——详细特性清单与能力介绍
tags:
  - research/agentic-cicd
  - research/report
  - company/aws
  - company/microsoft
  - company/github
  - company/azure
status: complete
as_of: 2026-08-07
confidence: high-for-mechanism-and-status medium-for-outcomes
---

# AWS 与 Microsoft 智能化 CI/CD 能力全景

> [!important] 阅读约定
> - 本报告是深度洞察成果，覆盖 AWS 与 Microsoft（含 GitHub、Azure DevOps、Azure）截至 **2026-08-07** 可核验的智能化 CI/CD 功能。
> - 每项能力标注**产品状态**（GA / Preview / limited public preview / unverified）、**发布日期**、**机制**、**用户可见产物**、**授权边界**与**一手来源链接**。
> - 所有效果数据（MTTR、解决率等）均为厂商自述，不进入正式结论。区分"来源明确陈述的事实""分析推断""待验证假设"。
> - `unverified` 表示无法从官方来源核验生命周期，不代表产品不存在。
> - 除业务层能力清单外，本报告新增**平台层交叉对比**（AgentCore 双闭环 vs Microsoft 三层收敛），见下方"平台层交叉对比"章节。

## 一、结论先行

两家厂商都把 Agent / AI 放进了软件交付链的**发布前审查、合并前门禁、发布后恢复**三个位置，但机制方向相反：

- **AWS 路线：收敛"交付—运行上下文"。** 以 AWS DevOps Agent 为核心，把资源拓扑、跨仓依赖、流水线晋级关系与运行遥测汇成共享上下文，再用于发布就绪审查、变更驱动发布测试与事件调查。Production operations 已 GA（2026-03-31），Release Management 为 `us-east-1` Preview。其运行与治理底座为 **Amazon Bedrock AgentCore**（`built on AgentCore` 已获官方证实，见 [AWS DevOps Blog 2026-03-31](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)）。
- **Microsoft 路线：仓库内"修复—门禁"闭环。** 以 GitHub Copilot / Code Quality 为核心，把代码变更、质量与安全发现、Draft PR 修复和 Ruleset 门禁在仓库内闭环，再以 Azure SRE Agent 延伸到运行恢复。coding agent / code review / Code Quality / Dependabot remediation 已 GA；Agentic autofix 与 Agentic Workflows 为 Public Preview；Azure 侧多次级仍为 limited preview。

**两条路线都没有取消确定性 CI/CD Gate。** `BLOCK`、Check Run、Autofix PR、缓解建议、Autonomous run mode 都不等于自动合并、部署或恢复授权。

**平台层（治理 Agent 的底座）呈现不同结构：** AWS 以 AgentCore 单产品双闭环（行动闭环 + 质量闭环）承载；Microsoft 以 Foundry Agent Service + Agent 365 + Entra Agent ID 三层收敛承载，没有单一对等物。两家都先做"可治理、可观测、可版本化的 Agent 平台"，业务层 Agent 的写权限、版本与质量信号最终收敛到平台层控制面（详见"平台层交叉对比"章节，分析推断 medium-high）。

---

## 二、AWS 智能化 CI/CD 能力清单

### 总表

| ID | 能力 | CI/CD 阶段 | 状态/日期 | 一手来源 |
|---|---|---|---|---|
| A1 | 交付—运行上下文地图（Topology + Learned Skills） | 全部阶段共享底座 | GA；Production GA 2026-03-31；Code Dep/Pipeline Topology 技能 2026-06-11 | [Learned skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)、[Topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html) |
| A2 | 发布就绪审查 + 托管验证 build/run/test | 编码/PR/合并前 | Preview，仅 us-east-1，2026-06-11 文档加入 | [Release readiness code review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html)、[Release management 索引](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html) |
| A3 | 变更驱动的探索式发布测试 | 部署前验证/发布验证 | Preview，仅 us-east-1 | [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) |
| A4 | 事件自主分流与根因调查 | 发布后验证、事故调查与恢复准备 | GA；GA 2026-03-31；内联缓解建议 2026-07-27 | [Autonomous incident response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html)、[GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/) |
| A5 | 按需 SRE 任务与交互式证据产物 | 运行、发布后复盘 | GA | [On-demand DevOps tasks](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-on-demand-devops-tasks.html) |
| A6 | 历史事故预防与改进待办 | 事故复盘 → 改进 | GA；优先级排序 2026-05-13 | [Proactive incident prevention](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-proactive-incident-prevention.html) |
| A7 | 可调度自定义 SRE Agent | 持续运行治理 | GA；2026-06-12 文档加入；动态子代理 2026-07-27 | [Custom agents executing](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-executing-custom-agents.html)、[What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| A8 | IDE/变换 Agent 内的发布反馈回路（Kiro、Claude Code、Transform） | 代码生成、提交前 | 集成层 GA（MCP 远程访问 2026-06-12）；审查/测试继承 Release Preview | [Remote servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)、[Kiro 博客](https://aws.amazon.com/blogs/devops/supercharge-your-cloud-operations-with-the-kiro-power-for-aws-devops-agent/) |
| A9 | MCP/A2A 无界面接入与外部子 Agent 扩展 | 全部阶段 | GA；2026-06-12；500 工具限额 2026-05-19 | [MCP servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-mcp-servers.html)、[Remote A2A agents](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-remote-a2a-agents.html) |
| N1 | Sandbox 沙箱（Agent 写/运行代码） | 运行调查 | Preview，2026-07-23 | [What's new §2026-07-23](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| N2 | Amazon Q Developer IDE 代码审查（六类检测） | 审查/门禁（IDE 内） | GA | [Amazon Q code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html) |
| N3 | Amazon Q Developer for GitHub 自动 PR 审查 | 审查/门禁（GitHub） | Preview | [GitHub code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html) |
| N4 | AWS Transform continuous modernization（技术债分析 + 修复 PR） | 代码变更/恢复（相邻） | GA，2026-08-03 | [Transform GA 博客](https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/) |
| N5 | DevOps Agent Memories（指令/监控记忆） | 调查加速 | 2026-06-12 公告 | [What's new §2026-06-12](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| N6 | Webhook / Asset API / EventBridge 接口族 | 事件驱动接入 | 2026-04-28 至 06-08 | [What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |

### 逐项能力介绍（AWS）

#### A1 交付—运行上下文地图（环境智能）—— GA

自动发现资源（CloudFormation 栈 + AWS Resource Explorer 标签资源），结合配置、标签、代码仓、CI/CD 与遥测构建关系图，生成四类结构化学习技能：
- `understanding-agent-space`：容器架构、关键请求路径、可观测性覆盖；
- `understanding-code-dependencies`：服务间/包依赖（2026-06-11 加入）；
- `understanding-pipeline-topology`：流水线阶段与环境晋升（2026-06-11 加入）；
- `tool-use-best-practices`：每 30 次调查更新。

**产物：** Topology 交互图、Pipeline 视图、版本化只读 Summary report、"Chat about report"。**边界：** 只读分析；Agent Space 数据/会话隔离；技能每 3 天刷新（活跃空间）；地图是派生上下文，不证明因果关系。

来源：[Learned skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)、[Topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html)、[About AWS DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html)

#### A2 发布就绪审查 + 托管验证 —— Preview（us-east-1）

四透镜审查：①自然语言 Skills 标准评估；②知识图谱跨仓库依赖分析；③CloudFormation 访问控制/网络 WAF 最佳实践核验；④AWS 托管验证环境中 clone/build/run/test（自动安装依赖、构建、生成并运行测试计划；出站网络为预定义 allowlist，如 npm/pypi/maven）。产出 `BLOCK / Proceed with Caution / Safe to Release` 三档结论。

**产物：** Changes 页报告（风险类别 + Blocking/Warning/Informational 严重级 + Execution journal）、PR/MR 内联评论、GitHub Check Run（可配置为 required status check 门禁）。

**关键边界：** 审查 Agent 内置护栏——**阻断明文凭据、阻断敏感文件+网络组合外泄、阻断一切变更性 AWS API（mutative 操作全禁）、强制阶段顺序**；自动触发仅限私有仓库；每仓库可独立开关；私有依赖需显式授予 runtime role 与 VPC 私网访问；文档称 Reviews 常 8–10 分钟完成（厂商自述）。

来源：[Release management 索引](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)、[Release readiness code review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html)

#### A3 变更驱动的探索式发布测试 —— Preview（us-east-1）

基于代码变更或测试意图生成测试计划，对客户提供的 production-like 目标 URL（Web UI 浏览器交互 + REST API）执行探索式回归/UAT/用户旅程/集成/边界测试（含边缘案例、错误处理、schema 校验）。

**产物：** GitHub Check Run（pass/fail + 摘要）；web app 测试时间线、UI 截图、复现步骤、建议修复。

**关键边界（副作用）：** **测试会向被测应用发送真实写操作（POST/PUT/DELETE），可能创建/修改/删除数据**——文档明确警告仅用于能容忍探索性写操作的环境，建议只对 staging；仅支持公网可达端点，不支持私有网络端点；CI 触发经 webhook（HMAC-SHA256 或 API key）。

来源：[Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)

#### A4 事件自主分流与根因调查 —— GA（2026-03-31）

从 ServiceNow 工单/监控 webhook（PagerDuty、Grafana 告警）/手动触发；分流阶段在 20 分钟回看窗口内做 AI 关联，决策 `LINKED / SKIPPED / PROCEED`；调查后自动生成根因分析、缓解计划（AWS Support 案例可携带完整调查上下文）。2026-07-27 起告警触发调查时内联展示缓解建议；2026-06-15 结构化反馈闭环（正确/错误 RCA 标注 → 准确率指标）。

**边界：** AWS native production 工具默认不修改基础设施或应用；缓解建议由人审阅后应用；Sandbox（2026-07-23 Preview）将每次调查隔离在 Lambda MicroVM 中并把沙箱内 AWS CLI/SDK 调用代理为只读；Basic Support 客户不能升级 case。

来源：[Autonomous incident response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html)、[GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)

#### A5 按需 SRE 任务与交互式证据产物 —— GA

面向资源/系统健康/调查洞察/预防建议的自然语言查询；按页面上下文响应；Chat 可生成版本化产物（周度运行健康报告、错误趋势报告等）、支持附件（图片/文档/代码，≤3.75MB×20）。

**边界：** 会话历史保留 90 天且按 Agent Space/用户隔离；IAM 权限控制（aidevops:ListChats/CreateChat/SendMessage）；生成报告不是变更执行。

来源：[On-demand DevOps tasks](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-on-demand-devops-tasks.html)

#### A6 历史事故预防与改进待办 —— GA

每周自动评估历史调查（可暂停、可手动 Run Now），产出四类建议（可观测性/基础设施/治理/代码优化）；AI 排序 + 确定性打分；代码类建议附带 **agent-ready 规格**（问题陈述→目标仓库→代码变更→测试要求→实施计划），可直接交接给编码 Agent。2026-05-13 起按 Impact、Likelihood、Recovery 排序。

**边界：** 建议为顾问性质；`Keep / Discard / Implemented` 三态；未采纳建议约 6 周自动移除；MTTR/预防价值描述为厂商自述。

来源：[Proactive incident prevention](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-proactive-incident-prevention.html)

#### A7 可调度自定义 SRE Agent —— GA（2026-06-12）

用户定义系统提示 + 技能 + MCP 工具 + 触发器；调度支持 EventBridge 兼容 **cron 与 rate 表达式**（如 `cron(0 9 ? * MON-FRI *)`、`rate(1 hour)`）；可 Run Now / Run with prompt / Chat 触发；2026-07-27 起支持动态子代理委派（接近上下文上限时自动委派）。

**边界：** 单次一个 invocation；工具调用轨迹可审计；只能调用被分配的工具，但这些 MCP 工具可能写 ticket/消息/外部系统，需逐 Agent 最小权限、输出审阅与 trigger 治理；Release Manager 工具集（2026-07-27）允许自定义 Agent 创建/列出/取消 Release Manager 任务。

来源：[Custom agents executing](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-executing-custom-agents.html)、[What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)

#### A8 IDE/变换 Agent 内的发布反馈回路 —— GA（集成层）

① **Kiro Power**（`aws-devops-agent` MCP，2026-06-19 博客）将发布就绪审查、发布测试、调查直接带入 IDE，可基于 investigation finding 生成修复代码；② **Claude Code 插件**（`aws-agents-for-devsecops`）在代码生成期调用审查；③ **AWS Transform custom skill**（`aws-samples/aws-transform-custom-samples`）使 Transform 生成/修改代码后触发审查。

**边界：** 经 `https://connect.aidevops.{region}.api.aws/mcp` 的 access token（read/operate 双作用域、1–60 天、可选 IP allowlist）或 SigV4 认证；生成的修复仍是候选代码。

来源：[Remote servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)、[Kiro 博客](https://aws.amazon.com/blogs/devops/supercharge-your-cloud-operations-with-the-kiro-power-for-aws-devops-agent/)

#### A9 MCP/A2A 无界面接入与外部子 Agent 扩展 —— GA

入站：外部 MCP 服务器（Streamable HTTP；OAuth Client Credentials/3LO、API key、SigV4）作数据源，按 Agent Space 工具 allowlist（上限 500 工具）；出站：**A2A v1.0** 远程 Agent 注册后，Agent 可在调查中委派子任务并吸收其发现（当前仅限事故调查）；另有 webhook、Asset REST API、EventBridge 事件。

**边界：** 外部 Agent 强烈建议只读；每次 MCP/A2A access token 认证记录 CloudTrail `AuthenticateAccessToken` 事件（2026-07-21 起）；MCP/A2A 是连接协议不是自动授权。

来源：[MCP servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-mcp-servers.html)、[Remote A2A agents](https://docs.aws.amazon.com/devopsagent/latest/userguide/configuring-integrations-and-knowledge-connecting-remote-a2a-agents.html)、[Remote servers](https://docs.aws.amazon.com/devopsagent/latest/userguide/accessing-devops-agent-connect-to-devops-agent-remote-servers.html)

#### N1 Sandbox 沙箱 —— Preview（2026-07-23）

每次调查隔离在 Lambda MicroVM；Agent 可写/运行代码；沙箱内 AWS API 调用被代理为只读；出站按显式 allowlist；可预装 pip/npm 包、从沙箱文件系统读技能。可用区域 us-east-1/us-west-2/ap-northeast-1/eu-west-1。

来源：[What's new §2026-07-23](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)

#### N2/N3 Amazon Q Developer 代码审查

- **N2 IDE 代码审查（GA）：** 由 Amazon Q Detector Library（源自 CodeGuru）驱动六类检测——SAST、secrets、IaC、代码质量、**代码部署风险（评估发布/部署风险）**、SCA。来源：[Amazon Q code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html)
- **N3 GitHub 自动 PR 审查（Preview）：** PR 创建/重开时自动审查，整体摘要 + 线程化 findings + 建议修复（可生成 commit）；仅私有仓库，Writing/Maintain/Admin 角色。来源：[GitHub code reviews](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/github-code-reviews.html)

#### N4 AWS Transform continuous modernization —— GA（2026-08-03）

连接 GitHub/GitLab/Bitbucket/本地仓库，按需或定时扫描，产出优先级化技术债 findings，并按指示**自主生成验证过的修复 PR**；Web app + `atx ct` CLI + Kiro Power。属"变更/恢复"相邻能力（自助修复），非门禁。

来源：[Transform GA 博客](https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/)

### AWS 平台底座：Amazon Bedrock AgentCore（补充）

AgentCore 是 AWS 的**通用 Agent 生产控制面（平台层）**，不是垂直业务 Agent，也不属于 CI/CD 编排能力本身。但在本专题中必须补充，因为 AWS 官方确认 **AWS DevOps Agent built on Amazon Bedrock AgentCore**（2026-03-31 博客原文："DevOps Agent is built on Amazon Bedrock AgentCore with dedicated infrastructure for memory, policies, evaluations, and observability"）。因此 AgentCore 是上方所有 AWS DevOps Agent 能力的运行与治理底座。

| 能力域 | 代表能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|---|
| 编排与运行 | Harness、Runtime、CLI/CDK、version/endpoint、导出为代码、Step Functions 集成 | 平台 GA 2025-10-13；Harness GA 2026-06-17 | 托管 agent loop（Strands 框架）+ 框架无关 serverless Runtime；每会话隔离 microVM + 持久文件系统；不可变版本 + 命名端点 | [What's New GA](https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available/)、[Harness GA](https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available/)、[Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) |
| 工具与行动 | Gateway、Identity、Policy、Browser、Code Interpreter、Web Search | Policy GA 2026-03-03；Web Search GA 2026-06-17（仅 us-east-1） | Gateway 把 API/Lambda/服务转 MCP 工具；Cedar Policy 在 Gateway 外确定性拦截；托管浏览器与沙箱代码执行 | [Policy GA](https://aws.amazon.com/about-aws/whats-new/2026/03/policy-amazon-bedrock-agentcore-generally-available/)、[Gateway](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html)、[Web Search](https://aws.amazon.com/blogs/aws/announcing-web-search-on-amazon-bedrock-agentcore-ground-your-ai-agents-in-current-accurate-web-knowledge/) |
| 状态与上下文 | Memory、session/persistent filesystem | GA | 短期会话 + 长期策略记忆；harness 默认托管（SEMANTIC+SUMMARIZATION、按 actorId 隔离） | [Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html) |
| 观测与质量 | Observability、Evaluations、Optimization、Insights | Evaluations GA 2026-03-31；Optimization 主体 GA 2026-06-17；Insights Preview | 在线/按需/批处理评估（13 内置 evaluator + 自定义）；2026-07-23 起 traces+prompts+logs 统一单 log group 并按 Agent 粒度 IAM/CMK | [Evaluations GA](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/)、[Observability 统一](https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-bedrock-agentcore-unified-observability-single-log-group/)、[Optimization](https://aws.amazon.com/blogs/machine-learning/new-in-amazon-bedrock-agentcore-build-agents-with-broader-knowledge-and-continuous-learning/) |
| 资产与经济 | Agent Registry、Payments | Registry Preview（2026-04-09）；Payments Preview（2026-05-07） | 组织内容器目录（agent/tool/skill/MCP server）+ 审批流；x402 微支付 + 会话预算上限 | [Registry](https://aws.amazon.com/about-aws/whats-new/2026/04/aws-agent-registry-in-agentcore-preview/)、[Payments](https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-bedrock-agentcore-payments-preview/) |

**关键边界：**
1. **AgentCore 支撑上方能力，但它不是上方能力的 CI/CD 编排功能。** 传统 CodePipeline/CodeBuild 仍由确定性规则执行；AgentCore 治理的是"Agent 本身"的运行、工具授权与质量。
2. **`DevOps Agent built on AgentCore` 已证实**，来源为 [AWS DevOps Blog 2026-03-31](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)，且限定专用基础设施为 memory/policies/evaluations/observability 四类；**不**外推 Runtime 部署拓扑或模型选择。
3. **`AWS Transform built on AgentCore` 未证实（unverified）**。Transform 明确由 Amazon Bedrock 驱动，不得绘制 `Transform → AgentCore` 为产品内部依赖。
4. **区域可用性逐能力不同**：Runtime/Gateway/Identity/Observability 20 区；Harness/Memory 15 区；Payments 4 区；Registry 5 区；Web Search 仅 us-east-1。不能因平台 GA 推断所有组件全域可用。
5. **AgentCore 对比方在 Microsoft 侧是"三层收敛"而非单点对等物**：运行/构建控制面为 Microsoft Foundry Agent Service（GA，公告日期未核验）+ Foundry Control Plane（多数特性 Preview）；组织治理控制面为 Microsoft Agent 365（GA 2026-05-01，官方定位 "The Control Plane for Agents"）；身份控制面为 Entra Agent ID（GA 2026-04）。六域均可映射，唯 Payments 无直接对等、发布不支持流量灰度。详见 [[00_sources/research-microsoft-agent-platform-control-plane-2026-08-07|Microsoft Agent 生产控制面研究报告 (2026-08-07)]] 与下方"平台层交叉对比"章节。

完整机制与双闭环分析见 [[50_deepdives/amazon-bedrock-agentcore/README|Amazon Bedrock AgentCore 专题]]；最新能力核验见 [[00_sources/research-amazon-bedrock-agentcore-capabilities-2026-08-07|AgentCore 能力核验 (2026-08-07)]]。

### 平台层交叉对比：AgentCore 双闭环 vs Microsoft 三层收敛

本节回答"Agent 平台层（治理 Agent 的基础设施）谁在对标谁"，与业务层（Agent 做 CI/CD）的能力清单区分开。

**对比结论：** AgentCore 是**单产品双闭环**（行动闭环 + 质量闭环在一个控制面内），Microsoft 侧是**三层收敛的分散—分层结构**，没有单一产品同时承担全部职责。

| 维度 | AWS AgentCore | Microsoft（Foundry / Agent 365 / Entra） | 差异点 |
|---|---|---|---|
| 行动闭环 | Harness/Runtime + Gateway/Identity/Policy 单控制面 | Agent Service 运行隔离 + Entra Agent Identity + MCP 工具授权（`require_approval`）+ AI Gateway（APIM）策略 | AWS 单点；Microsoft 跨越运行、身份、网关三个产品 |
| 质量闭环 | Observability → Evaluations → Optimization → immutable version-endpoint | Agent Service：trace（OTel）→ evaluation（含 GitHub Actions 作 CI 质量门）→ Agent Optimizer（Preview）→ immutable agent version | 机制同构；Microsoft 桥的落点是 GitHub Actions |
| 组织治理 | Agent Registry（Preview，组织内容器目录） | Agent 365（M365 admin center，Observe/Govern/Secure）+ Foundry Control Plane（订阅级） | Agent 365 Registry Sync（Preview）原生支持 Amazon Bedrock / Google Cloud 跨云治理 |
| 身份 | AgentCore Identity（Gateway 侧授权） | Entra Agent ID（GA 2026-04，agent identity + blueprint，OBO + client credentials） | Microsoft 身份是独立租户级产品，独立于运行层 |
| 发布 | version-endpoint 稳定端点 + 流量路由 | Agent Application 稳定 endpoint + Deployment 子资源；**100% 单版本路由，不支持灰度** | 流量灰度是定位到的能力差异 |
| 变现 | Payments（Preview，x402 微支付） | 无直接对等；资源计费 + 按用户许可（Agent 365 $15/user/月 或 M365 E7） | Payments 为 AWS 独有方向 |
| 平台级 | 单产品 GA 2025-10-13 | Entra Agent ID GA 2026-04 → Agent 365 GA 2026-05-01 → Agent Service 文档无 preview 横幅（公告日期未核验） | Microsoft 平台级控制面产品化晚约 6-7 个月 |

**关键推断（分析，非事实）：** 两家都证明"先有可治理、可观测、可版本化的 Agent 平台，才谈得上把 Agent 放进发布/门禁/恢复流程"。业务层 Agent 的写权限、版本、质量信号最终收敛到平台层控制面 —— 智能化 CI/CD 的成熟度取决于平台层而非业务层功能数量。该推断置信度 medium-high，依据为双方平台层状态（发现七）与业务层能力清单（发现一至六）的分层比对。

---

## 三、Microsoft 智能化 CI/CD 能力清单

### 总表

| ID | 能力 | CI/CD 阶段 | 状态/日期 | 一手来源 |
|---|---|---|---|---|
| M1 | Copilot coding agent（异步开发 Agent） | 编码、PR 前 | GA 2025-09-25 | [GA changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available) |
| M2 | agentic Copilot code review | PR 审查 | GA 2026-03-05；skills/MCP 2026-07-29 GA | [Agentic architecture changelog](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture)、[skills/MCP GA](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available) |
| M3 | Dependabot 告警 → AI agent 修复 | 依赖/安全告警 → 修复 PR | GA 2026-04-07 | [Dependabot → AI agents changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation) |
| M4 | GitHub Code Quality | PR 合并前质量门禁 | GA 2026-07-20 | [Code Quality GA changelog](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available) |
| M5 | Copilot code review for Azure Repos | PR 审查（Azure DevOps） | limited public preview（Sprint 275）；technical preview 2026-06-02 | [Technical preview changelog](https://github.blog/changelog/2026-06-02-github-copilot-code-review-for-azure-repos-is-now-in-technical-preview)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update) |
| M6 | Copilot Autofix for code scanning（Azure DevOps） | 扫描告警 → 修复 PR | limited public preview（Sprint 275） | [Autofix docs](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning-autofix?view=azure-devops)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update) |
| M7 | Azure SRE Agent（运维 Agent） | 发布后验证、事故调查与恢复准备 | 生命周期 unverified（overview ms.date 2026-07-30） | [Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview)、[run modes](https://learn.microsoft.com/en-us/azure/sre-agent/run-modes) |
| M8 | GitHub Copilot for Azure / Azure MCP / Azure Skills | 编码、部署前 IaC 生成与验证 | 多个入口 GA；文档未逐项标记 | [Get started](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/get-started)、[Azure MCP Server overview](https://learn.microsoft.com/en-us/azure/developer/azure-mcp-server/overview) |
| M9 | GitHub Agentic Workflows（自然语言 → Actions） | 持续集成定义与执行 | Public Preview 2026-06-11 | [Public preview changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview) |
| M10 | Agentic autofix for code scanning（GitHub） | 扫描告警 → 修复 PR（修复+重跑验证） | Public Preview 2026-07-10 | [Agentic autofix changelog](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview) |
| M11 | Fix with Copilot for failing Actions | CI 失败 → 修复 | 2026-05-18 起；Pro/Pro+/Max 2026-06-04 | [Fix with Copilot changelog](https://github.blog/changelog/2026-06-04-fix-with-copilot-for-failing-actions-now-in-pro-pro-and-max) |
| M12 | Code-to-cloud risk visibility（Defender for Cloud） | 代码 → 运行风险关联 | GA 2026-05-05 | [Code-to-cloud GA changelog](https://github.blog/changelog/2026-05-05-code-to-cloud-risk-visibility-with-microsoft-defender-for-cloud-is-now-generally-available) |
| M13 | Remote Azure DevOps MCP Server | 跨工具接入层 | Public Preview（Sprint 271）；GA 目标 2026 Q3 未落地 | [Remote MCP docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server)、[roadmap](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline) |

### 逐项能力介绍（Microsoft）

#### M1 Copilot coding agent —— GA 的异步自治开发 Agent

用户在 GitHub 上创建任务，Agent 在 GitHub Actions 自带开发环境中异步运行，可编辑代码、运行测试并交付 **Draft PR**；用户在 PR 评论中可要求修改，Agent 迭代。所有付费 Copilot 计划可用；企业组织需管理员启用。

**边界：** 交付物是待合并的草案，合并仍由人决定。Azure Boards 支持从 Work Item 发起并回链进度（文档更新 2026-06-17）。

来源：[GA changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available)、[Azure Boards integration](https://learn.microsoft.com/en-us/azure/devops/boards/github/work-item-integration-github-copilot?view=azure-devops)

#### M2 agentic Copilot code review —— Agent 架构 + 内联审查

审查从静态规则升级为可调用外部工具（Team 自定义 Agent skills、MCP server）的 Agent；主动取代码、跑分析、生成行级评论与可一键应用建议；可配置自动评审。

**边界：** 2026-04-27 起消耗 Actions minutes；自托管 runner 需额外设置；评论是建议，不是 Required Review，不阻断合并。

来源：[Agentic architecture changelog](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture)、[skills/MCP GA](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available)、[About code review](https://docs.github.com/en/copilot/concepts/agents/code-review)

#### M3 Dependabot 告警 → AI agent 修复 —— GA

Dependabot 告警可指派给 coding agent，Agent 分析 advisory 与依赖实际用法，跨仓库生成修复 Draft PR，并尝试解决升级引发的测试失败。

**边界：** 需 GitHub 组织 Code Security + 含 coding agent 的 Copilot 计划；仅 GitHub.com。生成修复可能不完整，必须评审 PR、运行测试并确认后才可合并。

来源：[Dependabot → AI agents changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation)

#### M4 GitHub Code Quality —— 合并前的 AI 质量门禁（GA）

组合：**CodeQL 确定性检测 + AI 辅助检测 + Copilot Autofix + 测试覆盖率（Cobertura XML）+ Ruleset 强制执行**。GA 后新增：组织级 enablement 与仪表盘、覆盖率阈值门禁、evaluate mode 渐进启用、管理 API。

**边界：** 仅 Enterprise Cloud + Team；定价 $10/active committer/月 + AI/计算用量；GHES 暂不支持。门禁权威来自 Ruleset 的确定性阈值，不来自模型结论。GitHub 内部"67.3% findings 在合并前被解决"是厂商自述。

来源：[Code Quality GA changelog](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available)、[About Code Quality](https://docs.github.com/en/code-security/concepts/about-code-quality)

#### M5 Copilot code review for Azure Repos —— limited public preview

把 agentic code review 带到 Azure Repos；按项目/仓库策略自动触发；支持 custom instructions 与 skills；在 Azure Pipelines agent pool 上运行。无需 GitHub Copilot 订阅，按 GitHub AI credits 计费。Sprint 276 增加项目级成本报告。

**边界：** 三级 enablement + 个人 opt-in；Copilot 只留下 Comment review，**不**批准、不请求更改、不满足 required-reviewer policy、不阻断合并；路线图称 2026 Q3 转 public preview（截至访问日未落地）。

来源：[Technical preview changelog](https://github.blog/changelog/2026-06-02-github-copilot-code-review-for-azure-repos-is-now-in-technical-preview)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update)、[Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update)

#### M6 Copilot Autofix for code scanning（Azure DevOps）—— limited public preview

对 GitHub Advanced Security for Azure DevOps 的 CodeQL 告警生成精准修复建议并创建带修复的 PR。Sprint 275 以 limited public preview 开放（需注册）；Sprint 276 增加 org/project/repo 三级启用、失败状态与重试。

**边界：** 开 PR 不等于合并；GA 的 Advanced Security status checks 可用 `NewHighAndCritical` / `AllHighAndCritical` Branch Policy 阻断 PR 完成，说明 Gate 仍是确定性 Branch Policy。

来源：[Autofix docs](https://learn.microsoft.com/en-us/azure/devops/repos/security/github-advanced-security-code-scanning-autofix?view=azure-devops)、[Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update)、[Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update)

#### M7 Azure SRE Agent —— 运维阶段 Agent（生命周期 unverified）

连接可观测性（Azure Monitor、App Insights、Log Analytics、Grafana）、事件平台（Azure Monitor Alerts、PagerDuty、ServiceNow）与代码仓库（GitHub、Azure DevOps）三端，进行事件分诊、跨信号根因关联与缓解建议。

- **Run modes：** Review（默认）与 Autonomous，按 response plan 与 scheduled task 分别设置；SRE Agent Administrators 可审批。overview 的"## No change deploys without human sign-off"与 Autonomous mode 并存。
- **扩展原语五种：** skills、subagents（内建 6 个：Explore、Plan、CodeReview、Bash、Verification、GeneralPurpose）、Python tools、MCP servers（40+ 托管 connector）、agent hooks。
- **计费：** AAU（always-on 4 AAU/agent-hour + active flow）。
- **区域：** Sweden Central、East US 2、Australia East；入口 `sre.azure.com`。

**边界：** 连接器只暴露获授权的数据/动作；Review mode 下 Azure 写操作必须由 Administrator 批准；Autonomous mode 可立即执行，适用非生产或受信任的重复任务；资源权限来自 managed identity 的 Azure RBAC。

**证据缺口：** overview、pricing、create-agent、run-modes 均未显式标注 Preview 或 GA，故生命周期状态记 `unverified`。

来源：[Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview)、[run modes](https://learn.microsoft.com/en-us/azure/sre-agent/run-modes)、[pricing-billing](https://learn.microsoft.com/en-us/azure/sre-agent/pricing-billing)、[create-agent](https://learn.microsoft.com/en-us/azure/sre-agent/create-agent)

#### M8 GitHub Copilot for Azure / Azure MCP / Azure Skills

VS Code/VS 2022/VS 2026 内以 Agent 模式查询 Azure 资源、生成 IaC（Bicep/ARM、az CLI）、排查应用。底层工具经 Azure MCP Server（GitHub Copilot agent mode、OpenAI Agents SDK、Semantic Kernel 均可作为 MCP client）。`azure-prepare`、`azure-validate`、`azure-deploy`、`azure-diagnostics`、`azure-cost` 等 26+ skills 属 **Azure Skills Plugin**（github.com/microsoft/azure-skills）。

**边界：** 实际工具可见性由 Azure subscription permissions 决定；文档要求审查 AI 输出及成本/安全影响。文档未逐项标注入口 GA/Preview。

来源：[Get started](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/get-started)、[Azure MCP Server overview](https://learn.microsoft.com/en-us/azure/developer/azure-mcp-server/overview)

#### M9 GitHub Agentic Workflows —— 自然语言定义 CI（Public Preview）

以 Markdown 自然语言描述工作流，系统解析为 Actions YAML。默认只读；执行在 sandbox 容器内，受 Agent Workflow Firewall、integrity filter 与威胁检测约束。2026-06-11 起无需 PAT。

**边界：** 改变"CI 定义"形态，不改变执行权限模型；Safe Output 仍需原 CI / Policy 复验。

来源：[Public preview changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview)、[gh-aw docs](https://github.github.io/gh-aw/)

#### M10 Agentic autofix for code scanning（GitHub）—— Public Preview

对 CodeQL 与第三方 code scanning 告警，Agent 跨代码库探索相关文件、生成修复并**重跑原分析验证**，确认修复关闭告警后开 Draft PR；通常 2–4 分钟。取代经典 autofix。

**边界：** 消耗 AI credits + Actions minutes；需 Code Security/Advanced Security + 含 cloud agent 的 Copilot；验证是重跑 CodeQL，不证明第三方扫描告警或自定义查询已修复；修复是候选 PR，需人工评审。

来源：[Agentic autofix changelog](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview)

#### M11 Fix with Copilot for failing Actions

对失败的 Actions job，Copilot 分析并将修复建议为 PR。2026-06-04 扩展至 Pro/Pro+/Max；2026-07-23 支持 GitHub Mobile。

来源：[Fix with Copilot changelog](https://github.blog/changelog/2026-06-04-fix-with-copilot-for-failing-actions-now-in-pro-pro-and-max)

#### M12 Code-to-cloud risk visibility（Defender for Cloud）—— GA

把 GitHub 代码告警与 Defender for Cloud 运行时上下文关联，支持运行时感知过滤与 campaign 定向。属码→云跨界风险视图，非 CI/CD 门禁本身。

来源：[Code-to-cloud GA changelog](https://github.blog/changelog/2026-05-05-code-to-cloud-risk-visibility-with-microsoft-defender-for-cloud-is-now-generally-available)

#### M13 Remote Azure DevOps MCP Server —— Public Preview（GA 未落地）

以 remote MCP server 暴露 Azure DevOps toolsets（repos/work items/pipelines/wiki/testplan/advsec/elm），streamable HTTP 传输 + Entra ID 认证，支持 VS、VS Code、Foundry、Copilot Studio、GitHub Copilot。Sprint 276（2026-07-09）增加 Enterprise Live Migration tools（ELM 本身 private preview）。

**边界：** 路线图仍标 2026 Q3 GA 未落地；DevOps 索引页仍写 "(preview)"；不能写成 GA。

来源：[Remote MCP docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server)、[Sprint 271](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-271-update)、[roadmap](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline)

---

## 四、双厂商能力对比

### 按交付阶段的分布对照

| CI/CD 阶段 | AWS | Microsoft |
|---|---|---|
| 编码 / PR 前 | A8（Kiro/Claude/Transform 内联审查） | M1 coding agent、M8 Copilot for Azure |
| PR 评审 | A2 Release readiness；N2/N3 Amazon Q 审查 | M2 Copilot code review、M5 Azure Repos review |
| 合并前门禁 | A2 的 `BLOCK` 映射 required status check | M4 Code Quality 的 Ruleset 阈值 |
| 发布测试 | A3 Release testing（真实写请求） | M11 Fix with Copilot（CI 失败修复） |
| 依赖/安全修复 | N4 Transform 技术债修复 PR | M3 Dependabot→AI agent、M10 Agentic autofix、M6 Azure DevOps Autofix |
| 发布后调查与恢复 | A4/A5/A6 调查、SRE 任务、预防 | M7 Azure SRE Agent、M12 Defender code-to-cloud |
| 接入/定义层 | A9 MCP/A2A、N6 webhook/API | M9 Agentic Workflows、M13 Remote MCP、M8 Azure MCP/Skills |

### 关键差异

| 维度 | AWS | Microsoft |
|---|---|---|
| 核心叙事 | 收敛"交付—运行上下文"，发布前后共用同一份环境证据 | 仓库内"候选修复—质量/安全门禁"闭环，再延伸到运行 |
| 发布前验证 | 托管验证环境 build/run/test + 对部署目标探索式测试 | coding agent 跑测试；Code Quality 覆盖率门禁；Autofix 重跑扫描 |
| 运行阶段 | DevOps Agent 事件调查（GA）+ 自定义 SRE Agent + Sandbox | Azure SRE Agent（Review/Autonomous，状态 unverified） |
| 生命周期成熟度 | Production operations GA；Release Management Preview | GitHub 主链多已 GA；Azure 侧多 limited preview |
| 门禁持有者 | GitHub required check / GitLab approval rule（外部映射） | GitHub Ruleset（确定性阈值） |
| 扩展面 | MCP/A2A、webhook、EventBridge、Asset API | MCP servers、Azure DevOps MCP、Skills、hooks |

### 受限综合判断（分析推断，非厂商原话）

> AWS 更像把智能化收敛到"发布—运行上下文"，Microsoft 更像把智能化嵌入"仓库—工作流—工具接口"；两条路线都在扩大 Agent 的判断范围，但没有取消确定性 CI/CD Gate。

该判断不支持"哪家公司整体领先"、成熟度排名、跨企业效果或端到端自治结论。

---

## 五、生命周期与证据边界汇总

| 对象 | 状态（as of 2026-08-06） | 来源 |
|---|---|---|
| AWS Production operations | GA（2026-03-31） | [GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/) |
| AWS Release Management | Preview，仅 us-east-1（文档记录 2026-06-11） | [Release management 索引](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html) |
| AWS Sandbox | Preview（2026-07-23） | [What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) |
| Amazon Q IDE 审查 | GA；GitHub 版 Preview | [Amazon Q docs](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/code-reviews.html) |
| AWS Transform | GA（2026-08-03） | [GA 博客](https://aws.amazon.com/blogs/devops/analyze-and-remediate-technical-debt-autonomously-with-aws-transform-continuous-modernization/) |
| GitHub Copilot coding agent | GA（2025-09-25） | [GA changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available) |
| agentic code review | GA（2026-03-05） | [Changelog](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture) |
| GitHub Code Quality | GA（2026-07-20） | [GA changelog](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available) |
| Dependabot → AI agent | GA（2026-04-07） | [Changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation) |
| Agentic autofix（GitHub） | Public Preview（2026-07-10） | [Changelog](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview) |
| Agentic Workflows | Public Preview（2026-06-11） | [Changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview) |
| Azure Repos Copilot review / Autofix | limited public preview | [Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update) |
| Azure SRE Agent | 生命周期 unverified | [Overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview) |
| Remote Azure DevOps MCP | Public Preview；GA 目标未落地 | [Remote MCP docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server) |

## 六、证据缺口

1. **效果数据无独立复核。** MTTR 改善（WGU、Zenchef 等）、GitHub 内部 67.3% 解决率、Reviews 8–10 分钟均无第三方独立验证。
2. **Azure SRE Agent 生命周期缺失。** 官方文档未显式标注 Preview/GA，标记 `unverified`。
3. **Release Management 公告 URL 未定位。** 以用户指南为准，What's New 公告 URL 未核验。
4. **Azure DevOps 侧 GA 时间未落地。** M5/M6/M13 的 2026 Q3 public preview/GA 目标截至访问日未在 release notes 中落地。
5. **日期小冲突已记录。** AWS Production GA（文档 03-30 vs What's New 03-31）；Release Management 文档历史 06-11 vs 早前底稿 06-17。
6. **Azure Skills / MCP 入口未逐项标注生命周期。** 只报告文档日期，不推断生命周期。

## 七、方法说明

- 本报告基于 2026-08-06（部分 2026-08-05）对 AWS、GitHub、Microsoft Learn 官方来源的实际访问；状态/日期/版本号均来自访问页面。AgentCore 补充部分与平台层交叉对比基于 2026-08-07 核验。
- 研究输入见 [[00_sources/research-aws-intelligent-cicd-capabilities-2026-08-06|AWS Source Brief]]、[[00_sources/research-microsoft-intelligent-cicd-capabilities-2026-08-06|Microsoft Source Brief]]、[[00_sources/research-amazon-bedrock-agentcore-capabilities-2026-08-07|AgentCore 能力核验]] 与 [[00_sources/research-microsoft-agent-platform-control-plane-2026-08-07|Microsoft Agent 生产控制面研究报告]]。
- **能力全景图：** 截至 2026-08-06，AWS 与 Microsoft 官方均未发布单张"智能化 CI/CD 能力全景图"（详见 [[00_sources/research-aws-official-intelligent-cicd-capability-map-2026-08-06|AWS 全景图核研]] 与 [[00_sources/research-microsoft-official-capability-map-panorama-2026-08-06|Microsoft 全景图核验]]）。本报告中的阶段分布对照表为主 Agent 自绘整理，非厂商原图。
- **AgentCore 定位：** 作为 AWS 平台底座补充（非 CI/CD 编排能力本身）；`DevOps Agent built on AgentCore` 已证实，`Transform built on AgentCore` 未证实（unverified）。
- 排除项：CodeGuru Reviewer（2025-11-07 停新关联）、CodeCatalyst（2025-11-07 停新客户）、传统 CodePipeline/CodeBuild/Azure Pipelines/GitHub Actions/CodeQL/Dependabot 版本更新的确定性能力本身。
- 所有"收益/效率"表述为厂商自述，未外推为行业平均值。
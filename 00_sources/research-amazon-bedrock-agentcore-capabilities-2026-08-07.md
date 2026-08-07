---
title: Amazon Bedrock AgentCore 最新能力清单与产品状态核验（2026-08-07）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - platform/aws
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
date: 2026-08-07
confidence: high
source_policy: 仅使用 AWS 官方一手来源（AgentCore Developer Guide、AWS What's New、AWS 官方博客）；所有链接于 2026-08-07 访问。
---

# Amazon Bedrock AgentCore 最新能力清单与产品状态核验报告

> 研究任务：核验 Amazon Bedrock AgentCore 最新能力清单与产品状态；基线为 2026-08-03 已有深度洞察（能力分域：编排与运行 / 工具与行动 / 状态与上下文 / 观测与质量 / 资产与经济 / 平台级）。

## 1. 检索范围与访问时间

- 访问时间：**2026-08-07**（完整会话，单日完成）
- 来源范围：仅 AWS 官方一手来源
  - AgentCore 官方文档（`docs.aws.amazon.com/bedrock-agentcore/latest/devguide/`，20+ 子页面，含 .md 原文）
  - AWS What's New 公告（GA/Preview 日期逐条核验 `Posted on` 字段）
  - AWS 官方博客（AWS News Blog / AWS ML Blog / AWS DevOps Blog；含 AgentCore 分类 RSS 全量 16 条）
  - AgentCore 定价页、Bedrock Agents Classic 维护模式页、AWS DevOps Agent 产品页/文档/博客、AWS Transform 产品页
- 检索方法备注：AWS What's New 目录 API 的 keyword 检索返回 0 结果（疑似接口行为问题），故所有日期均通过「博客内嵌 What's New 链接 + 直接抓取公告页」双通道逐条核验；AgentCore 分类博客使用官方 RSS feed 全量枚举。

## 2. 能力清单总表（能力 / 状态·日期 / 机制 / 来源链接）

### 2.0 平台级

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| AgentCore 平台 | **GA（2025-10-13）**；2025-07-16 起 Preview | 通用 Agent 生产控制面：构建、部署、运维 Agent 的平台；GA 时补齐 VPC、AWS PrivateLink、CloudFormation、资源标签 | [What's New GA](https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available/)；[Launch blog（Preview）](https://aws.amazon.com/blogs/aws/introducing-amazon-bedrock-agentcore-securely-deploy-and-operate-ai-agents-at-any-scale/) |
| Bedrock Agents Classic 维护模式 | **2026-07-30 起对新客户关闭**（更名为 Agents Classic） | 仅 `CreateAgent`/`InvokeInlineAgent` 面向无历史使用账户受限（过去 12 个月有使用则白名单）；模型目录冻结；无 EOL 期限；推荐迁移至 AgentCore | [Agents Classic 维护模式文档](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-classic-maintenance-mode.html) |

### 2.1 编排与运行

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| Harness | **GA（2026-06-17）**；2026-04 起 Preview | 配置声明的托管 agent loop（`CreateHarness`/`InvokeHarness` 两 API）；每会话隔离 microVM + 文件系统 + shell；基于 Strands Agents 框架；无单独 harness 费用，按底层资源计费 | [What's New Harness GA](https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available/)；[Harness 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)；[Harness GA 博客](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-harness-is-now-generally-available-go-from-idea-to-production-grade-agent-in-minutes/) |
| Runtime | GA（自 2025-10-13 平台 GA 起） | 框架/模型无关的 serverless 托管；每会话隔离 microVM、最长 8 小时执行、持久文件系统、100MB payload、双向流式、MCP/A2A/AG-UI 协议 | [Runtime 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) |
| CLI / CDK / SDK / MCP server | GA | `@aws/agentcore` npm CLI（Node 20+）；CLI 底层使用 `@aws/agentcore-cdk` CDK 构造；另有 Python SDK、AWS SDK、AgentCore MCP server（兼容 Kiro/Cursor/Claude Code/Amazon Q CLI） | [开发接口文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/develop-agents.html)；[CLI 快速开始](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agentcore-get-started-cli.html) |
| version / endpoint | GA | Harness 与 Runtime 均自动版本化（创建即 V1，不可变版本）+ 命名端点（`DEFAULT` 自动指向最新版，可手动 pin 环境） | [Harness 版本化](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-versioning.html) |
| 导出为代码 | GA | `agentcore export harness` 导出为 Strands Python 代码；Claude Agents SDK "coming soon"；可部署到 AgentCore Runtime / Lambda / ECS-Fargate / K8s / 自托管 | [Harness export 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-export.html) |
| Step Functions 集成 | GA | `InvokeHarness` 为 AWS Step Functions 一等状态（Workflow Studio 可拖拽） | [Harness GA 博客](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-harness-is-now-generally-available-go-from-idea-to-production-grade-agent-in-minutes/) |

### 2.2 工具与行动

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| Gateway | GA | 全托管 AI 网关：API/Lambda/既有服务 → MCP 工具；支持 A2A、模型路由、OpenAPI/Smithy/Lambda 输入；Salesforce/Slack/Jira/Asana/Zendesk 1-click 集成；入站+出站认证 | [Gateway 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html) |
| Identity | GA | Agent 工作负载身份与凭证管理；OAuth/API key 凭证提供者、Token Vault；与 Cognito/Okta/Microsoft Entra ID/Auth0 兼容 | [Identity 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html) |
| Policy | **GA（2026-03-03）**；2025-12-02 起 Preview | Cedar 语言 + 自然语言写策略；policy engine 附着 Gateway，边界外确定性拦截；GA 后含 Bedrock Guardrails 集成（2026-06-17 起 GA） | [What's New Policy GA](https://aws.amazon.com/about-aws/whats-new/2026/03/policy-amazon-bedrock-agentcore-generally-available/)；[Policy 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy.html)；[Evaluations+Policy 博客](https://aws.amazon.com/blogs/aws/amazon-bedrock-agentcore-adds-quality-evaluations-and-policy-controls-for-deploying-trusted-ai-agents/) |
| Browser | GA | 托管隔离浏览器；会话默认 15 分钟/最长 8 小时；Live View、会话录制（自定义 browser）、CloudTrail、CloudWatch metrics | [Browser 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/browser-tool.html) |
| Code Interpreter | GA | 沙箱代码执行（Python/JS/TS）；默认 15 分钟/最长 8 小时；内联上传 100MB / S3 上传 5GB；CloudTrail | [Code Interpreter 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/code-interpreter-tool.html) |
| Web Search | **GA（2026-06-17）**，仅 us-east-1 | AgentCore 自建索引（数十亿文档）+ Amazon 知识图谱；经 Gateway connector 暴露为 MCP `WebSearch` 工具；查询不出 AWS；定价 $7/1000 查询；首方 `agentcore_web_search` 工具类型 "coming soon" | [Web Search 博客](https://aws.amazon.com/blogs/aws/announcing-web-search-on-amazon-bedrock-agentcore-ground-your-ai-agents-in-current-accurate-web-knowledge/)；[Web Search 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-target-connector-web-search-tool.html) |

### 2.3 状态与上下文

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| Memory | GA | 短期（会话多轮）+ 长期（语义/摘要/用户偏好/episodic 策略）；harness GA 后配置上默认可自动托管（`SEMANTIC`+`SUMMARIZATION`、30 天过期、按 actorId 隔离） | [Memory 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)；[Harness GA 博客](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-harness-is-now-generally-available-go-from-idea-to-production-grade-agent-in-minutes/) |
| session / persistent filesystem | GA | 每会话隔离 microVM + 跨会话持久文件系统；支持 S3 Files / EFS 挂载；会话过期后 memory 与文件仍可持久 | [Harness 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness.html)；[Runtime 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) |

### 2.4 观测与质量

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| Observability | GA；**2026-07-23 起统一可观测** | CloudWatch 驱动的 traces/logs/metrics；OTEL 兼容；2026-07-23 起 agent traces+prompts+logs 统一到单 log group（`/aws/bedrock-agentcore/runtimes/<agent_id>-<endpoint_name>`），支持按 Agent 粒度 IAM 与 CMK 加密 | [Observability 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability.html)；[What's New 统一可观测](https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-bedrock-agentcore-unified-observability-single-log-group/) |
| Evaluations | **GA（2026-03-31）**；2025-12-02 起 Preview | 在线/按需/批处理评估；13 个内置 evaluator + 自定义；基于 Strands/LangGraph traces，LLM-as-a-Judge | [What's New Evaluations GA](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/)；[Evaluations 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations.html) |
| Optimization | Recommendations / configuration bundles / A-B testing **GA（2026-06-17）**；Insights **Preview（2026-06-17 起）** | 基于 traces 生成 prompt/tool 描述推荐；versioned 配置包；Gateway 流量切分 A-B + 统计显著性；Insights 提供失败分类/意图聚类/轨迹摘要 | [Optimization 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/optimization.html)；[Insights 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/insights.html)；[New in AgentCore 博客](https://aws.amazon.com/blogs/machine-learning/new-in-amazon-bedrock-agentcore-build-agents-with-broader-knowledge-and-continuous-learning/) |
| Insights | **Preview（2026-06-17 起）**，公开预览期免费 | 失败模式 3 级分类（category→subcategory→root cause cluster）、用户意图聚类、执行轨迹聚类；批处理或 DAILY/WEEKLY/MONTHLY 定时触发 | [Insights 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/insights.html)；[AgentCore 定价页](https://aws.amazon.com/bedrock/agentcore/pricing/) |

### 2.5 资产与经济

| 能力 | 状态/日期 | 机制 | 来源 |
|---|---|---|---|
| Agent Registry | **Preview（2026-04-09 起）** | 组织内资源目录：agents/tools/skills/MCP servers/自定义资源；审批流 + 语义/关键词混合搜索 + MCP 端点；IAM 或 JWT 鉴权；预览期免费 | [What's New Agent Registry](https://aws.amazon.com/about-aws/whats-new/2026/04/aws-agent-registry-in-agentcore-preview/)；[Registry 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/registry.html) |
| Payments | **Preview（2026-05-07 起）** | 基于 x402 协议的微支付；Coinbase CDP + Stripe(Privy) 钱包；会话级预算上限（maxSpendAmount/expiry）；Coinbase x402 Bazaar MCP server（10,000+ 端点）经 Gateway 暴露 | [What's New Payments](https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-bedrock-agentcore-payments-preview/)；[Payments 文档](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/payments.html) |

### 2.6 区域可用性（截至 2026-08-07 文档值）

来源：[AgentCore 支持区域表](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agentcore-regions.html)

| 能力 | 覆盖区域 |
|---|---|
| Runtime / Gateway / Identity / Built-in Tools / Observability | 20 区（含 AWS GovCloud US-West） |
| Harness / Memory | 15 区（不含 Milan、Spain、Malaysia、Thailand、GovCloud） |
| Evaluations | 19 区（不含 Milan、Spain、Malaysia、Thailand） |
| Policy | 19 区（不含 GovCloud） |
| Optimization | 15 区（不含 GovCloud 及 Milan/Spain/Malaysia/Thailand） |
| Payments (preview) | 4 区：us-east-1、us-west-2、eu-central-1、ap-southeast-2 |
| Agent Registry (preview) | 5 区：us-east-1、us-west-2、eu-west-1、ap-southeast-2、ap-northeast-1 |
| Web Search | 仅 us-east-1 |

## 3. 相对 2026-08-03 底稿的更新

**底稿各能力状态与本次官方核验全部一致，未发现矛盾项：**

- Harness GA 2026-06-17 ✓；Policy GA 2026-03-03 ✓；Evaluations GA 2026-03-31 ✓；Optimization（batch/recommendation/A-B GA、2026-06-17；insights Preview）✓；Agent Registry Preview 2026-04-09 ✓；Payments Preview 2026-05-07 ✓；平台 GA 2025-10-13 ✓；Agents Classic 2026-07-30 关停 ✓。

**2026-08-03 之后的新增公告：未发现。**
- 已核验 2026-08-03 AWS Weekly Roundup（无 AgentCore/新能力公告）及 07-27 / 07-20 周报；7 月底至 8 月初无 AgentCore 新模块、新区域或新集成公告。

**2026-08-03 底稿可能未覆盖、需主 Agent 确认/补入的近期更新项：**

1. **Observability 统一化（2026-07-23）**：traces + prompts + logs 合并到单 log group，新增按 Agent 粒度 IAM 与 CMK 加密。公告日期早于底稿标注日，是否已入底稿需人工确认；若未入，建议补入「观测与质量」域。
2. **Web Search 有明确 GA 日期（2026-06-17）与区域约束（仅 us-east-1）**；当前须经 Gateway connector 使用，首方 `agentcore_web_search` 工具类型 "coming soon"。
3. **Harness 附带子能力（GA 日随 harness）**：托管 memory 默认开启、Skills（Git/S3/AWS-curated catalog）、会话中切换模型供应商、`agentcore add` 资源脚手架、Gateway 层 Guardrails 集成（GA）。
4. **Bedrock Managed Knowledge Base（2026-06-17）**：组织知识层，经 AgentCore Gateway 与 AgentCore 集成；非 AgentCore 自有模块，但属 AgentCore 生态能力。

## 4. AgentCore 与 DevOps Agent / Transform 的官方关系表述

### 4.1 DevOps Agent → AgentCore：已证实（built on）

AWS DevOps Blog 于 **2026-03-31**（DevOps Agent GA 同日）原文表述：

> "DevOps Agent is not a chat interface layered over a large language model. It is **built on Amazon Bedrock AgentCore** with dedicated infrastructure for memory, policies, evaluations, and observability."

- 来源：[Leverage agentic AI for autonomous incident response with AWS DevOps Agent](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)（发布 2026-03-31T15:58:13-07:00，2026-08-07 访问）
- 支撑边界：该表述证明 DevOps Agent 使用 AgentCore 作为平台底座，且明确限定其专用基础设施为 **memory、policies、evaluations、observability 四类**；**不**公开全部内部组件、Runtime 部署拓扑、具体模型或逐调用链。
- 交叉验证：与仓库既有核验一致（`research-agentcore-transform-devops-agent-relationship-2026-08-03.md` 结论 2）。

### 4.2 Transform → AgentCore：unverified

截至本次核验，AWS Transform 产品页、User Guide、FAQ、公告及 AgentCore 文档中**未找到** "Transform is built on AgentCore" 或等价架构表述。不得绘制 `Transform → AgentCore` 为产品内部依赖。Transform 明确由 Amazon Bedrock 驱动（差异点，非 AgentCore）。[Transform 产品页](https://aws.amazon.com/transform/)

### 4.3 定位区分

- AgentCore 是**通用 Agent 生产控制面（平台层）**，不是垂直业务 Agent；DevOps Agent 是**上层 CI/CD 编排/运维 Agent**，其与 AgentCore 的 "built on" 关系已证实（见 4.1）。
- 该关系支持在 Presentation 中绘制 `DevOps Agent → AgentCore`（使用 memory/policies/evaluations/observability）的明确箭头；不得外推到 Runtime 部署拓扑或模型选择。

## 5. 证据缺口与复核记录

**已验证闭合项**
- 全部 GA/Preview 日期均有 What's New 公告页 `Posted on` 字段 + 官方博客双源交叉验证（AgentCore GA、Policy、Evaluations、Harness、Web Search、Payments、Registry、DevOps Agent GA、DevOps Agent built-on AgentCore 表述）。
- 区域表取自 agentcore-regions.md 原文，2026-08-07 访问。

**证据缺口**
1. **Transform 是否构建于 AgentCore**：unverified；仅基于「未检索到」而非「业界不存在」，需后续定向检索 AWS re:Post / 发布会 transcripts 再确认。
2. **AWS What's New 目录 API keyword 检索异常**（返回 0 结果），存在漏筛个别公告的可能；已用 AgentCore 分类 RSS（全量 16 条）+ 周报交叉弥补，但非 RSS 覆盖的历史公告无法 100% 枚举。
3. **AgentCore doc-history 页面为 JS 渲染无法抓取**，未获得官方版本文档时间线；能力状态以「当前文档 + 公告」为准。
4. **Web Search 首方 `agentcore_web_search` 工具类型**：文档标注 "coming soon"，区域/GA 计划未公开。
5. **Payments / Registry 定价**：Registry 预览期免费；Payments 依赖 Coinbase/Privy 第三方定价，未核验第三方费率主源。
6. **Insights 区域路由披露**：ap-south-1 使用 Bedrock APAC 跨区推理 profile、ap-northeast-2 使用 global profile（可能将输入/输出存储于 profile 内其他区域）；已记录，未交叉验证。

**复核要求**：本文件为研究输入；若进入 `50_deepdives/` 或 Presentation，需对 2026-06/07 新增项（Web Search GA、Observability 统一、Managed Knowledge Base、Guardrails 集成）逐主张回链到本文件所列 URL 并再次核对访问时间。
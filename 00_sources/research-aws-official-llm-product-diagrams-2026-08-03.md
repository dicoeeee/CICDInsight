---
title: AWS 官方 LLM 与 Agent 产品关系图核验
date: 2026-08-03
as_of: 2026-08-03
scope: Amazon Bedrock、Amazon Nova、Amazon SageMaker AI、Amazon Bedrock AgentCore、Kiro、Amazon Q、AWS Transform、AWS DevOps Agent 的官方关系图；仅 AWS 官方文档、官方博客和官方产品页。
status: complete
confidence: high
---

# AWS 官方 LLM 与 Agent 产品关系图核验

## 结论摘要

**截至 2026-08-03，未检索到 AWS 发布的单一官方图，同时展示 Amazon Bedrock、Nova、SageMaker AI、AgentCore、Kiro、Amazon Q、AWS Transform 和 AWS DevOps Agent 的产品层级或完整关系。**

这里的“未检索到”是对本次 AWS 官方文档、官方博客、官方产品页的定向检索结果，不等同于证明 AWS 从未在会议演讲或未索引材料中使用过类似图。现有官方图分为三种不同用途：

1. **AWS AI 当前技术栈图**：回答基础设施、数据、模型、Agent 平台和上层 Agentic Solutions 的宏观分层；
2. **客户/参考方案架构图**：回答若干服务如何共同完成一个特定工作流；
3. **单产品接入或工作流图**：回答 DevOps Agent 或 Transform 的局部使用方式。

它们不能被拼接后声称为 AWS 发布的“统一产品架构”。尤其不能把“在一个客户方案中与 AgentCore 组合使用”外推为“该 AWS 产品构建于 AgentCore”。

### 对当前 PPT 的直接判断

**AWS 当前总图可以直接作为“宏观技术栈框架”，但不能单独作为完整的产品全景关系图。**

最适合的做法是将下列官方图作为证据，重新绘制一张明确区分 `产品层级`、`已证实构建关系` 和 `可选集成关系` 的编辑型示意图；图注须标明“基于 AWS 官方产品资料整理，非 AWS 原图”。

## 核验口径

### 纳入条件

- 图或同页明确讨论上述产品中的至少两个；
- 图名、图意或页面正文能精确说明关系；
- 页面属于 AWS 官方文档、AWS 官方博客或 AWS 官方产品页。

### 不把以下内容当作产品关系图

- 同页的标签、产品目录或营销页罗列；
- 只在正文中出现、但图中未呈现的产品；
- 客户自建 Agent 的参考架构；
- 因共同使用 Bedrock、MCP、A2A 或 Kiro Power 而推定的内部产品依赖。

## 最接近的 AWS 官方图

| 类别 / 图名 | 页面与发布日期 | 图中明确覆盖的产品 | 关键缺失 | 可否直接用于当前 PPT | 准确使用方式 |
|---|---|---|---|---|---|
| **宏观分层：AWS AI — From silicon to agentic solutions** | [Amazon Bedrock or Amazon SageMaker AI?](https://docs.aws.amazon.com/decision-guides/latest/bedrock-or-sagemaker/bedrock-or-sagemaker.html)，AWS Decision Guide 页面元数据最后更新 **2025-06-27**；但 2026-08-03 实际返回的图片为 **©2026** 当前版本 | Kiro、Quick、Connect、Marketplace；Bedrock AgentCore；SageMaker、Bedrock；数据基础、AI 芯片和全球基础设施。 | Amazon Nova、AWS Transform、AWS DevOps Agent；也没有展开 AgentCore 与上层产品的逐项构建关系。 | **可作为宏观总框架直接引用；不可单独作为完整产品关系图。** | 用来证明 AWS 当前公开的六层产品栈，以及 AgentCore 位于“Agent development & orchestration”层。图中上下排布是产品组合分层，不能自动解释为每个上层产品都明确构建于 AgentCore。 |
| **Transform–AgentCore 客户方案：Figure 1 – AWS Transform custom Agentic Orchestration Architecture using Strands agents and Amazon Bedrock AgentCore** | [Agentic application modernization at scale with Strands and Amazon Transform custom](https://aws.amazon.com/blogs/devops/use-generative-ai-agents-for-application-modernization-at-scale-with-strands-amazon-transform-custom-and-amazon-bedrock-agentcore/)，AWS DevOps & Developer Productivity Blog，**2026-05-11** | Amazon Bedrock、Amazon Bedrock AgentCore、AWS Transform custom；另有 Strands Agents、AWS Batch 和该示例所需的应用组件。 | Nova、SageMaker AI、Kiro、Amazon Q、AWS DevOps Agent。 | **否**，不能作为产品谱系；**可**作为“Transform custom 可被 AgentCore 承载的客户自建编排方案”机制图。 | 图中的 AgentCore 运行的是该方案的 orchestrator agent，并用 AgentCore Memory 保存状态；AWS Transform custom CLI 是执行工具。它证明可组合架构，不证明 AWS Transform 产品本身 built on AgentCore。 |
| **迁移方案：Figure 1 – Migration solution architecture showing AWS Transform, CMF, AWS MGN, and Amazon Bedrock AgentCore integration** | [Accelerate large-scale server migrations with AI-driven orchestration](https://aws.amazon.com/blogs/migration-and-modernization/accelerate-large-scale-server-migrations-with-ai-driven-orchestration/)，AWS Migration & Modernization Blog，**2026-06-26** | AWS Transform、Amazon Bedrock AgentCore、Kiro CLI；另有 Cloud Migration Factory、AWS Application Migration Service 和迁移工具链。 | Nova、SageMaker AI、Amazon Q、AWS DevOps Agent；也不是 Bedrock / AgentCore 的产品层级图。 | **否**，不宜直接用于 CI/CD 洞察页；仅适合解释“Transform、Kiro、AgentCore 在迁移工作流中的分工”。 | 页面明确：Transform 做发现与迁移波次规划，AgentCore 用于客户构建的迁移任务 Agent，Kiro CLI 是经 MCP 与 CMF 交互的会话入口。不要推导为 Kiro 或 Transform 构建于 AgentCore。 |
| **DevOps Agent 接入图：Accessing DevOps Agent**（页面未给图号） | [Accessing DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-accessing-devops-agent-index.html)，AWS DevOps Agent User Guide；**页面未标注发布日期**，访问日 2026-08-03 | AWS DevOps Agent、IDE/MCP client、A2A/ACP agent 或 orchestrator、监控和工单系统、API；正文明确举 Kiro 为 MCP-compatible IDE 示例。 | Amazon Bedrock、Nova、SageMaker AI、AgentCore、Amazon Q、AWS Transform。 | **否**，它是接入面图而非产品关系图；可用于说明 Kiro 到 DevOps Agent 的调用边界。 | 可证明 Kiro 等 IDE 可经 MCP 调用 DevOps Agent；不能证明 Kiro 是 DevOps Agent 的运行时或其父层。 |
| **DevOps Agent 工作流：Figure 3 – AWS DevOps Agent investigation workflow** | [Leverage Agentic AI for Autonomous Incident Response with AWS DevOps Agent](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)，AWS DevOps & Developer Productivity Blog，**2026-03-31** | AWS DevOps Agent 的事件探测、根因分析和缓解建议流程，以及案例中的监控、代码和运行环境信号。 | AWS Transform、Kiro、Amazon Q、Nova、SageMaker AI；**图中也未画出 AgentCore**。 | **否**，适合作为 DevOps Agent 运行机制图，而非产品关系图。 | 同一篇正文（非该图）明确写明 DevOps Agent “is built on Amazon Bedrock AgentCore with dedicated infrastructure for memory, policies, evaluations, and observability”。因此可在自绘图中画 `AgentCore → DevOps Agent`，但不要把 Figure 3 误称为该关系图。 |

## AgentCore–Transform–DevOps Agent：是否存在官方三者关系图

### 结论：未检索到

本次检索未找到一张 AWS 官方图同时画出 **Amazon Bedrock AgentCore、AWS Transform、AWS DevOps Agent**，并解释三者的层级或运行关系。

已找到的最接近材料分别覆盖其中两者或给出文字关系：

| 已核验关系 | AWS 一手证据 | 图形状态 | 可写入 PPT 的事实边界 |
|---|---|---|---|
| `AgentCore → DevOps Agent` | [AWS DevOps Agent 官方博客](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/) 明确称 DevOps Agent “is built on Amazon Bedrock AgentCore”，并点名 memory、policies、evaluations、observability。 | **文字直接证据；该页的 Figure 3 不是层级图。** | 可以画“DevOps Agent 构建于 AgentCore”，但不能补写 AgentCore Runtime、具体模型或内部服务调用拓扑。 |
| `Bedrock → AWS Transform` | [AWS Transform 跨区域处理说明](https://docs.aws.amazon.com/transform/latest/userguide/cross-region-processing.html) 明确称 AWS Transform “is powered by Amazon Bedrock”。 | 无对应全局关系图。 | 可以画 Bedrock 是 Transform 的已公开模型/推理底座。 |
| `AgentCore ↔ Transform custom` | 上表的 **Figure 1 – AWS Transform custom Agentic Orchestration Architecture**。 | **客户参考架构图。** | 只能画“Transform custom 可在客户架构中被 AgentCore 上的编排 Agent 调用”；不得画“Transform built on AgentCore”。 |
| `Transform custom → DevOps Agent` | [Release readiness code reviews](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) 说明：安装 skill 后，Transform 生成或修改代码时会调用 DevOps Agent 的 release-readiness review，发现回到 Transform 输出。 | 无官方关系图；是文档化的可选流程。 | 可以画一条标注“可选、安装 skill 后”的单向集成箭头。它不是通用上下游依赖，也不是自动部署授权链。 |
| `Kiro → Transform / DevOps Agent` | [AWS Transform developer tools](https://docs.aws.amazon.com/transform/latest/userguide/developer-tools.html) 记录 Transform Kiro Power；DevOps Agent 的 [release readiness 文档](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) 记录 Kiro Power / CLI 接入。 | 分散在工具接入页，非平台层级图。 | Kiro 是开发者入口 / 调用面；不能画成 Transform 或 DevOps Agent 的运行时父层。 |

## 适合当前 PPT 的自绘图边界

### 可安全表达的实线

```text
Amazon Nova ──可通过──> Amazon Bedrock
Amazon SageMaker AI ──训练或定制模型后可导入──> Amazon Bedrock
Amazon Bedrock AgentCore ──构建基础设施──> AWS DevOps Agent
Amazon Bedrock ──模型/推理底座──> AWS Transform
AWS Transform custom ──安装 skill 后，可选调用──> AWS DevOps Agent release-readiness review
Kiro ──Power / MCP 接入面──> AWS Transform、AWS DevOps Agent
```

### 必须标为“可选”或“未证实”的关系

```text
Amazon Bedrock ──可选模型提供者──> AgentCore
AWS Transform ──可与──> AgentCore 组合（客户参考架构）
AWS Transform ──构建于──> AgentCore                         [未证实，禁止画]
Kiro ──运行时托管──> Transform / DevOps Agent               [未证实，禁止画]
Amazon Q ──统一父层──> Kiro / Transform / AgentCore / DevOps Agent [未证实，禁止画]
```

### 面向 CI/CD 页的建议

不要使用“全 AWS AI 栈”作为该页的主图。它会让 Nova、SageMaker AI 和 Q 占据视觉重心，却不能解释用户要的核心机制。

建议在 AWS 一侧绘制为两条并列但不同性质的线：

```text
模型与 Agent 平台线：Nova / Bedrock / AgentCore → DevOps Agent
开发与现代化工作线：Kiro → Transform custom ──(可选 release-review skill)──> DevOps Agent
```

并在图下注释：**“实线为 AWS 已明确公开的产品关系；虚线或括号为可选集成。AWS 未公开 Transform 构建于 AgentCore 的证据。”**

## 来源与访问记录

| ID | 官方来源 | 类型 | 发布 / 更新时间 | 访问日 | 采用理由 |
|---|---|---|---|---|---|
| S1 | [Amazon Bedrock or Amazon SageMaker AI?](https://docs.aws.amazon.com/decision-guides/latest/bedrock-or-sagemaker/bedrock-or-sagemaker.html) | AWS Decision Guide | 最后更新 2025-06-27 | 2026-08-03 | AWS generative AI stack 图；Bedrock、SageMaker AI、Q 与 Nova 的宏观关系。 |
| S2 | [Agentic application modernization at scale with Strands and Amazon Transform custom](https://aws.amazon.com/blogs/devops/use-generative-ai-agents-for-application-modernization-at-scale-with-strands-amazon-transform-custom-and-amazon-bedrock-agentcore/) | AWS 官方博客 | 2026-05-11 | 2026-08-03 | Figure 1：Transform custom 和 AgentCore 的客户组合架构及其边界。 |
| S3 | [Accelerate large-scale server migrations with AI-driven orchestration](https://aws.amazon.com/blogs/migration-and-modernization/accelerate-large-scale-server-migrations-with-ai-driven-orchestration/) | AWS 官方博客 | 2026-06-26 | 2026-08-03 | Figure 1：Transform、AgentCore、Kiro 的迁移方案关系。 |
| S4 | [Accessing DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-accessing-devops-agent-index.html) | AWS User Guide | 页面未标注发布日期 | 2026-08-03 | DevOps Agent 接入图及 Kiro/MCP 边界。 |
| S5 | [Leverage Agentic AI for Autonomous Incident Response with AWS DevOps Agent](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/) | AWS 官方博客 | 2026-03-31 | 2026-08-03 | Figure 3：DevOps Agent 调查流程；正文中 DevOps Agent built on AgentCore 的直接表述。 |
| S6 | [Release readiness code reviews](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | AWS User Guide | 页面未标注发布日期 | 2026-08-03 | Transform custom 到 DevOps Agent release-review 的可选集成；Kiro 接入。 |
| S7 | [Cross-region processing in AWS Transform](https://docs.aws.amazon.com/transform/latest/userguide/cross-region-processing.html) | AWS User Guide | 页面未标注发布日期 | 2026-08-03 | Transform 由 Bedrock 驱动的直接表述；未出现 AgentCore 依赖。 |
| S8 | [AWS Transform developer tools](https://docs.aws.amazon.com/transform/latest/userguide/developer-tools.html) | AWS User Guide | 页面未标注发布日期 | 2026-08-03 | Transform 的 Kiro Power 与 MCP 接入边界。 |

## 结论的置信度与限制

- **“无单一官方全景图”**：高置信度的定向检索结论；仍保留未索引活动材料或后续发布的可能。
- **`DevOps Agent built on AgentCore`**：高置信度，AWS 有直接文字陈述。
- **`Transform built on AgentCore`**：未证实；当前正式资料仅明确 Transform 由 Bedrock 驱动。
- **`Transform custom → DevOps Agent`**：高置信度，但仅限安装 release-readiness skill 后的可选集成。

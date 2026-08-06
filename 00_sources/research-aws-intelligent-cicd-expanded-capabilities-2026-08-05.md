---
title: AWS 智能化 CI/CD 扩展能力候选池
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/aws
status: complete
as_of: 2026-08-05
accessed: 2026-08-05
confidence: high-for-mechanism; medium-for-product-lifecycle-where-AWS-did-not-label-a-feature-separately
---

# AWS 智能化 CI/CD：从“发布前审查”扩展到“上下文—验证—运行反馈—可编程运维”

## 提纲

1. 范围与产品状态边界
2. 可上 PPT 的九项候选能力
3. 相对原 CD1 的保留／拆分／替换建议
4. 明确排除项与证据缺口

## 结论先行

截至 2026-08-05，AWS 最有区分度的不是再增加一个通用编码 Agent，而是让 **AWS DevOps Agent** 持续汇聚资源拓扑、跨仓代码依赖、流水线晋级关系、遥测和部署历史，再将该上下文用于发布审查、变化相关测试和运行事件调查。适合放在 AWS 半页的能力池可以扩至九项；其中应优先呈现“拓扑／依赖／流水线理解”“发布就绪审查”“变更驱动发布测试”“事件调查”“预防建议”“可调度的自定义 SRE Agent”。

生命周期必须拆开写：Production operations 在 **2026-03-31 GA**；Release management（发布就绪审查、自动化验证和 release testing）在 **2026-06-17 Preview**，且仅 `us-east-1`。2026-06 的 custom agents、MCP/A2A、memory 等增量公告没有逐项写 GA／Preview；不能把它们单独断言为 GA，只能写为已发布的 GA 产品面增量，并标明其工具权限仍由客户配置。

## 范围与判断口径

- 只使用 AWS 官方文档、What's New、官方博客与官方 GitHub 链接；全部于 2026-08-05 访问。
- “能力”指可以由用户配置、调用或消费的可见产品功能；不把底层模型、通用 Amazon Bedrock AgentCore、传统 CodePipeline 规则本身写成 AWS 原生智能 CI/CD 功能。
- 运行时 Agent、MCP、Check Run、recommendation 或生成的代码，只证明分析／工具调用／候选变更可用；合并、部署、恢复仍由 GitHub/GitLab Gate、目标环境、IAM、外部工具权限和人工流程决定。
- 任何关于 MTTR、命中率、质量或效率的数值／效果都只属 AWS 厂商自述，未将其写成页面结论。

## 候选能力池

| # | 候选能力（可见标签） | 机制与 CI/CD 阶段 | 用户可见产物 | 状态、日期与授权／副作用边界 | 上页建议 |
|---|---|---|---|---|---|
| A1 | **交付—运行上下文地图** | Agent Space 从资源配置、CloudFormation、标签、代码仓、CI/CD 与遥测中生成环境理解；新增的 *Code Dependencies* 映射服务／包依赖，*Pipeline Topology* 映射流水线阶段与环境晋级。阶段：代码变更、构建、部署、运行的共享上下文。 | Topology／Pipeline view；版本化、只读的 Summary report；可查询依赖、部署环境与关键路径。 | Production operations **GA 2026-03-31**；两项 learned skills 于 **2026-06-11** 加入，未被 AWS 单独标生命周期。地图会受已接入的账户、标签、仓库、Pipeline 与遥测质量限制；它是派生上下文，不能证明某次变更的因果关系。 | **强烈推荐，新增。** 放在所有执行能力上方作为 AWS 的差异化底座，而非与功能卡片并列。 |
| A2 | **发布就绪审查与托管验证** | 对 PR/MR、branch 或 commit 检查跨仓依赖、内部标准与访问控制；可在 AWS 管理的 verification environment clone、build、run、test。阶段：编码／PR／合并前。 | Release readiness report：`BLOCK / Proceed with Caution / Safe to Release`、受影响代码位置、建议、执行 journal；PR/MR inline comment 与整体状态。 | Release management **Preview 2026-06-17，`us-east-1`**。每仓库可独立开关；私有依赖要显式授予 runtime role 与 VPC 私网访问。`BLOCK` 只有被配置为 required status check／MR approval rule 才能阻止合并。 | **保留并细化原“发布就绪审查”。** 可把 A1 作为其上下文来源，避免把它误写成普通 LLM code review。 |
| A3 | **变更驱动的探索式发布测试** | 对已部署的 Web／API 应用，根据代码变更或测试意图生成测试计划，并在客户提供的 production-like target 执行功能、集成、用户旅程与边界测试。阶段：部署前验证／发布验证。 | Test plan、execution summary、metrics、logs、traces；GitHub Commit／PR Check Run。 | Release management **Preview 2026-06-17，`us-east-1`**。目标端可能收到 `POST`、`PUT`、`DELETE` 请求；需隔离环境、最小权限测试凭证与可清理数据。Check Run 本身不是发布权力。 | **保留原“变更驱动发布测试”。** 与 A2 分开：A2 在 AWS 托管验证环境，A3 对客户已部署目标产生真实副作用。 |
| A4 | **事件自主分流与根因调查** | Alert、ticket、webhook 或人工触发后，Agent 把 telemetry、代码、部署历史、拓扑和已授权工具关联；可 link／skip／proceed，再形成可能根因与缓解计划。阶段：发布后验证、事故调查与恢复准备。 | Investigation timeline、root-cause assessment、mitigation plan、journal；可将调查内容导出 Markdown，或更新 ticket／chat。 | Production operations **GA 2026-03-31**。AWS native production tools 默认不修改基础设施或应用；创建 ticket／Support case 是有限例外。调查和计划不等于自动恢复；自带 MCP／webhook 的写权限另行治理。 | **保留原“上线后事件调查”。** 从“智能告警摘要”提升为“跨变更与运行证据的调查”。 |
| A5 | **按需 SRE 任务与交互式证据产物** | 以自然语言查询资源健康、部署、历史事件模式、架构与建议；可 steer 正在进行的 investigation。阶段：运行、发布后复盘，也可回看部署。 | Chat artifact（报告、文档、图表）、资源／拓扑问答、调查补充结论；AWS Support case 可附带调查上下文。 | Production operations **GA 2026-03-31**。依旧只在已授权的数据源、角色与工具范围内工作；生成报告或建议不是变更执行。 | **可选新增。** 若页面要表现“人如何消费运维智能”，与 A4 合并为“调查与 SRE 助手”；若强调自动化，优先给 A7。 |
| A6 | **历史事故预防与改进待办** | 每周或按需分析历史调查，识别重复根因、可靠性／性能／成本模式，生成改进建议；建议可排序并导出。阶段：事故复盘 → 测试、流水线、架构或可观测性改进。 | Improvements／recommendations backlog、报告；代码或配置问题可形成供 Coding Agent 消费的 specification。 | Production operations **GA 2026-03-31**。建议由团队 `Keep / Discard` 并在外部系统实施；AWS 对 MTTR 与预防价值的描述是厂商自述。 | **保留原“事故回流与预防建议”。** 页面表述应是“生成并排序改进待办”，不要写“自动预防故障”。 |
| A7 | **可调度的自定义 SRE Agent** | 用户定义 system prompt、skills 和经挑选的 MCP tools；按需或按 cron／rate 触发，运行跨遥测、CI/CD、ticketing 等多步工作流。阶段：持续运行治理、发布报告、配置审计与趋势分析。 | 每次 invocation trajectory（推理步骤、工具调用和结果）；text、持久 artifact、`Proposed` recommendation。 | 2026-06-12 文档加入、2026-06-15 AWS 公告；公告未给独立 GA／Preview 标签。仅能调用被分配的工具，但这些 MCP tools 可能写 ticket、消息或外部系统；需要逐 Agent 最小权限、输出审阅与 trigger 治理。 | **强烈推荐，新增。** 它把 AWS 从“内建调查 Agent”扩至“组织可编程的 SRE 例行自动化”。 |
| A8 | **IDE／变换 Agent 内的发布反馈回路** | Kiro Power 与 Claude Code plugin 可在生成代码过程中调用 release-readiness review；AWS Transform custom 的官方 sample skill 可在 Transform 生成／修改代码后触发 review，并在最终变更前处理 findings。阶段：代码生成、现代化改造、提交前。 | IDE 内 findings、review report；Kiro 可基于 investigation finding 在本地 workspace 生成 remediation code；Transform 输出内呈现 review finding。 | 审查／测试部分继承 Release management **Preview 2026-06-17**；Kiro／Transform 接入公告和文档未将接入本身单独标为 GA。生成的修复仍是候选代码，须经版本控制、测试和合并 Gate。 | **推荐作为 A2 的“开发者入口”注释或箭头，不建议另占一张主卡。** 它比“通用 IDE 插件”更有意义，因为把运行／依赖上下文前移到代码生成。 |
| A9 | **MCP／A2A 无界面接入与外部子 Agent 扩展** | DevOps Agent 可作为 remote MCP／A2A endpoint 被 IDE、CLI 或外部 Agent 调用；也可将远程 A2A sub-agent 作为工具接入 Agent Space。阶段：跨工具协作与运行自动化扩展。 | Headless chat／investigation 调用；scoped access token 或 SigV4 身份；外部 Agent 的工具调用结果。 | 2026-06-12 文档加入、2026-06-15 AWS 公告；未给独立 GA／Preview 标签。MCP/A2A 是连接协议，不是自动授权；token scope、IAM、Agent Space 工具集及远端 Agent／MCP server 的权限各自生效。 | **候选但不建议作主视觉。** 适合页脚“开放扩展面”，否则容易把接口能力误读为端到端自治。 |

## 相对原 CD1 的取舍

原 CD1 的四项仍成立，但应从功能清单升级为如下结构：

```text
持续上下文（新增 A1）
  ├─ 发布前：A2 发布就绪审查／托管验证 → A3 变化相关发布测试
  ├─ 发布后：A4 事件分流与调查 → A6 改进建议回流
  └─ 可编程运行：A7 自定义、可调度 SRE Agent
      （A8 为开发者入口；A9 为扩展接口）
```

| 原 CD1 项 | 建议 | 原因 |
|---|---|---|
| 发布就绪审查 | **保留，改为 A2 并显式带出托管验证** | 可证明跨仓依赖、标准、访问控制和 AWS-managed build/run/test；不要把 `BLOCK` 当作天然 merge gate。 |
| 变更驱动发布测试 | **保留，改为 A3** | 是最清晰的发布验证功能；必须把客户目标上的 HTTP 副作用写出来。 |
| 上线后事件调查 | **保留，改为 A4；可与 A5 组合** | 当前 GA，且真正体现代码、部署、遥测的共用上下文。 |
| 事故回流与预防建议 | **保留，改为 A6** | 说明运行反馈如何回流交付，但只是一份可处理的改进建议，而非自动修复。 |
| （无） | **新增 A1** | A1 是 AWS 相对于一般 Code Review／Chat Agent 的关键机制，应取代其中一个泛泛的功能槽位。 |
| （无） | **新增 A7** | 自定义、定时的 SRE workflow 能展示 AWS 从“产品内功能”到“可编程运维 Agent”的扩展。 |

### 推荐给单页左右对比的 AWS 最终能力簇

若允许每侧多项而不是固定四项，AWS 可呈现为六项、三层结构：

1. **上下文地图：**资源拓扑、跨仓依赖、Pipeline 晋级关系与 versioned summary；
2. **发布就绪审查：**标准、依赖、权限与托管验证；
3. **探索式发布测试：**按变化生成测试并在客户 production-like target 执行；
4. **事件调查：**把 telemetry、代码与部署历史连接到根因／缓解计划；
5. **预防改进：**历史事件 → 排序建议／可消费 specification；
6. **自定义 SRE Agent：**用户配 prompt／skills／最小工具集，按计划运行并输出报告或建议。

开发者入口（Kiro、Claude Code、Transform）宜作为第 2、4 项之间的连线标注；MCP/A2A 宜列页脚授权边界，不占主要功能空间。

## 明确排除项

- **Amazon Bedrock AgentCore：**是通用 Agent 构建、运行、身份、网关和治理平台，不是 AWS DevOps Agent 的 CI/CD 编排功能；即使可将 CodePipeline 等动作封装为工具，也不构成原生交付 Gate。
- **传统 CodePipeline／CodeBuild 规则：**本身是确定性 CI/CD 机制；除非有官方证据证明某个 DevOps Agent 触发／反馈链，否则不因为与 Agent 并存就贴为智能化能力。
- **Amazon CodeGuru Reviewer：**官方 FAQ 表明自 **2025-11-07** 起不能新建 repository association；不应作为截至本研究日期的长期 AWS 代码审查核心能力。
- **“自动部署／自动恢复”表述：**未找到官方材料证明 DevOps Agent 将合并、部署和 remediation 以一个端到端事务自动执行；native production tools 默认不改基础设施或应用，外部 MCP／webhook 的副作用仍是客户授权面。

## 主要一手来源（均访问于 2026-08-05）

| 来源 | 发布／更新信息 | 本研究使用范围 |
|---|---|---|
| [AWS DevOps Agent is now generally available](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/) | 2026-03-31 | Production operations GA、调查、预防、按需 SRE、Azure/on-prem、厂商效果声明。 |
| [AWS DevOps Agent release management preview](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) | 2026-06-17 | Release management Preview、`us-east-1`、就绪审查和 release testing 的范围。 |
| [Release readiness code reviews](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | 文档当前版本 | A2 的 repository 开关、verification environment、runtime role/VPC、产物、SCM gate、Kiro/Claude/Transform 接入与 guardrail。 |
| [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) | 文档当前版本 | A3 的 customer target、Web/API 测试和 HTTP 副作用边界。 |
| [Learned skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html) | 文档当前版本 | A1 的四种 learned skills、Topology view、versioned read-only Summary report 和刷新语义。 |
| [What's new — AWS DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html) | 2026-05—06 feature history | A1/A7/A8/A9 的功能加入日期。 |
| [Custom Agents](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-custom-agents-index.html)；[Custom agent outputs](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-custom-agent-outputs.html) | 文档当前版本 | A7 的 prompt、技能、最小工具集、schedule、trajectory、artifact/recommendation 与 MCP 副作用。 |
| [AWS DevOps Agent expands with custom SRE agents and MCP/A2A](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-custom-agents/) | 2026-06-15 | A7/A9 的公开发布、Kiro/Claude 等 headless access、memory/Git-managed skills。 |
| [Kiro Power for AWS DevOps Agent](https://aws.amazon.com/blogs/devops/supercharge-your-cloud-operations-with-the-kiro-power-for-aws-devops-agent/) | AWS 官方博客，页面当前版本 | A8 的 local workspace + cloud context、IDE 内 investigation/review/test/remediation code 入口及 token scope。 |
| [Amazon CodeGuru Reviewer FAQ](https://aws.amazon.com/codeguru/reviewer/faqs/) | 页面当前版本 | CodeGuru Reviewer 不进入长期核心候选池的 availability-change 证据。 |

## 证据缺口

1. AWS 没有为 2026-06 custom agents、MCP/A2A、Kiro Power 和 memory 等每项分别提供 GA／Preview 标注；因此本文件只报告其发布日期和产品公告措辞，不推断独立生命周期。
2. 没有发现可比较、独立复核的 release-test 缺陷检出率、误报率、根因准确率或跨客户 MTTR 数据；所有 AWS 效果性描述仍为厂商自述。
3. 拓扑／依赖／Pipeline 关系的完整性依赖接入与持续刷新；公开资料没有给出完整性或陈旧度的量化 SLA，故不应写成实时且完整的系统真相。

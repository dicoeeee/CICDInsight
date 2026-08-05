---
title: AWS DevOps Agent Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/aws
status: complete
as_of: 2026-08-03
confidence: high-for-mechanism-medium-for-outcomes
---

# AWS DevOps Agent Claim—Evidence—Gap Matrix

## 证据标记

- **A：** AWS 当前 User Guide、Pricing、Quotas、What's New 和 AWS 官方 GitHub 仓库；可证明产品机制、接口、限制和明确状态。
- **B：** AWS 官方博客、示例和厂商案例；可证明 AWS 自身的实现说明或产品主张，效果不可外推。
- **分析：** 主 Agent 基于多项 A/B 事实形成的架构、比较或采用判断，不冒充 AWS 原话。
- **缺口：** 当前未找到足以核验的独立客户结果、跨产品基准或第三方复现。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| AWS-C01 | Production operations 于 2026-03-31 GA | [AWS GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/) 明确发布时间和 GA 范围；[[00_sources/briefs/2026-aws-devops-agent-production-operations-ga\|Source Brief]] | GA 公告中的 MTTR 改善是厂商自述；GA 不自动覆盖后续 Preview 能力 | high | passed |
| AWS-C02 | Release Management 截至 2026-08-03 仍是 `us-east-1` Preview | [Release Management 公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) 与 [Supported Regions](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-supported-regions.html) 正向标记 Preview 和区域 | AWS 文档称 GA 时将扩区，但这是 Roadmap 表述，不是当前事实 | high | passed |
| AWS-C03 | Agent Space 是账号资源、第三方连接、权限和数据的首要隔离边界 | [Security](https://docs.aws.amazon.com/devopsagent/latest/userguide/aws-devops-agent-security.html) 明确称 primary security boundary，并列出独立配置、权限、账户和资源范围 | 账号级 registration provider 可能被多个 Space 复用；仍需审计共享连接 | high | passed |
| AWS-C04 | Topology 由资源发现、配置、CloudFormation、Tag、代码/部署和观测关系共同生成 | [Topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html) 分列 discovery、relationship、code/deployment、observability mapping | 非 CloudFormation 资源依赖 Tag 与 Resource Explorer；图不等于完整或已证明因果 | high | passed |
| AWS-C05 | Learned skills 将 Agent Space、代码依赖、Pipeline 拓扑和历史工具用法变成结构化知识 | [Learned Skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html) 列出四类文件及内容 | 它们是服务生成的派生知识；完整性和新鲜度仍取决于集成与环境质量 | high | passed |
| AWS-C06 | Pipeline Topology 将步骤、环境晋级和部署状态带入 Agent 任务 | [Learned Skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html) 明确映射 pipeline steps、promotions、deployments | 这是 Agent 可读取的知识，不是取代 CI orchestrator 或获得 deployment 权限 | high | passed |
| AWS-C07 | 事件调查可由内建工单集成、Webhook 或人工启动，并产生计划、证据、根因和 mitigation plan | [Autonomous incident response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html) 描述三种入口与调查输出 | 自动启动与生成 plan 不等于自动执行生产变更 | high | passed |
| AWS-C08 | 内建生产运维工具默认不能修改基础设施或应用，例外是创建 Ticket 与 Support Case | [Security](https://docs.aws.amazon.com/devopsagent/latest/userguide/aws-devops-agent-security.html) 在 prompt-injection 防护中明确 limited write capabilities | Custom MCP/A2A 或客户 EventBridge 工作流可以扩展写面，其安全责任不再等同于 native tools | high | passed |
| AWS-C09 | Release readiness 同时评估标准、跨仓依赖、访问控制，并可执行 managed verification testing | [Release Management](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html) 与 [Release readiness](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | 自然语言 Skills 不等于确定性 Policy-as-Code；只有 CloudFormation 访问控制检查被明确说明 | high | passed |
| AWS-C10 | Managed verification environment 会 clone/build/run/test，并以 runtime role、网络 allowlist 或 VPC 访问内部依赖 | [Release readiness](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) 描述 dedicated compute、allowlist、VPC ENI 与 runtime role | AWS 未公开完整隔离实现、构建缓存、依赖供应链保证或跨客户性能基准 | high | passed |
| AWS-C11 | Release testing 根据代码变更或 test intent 生成计划，并对已部署 Web/API 目标执行 | [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) 说明 plan → execute → report | 当前仅支持 Web UI 与 REST API；不证明覆盖所有服务、协议或质量风险 | high | passed |
| AWS-C12 | Release testing 可能发出 `POST / PUT / DELETE` 并改变数据 | [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) 明确警告真实写请求并建议 staging | 因此不能把 release testing 当作天然只读；目标隔离、通知、支付和删除风险由客户控制 | high | passed |
| AWS-C13 | Readiness 结果与 release testing 结果只有映射到宿主规则才形成 Gate | [Release readiness](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) 说明 GitHub required status check / GitLab approval rule；[Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) 说明 GitHub Check Run | Agent 的 `BLOCK` 或普通 Check Run 不是天然发布授权；宿主保护规则决定阻断 | high | passed |
| AWS-C14 | GitHub release-testing 接入采用 Action 提交 Webhook、Agent Space 异步执行并回写 Check Run | [AWS 官方仓库](https://github.com/aws-actions/devops-agent-qa) 与 [User Guide](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) 均支持此链 | User Guide 正文与示例的 Action 名不一致；正式结论只引用机制，不把单一包名当稳定合同 | high for mechanism; low for identifier stability | passed-with-conflict |
| AWS-C15 | Agent Space 数据存储区域与 Bedrock 推理处理区域并不总是相同 | [Security](https://docs.aws.amazon.com/devopsagent/latest/userguide/aws-devops-agent-security.html) 说明区域存储、同地理范围路由和三类 global-routing 例外 | SCP/Control Tower 的区域限制不约束该服务推理路由；需单独做数据驻留评估 | high | passed |
| AWS-C16 | AWS 声明不使用 Agent 数据、聊天或集成数据训练模型或改进产品 | [Security — Data usage](https://docs.aws.amazon.com/devopsagent/latest/userguide/aws-devops-agent-security.html) 明确说明 | 这不消除 PII、第三方连接、客户反馈和数据源可信治理责任 | high | passed |
| AWS-C17 | GA 能力按 Agent 工作秒计费，Preview Release Management 暂无额外费用；关键并发有配额 | [Pricing](https://aws.amazon.com/devops-agent/pricing/) 为 `$0.0083/agent-second`；[Quotas](https://docs.aws.amazon.com/devopsagent/latest/userguide/quotas.html) 列出 Investigation、Review、Test、Custom Agent 并发 | CloudWatch 查询、Trace 和其他服务另收费；Preview 定价与配额可能变化 | high | passed |
| AWS-C18 | AWS DevOps Agent 的统一价值在“共同交付—运行上下文”，不是单个告警摘要或自动修复 | AWS-C03—C13；[[00_sources/research-aws-devops-agent-core-2026-08-03\|核心产品研究]] | **分析推断。** 没有公开 benchmark 证明共同上下文本身必然提高正确率或降低 MTTR | medium-high | passed-as-analysis |
| AWS-C19 | 产品更像上下文与证据控制面，而不是完整 CI/CD orchestrator 或 Runbook executor | AWS-C06、C08、C13；CI Job/部署/审批仍由外部 Pipeline、SCM 与 IAM 控制 | Custom MCP、EventBridge 和 Coding Agent 可组合写动作，但这属于客户扩展后的系统，不是内建权限边界 | medium-high | passed-as-analysis |
| AWS-C20 | 当前可支撑受限架构页面，但不能支撑普遍 ROI 或端到端自治结论 | AWS-C01—C19；本轮未找到可独立复核的客户效果、误报率和验证覆盖数据 | “未找到”是证据缺口，不是证明客户不存在或产品无效 | high for boundary | passed |

## 生命周期与状态审计

| 对象 | 截至 2026-08-03 的状态 | 可安全表述 | 不能写成 |
|---|---|---|---|
| Production operations | GA；所有支持区域 | GA 的事件调查、预防建议、按需 SRE 任务 | 整个产品所有能力均 GA；自动修复生产 |
| Release Management | Preview；仅 `us-east-1` | Preview 的 readiness review、managed verification、release testing | GA、全球可用、自动发布 |
| Custom agents | 当前正式文档能力；所有支持区域 | 可按需/定时运行并使用获配 tools/skills | 任意事件触发、无限并发、默认写权限 |
| Native production tools | 以读和建议为主 | 不修改资源/应用；可建 Ticket/Support Case | 通用 Runbook executor |
| Custom MCP/A2A | 客户可配置扩展面 | 能引入额外工具和 Agent | 与 native tools 具有相同安全保证或自动获得授权 |
| Release testing target | 客户提供的已部署 Web/API | 可执行真实 UI/API 请求 | 天然只读、适合无隔离地指向生产 |

## 主要证据缺口

- 独立客户的根因正确率、误报率、steering 比例、MTTR 对照与长期预防效果；
- Release readiness 和 release testing 的缺陷发现率、覆盖率、稳定性、单位成功验证成本与失败降级行为；
- Topology / learned skills 在缺失 Tag、非 CloudFormation、多云、遗留 Pipeline 和频繁变更环境中的准确率；
- 具体基础模型、planner、tool-selection、重试、ranking、context 管理和模型更新策略；
- Managed verification environment 的完整隔离、供应链、缓存、镜像和数据销毁实现；
- Preview GA 时间、正式定价、全区域覆盖和采购级 SLA；
- 从 AWS DevOps Agent 迁出 learned skills、investigation history 和组织知识的完整路径。

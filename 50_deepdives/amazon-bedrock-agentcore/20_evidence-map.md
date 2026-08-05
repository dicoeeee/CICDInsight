---
title: Amazon Bedrock AgentCore Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - platform/aws
status: complete
as_of: 2026-08-04
confidence: high-for-mechanism-medium-for-outcomes
---

# Amazon Bedrock AgentCore Claim—Evidence—Gap Matrix

## 证据入口

- [[00_sources/research-aws-llm-cicd-agent-platform-capabilities-2026-08-03|AgentCore 与 CI/CD Agent 平台能力研究]]：生命周期、Runtime、Gateway、Policy、Evaluations、Optimization 与 CI/CD 接点；
- [[00_sources/research-agentcore-transform-devops-agent-relationship-2026-08-03|AgentCore、AWS Transform 与 AWS DevOps Agent 层级关系核验]]：平台层与垂直 Agent 产品关系；
- [[00_sources/research-amazon-bedrock-agentcore-architecture-2026-08-03|AgentCore 产品边界、架构与运行约束]]：研究 Subagent 对当前能力域、状态、区域与配额的一手核验；
- [[00_sources/research-amazon-bedrock-agentcore-governance-2026-08-03|AgentCore 企业治理与落地边界]]：研究 Subagent 对 Identity、Gateway、Runtime、Memory、数据、成本与外部 Oracle 的一手核验；
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-mechanics-2026-08-03|AgentCore Evaluations 机制、数据合同与生产边界]]：研究 Subagent 对执行面、evaluator、Ground Truth、遥测、配额和数据边界的一手核验；
- [[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|AgentCore Evaluations 与 CI/CD 发布门禁边界]]：研究 Subagent 对回归、evidence chain、scenario contract 和 release authority 的一手核验；
- [[00_sources/research-amazon-bedrock-agentcore-memory-cicd-2026-08-04|AgentCore Memory 机制与 CI/CD 启发]]：研究 Subagent 与主 Agent 对 Event、strategy、Record、namespace、检索、保留和双层删除的一手核验；
- AWS 官方 Developer Guide、What's New、Release Notes、Pricing、Quotas 与 Security 页面：下表逐 Claim 直接链接。

## 证据标记

- **事实：**AWS 当前文档、API、价格、配额、发布说明或官方仓库直接陈述；
- **厂商自述：**AWS 对效率、生产级或安全价值的产品表述，未经独立客户数据验证；
- **分析：**基于多项事实形成的架构或采用结论，不冒充 AWS 原话；
- **缺口：**当前没有足够一手证据，不进入正式结论。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| AC-C01 | AgentCore 于 2025-07-16 Preview、2025-10-13 平台 GA | [Preview 公告](https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-bedrock-agentcore-preview/)；[GA 公告](https://aws.amazon.com/about-aws/whats-new/2025/10/amazon-bedrock-agentcore-available/) | 平台 GA 不等于后来新增的 Harness、Policy、Optimization Insights、Registry 和文件系统能力在同日或全部区域 GA | high | passed |
| AC-C02 | AgentCore 是可组合的 Agent 平台能力族，可独立或组合使用，并支持多 framework、model 与 protocol | [AgentCore Overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/) 明确 modular services、任意 framework / model；[Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) 列 MCP / A2A 与多个框架 | “支持任意模型 / 框架”是接口兼容性，不证明行为、成本、trace 语义或迁移体验完全等价 | high | passed |
| AC-C03 | Amazon Bedrock Agents 已成为 Agents Classic，并于 2026-07-30 起不再向新客户开放；AWS 指向 AgentCore 的相似能力 | [Bedrock Agents 如何工作](https://docs.aws.amazon.com/bedrock/latest/userguide/agents-how.html) 的 maintenance-mode 注记 | 现有客户仍可继续使用；“探索 AgentCore”不是一键迁移、兼容保证或强制停服 | high | passed |
| AC-C04 | Runtime 提供托管基础设施；Harness 提供配置式 agent loop，且 Harness 是运行在 Runtime 内的托管抽象 | [Harness vs. Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-vs-runtime.html) 直接区分“bring code”与“declare config” | Harness 由 Strands Agents 驱动；导出代码目前不能证明对任意框架保持同一行为 | high | passed |
| AC-C05 | Harness 与 Runtime 共享 IAM / JWT + microVM 的安全边界，不新增独立隔离层 | [Runtime Security Best Practices](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-security-best-practices.html) 明确说明 shared trust boundary | 托管 loop 不自动降低工具权限、prompt injection 或 execution-role 泄露风险 | high | passed |
| AC-C06 | Runtime 为每个 user session 提供独立 microVM，但 session-to-user 映射、最小权限和持久文件权限仍由客户负责 | [Runtime Security Best Practices](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-security-best-practices.html) 明确 session isolation、应用侧映射、MMDS credentials 与 filesystem permission 行为 | microVM 隔离防止跨工作负载访问，不验证 Agent 生成的命令、代码或业务动作正确 | high | passed |
| AC-C07 | Runtime 具有明确规模与执行上限：如每 session 最大 2vCPU / 8GB、异步任务最长 8 小时，配额按区域和能力分别管理 | [AgentCore Quotas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html) | 部分配额可调，数值和区域会变化；实施时必须对目标区域重新查询，不能把上限当 SLA | high | passed |
| AC-C08 | Harness / Runtime 支持不可变版本和 Endpoint 指向，为发布、切换和回滚提供平台原语 | [Harness versioning and endpoints](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/harness-versioning.html) 明确每次更新新建 immutable version | 版本原语不自动决定何时发布、如何分流、是否通过业务验证；Endpoint 切换仍需外部变更控制 | high | passed |
| AC-C09 | Gateway 已从单纯 MCP 转换层扩展为 Agent、工具和模型流量的统一入口 | [Gateway Overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html) 明确 API / Lambda → MCP、A2A passthrough、HTTP service 与 model routing | 统一入口只有在调用方不能绕过它时才是统一控制面；过宽 target 会把风险集中到 Gateway | high | passed |
| AC-C10 | Identity 为 Agent workload、用户委托与第三方凭证提供身份 / credential 管理 | [Identity](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/identity.html)；[OBO GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-bedrock-agentcore/) | Identity 解决认证和 token 获取，不替代下游资源授权、业务审批或 source-of-truth 的状态判断 | high | passed |
| AC-C11 | Policy 在 Agent code 外、Gateway 工具调用前逐次评估 Cedar 规则，采用 default-deny、forbid-wins，并用 tool schema 做创建时验证 | [Policy Core Concepts](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-core-concepts.html)；[Policy GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/policy-amazon-bedrock-agentcore-generally-available/) | Policy 只覆盖进入已关联 Gateway 的请求；它不验证代码、测试、制品、部署健康或业务结果 | high | passed |
| AC-C12 | 若调用者可直达 Runtime，就可绕过 Gateway Policy、Guardrails 和 interceptor | [Runtime Security Best Practices](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-security-best-practices.html) 明确记录 bypass 风险和限制直达路径的建议 | 因此“配置了 Policy”不能单独证明所有 Agent 行动已受治理 | high | passed |
| AC-C13 | Memory 同时提供 session 内 short-term events 和跨 session long-term records；删除 Event 不会自动删除已提炼的长期 Record | [AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)；[DeleteEvent](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-delete-event.html)；[DeleteMemoryRecord](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-delete-memory-records.html) | long-term memory 是派生上下文，不是独立核验的组织事实；敏感内容的遗忘流程必须覆盖 raw event 与 derived record 两层 | high | passed |
| AC-C14 | Observability 将 Agent、Memory、Gateway 和 built-in tools 的 telemetry 汇入 CloudWatch，并可接入自定义 trace | [Service-provided observability](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-service-provided.html)；[统一日志公告](https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-bedrock-agentcore-unified-observability-single-log-group/) | trace 能重建执行路径，但不证明选择正确；prompt、tool 参数和输出进入日志会引入敏感数据、保留和 CloudWatch 成本 | high | passed |
| AC-C15 | Evaluations 有 online、on-demand、batch 三种执行面；batch 支持 expected response、assertions 与 expected tool trajectory | [Evaluation Types](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations-types.html)；[Evaluations GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/) | LLM-as-judge 是概率证据；最关键规则仍需 programmatic trajectory、Lambda code evaluator、Policy/IAM 与真实系统 Gate | high | passed |
| AC-C16 | Optimization 在 2026-06-17 呈混合状态：batch evaluations、recommendations、A/B tests GA；failure / intent / trajectory insights Preview | [Optimization 公告](https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-new-optimization-capabilities/) | AWS 的“prove they work”是产品措辞；A/B 和 evaluation 只能证明所选指标改善，不能自动证明高风险动作可发布 | high | passed |
| AC-C17 | Recommendation 不应自动写入生产 Agent，AWS 的优化流程保留批准与验证步骤 | [Optimization Preview 公告](https://aws.amazon.com/about-aws/whats-new/2026/05/bedrock-agentcore-optimization-preview/) 说明 recommendation 在发布前需要批准 | 审批存在不证明推荐无偏、数据充分或不会对未覆盖人群回归 | high | passed |
| AC-C18 | AWS Agent Registry 截至 2026-08-03 仍为 Preview，可登记和审批 Agent、tool、skill、MCP server 与 custom resource | [Registry Preview 公告](https://aws.amazon.com/about-aws/whats-new/2026/04/aws-agent-registry-in-agentcore-preview/) | Registry 管理发现、元数据与审批，不在每次调用时强制授权，不能替代 Gateway Policy / IAM | high | passed |
| AC-C19 | Step Functions 能把 AgentCore reasoning / Harness 放入确定性 workflow，并在关键动作前保留 human approval | [Step Functions 集成公告](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-step-functions-agentcore/)；[Step Functions 文档](https://docs.aws.amazon.com/step-functions/latest/dg/connect-bedrockagentcore.html) | 工作流可观察 / 可审批不等于终止语义自动一致；幂等、超时、补偿和 Agent side effect 仍需设计 | high | passed |
| AC-C20 | AgentCore 采用模块化消费计费，完整 TCO 横跨 Runtime、Gateway、Policy、Memory、Evaluations、CloudWatch、模型和网络 | [AgentCore Pricing](https://aws.amazon.com/bedrock/agentcore/pricing/) | AWS 示例不是客户基准；“I/O wait free”收益依赖真实 CPU / memory / background process profile，不能直接外推 | high | passed |
| AC-C21 | AgentCore 的核心架构可理解为“行动闭环 + 质量闭环” | **分析：**AC-C04、AC-C09—C17：运行/编排 → 工具/身份/策略 → action；trace → evaluation → recommendation/experiment → version rollout | AWS 没有把所有模块定义为强制单体架构；模块可独立使用，闭环完整性取决于企业是否连接每一段 | medium-high | passed-as-analysis |
| AC-C22 | Agent 的最小发布单元不应只有代码，还应包含 prompt、model pin、tool schema、policy、memory strategy、evaluation set 与 endpoint mapping | **分析：**AC-C08、AC-C11、AC-C13—C17 | AWS 提供各类资源与版本原语，但未公开一个自动把所有资源锁成原子 release bundle 的通用合同；需要组织自建版本关联与变更记录 | medium-high | passed-as-analysis |
| AC-C23 | AgentCore 提供 Agent 生产控制面，但不是业务正确性 Oracle 或 CI/CD orchestrator | **分析：**AC-C06、AC-C11—C17、AC-C19；[[00_sources/research-aws-llm-cicd-agent-platform-capabilities-2026-08-03|CI/CD 接点研究]] | 可调用工具、policy allow、evaluation pass 和 trace complete 都不是代码正确、制品可信、部署健康或业务审批的同义词 | high for boundary | passed-as-analysis |
| AC-C24 | “任意模型 / framework / protocol”降低应用层耦合，但不能消除 AgentCore 控制面的平台锁定 | **分析：**AC-C02、AC-C04、AC-C08—C18 | IAM、CloudWatch、Cedar schema、Memory records、evaluation history、Gateway targets 与 Endpoint 语义仍是 AWS-specific；Harness export 只覆盖 loop 代码的一部分 | medium-high | passed-as-analysis |
| AC-C25 | 截至观察日，公开一手材料不足以证明 AgentCore 对所有企业 Agent 普遍降低成本、提高正确率或缩短上线周期 | 本专题的 AWS 官方产品、文档、价格、配额与发布说明负面搜索 | “未找到独立 benchmark”不是证明产品无效；需要客户 workload、故障率、评测覆盖和迁移成本数据 | high for evidence gap | passed |
| AC-C26 | AgentCore 当前不能再用固定“八个服务”描述 | [2025 Preview 公告](https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-bedrock-agentcore-preview/) 当时列七项；[当前 Overview](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/) 与 [Quotas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html) 已覆盖 Harness、Policy、Evaluations、Optimization、Payments、Registry 等更多能力域 | 固定数字会随发布过期；应按“运行、行动治理、状态、质量、资产与内建工具”能力域表达 | high | passed |
| AC-C27 | Gateway tool list 只表示存在潜在允许条件，不等于带真实参数的实际调用会获准 | [Use Gateway with Policy](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/use-gateway-with-policy.html) 区分 listing meta-action 与真实 invocation 授权 | 产品界面或 Agent 能“发现”工具不能证明它拥有执行权限，更不能证明目标系统接受副作用 | high | passed |

## Memory 补充 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| AC-M01 | AgentCore Memory 的短期层以带时间戳、不可变 Event 保存交互，并按 actor / session 组织 | [Memory terminology](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-terminology.html)；[Create event](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-create-event.html) | 当前只有 conversational payload 进入长期提取；Event 是记录，不自动证明工具返回或业务状态正确 | high | passed |
| AC-M02 | 长期 Record 由 ACTIVE strategy 对后续事件执行 extraction / consolidation 生成；无 strategy 时不产生长期记录 | [Memory strategies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-strategies.html)；[Enable long-term memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-enabling-long-term-memory.html) | strategy、提示、模型和 schema 会改变提炼结果；新 strategy 不回溯处理此前事件 | high | passed |
| AC-M03 | 长期检索在 namespace / namespacePath 范围内按 query、strategy、metadata 与 topK 选择 Record | [RetrieveMemoryRecords API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_RetrieveMemoryRecords.html)；[Namespaces](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/specify-long-term-memory-organization.html) | 普通 Memory API 只返回记录；Harness 可自动注入，但不能外推为所有 Runtime / framework 的默认行为 | high | passed |
| AC-M04 | 删除 Event 不会级联删除派生 Record；raw-event expiry 变更也只影响之后新写入事件 | [Delete event](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/short-term-delete-event.html)；[Delete memory record](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/long-term-delete-memory-records.html)；[Create memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory-create-a-memory-store.html) | 日志、缓存和已经注入的上下文还需在各自系统内治理；单个 API 成功不证明全链路遗忘 | high | passed |
| AC-M05 | 对 CI/CD 而言，Memory 应作为候选上下文而非发布事实；测试、制品、审批、部署与 SLO 仍从事实系统核验 | **分析：**AC-M01—M04、AC-C13、AC-C23 | AWS Memory 没有发布审批或业务 Oracle 语义；该判断是企业控制边界，不是 AWS 产品声明 | high for boundary | passed-as-analysis |
| AC-M06 | Agent 发布单元应记录 memory strategy、namespace、检索参数、保留期和 evaluation set 的版本关联 | **分析：**AC-M02—M04、AC-C08、AC-C15、AC-C22 | AWS 未公开一个把 code、prompt、model、memory、evaluation 与 endpoint 锁成原子 release bundle 的通用合同 | medium-high | passed-as-analysis |

## Evaluations 补充 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| AC-E01 | Evaluations 本体于 2026-03-31 GA；Batch Evaluation 于 2026-06-17 GA；Dataset Evaluation 截至观察日仍为 Public Preview | [Evaluations GA](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/)；[Release Notes](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html)；[Dataset Evaluation](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/dataset-evaluations.html) | Batch Evaluation 的正式状态不能外推给 dataset management / runner 编排层；后者 API 仍可能变化 | high | passed |
| AC-E02 | Online、on-demand、batch 的 trigger、session source、Ground Truth 和结果合同不同 | [Evaluation Types](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations-types.html)；[Batch Evaluation](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/batch-evaluations.html) | 它们可覆盖多个生命周期阶段，不应机械写成 build / staging / production 一一绑定 | high | passed |
| AC-E03 | On-demand `Evaluate` 是同步、调用方提交 OTel spans 的 API，可携带 Ground Truth；AWS 明确把它用于 dev-time / CI/CD | [Evaluate API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_Evaluate.html)；[Dataset Evaluation](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/dataset-evaluations.html) | 同步 API 仍依赖完整 telemetry；若先从 CloudWatch 取 spans，需要等待 ingest，不能等同于 request-path 实时 Gate | high | passed |
| AC-E04 | Online Evaluation 处理 live production trace 的 sampling / filtering，不能使用依赖 Ground Truth placeholder 的 custom evaluator | [Evaluation Types](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations-types.html)；[Ground Truth Evaluations](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/ground-truth-evaluations.html) | online score 只能说明被抽中流量在所选 rubric 下的表现，不能证明所有生产任务满足真实答案 | high | passed |
| AC-E05 | `expectedResponse` 的 Correctness 与 `assertions` 的 GoalSuccessRate 仍由 LLM judge 评分；三个 trajectory evaluator 才是无 LLM 的程序化匹配 | [Ground Truth Evaluations](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/ground-truth-evaluations.html) | trajectory 只比较工具名称、顺序和是否允许额外工具；不核参数、返回、授权、副作用或业务结果 | high | passed |
| AC-E06 | AWS 官方当前存在 built-in evaluator 数量口径差异，不能把“13 个”当作截至日稳定完整 roster | [Evaluations GA](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/) 与 [Agentic AI Lens](https://docs.aws.amazon.com/wellarchitected/latest/agentic-ai-lens/agentops06-bp02.html) 写 13；[Prompt Templates](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/prompt-templates-builtin.html) 当前列出 14 个 LLM 模板；[Ground Truth](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/ground-truth-evaluations.html) 另列三个 trajectory ID | AWS 未公开带日期和版本的完整静态 ID / judge / score mapping 清单；当前精确 roster 需在目标账户 / Region 用 `ListEvaluators` 核对 | high for conflict | passed-with-conflict |
| AC-E07 | Dataset Evaluation 的 predefined scenario 支持每轮 expected response、session assertions 和 expected trajectory；simulated scenario 不支持前两类精确预期，只使用 assertions | [Dataset Schema](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/dataset-evaluations-schema.html) | Dataset Evaluation 为 Public Preview；模拟场景扩大人物和路径覆盖，但 Oracle 更依赖自然语言 LLM 判断 | high | passed |
| AC-E08 | Custom evaluator 可使用自定义 LLM judge 或 Lambda code evaluator，作用域为 TOOL_CALL / TRACE / SESSION | [Evaluators](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluators.html)；[Create Evaluator](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/create-evaluator.html) | Lambda 可做 deterministic checks / external API，但只证明已编码规则，且受输入、权限、timeout 和外部系统可靠性限制 | high | passed |
| AC-E09 | Batch 每 job 最多评 500 sessions；发现更多时只选择最近 500 个，结果同时包含平均分、失败计数与 CloudWatch 逐 session 明细 | [Batch Results](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/batch-evaluations-results.html)；[Get Batch Evaluation](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/batch-evaluations-get.html) | 平均分可能掩盖关键尾部失败；最近 500 个是 recency selection，不是总体代表性抽样 | high | passed |
| AC-E10 | Evaluation 成本随被评样本、evaluator 数、trace token 和 telemetry 存储 / 查询共同增长 | [AgentCore Pricing](https://aws.amazon.com/bedrock/agentcore/pricing/)；[AgentCore Quotas](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html) | AWS pricing 示例不是客户成本基准；采样、上下文长度、评估维度、CloudWatch retention 与 custom model / Lambda 都会改变 TCO | high | passed |
| AC-E11 | AgentCore Evaluation 可为 CI/CD 提供 quality signal 和 threshold input，但 API 没有制品批准、环境晋级或部署授权语义 | **分析：**[Evaluate API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_Evaluate.html)、[Batch Results](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/batch-evaluations-results.html) 的输出合同，与 [Pricing](https://aws.amazon.com/bedrock/agentcore/pricing/) 的 CI/CD integration 表述联合判断 | toolkit / pipeline 可配置阈值；把 score 映射为 block / override / approval 的权威和失败策略仍在外部 CI/CD | high for boundary | passed-as-analysis |
| AC-E12 | 可复现的 Agent release evidence 需要版本化 Scenario Contract，把 code / prompt / model / tool / policy / dataset / evaluator / endpoint 与 trace、target audit 和 Gate 关联 | **分析：**AC-E03—E11；[[00_sources/research-amazon-bedrock-agentcore-evaluations-cicd-2026-08-03|CI/CD 证据边界研究]] | AWS 原生 schema 覆盖 scenario、reference inputs 与 evaluation result，但未公开把全部资源绑定为原子 release bundle 的通用合同 | medium-high | passed-as-analysis |
| AC-E13 | Recommendations、Batch 与 A/B 构成闭环后，若同一 evaluator / dataset 同时定义优化目标和验收条件，会有代理指标或测试集过拟合风险 | **分析：**[Optimization](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/optimization.html) 的 observe—recommend—evaluate—experiment 流程 | 这是机制风险而非已证实产品缺陷；应通过 frozen versions、holdout scenarios、deterministic oracle、人工 / 业务复核和显式批准治理 | medium | passed-as-analysis |

## 生命周期快照

| 能力 | 截至 2026-08-03 | 可安全表述 | 不能写成 |
|---|---|---|---|
| AgentCore 平台、Runtime、Gateway、Identity、Memory、Observability | GA 基座；单项新增特性仍可能有独立状态 | 模块化生产平台基座 | 所有能力、所有区域、所有增量功能同状态 |
| Policy | GA（2026-03-03） | Gateway 外置 Cedar 授权 | 端到端业务授权 / 代码安全验证 |
| Evaluations | 本体 GA（2026-03-31）；Batch GA（2026-06-17）；Dataset Evaluation Public Preview | online / on-demand / batch Agent 行为评测；dataset runner 需保留 Preview 标签 | 确定性发布 Oracle / 全部评测子能力同状态 |
| Harness | GA（2026-06-17） | Runtime 上的托管 agent loop | 新的独立安全边界 / 通用 CI runner |
| Optimization | batch / recommendation / A/B = GA；insights = Preview | 受控的 observe—evaluate—improve 原语 | 自动自愈或无人批准的生产优化 |
| AWS Agent Registry | Preview | 私有目录、发现和审批资产 | 每次调用的运行时策略引擎 |
| Payments | Preview | x402 支付工具、wallet 与 spending-limit 原语 | 通用费用治理 / 自动采购授权 |
| Bedrock Agents Classic | 维护路径；2026-07-30 起不向新客户开放 | 现有客户可继续用，新增设计优先评估 AgentCore | 已停服 / 自动迁移完成 |

## 主要证据缺口

- 独立客户的上线周期、平台团队人力、每成功任务成本、Agent failure rate 与 regression escape rate；
- 从 Bedrock Agents Classic、LangGraph / CrewAI 自建平台或其他云 Agent 平台迁入 / 迁出的完整成本；
- Gateway、Policy、Identity、Memory、Evaluations 在大规模多租户下的端到端延迟和故障耦合；
- Memory 派生记录、evaluation history、Registry metadata 与 policy assets 的完整可移植 / 导出合同；
- 对 prompt injection、tool misuse、cross-session contamination 和 evaluator drift 的独立红队 / benchmark；
- built-in evaluator 的完整版本化 ID、judge model、prompt / rubric 和 label→number mapping 合同；
- trace、evaluation explanation、dataset metadata 与 result logs 的统一 retention / deletion / export 合同；
- Recommendation / A/B 对高风险企业动作的长期效果、回滚行为和未覆盖场景回归率。

---
title: AWS LLM 与 CI/CD Agent 底层平台能力研究（不含 AWS DevOps Agent）
date: 2026-08-03
as_of: 2026-08-03
status: research-complete
confidence: high
scope: Amazon Bedrock AgentCore 及 AWS 原生集成；只讨论与 CI/CD Agent 的运行、工具调用、流水线触发、验证/治理、Agent observability/evaluation 有直接机制关系的底层能力。不覆盖 AWS DevOps Agent，也不把通用基础模型推理服务当作 CI/CD 能力。
source_policy: 仅 AWS 官方文档、AWS What's New、AWS 官方博客和 AWS 官方仓库；全部链接于 2026-08-03 访问。
---

# AWS LLM 与 CI/CD Agent 底层平台能力

## 提纲

1. 范围、术语与证据口径
2. 截至观察日的 AWS 平台生命周期快照
3. 候选能力：运行、工具调用、触发、验证/治理、可观测与评估
4. 可实施路径与不可越过的事实边界
5. 来源登记

## 结论先行

1. **事实：AgentCore 是 AWS 当前最完整的 Agent 底座，不是 CI/CD 编排器。** 它自 2025-10-13 起整体 GA；Runtime/Harness 负责运行与会话隔离，Gateway/Identity/Policy 负责工具行动面和授权，Observability/Evaluations/Optimization 负责 Agent 的运行质量闭环。它们可以承载或治理 CI/CD Agent，但没有公开证据表明会替代 CodePipeline、CodeBuild 或现有发布审批系统。[S1][S2]
2. **事实：最强的“Agent → CI/CD 行动”接点是 AgentCore Gateway，而非模型调用。** Gateway 能把 Lambda、OpenAPI、Smithy/AWS service API、API Gateway 和 MCP server 暴露成 MCP 工具；它可在每次工具调用前由 Policy 评估。故可以把 `StartPipelineExecution` 等 CodePipeline API 做成显式、最小权限的 tool；AWS 文档并未声称存在一个开箱即用、具备发布语义和审批逻辑的 CodePipeline MCP 工具。[S3][S4][S5]
3. **事实：AgentCore Evaluations 是截至观察日唯一明确写入“CI/CD regression testing”的 Agent 质量能力。** 它已 GA，支持在线生产采样、按需评估和 batch evaluation；Ground Truth 可检验期望答案、行为断言和期望工具调用轨迹，custom evaluator 可使用 Lambda。它评估的是 Agent 行为，不是替代单元测试、制品签名、部署验证或人工变更审批。[S6][S7]
4. **分析推断：可行的企业架构是“双控制面”。** CI/CD 控制面继续持有构建、制品、部署、审批与回滚的确定性权威；AgentCore 控制面以受限工具、策略、身份与追踪提供“建议/触发/诊断/评估”。只有将 Gateway 的最小工具面、Policy 强制模式、Identity 的委托边界和 CodePipeline/Step Functions 的确定性状态机同时接上，才可把 Agent 的行动约束为可审计变更，而不是把自然语言输出当作发布授权。

## 1. 范围、口径与排除项

### 1.1 纳入标准

只纳入满足至少一项的 AWS 能力：

- **运行**：承载 Agent loop、会话或隔离执行环境；
- **工具调用**：将 CI/CD 的 API、Lambda、MCP 服务或内部 HTTP 服务变为 Agent 可调用的受控行动面；
- **触发/编排**：可从确定性工作流触发 Agent，或可作为 Agent 的受控 pipeline 触发出口；
- **验证/治理**：能在 Agent 工具调用或 Agent 版本改动前后执行可审计的策略、评测或审批；
- **可观测性/评估**：能得到 trace、tool trajectory、质量度量或回归信号。

### 1.2 明确排除

| 项目 | 排除理由 |
|---|---|
| AWS DevOps Agent | 用户明确排除；本文件不复述其 release management 或生产运维能力。 |
| 单纯的 Amazon Bedrock 模型推理 / 模型目录 | 它提供模型能力，不等于 CI/CD Agent 的运行、编排、验证或发布控制。仅在“Responses API + Gateway server-side tool execution”这一明确工具调用路径中引用。 |
| AgentCore Browser、Code Interpreter、Web Search、Memory | 这些是 Agent 工具/上下文原语。官方资料没有把它们定义为 CI runner、pipeline trigger 或发布治理能力；可由具体 Agent 选用，但不作为本研究的 CI/CD 平台候选。 |
| AWS CodePipeline、CodeBuild、EventBridge、Step Functions 的普通用法 | 它们不是 Agent 平台。本文件只记录其与 AgentCore 形成明确接点的 API、服务集成或部署路径。 |

### 1.3 事实与推断标识

- **事实**：可由本文件的 AWS 一手来源逐条支持。
- **分析推断**：基于事实给出的架构含义；不是 AWS 已交付的端到端产品承诺。
- **证据缺口**：官方资料未证明的功能、自治等级或效果，不提升为结论。

## 2. 生命周期快照（截至 2026-08-03）

| 产品层级 / 能力 | 生命周期状态 | 状态事实与时间边界 |
|---|---|---|
| Amazon Bedrock AgentCore 平台 | **GA** | 2025-07-16 public preview；2025-10-13 AWS 宣布 AgentCore GA，最初覆盖九个区域。区域、功能和配额必须以部署区域的当前矩阵复核，不能从“平台 GA”推导某个新能力已在所有区域可用。[S1][S2][S17] |
| AgentCore Runtime | **GA（随平台）** | 平台 GA 后的托管运行底座；运行时功能仍按单项发布演进。2026-07 起官方发布了统一 trace/log 交付等增量能力，故实施时应区分基础 Runtime 与具体 telemetry 特性。[S2][S8] |
| AgentCore Gateway / Identity / Observability | **GA（随平台）** | 初始 preview 已包含三者；整体 GA 公告将其列为平台能力。Identity 的 OBO token exchange 是 2026-04-30 起单独 GA 的增量。[S1][S2][S9] |
| Policy | **GA** | 2025-12-02 preview；AWS 2026-03-03 更新为 GA。与 Bedrock Guardrails 的 policy 集成在 2026-06 发布；需按 Guardrails 区域表复核，不可假定等同于 Policy 全区域覆盖。[S10][S11] |
| Evaluations | **GA** | 2025-12 public preview，2026-03-31 GA；公告明确在线、按需两个主评测面并写明按需评测可用于 CI/CD regression testing。[S6][S12] |
| Optimization（batch eval、recommendations、A/B testing） | **部分 GA / 部分 Preview** | 2026-06-17，batch evaluations、recommendations、A/B tests GA（14 区域）；failure/intent/trajectory insights 为 Preview（13 区域）。不能把 insight 的预览状态写成生产 GA。[S13] |
| AgentCore Harness | **GA** | 2026-04-22 preview；2026-06-17 GA。它是托管 agent loop，不能把它误称为通用 CI/CD runner。[S14][S15] |
| AWS Agent Registry | **Preview** | 2026-04-09 preview，五区可用；含登记、审批、发现和 CloudTrail 审计。是 Agent 资产治理候选，不是流水线 gate 本身。[S16] |

## 3. 候选能力与 CI/CD 接点

### A. Agent 运行与交付

| 候选能力 | 产品层级 / 阶段 | 核心机制（事实） | CI/CD 接点（事实） | 事实边界 |
|---|---|---|---|---|
| **AgentCore Runtime** | Agent hosting；GA | 托管 serverless Runtime 承载 Agent 或 tool；每个 session 是独立 microVM，CPU、内存与文件系统隔离。支持最长 8 小时的长任务；会话结束后 microVM 终止且内存清理。容器化应用可部署到 Runtime。[S18][S19] | 官方给出两条 Agent 自身交付路径：容器镜像可接入既有 container build/deploy pipeline；direct code deployment 以 Python ZIP 减少 Docker/ECR/CodeBuild 依赖。官方 starter-toolkit 示例的 launch 会以 CodeBuild 构建 Docker 镜像、以 ECR 存储，并创建 Runtime。[S19][S20] | Runtime 隔离的是 **Agent session**，不验证该 Agent 生成的变更正确性；默认会话状态不是长期耐久存储；它不是 CodeBuild 替代品，也没有内建 Git PR、制品晋级或部署审批语义。 |
| **AgentCore Harness** | 托管 agent loop；GA | 以 model、system prompt、tools、skills 和 instructions 定义 Agent；托管编排、工具执行、context、失败恢复与每会话隔离，并可导出 Strands 代码。 [S15] | AWS Step Functions 有 `InvokeHarness` 原生服务集成：在状态机中串/并行调用 Harness，并可用状态机已有的 retry、choice、human approval 等确定性步骤包围 Agent 调用。[S21][S22] | Harness 运行 Agent loop，但不等于“Agent 可以自行批准发布”。Step Functions 的 invoke 是 Request Response；官方特别说明停止 state-machine execution/task 不会停止 Harness 继续运行，实施时须设计超时、幂等、补偿和审计。[S21] |
| **AgentCore CLI + CDK 交付路径** | 开发/部署工具；CLI GA | CLI 可 scaffold、local test、启用 observability、部署和 invoke Runtime；AWS 文档称 CLI 使用 CDK 部署资源。[S23] | 将 Agent 的 runtime、gateway、policy 与 telemetry 配置纳入代码仓/CI 检查是可行路径；官方 2026-04 公告称 CLI 面向 agent lifecycle，CDK 是当时支持的 resource manager。[S14][S23] | 这是 Agent 平台的 IaC/交付工具，不证明 CLI 自动生成正确的 pipeline gate、策略或评测基线；该三者仍应作为版本化工件和独立验证对象。 |

### B. 工具调用、身份与流水线触发

| 候选能力 | 产品层级 / 阶段 | 核心机制（事实） | CI/CD 接点（事实） | 事实边界 |
|---|---|---|---|---|
| **AgentCore Gateway** | Tool / agent traffic gateway；GA | 将 Lambda、OpenAPI、Smithy、API Gateway stage 和 MCP server 等 target 聚合为单一 MCP tool endpoint；能进行 tool discovery、入站/出站认证与凭证处理。API Gateway target 可用 allow-list 只暴露指定 path + method。[S3][S4][S24] | 可用 Smithy/AWS service API 或自建最小 Lambda/API facade 暴露 CodePipeline `StartPipelineExecution`。该 API 可带 `clientRequestToken`、source revision 与 variables，并返回 execution ID；因此可把“只允许启动指定 pipeline/指定 revision”的约束固化为工具 schema + IAM + policy，而非让 Agent 任意调用 AWS CLI。[S5][S25] | AWS 没有在已核验资料中发布“CodePipeline 原生 MCP connector”或“AgentCore 一键 pipeline trigger”。将 API 接为工具是**组合设计**，不是 AWS 预制 CI/CD Agent；工具 schema、IAM 资源范围、幂等键与审批仍由实施方定义。 |
| **Bedrock Responses API 的 Gateway server-side tool execution** | 模型—工具原生集成；GA（依赖双方区域） | 请求可指定 Gateway ARN；Bedrock 自动发现工具、将模型选择的调用在服务端执行并将结果注入同一 API 响应，支持一轮内多次 tool call。[S26] | 若 CI/CD Agent 使用 Responses API，可减少客户端维护 tool-loop 的代码；Gateway 仍是工具、IAM 和策略边界。[S26] | 这不是“模型批准或保证了发布”。模型选择工具的非确定性仍存在；服务端执行不取代 tool-side authorization、policy、输入验证、CodePipeline 状态判断或人工审批。 |
| **AgentCore Identity（含 OBO）** | Agent identity / credential delegation；GA；OBO GA | Runtime 可用 IAM SigV4 或 OAuth/JWT 入站认证；Identity 支持 OAuth credentials。OBO 令牌交换可将已认证用户 token 换为带用户与 Agent identity、面向特定下游资源的 scoped-down token，避免重复 consent。[S9][S27] | 对“由开发者/值班者发起、Agent 代为查询或触发 pipeline”的路径，可将人、Agent、目标工具三者的委托链显式化，并把目标资源授权缩小到工具所需范围。[S9] | OBO 不是自动的业务授权：部署审批、CAB、环境门禁与 CodePipeline action role 仍必须各自执行。若使用未经验证的 user-id header，AWS 文档明确它依赖调用 workload 正确传值，生产有 IdP 时应使用 JWT 路径。[S27] |
| **CodePipeline + EventBridge（适配层）** | CI/CD trigger primitive；GA（非 AgentCore） | CodePipeline `StartPipelineExecution` 启动指定 pipeline；EventBridge 可按 schedule 以具备 `codepipeline:StartPipelineExecution` 权限的角色启动 pipeline。CodePipeline 状态变更会发送到 EventBridge。[S5][S28][S29] | 路径一：Agent 经 Gateway 最小工具发起 StartPipelineExecution，pipeline execution ID 回写给 Agent/trace。路径二：EventBridge 监听已完成的 pipeline/阶段事件，触发一个只读诊断或评估 workflow，而不是让 Agent 反向无条件发布。[S5][S29] | 这是 AWS 服务组合，不是 AgentCore 的原生 CodePipeline binding。EventBridge 事件说明“状态发生”，不是发布变更正确或安全的 oracle。 |

### C. 验证、治理与资产控制

| 候选能力 | 产品层级 / 阶段 | 核心机制（事实） | CI/CD 接点（事实） | 事实边界 |
|---|---|---|---|---|
| **Policy in AgentCore** | Gateway policy engine；GA | 以 Cedar policy 控制 Gateway tool call；每次 invocation 评估，默认拒绝、`forbid` 优先。schema 从 Gateway tool definition 生成，并在部署前验证 policy 引用的 action/参数类型；支持 LOG_ONLY 与 ENFORCE。 [S10][S30] | 将 `start pipeline`、`retry stage`、制品查询、日志读取等工具拆成显式 action，按 principal、tool、resource 与 context 定义 permit/forbid；先 LOG_ONLY 收集命中，再以可回滚 change 将经过测试的规则提升为 ENFORCE。[S30][S31] | Policy 是 agent-to-tool 的授权边界，不验证代码、测试结果或部署健康。若 Runtime 可被直接访问，它能绕过 Gateway policy；AWS 建议以 runtime resource policy/authorizer 限制仅能由 Gateway 调用。[S32] |
| **Policy + Bedrock Guardrails** | 输入/输出安全控制；GA 集成，但按区域 | Gateway policy 可调用 Guardrails 检查 prompt attack、content 和 sensitive information；能在 action 前 forbid，或 action 后以 `suppressOutput` 隐藏输出。官方提供 LOG_ONLY → 真实流量校准阈值 → ACTIVE 的测试路径。[S11][S31] | 可在 Agent 调用高风险 CI/CD tool 前拦截 prompt injection/敏感凭证外泄，并在工具或 runtime 返回后抑制敏感输出；适合把“可被 Agent 阅读/执行的上下文”作为独立检查面。[S11] | 不能把 Guardrails 当代码安全扫描、SBOM/签名验证、IaC policy 或部署验证的替代；它返回的是安全类别置信信号，最终阈值、误报/漏报容忍度需要用黄金集和真实流量校准。[S11][S31] |
| **AWS Agent Registry** | Agent/tool/skill catalog；Preview | Registry 可登记 Agent、tools、skills、MCP servers 与 custom resources；支持 URL discovery、approval workflow、IAM/OAuth、semantic/keyword search 和 CloudTrail audit。[S16] | 可把已审查的 CI/CD MCP tool、Agent endpoint 与 skill 作为可发现资产，发布前走 registry approval；利于阻止团队无审计地复建高权限发布工具。[S16] | Preview，且它管理元数据和发现/审批流；不在运行时拦截 tool call，也不能取代 Gateway Policy、IAM 或 pipeline approval。 |

### D. Agent observability、evaluation 与改进闭环

| 候选能力 | 产品层级 / 阶段 | 核心机制（事实） | CI/CD 接点（事实） | 事实边界 |
|---|---|---|---|---|
| **AgentCore Observability + CloudWatch / ADOT** | 运行 telemetry；GA | CloudWatch GenAI observability 可看 AgentCore agent、memory、gateway、built-in tools 的 service telemetry；Agent 可用 ADOT/OpenTelemetry 追加 trace/span/metric。trace 包含 request processing、tool invocation 参数/耗时、错误与 response 等执行路径。2026-07 新建 Runtime 可将 trace、prompts、structured logs 与 stdout 汇到单一 per-agent log group；既有 agent 需配置与升级 ADOT。[S8][S33] | 将每个 CI/CD Agent action 关联到 runtime session、trace、tool call、pipeline execution ID/commit SHA；可从 pipeline failure 反查 Agent 选择的工具与参数，也可把 Agent trace 送入 Evaluations。[S8][S33] | telemetry 提供观察，不等于“正确性证明”。默认指标、vended logs/spans 和自定义 trace 的启用条件并不相同；敏感 prompt/input/output 进入日志时需要 IAM、KMS、保留期与脱敏设计。 |
| **AgentCore Evaluations** | 行为质量验证；GA | online evaluation 对生产 trace 采样评分；on-demand evaluation 可针对指定 traces/spans；batch evaluation 异步处理 CloudWatch Logs 中多个 sessions。内置 13 个 evaluator 覆盖 response quality、安全、任务完成和 tool use；Ground Truth 支持 expected response、assertions、expected tool trajectory；custom evaluator 可是 LLM-as-judge 或 Lambda code。[S6][S7][S12] | 把 golden CI/CD agent scenarios（例如“只读诊断”“需要人工批准的发布请求”“禁止跨环境晋级”）作为数据集；在 Agent prompt/tool/policy 变更的 CI 中跑 on-demand 或 batch evaluation，检查 expected trajectory 和 code-based invariant。AWS 公告明确 on-demand 适用于 regression testing in CI/CD pipelines。[S6][S7] | LLM-as-judge 分数不是确定性证明。最关键的发布规则应使用 expected tool trajectory、断言、代码 evaluator、Policy/IAM 和真实 pipeline gate 的组合；评测通过不能替代签名、测试、变更审批或生产 SLO。 |
| **AgentCore Optimization** | production-to-improvement loop；batch eval/recommendation/A-B GA，insights Preview | 可从 production traces/evaluation outputs 生成 prompt/tool description 的 recommendations；batch eval 在数据集上比较，A/B testing 用 traffic split 比较版本；failure/intent/trajectory insights 仍为 Preview。[S13] | 适合把 Agent 的 prompt/tool-description 改动变为“建议 → 离线 batch eval → 明确批准 → 受控 A/B → rollout”的交付链，而不是直接把 production observation 自动写回 Agent。[S13] | AWS 没有证明 recommendation 在 CI/CD Agent 上必然提升变更质量；A/B 的生产流量比较也不是部署风险 gate。Preview insights 不应成为正式发布依赖。 |

## 4. 候选路径：从低风险到可控行动

### 路径 P1：只读 CI/CD 诊断 Agent（推荐起点）

`EventBridge / 人工请求 → Step Functions → Runtime 或 Harness → Gateway（read-only tools）→ CodePipeline/CloudWatch/制品元数据 → Observability + Evaluations`

- **事实基础：**Step Functions 可调用 Harness；Gateway 可接 API/Lambda/MCP；CodePipeline 状态变更进入 EventBridge；Evaluations 可对 trace/tool trajectory 评分。[S21][S24][S29][S7]
- **分析推断：**先限制为读取 pipeline 状态、测试日志、制品元数据和运行遥测，建立黄金轨迹和失败样本。这个阶段不授予 `StartPipelineExecution`、环境晋级或写生产配置权限。

### 路径 P2：受控 pipeline 触发 Agent

`Agent → Gateway（named start-pipeline tool）→ Policy + Identity + IAM → facade/Lambda 或 Smithy API → CodePipeline StartPipelineExecution → execution ID → EventBridge/CloudWatch 回流`

- **事实基础：**Gateway 可转换/代理上述 target 类型；Policy 每次 tool call 评估；CodePipeline API 支持 client request token 且返回 execution ID。[S3][S5][S10]
- **分析推断：**工具只暴露预先批准的 pipeline、环境与变量；将 revision、变更单/审批标识、caller identity 写入 schema/context。先以 LOG_ONLY 演练 policy，再以强制 policy + IAM resource restriction 实施。Agent 只能提出/发起受允许的 execution，不能把“模型说可以”解释为发布批准。

### 路径 P3：Agent 自身的交付质量门

`Agent code/prompt/tool/policy change → CLI/CDK build/deploy → on-demand or batch Evaluations（黄金集 + trajectory + Lambda assertions）→ 审批 → Runtime/Harness version rollout → Observability → 可选 A/B`

- **事实基础：**CLI/CDK 可部署 Agent；Evaluations 的 on-demand、batch 与 custom Lambda evaluator 已 GA；Optimization 的 batch/A-B/recommendations 已 GA。[S23][S6][S13]
- **分析推断：**将“Agent 定义”作为待交付工件：代码、prompt、tool schema、Cedar policy、evaluation dataset 同版本评审。评价 pass 只是一道 Agent 质量门，仍需将常规软件测试、供应链验证、部署健康检查放在 CI/CD 自身控制面。

## 5. 关键限制与未证实项

1. **无直接证据：**截至观察日未检索到 AWS 官方发布的“AgentCore 原生 CodePipeline MCP connector”或端到端的“Agent 自动审批/自动发布”产品；上述 pipeline tool 是受控组合路径。
2. **无直接证据：**Runtime/Harness 的 microVM 隔离不构成对 Agent 所产出代码、命令或 deployment 的正确性/安全性验证。
3. **区域与阶段不可折叠：**平台、Policy、Evaluation、Harness 与 Optimization 的 GA/Preview 及区域不同；Guardrails-in-Policy 也有独立区域矩阵。任何落地设计须用目标区域的 service availability、quota 和 release notes 再核对。
4. **双重授权不可省略：**Gateway Policy、Gateway/IAM 身份、下游 CodePipeline/Lambda/API 的 IAM 以及 pipeline 内部审批是不同层。允许一个 Gateway tool 并不自动授予下游资源操作权，反之亦然。
5. **可观测不等于评测，评测不等于门禁：**trace 是输入，评测产出是质量证据；确定性的 release gate 仍必须由可测试规则、外部验证和明确批准承担。

## 6. 官方来源登记（发布日期与访问日）

所有来源均为 AWS 官方，访问日均为 **2026-08-03**；未标出版日期的 Developer Guide/API Reference 记为“文档未标明”。

| ID | 官方来源 | 发布日期 | 本文使用的事实 |
|---|---|---:|---|
| S1 | [Amazon Bedrock AgentCore now available in preview](https://aws.amazon.com/about-aws/whats-new/2025/07/amazon-bedrock-agentcore-preview/) | 2025-07-16 | Preview 初始服务、Runtime 8h、Gateway/Identity/Observability 定位。 |
| S2 | [Make agents a reality with Amazon Bedrock AgentCore: Now generally available](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-is-now-generally-available/) | 2025-10-13 | AgentCore 平台 GA。 |
| S3 | [AgentCore Gateway developer guide](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway.html) | 文档未标明 | Gateway target、MCP、认证、凭证和语义工具选择。 |
| S4 | [MCP targets](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-targets-mcp.html) | 文档未标明 | Lambda/API Gateway/OpenAPI/Smithy/MCP target 类型。 |
| S5 | [CodePipeline StartPipelineExecution API](https://docs.aws.amazon.com/codepipeline/latest/APIReference/API_StartPipelineExecution.html) | 文档未标明 | 触发 pipeline、revision/variables、client token 与 execution ID。 |
| S6 | [AgentCore Evaluations is now generally available](https://aws.amazon.com/about-aws/whats-new/2026/03/agentcore-evaluations-generally-available/) | 2026-03-31 | Evaluations GA、13 built-ins、online/on-demand、CI/CD regression、Ground Truth、Lambda custom evaluator。 |
| S7 | [Evaluation types](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/evaluations-types.html) | 文档未标明 | online/on-demand/batch 的执行语义与 batch 输入/输出。 |
| S8 | [Unified observability in a single log group](https://aws.amazon.com/about-aws/whats-new/2026/07/amazon-bedrock-agentcore-unified-observability-single-log-group/) | 2026-07-23 | trace/log 单组、适用新旧 Runtime 的条件。 |
| S9 | [AgentCore Identity OBO token exchange](https://aws.amazon.com/about-aws/whats-new/2026/04/amazon-bedrock-agentcore/) | 2026-04-30 | OBO GA、scoped-down token 与 14 区域。 |
| S10 | [AgentCore Policy launch and GA update](https://aws.amazon.com/blogs/aws/amazon-bedrock-agentcore-adds-quality-evaluations-and-policy-controls-for-deploying-trusted-ai-agents/) | 2025-12-02；更新 2026-03-03 | Policy preview 与 GA 时间。 |
| S11 | [Guardrails in policies](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-guardrails-in-policies.html) | 文档未标明 | Guardrails 类别、输入/输出检查、区域与阈值校准。 |
| S12 | [Build reliable AI agents with AgentCore Evaluations](https://aws.amazon.com/blogs/machine-learning/build-reliable-ai-agents-with-amazon-bedrock-agentcore-evaluations/) | 2026-03-31 | OTEL/OpenInference trace、评测生命周期、Ground Truth 细节。 |
| S13 | [AgentCore optimization capabilities](https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-new-optimization-capabilities/) | 2026-06-17 | insights Preview；batch/recommendations/A-B GA 与区域。 |
| S14 | [AgentCore new features to build agents faster](https://aws.amazon.com/about-aws/whats-new/2026/04/agentcore-new-features-to-build-agents-faster/) | 2026-04-22 | Harness preview、CLI、CDK 生命周期定位。 |
| S15 | [AgentCore harness is now generally available](https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-agentcore-harness-generally-available/) | 2026-06-17 | Harness GA、managed loop、tools/memory/identity/observability。 |
| S16 | [AWS Agent Registry preview](https://aws.amazon.com/about-aws/whats-new/2026/04/aws-agent-registry-in-agentcore-preview/) | 2026-04-09 | Registry preview、approval workflow、CloudTrail、区域。 |
| S17 | [AgentCore release notes](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/release-notes.html) | 文档按月更新 | 平台/功能发布历史与生命周期交叉核验。 |
| S18 | [Host agent or tools with AgentCore Runtime](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/agents-tools-runtime.html) | 文档未标明 | Runtime 的 serverless hosting、隔离、长任务与持久文件系统。 |
| S19 | [Runtime direct code deployment](https://aws.amazon.com/blogs/machine-learning/iterate-faster-with-amazon-bedrock-agentcore-runtime-direct-code-deployment/) | 2025-11-04 | container vs ZIP、既有 CI/CD pipeline 接点。 |
| S20 | [Move AI agents from POC to production](https://aws.amazon.com/blogs/machine-learning/move-your-ai-agents-from-proof-of-concept-to-production-with-amazon-bedrock-agentcore/) | 2025-09-19 | Starter Toolkit 经 CodeBuild/ECR/Runtime 的示例部署链。 |
| S21 | [Invoke AgentCore harness with Step Functions](https://docs.aws.amazon.com/step-functions/latest/dg/connect-bedrockagentcore.html) | 文档未标明 | `InvokeHarness` 集成、响应模式与停止限制。 |
| S22 | [Step Functions adds AgentCore integration](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-step-functions-sdk-integrations/) | 2026-03-26 | 早期 SDK integration 可调用 Runtime/并行 Agent/provisioning；后来 Harness 使用优化集成。 |
| S23 | [Get started with the AgentCore CLI](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-get-started-cli.html) | 文档未标明 | CLI 的 scaffold/test/observability/deploy/invoke 与 CDK 前提。 |
| S24 | [API Gateway stages as Gateway targets](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/gateway-target-api-gateway.html) | 文档未标明 | API 到 MCP 翻译、allow-list 与限制。 |
| S25 | [AgentCore Gateway supports API Gateway](https://aws.amazon.com/blogs/machine-learning/streamline-ai-agent-tool-interactions-connect-api-gateway-to-agentcore-gateway-with-mcp/) | 2025-12-08 | API Gateway target 的公告和内建 security/observability 表述。 |
| S26 | [Bedrock server-side tool execution with AgentCore Gateway](https://aws.amazon.com/about-aws/whats-new/2026/02/amazon-bedrock-server-side-tool-execution-agentcore-gateway/) | 2026-02-24 | Responses API + Gateway ARN、服务端执行与区域条件。 |
| S27 | [Runtime inbound and outbound authorization](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-oauth.html) | 文档未标明 | IAM/JWT、OAuth、user-id header 限制与 Gateway 单一入口。 |
| S28 | [Start a pipeline on a schedule](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-trigger-source-schedule.html) | 文档未标明 | EventBridge target 与 `codepipeline:StartPipelineExecution`。 |
| S29 | [Monitoring CodePipeline events](https://docs.aws.amazon.com/codepipeline/latest/userguide/detect-state-changes-cloudwatch-events.html) | 文档未标明 | CodePipeline → EventBridge 状态事件。 |
| S30 | [Policy core concepts](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-core-concepts.html) | 文档未标明 | Cedar、default-deny、forbid-wins、schema/validation/analysis。 |
| S31 | [Test a policy in LOG_ONLY mode](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/policy-test-a-policy.html) | 文档未标明 | shadow test、阈值校准与 ACTIVE 推进。 |
| S32 | [Runtime security best practices](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/runtime-security-best-practices.html) | 文档未标明 | Gateway bypass 风险及限制 Runtime 直接调用。 |
| S33 | [Understand observability telemetry](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/observability-telemetry.html) | 文档未标明 | session/trace/span 定义、CloudWatch 与 ADOT instrumentation。 |

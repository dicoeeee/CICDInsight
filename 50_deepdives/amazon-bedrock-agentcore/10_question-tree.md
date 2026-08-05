---
title: Amazon Bedrock AgentCore Question Tree
tags:
  - research/agentic-cicd
  - research/question-tree
  - platform/aws
status: complete
as_of: 2026-08-03
confidence: high
---

# Amazon Bedrock AgentCore Question Tree

## Q0：AgentCore 到底改变了什么？

**主问题：**AgentCore 是否把 Agent 从“模型加循环的应用代码”变成一种拥有独立运行、行动治理与质量回归生命周期的生产工作负载？

### H0 假设

AgentCore 的差异化价值不在提供又一种推理框架，而在把 Agent 生产化所需的运行、工具、身份、策略、观测、评估和版本控制拆成可组合服务。该平台能把行动与证据标准化，但不能替代业务系统、CI/CD 或人工审批对结果正确性的最终裁决。

### H0 验收

- 至少用 Runtime / Harness、Gateway / Identity / Policy、Observability / Evaluations / Optimization 三组机制证明；
- 明确一个能推翻 H0 的反例：若平台只有托管运行而没有独立行动治理与质量回归链，则 H0 不成立；
- 不用市场定位语句代替接口、安全和生命周期证据。

## Q1：产品层级与迁移方向

### Q1.1 AgentCore 是什么，不是什么？

- 是否独立于特定模型、框架和协议？
- 它是 Agent framework、PaaS、Runtime，还是控制面能力族？
- Amazon Bedrock 在其中是必需模型层还是可选模型提供者？

### Q1.2 与相邻产品的边界是什么？

- Harness 与 Runtime 的责任如何分开？
- AgentCore 与 Strands Agents、LangGraph、OpenAI Agents SDK 等 framework 的关系是什么？
- Amazon Bedrock Agents Classic 的新客户策略是否明确把 AgentCore 指向未来路径？
- AWS DevOps Agent “built on AgentCore”能证明什么，不能证明什么？

### 验收

- 产品关系必须由 AWS 直接陈述或接口合同支持；
- “支持 / 集成 / 构建于 / 替代”四类关系分开；
- 不绘制未公开的内部部署拓扑。

## Q2：运行与编排责任

### Q2.1 Runtime 提供什么生产合同？

- 会话隔离、生命周期、异步任务、流式传输、容器 / direct code deployment；
- 版本、Endpoint、资源策略、网络与执行角色；
- 默认和持久文件系统的责任差异。

### Q2.2 Harness 抽象掉了什么？

- model、instruction、tool、skill、memory、limits 如何成为配置；
- Harness 是否与 Runtime 共享同一安全边界；
- 导出到 Strands code 后，责任如何从配置式服务回到团队代码。

### Q2.3 运行隔离不能证明什么？

- Agent 生成的命令、代码和变更是否正确；
- session ID 是否正确映射到真实用户；
- 执行角色权限是否最小；
- 下游系统是否允许或应当执行该动作。

## Q3：行动治理

### Q3.1 工具面如何形成？

- Gateway 能接入哪些 target 与协议？
- tool schema、语义发现、模型路由是否改变最小工具面的设计？
- Agent 是否存在绕过 Gateway 直达 Runtime 或下游系统的路径？

### Q3.2 身份链如何表达？

- caller、user、agent workload、gateway 与 downstream resource 分别是谁？
- IAM SigV4、JWT、OAuth credential 与 OBO token exchange 分别解决什么问题？
- `UserId` 路径的验证责任在哪里？

### Q3.3 Policy 能做什么？

- Cedar schema 是否从 tool definition 生成；
- default-deny、forbid-wins、LOG_ONLY / ENFORCE 如何工作；
- Policy、Guardrails、IAM 与业务审批是否为四种不同控制。

### 验收

- 高风险动作必须呈现 `Gateway Policy + Identity/IAM + downstream authorization + business gate` 四层；
- 任一层缺失时，不宣称端到端授权成立。

## Q4：记忆与状态

- Short-term memory 与 long-term memory 的语义、保留和检索边界是什么？
- actor / session 作用域与应用侧 user mapping 如何配合？
- Memory 是上下文资产、业务记录，还是事实系统？
- 删除、保留、跨 Agent 共享和派生记忆会引入什么治理风险？

### 验收

- 不把摘要、偏好和从会话中提取的记录写成经过独立验证的组织事实；
- 业务状态、审批状态和系统记录仍以外部 source of truth 为准。

## Q5：可观测、评估与改进闭环

### Q5.1 Observability 提供什么证据？

- trace、span、tool invocation、prompt / response 与服务指标覆盖到何处；
- 默认 telemetry 与需要 ADOT / OpenTelemetry 接入的数据如何区分；
- 敏感输入输出进入 CloudWatch 后的访问、保留和成本责任是什么。

### Q5.2 Evaluations 能证明什么？

- online、on-demand、batch 各自服务于监控、定向调查还是回归；
- ground truth、assertion、expected tool trajectory 和 custom evaluator 的确定性强弱；
- LLM-as-judge 分数为什么不能替代硬性安全与业务 Gate。
- Evaluations GA、Batch GA 与 Dataset Evaluation Public Preview 如何分开；
- predefined scenario 与 simulated scenario 如何交换覆盖广度和 Oracle 强度；
- batch average、最近 500 session 选择和 evaluator failure 如何影响 release evidence；
- 当前 built-in evaluator 数量与版本口径为何需要按账户 / Region 实测。

### Q5.3 Optimization 如何闭环？

- insights、recommendations、batch evaluation、A/B test 的生命周期状态；
- 推荐是否需要批准，版本如何发布和回滚；
- 生产流量实验能否替代预发布风险控制。
- 同一 evaluator / dataset 同时驱动 recommendation 和验收时，如何避免代理指标或测试集过拟合。

### Q5 验收

- 分别给出 Online、On-demand、Batch 的 trigger、source、Ground Truth 和 output；
- 把 expected response / assertion 的 LLM judge 与 programmatic trajectory / Lambda code evaluator 分层；
- 形成版本化 Agent Scenario Contract，并标出 AgentCore 原生字段与企业外部 Oracle；
- 不用平均分、A/B significance 或 evaluation pass 代替关键场景 must-pass、真实副作用验证和 release approval。

## Q6：交付与运行模型

- Agent code、prompt、model pin、tool schema、Cedar policy、memory strategy、evaluation set 应否共同版本化？
- CLI / CDK 生成的开发权限能否直接进入生产？
- Harness / Runtime version 与 endpoint 能否支撑 canary、rollback 和审计？
- Step Functions 在 Agent 前后持有 retry、choice、human approval 时，确定性控制面如何分工？

### 验收

- 形成最小 Agent Release Unit；
- 明确“评测通过”与“允许发布”不是同一事件；
- 给出从只读 Agent 到受控写动作的分阶段门槛。

## Q7：企业价值、经济性与锁定

### Q7.1 价值假设

- 是否减少每个团队重复建设 session isolation、tool gateway、OAuth、trace 与 evaluation plumbing；
- 是否让安全、平台和业务团队在 Agent code 外共同治理行动；
- 是否能将生产失败样本回流到可重复的回归集。

### Q7.2 经济性

- Runtime、Gateway、Policy、Memory、Evaluations、CloudWatch、模型推理与网络如何分别计费；
- 活跃资源计费在 I/O 等待型 Agent 中的收益是否需要用真实 workload profile 验证；
- 配额、区域差异和日志量是否会成为规模上限或隐性成本。

### Q7.3 可移植性

- 模型 / framework / protocol 可替换，是否仍依赖 AgentCore API、IAM、CloudWatch、Cedar schema 与版本语义；
- Harness export to code 能减少哪类锁定，不能迁移哪些托管控制面数据与行为；
- Registry、Memory、evaluation history 和 policy assets 的退出路径是否完整公开。

## Q8：CI/CD 场景的控制边界

- AgentCore 能否承载 CI/CD 诊断 Agent、受控 pipeline trigger 和 Agent 自身的发布质量门？
- Gateway tool 是否等于 CodePipeline / GitHub / GitLab 原生发布语义？
- Agent Evaluations 是否能替代单元测试、制品签名、部署验证或 Required Check？
- 如何让 pipeline execution ID、commit SHA、agent version、policy version 和 trace ID 形成可审计链？

### 验收

- 保留 CI/CD 控制面对构建、制品、部署、审批和回滚的权威；
- AgentCore 只持有明确授予的建议、读取、触发或诊断能力；
- 不把自然语言输出、工具可调用或模型自评当作发布授权。

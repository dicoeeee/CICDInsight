---
title: AWS DevOps Agent 问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/aws
status: complete
as_of: 2026-08-03
confidence: high
---

# AWS DevOps Agent 问题树

## Q0：AWS DevOps Agent 是否形成了新的 DevOps 控制面？

验收标准：能用一手证据说明其共同上下文、任务循环、执行/授权边界和产品状态；不能只依赖“Agent”“自动化”或厂商收益描述。

### Q1：产品到底由哪些生命周期不同的能力组成？

- Q1.1 Production operations 的 Preview、GA 时间点与 GA 范围是什么？
- Q1.2 Release Management 的发布日、区域与 Preview 限制是什么？
- Q1.3 Custom agents、MCP/A2A、learned skills 属于什么扩展面？
- Q1.4 “AWS DevOps Agent 已 GA”在什么语境下成立，何时会误导？

### Q2：Agent Space 管理的真实对象是什么？

- Q2.1 它如何定义账号、资源、第三方连接、权限和数据落点？
- Q2.2 Topology 的资源与关系分别来自 CloudFormation、标签、Resource Explorer、代码/部署和遥测中的哪些证据？
- Q2.3 四类 learned skills 如何生成、刷新、版本化和被任务加载？
- Q2.4 环境模型的新鲜度、覆盖率和错误如何被人检查？

### Q3：生产事件调查如何运行？

- Q3.1 工单、Webhook 和人工触发分别如何进入调查？
- Q3.2 Triage 如何关联、跳过或启动事件，错误决定能否被人工纠正？
- Q3.3 调查读取哪些指标、日志、Trace、代码和部署历史？
- Q3.4 结果是根因、mitigation plan、Support Case，还是实际生产变更？
- Q3.5 Journal、CloudTrail 和用户反馈能证明什么，不能证明什么？

### Q4：Release Management 如何进入软件交付链？

- Q4.1 Release readiness 如何检查标准、跨仓依赖与访问控制？
- Q4.2 Managed verification environment 如何 clone/build/run/test，如何接入 VPC 与 runtime role？
- Q4.3 Release testing 如何按变更生成计划并对已部署 Web/API 目标执行？
- Q4.4 GitHub Check Run、required status check、GitLab approval rule 的权力分别来自哪里？
- Q4.5 测试写请求、目标数据、外部通知和支付等副作用由谁负责？

### Q5：安全与数据边界在哪里？

- Q5.1 Agent Space、primary/secondary IAM role 与 web app role 如何分层？
- Q5.2 数据存储区域与 Bedrock inference 路由是否一致？
- Q5.3 Native tools、custom MCP tools、EventBridge 外接自动化的写权限是否相同？
- Q5.4 Prompt injection、PII、第三方连接、共享注册和客户责任如何约束采用？
- Q5.5 AWS 是否使用客户 Agent 数据训练模型或改进产品？

### Q6：它与既有方案的差异是什么？

- Q6.1 相对 AIOps 事件关联，是否多了代码、Pipeline、部署和 learned skills 的共同上下文？
- Q6.2 相对 Runbook automation，是否负责执行确定性恢复动作？
- Q6.3 相对 CI/CD orchestrator，是否负责 Job Graph、环境晋级、审批和部署执行？
- Q6.4 相对代码审查/测试工具，它如何利用运行拓扑和跨仓关系？
- Q6.5 哪些差异是机制事实，哪些仍只是 AWS 设计意图？

### Q7：企业何时值得试点？

- Q7.1 账号、Tag、CloudFormation、Resource Explorer、Repository、Pipeline 与 Telemetry 覆盖是否达到最低门槛？
- Q7.2 如何把观察、建议、验证、Gate 与生产写动作分阶段授权？
- Q7.3 如何评测根因正确率、steering、误报、验证缺陷发现率、成本和时延？
- Q7.4 哪些失败条件应停止扩面或回退到现有流程？

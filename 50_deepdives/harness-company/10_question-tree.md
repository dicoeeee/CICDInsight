---
title: Harness 公司深度洞察问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/harness
status: complete
as_of: 2026-07-16
---

# Harness 公司深度洞察问题树

## Q1. Harness 到底在卖什么？

- 是新的 CI/CD Agent，还是完整的软件交付控制面？
- “AI for everything after code”对应哪些已发布模块？
- 公司扩张、并购和开放接口如何支撑这一战略？

## Q2. 用户从哪里使用 AI？

- Harness UI 中的 DevOps Agent 能创建和修改什么？
- IDE、MCP、CLI、Skills 提供什么外部入口？
- Pipeline 中的 Worker Agent 与 UI Agent 有何不同？
- 专项 Agent 是独立产品、Marketplace 模板，还是 Worker Agent 的实例？

## Q3. Agent 如何理解环境？

- Knowledge Graph 建模哪些实体、关系和权限？
- HQL、RAG、原始 API 与 MCP 各负责什么？
- 如何减少 Tool Schema、原始 API Payload 和上下文成本？
- 数据新鲜度、跨模块 Join 和外部工具覆盖有何限制？

## Q4. Agent 如何执行动作？

- Pipeline、Agent Step、Containerized Step Group 和 Delegate 如何配合？
- 模型如何配置，DevOps Agent 与 Worker Agent 是否都支持 BYOM？
- Agent 如何调用 Harness 资源和第三方 MCP 工具？
- 顺序、并行、条件、矩阵和循环如何形成复杂工作流？

## Q5. 企业为何能够或不能够信任它？

- 容器/VM、文件系统、进程、凭据和网络如何隔离？
- Agent 以谁的身份运行，Token 生命周期多长？
- RBAC、OPA、Agent Grant 和 MCP Tool Allowlist 如何组合？
- AI Rules 与强制 Policy 有什么区别？
- Tool Call、Prompt、模型、成本和结果如何审计？

## Q6. 现在可以做哪些 CI/CD 任务？

- PR 评审、测试生成、Coverage 和 CI AutoFix；
- Pipeline/IaC/Policy 创建与故障分析；
- Kubernetes Manifest、IaC Drift、依赖和零日漏洞修复；
- Test Automation、发布就绪、Feature Flag 和 Incident Response；
- 哪些任务能到 L3，哪些仍应停在建议或 PR？

## Q7. 有哪些可信实践证据？

- 客户是否已经使用 Worker Agents，还是仅宣布合作？
- AI Test Automation 是否有可量化案例？
- 机制自测、内部 Benchmark、客户结果和独立评测分别有多少？
- 能否看到跨语言、跨失败类型、长期回归和总成本？

## Q8. 产品状态是否一致？

- 总览 GA 与 Support/销售开通/Feature Flag 是否冲突？
- 文档、博客和 GitHub 仓库的 MCP 资源数为何不同？
- 模型版本、数据保留和身份描述是否一致？
- SaaS、Self-Hosted Worker Runtime 与 SMP 产品支持如何区分？

## Q9. 企业应如何试点和采购？

- 应先采用 UI Agent、Worker Agent 还是外部 MCP？
- 哪些控制项必须在 PoC 之前验证？
- 如何建立场景评测、红队、成本和升级门槛？
- 哪些证据缺口会阻止生产授权？

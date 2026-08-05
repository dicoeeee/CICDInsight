---
title: Qovery LLM/CI/CD 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/qovery
status: complete
as_of: 2026-08-03
---

# Qovery LLM/CI/CD 专题问题树

## 提纲

1. 产品层次与时间线
2. 交付作业流变化
3. 上下文与工具面
4. 自治、授权与验证
5. 成熟度与效果证据
6. 企业采用判断

## Q1：Qovery 的 LLM 能力究竟是哪几层

- Copilot、Console、Slack、MCP Server、Skills、RDE 和 Agentic Workflow 是一个产品还是多个相邻入口？
- 哪些由 Qovery 托管模型执行，哪些依赖用户选择的外部 Coding Agent？
- 2025 Alpha、Closed Beta、Self-service Beta、MCP live、Skills release 与 Agentic Platform 的时间关系是什么？
- 当前页面、Changelog 和 GitHub 所表达的成熟度是否一致？

## Q2：LLM 在 CI/CD 里新增了什么作业

- 能否从代码库识别语言、端口、数据库、变量和部署依赖？
- 能否生成 Dockerfile、Terraform、Environment 配置和部署阶段？
- 能否创建/克隆 Preview Environment、切换 PR branch、设置回收并返回 URL？
- 能否理解 Build/Deployment timeline、日志、指标和 Kubernetes events？
- 输出是说明、报告、候选补丁、真实配置变更、Deployment 还是验证证据？
- Scheduled / recurring task 是否把自然语言意图变成持续后台作业？

## Q3：Copilot 故障诊断是否形成闭环

- 它读取 Deployment log、Application log、Deployment history、Config change、Metric、Kubernetes event 和 Node state 中的哪些部分？
- “识别根因”是模型判断还是确定性 Error pattern / Platform state 与模型的组合？
- “自动修复”具体覆盖 Qovery config、Skill 生成文件、用户代码、Service restart 还是生产资源？
- 修复后是否重新部署、检查 Running status、Health、Log 和 Endpoint？
- 谁定义成功，LLM 是否可能用自己的解释替代测试与业务 Oracle？

## Q4：MCP 与 Skills 的分工是什么

- MCP 是否仅管理既有基础设施，Skills 是否负责从代码库建立首次部署？
- MCP 默认只读如何实现，读写需要哪些独立开关和 Token/RBAC 条件？
- 2026-07 Cluster state tool 相比 Tail log 增加了什么？
- Skills 是稳定 API、可执行程序，还是可变的提示/作业说明？
- Skills 中的 usage tracking、自动修复、重试和文件修改规则是否符合企业授权要求？
- Skill 通过 CLI/API/Terraform 产生的动作如何回到统一 Audit？

## Q5：传统 Qovery 原语与 LLM 新能力如何分开

- Auto-deploy、Deployment Pipeline、Environment、Preview、Rollback、Build Dashboard、Logs/Metrics 原本已经提供什么？
- LLM 新增的是能力本身，还是发现、规划、参数生成和多步编排？
- KRR、Health check、Build timing 等确定性机制应如何与 LLM 推理分层？
- Argo CD、Terraform/OpenTofu、GitHub/GitLab CI 仍持有什么所有权？

## Q6：授权边界是否足够清楚

- JWT 用户身份与 dedicated Copilot Role 的 blast radius 有何差异？
- `read_write=true`、组织写开关、Token 权限和逐动作确认是否缺一不可？
- Console、Slack 和 MCP 对写能力的当前文档是否一致？
- Secret path 绑定能否避免 Secret value 进入 Agent context？
- GET/只读操作是否仍可能返回敏感配置、日志 PII 或 Database credential？
- Audit 记录的是 API action、Skill usage、Agent reasoning 还是全部？

## Q7：Ticket → Sandbox → PR 是否已经成熟

- RDE 是 Remote Development Environment、AI Builder Portal 还是 Agent Runtime？
- Sandbox 是否包含应用、Database、Service、Seed data、Network allowlist、Scoped secret、Cost control 和 Audit？
- Linear/Jira Trigger、Coding Agent、E2E Test、Preview URL 和 PR 是否已有正式文档/公开客户证据？
- “Closed access / coming next”与产品页中的完成式表达如何处理？

## Q8：效果和案例能证明什么

- 是否有独立客户材料证明 Copilot 的 Root cause accuracy、Fix success、MTTR、Deployment frequency 或 Change failure rate 改善？
- Qovery 的内部 dogfooding 或产品 Demo 能证明哪些机制，不能证明哪些普遍结果？
- 是否有可复核的误报、回滚、失败重试、Token 成本、延迟和人工介入数据？
- 未检索到证据能否只记录为 Gap，而不写成“没有客户效果”？

## Q9：企业应该如何试点

- 是否先从只读故障诊断和 Deployment explain 开始？
- 何时允许非生产配置候选变更，哪些动作必须逐次批准？
- Skill 包、MCP 工具列表、API Token、模型版本和环境蓝图如何版本化？
- 哪些确定性 Gate 必须保留在 Agent 外：Test、Policy、Signature、SLO、Approval、Rollback？
- PoC 应测哪些质量、速度、成本、权限和可恢复性指标？

## Q10：Presentation-ready 的证明标准是什么

- 是否能用一页解释 Copilot、MCP、Skills 与 Control Plane 的分层关系？
- 是否明确产品状态和文档冲突，不把 Roadmap 讲成 GA？
- 是否把 LLM planning、Tool execution、Platform state、External Gate 分成四层？
- 是否避免“全自动 CI/CD”“零泄漏”“自动修复一切”“不再需要 DevOps”等不可证主张？
- 是否保留无独立客户效果数据这一限制？

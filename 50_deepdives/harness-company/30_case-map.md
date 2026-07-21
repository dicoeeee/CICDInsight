---
title: Harness 公司应用与案例地图
tags:
  - research/agentic-cicd
  - research/case-map
  - company/harness
status: complete
as_of: 2026-07-16
---

# Harness 公司应用与案例地图

## 产品化应用模式

| 模式 | 输入 | Agent 行为 | 确定性出口 | 自治上限判断 | 证据 |
|---|---|---|---|---|---|
| Pipeline 生成/修改 | 自然语言、现有模板、Connector/环境名 | DevOps Agent 创建或修改 Step、Stage、Pipeline | YAML Review、Save、OPA、执行门禁 | L1—L2 | [DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) |
| Policy 生成 | 合规意图、现有 Pipeline | 生成并集成 Rego | OPA 编译/评估、人工审核 | L1—L2 | [DevOps Agent](https://developer.harness.io/3k-docs/ai/devops-agent/) |
| Pipeline 故障分析 | Log、执行对象、上下文 | Root Cause、Fix 建议、YAML Auto-repair | 重跑 Pipeline、原 Gate | L1—L2 | [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| PR Review | PR Diff、历史代码 | 分析 Agent 生成 Review Prompt，Coding Agent 评审 | PR Comment、人工采纳 | L1—L2 | [Code Quality](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/) |
| Coverage 增强 | 代码库、覆盖报告 | 生成测试、运行测试、生成报告 | Branch/PR、Coverage Oracle | L2—L3 | [Code Quality](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/) |
| CI AutoFix | 失败执行、日志、代码 | 诊断、修改、限次迭代、重触发 Build | PR Branch、原 CI | SH2—SH3 | [Code Quality](https://developer.harness.io/3k-docs/platform/getting-started/agents/code-quality/)、[Worker Launch](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| Manifest Remediation | 部署错误、Manifest、K8s 状态 | 修改 Manifest | 非生产部署验证、PR/审批 | L2—L3 | [Worker Agents](https://developer.harness.io/docs/platform/harness-ai/harness-agents/) |
| IaCM Remediation | Drift、安全/成本发现、IaC | 编辑基础设施配置 | Plan、Policy、Approval、Apply | L2—L3 | [Worker Launch](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| Feature Flag Cleanup | Flag 状态、代码引用、实验信息 | 判断陈旧 Flag 并生成清理变更 | PR、FME Policy | L2 | [Worker Launch](https://www.harness.io/blog/introducing-autonomous-worker-agents) |
| 安全修复 | SAST/SCA/SBOM/Runtime Finding | 解释、排序、修复建议或 Patch | Scanner 复验、PR、Policy | L2—L3 | [Harness AI Overview](https://developer.harness.io/docs/platform/harness-ai/overview/) |
| E2E Intent Testing | 自然语言意图、DOM、截图、App Knowledgebase | 探索、生成动作/断言、自愈 Locator | 真实浏览器、API/视觉断言、Suite Result | L3（测试沙箱） | [Intent Testing](https://developer.harness.io/docs/ai-test-automation/get-started/intent-driven/) |
| 事故记录与 RCA | Slack/Teams/Zoom、Alert、Change Event | Scribe、时间线、Change Correlation、RCA Theory | 人类确认、Incident Timeline | L1—L2 | [AI Scribe](https://developer.harness.io/docs/ai-sre/ai-agent/)、[RCA Agent](https://developer.harness.io/3k-docs/ai-sre/ai-agent/rca-change-agent/) |
| Incident Runbook | Incident Type、Alert、人工触发 | 选择或编排预定义动作 | Harness/Jenkins/GitHub Pipeline、人工步骤 | 批准后 L3；局部 SH4 | [Runbooks](https://developer.harness.io/docs/ai-sre/runbooks/) |
| IDE 内交付操作 | 开发者问题、当前代码、Harness URL | MCP 查询/诊断/创建/执行 | MCP Elicitation、RBAC、Audit | L1—L3 | [MCP Server](https://developer.harness.io/docs/platform/harness-aida/harness-mcp-server/) |

## 署名企业案例

| 案例 | 已公开事实 | 可以证明 | 不能证明 | 证据等级 |
|---|---|---|---|---|
| Verint Worker Agent | 官方首发引语称 4 天构建 Kubernetes/Pipeline Troubleshooting Agent，计划覆盖约 200 名运维人员和约 1,000 名开发者 | 创建体验和组织推广意向 | 大规模上线后的成功率、节省时间和风险 | first-party adoption signal |
| United Airlines RiskSentinel | 官方首发引语称 4 天从想法到“production-ready”安全修复 Agent | 客户能在平台上构建受治理专项 Agent | 独立安全验证、真实生产授权范围和持续效果 | first-party launch testimonial |
| Workday | 2026-03 宣布将 Harness 用于交付、测试、验证和安全 | 大型企业选型和采用计划 | Worker Agent 已上线或带来量化收益 | public adoption commitment |
| Gameopedia AI Test Automation | 厂商案例称维护工作下降 40% 以上、每名测试人员每天节省 2—3 小时、新员工上手从约 30 天降至 2—5 天 | 真实团队从 Selenium 迁移的方向性收益 | 独立对照、长期缺陷逃逸率和 CI 集成效果；案例当时尚计划后续接 CI | named vendor case |
| Wasimil AI Test Automation | 厂商案例称失败率由约 50% 降到不足 10%，维护由约 2 小时/日降到 45—60 分钟/日，发布由每周两次到每天 | 动态 Web 产品中自愈和 No-code 可能改善维护 | 数据采集方法、样本期、缺陷严重度和其他流程变化影响 | named vendor case |
| Siemens Healthineers PoC | 厂商案例称单场景创建由多日降至不足 1 小时，调试维护由小时降至分钟 | 复杂 Web Workflow 的 PoC 可行性和上手速度 | 规模化、受监管验证、生产 KPI 已达成 | named PoC case |

## 厂商测试与产品自证

| 证据 | 结果 | 正确使用方式 | 主要限制 |
|---|---|---|---|
| 50-Stage Pipeline | DevOps Agent 官方称已验证更长复杂 Pipeline | 作为容量上限方向性证据 | 未披露模板复杂度、任务集和成功标准 |
| Worker 隔离回放 | Harness 称重放真实 CVSS-9.0 漏洞后，从 709 个含 Secret 环境变量降至 33 个且无可用凭据 | 证明四层隔离有自动测试和针对性红队 | 第一方测试，攻击面覆盖和独立复验未知 |
| AI Test 22 Task Benchmark | 2024 厂商测试称 Harness 86%、Browser-use 64%、Operator 45%、Computer Use 41% | 说明专项测试 Agent 可能优于通用 Browser Agent | 样本小、任务和实现由 Harness 选择、时间已早于 2026 产品 |
| Knowledge Graph Token 估算 | Harness 架构文称复杂跨模块问题从约 25—35 万 Token 降至约 1.2 万 | 说明 Schema/关系建模和预聚合的设计价值 | 不是独立 Benchmark，假设和工作负载有限 |

## 推荐复现案例

### Case A：只读 Pipeline Investigator

1. 选择 50—100 个已知根因的历史失败；
2. 仅开放 Execution、Log、Repo Diff 和 Test Result 读取；
3. 比较 DevOps Agent、Worker Agent 和人工 On-call 的分类准确率；
4. 记录错误归因、证据引用、耗时和 Token/Runner 成本；
5. 未达标时不进入修复权限试点。

### Case B：CI AutoFix Draft PR

1. 只纳入依赖锁、格式、简单编译和确定性测试失败；
2. Agent 只能写临时 Branch，不得修改 CI Gate；
3. 原 CI 全量验证，失败最多循环 2—3 次；
4. 输出 Draft PR 和证据包，由代码 Owner 合并；
5. 分 Failure Class 统计 Verified Fix Rate 和回归率。

### Case C：非生产 Manifest Remediation

1. 构造缺少字段、错误 Image Tag、资源限制和健康检查问题；
2. 只允许访问单 Namespace 和非生产 Cluster；
3. 先生成 Patch 和 Dry-run，再由审批触发部署；
4. 以 Kubernetes Admission、Smoke Test 和 Rollback 证明结果；
5. 红队 Prompt Injection、错误 MCP Tool 和越权请求。

### Case D：AI Test Selector Drift

1. 固定业务断言，制造 DOM ID、位置、文案和布局变化；
2. 比较 Fast、Resilient/Smart Selector 和 Playwright 路径；
3. 区分正确自愈、错误元素命中、真回归和不可判定；
4. 连续运行多版本，测维护时间与缺陷逃逸；
5. 关键交易仍保留独立 API/数据断言。

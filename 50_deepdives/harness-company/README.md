---
title: Harness 公司 Agentic Software Delivery 专题
aliases:
  - Harness Company Deep Dive
  - Harness AI 专题
tags:
  - research/agentic-cicd
  - research/deep-dive
  - company/harness
status: complete
as_of: 2026-07-22
topic_id: harness-company
topic_type: company
companies:
  - Harness
stages:
  - cross-stage
confidence: medium-high
presentation_ready: true
refresh_after: 2026-09-16
---

# Harness 公司 Agentic Software Delivery 专题

> [!abstract] 当前判断
> Harness 公司正在把 Software Delivery Platform 从“固定 Pipeline 自动化”升级为“受治理的 Agent 执行与控制面”。DevOps Agent 负责设计/操作 Harness 资源，Worker Agents 作为 Pipeline Step 多轮行动，Knowledge Graph/HQL 提供结构化上下文，MCP/CLI/Skills 提供外部接口，专项 Test/Security/SRE Agent 提供领域闭环。架构已经相当完整，但通用 Worker Agent 刚于 2026-06 GA，当前最可信的生产边界仍是 L2 可审查变更和批准后 L3，而不是关键发布的普遍 L4。2026-07-22 的 CI 专项复核进一步确认：Code Quality 旧入口与 Managed Worker 是不同实现，且两份当前权限文档在触发 Principal 与资源范围上存在冲突，不能把 scoped-token 架构当作任意账户、任意 Trigger 的既成事实。

## 关键结论

1. Harness 公司和“Agent Harness 工程”是不同研究对象；本专题只分析 Harness Inc. 产品和实践。
2. Harness 不是单一 Agent，而是 UI Agent、Pipeline Worker Agent、专项 Agent、Knowledge Graph、MCP/CLI/Skills 和治理底座的组合。
3. Knowledge Graph 负责理解，MCP 负责动作和长尾扩展，Pipeline 负责编排，外部 Oracle 负责证明。
4. Worker Runtime 的四层隔离、Scoped Token 和 Tool Intersection 具有行业参考价值；但委托 Principal 的精确语义、默认只读权限和 Stage/Step Group Token 传播仍需按账户与 Trigger 独立验证。
5. 总览“GA”不能替代账户可获得性；AI Test、Scribe、Hosted MCP、细粒度 Permission 等仍有销售/Support/Flag 条件。
6. 委托权限也有触发方式边界：当前事件 Trigger Run 不能继承某个触发人的 scoped token，必须与手动/API Run 分开评估。
7. 公开量化案例主要来自 AI Test Automation；Worker Agent 大型企业案例目前更多是采用信号，缺少跨客户效果基准。
8. 最佳落地路径是只读/建议 → Draft PR/非生产验证 → 批准后受限动作，不直接开放 Merge、Promote 和生产管理员权限。
9. Code Review、Coverage 和 AutoFix 至少存在 PR Agent/Execute API 与 Managed Worker 两类入口；职责可以串联，Runtime、凭据和权限结论不能互相继承。

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/harness-company/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/harness-company/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/harness-company/20_evidence-map|证据矩阵]] |
| Case Map | 完成 | [[50_deepdives/harness-company/30_case-map|应用与案例]] |
| Labs | 已设计、未执行 | [[50_deepdives/harness-company/40_labs/README|实验计划]] |
| Findings | 完成 | [[50_deepdives/harness-company/50_findings|分析发现]] |
| Playbook | 完成 | [[50_deepdives/harness-company/60_playbook|企业 Playbook]] |
| Report | 完成 | [[50_deepdives/harness-company/90_report|完整报告]] |
| Research Evidence | 完成 | [[50_deepdives/harness-company/research-evidence|一手证据底稿]] |
| Harness CI Validation | 完成 | [[50_deepdives/harness-company/ci-validation-2026-07-22|2026-07-22 CI 检验与补充]] |

## 上下游关系

- L0：[[00_sources/briefs/2026-harness-ai-platform|Harness AI Platform]]、[[00_sources/briefs/2026-harness-worker-agents|Worker Agents]]、[[00_sources/briefs/2026-harness-worker-agent-security|Worker Security]]、[[00_sources/briefs/2026-harness-code-quality-agents|Code Quality]]、[[00_sources/briefs/2026-harness-ai-test-automation|AI Test Automation]]、[[00_sources/briefs/2026-harness-ai-sre|AI SRE]]。
- Company：[[20_summaries/companies/README#4. Harness：从 Pipeline 平台到 Agent 控制面|Harness 公司分析]]。
- Stage：[[30_summaries/stages/README|CI/CD 八阶段总结]]。
- Tool：[[10_summaries/tools/README|Agent 工具与技术栈]]。
- Crosscutting：[[40_summaries/crosscutting/README|横向变化总结]]。

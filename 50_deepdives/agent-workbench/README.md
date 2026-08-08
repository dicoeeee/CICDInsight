---
title: Agent 工作台、专家团与交付角色重构专题
aliases:
  - Agent Workbench Deep Dive
  - 通用 Agent Harness 与专家团
tags:
  - research/agentic-cicd
  - research/deep-dive
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
topic_id: agent-workbench
topic_type: technology
stages:
  - cross-cutting
tools:
  - Tencent WorkBuddy
  - ChatGPT Work
  - Codex
  - ChatGPT Workspace Agents
  - Harness Inc. Worker Agents
  - GitHub Agentic Workflows
companies:
  - Tencent
  - OpenAI
  - Harness Inc.
  - GitHub
confidence: medium-high
presentation_ready: true
refresh_after: 2026-09-08
---

# Agent 工作台、专家团与交付角色重构专题

> [!abstract] 当前判断
> **Agent 工作台把交付能力变成开发者可自助消费的服务；发布、运维和平台人员的价值重心，则从逐单执行逐步上移到设计和运营通用 Agent Harness。**
>
> 产品事实已经能够证明“目标与产物工作台”“多专家/Subagent 协作”“Skill、上下文与权限供给”“外部 CI/CD 门禁”四类机制同时存在；但“岗位已经普遍完成迁移”仍缺少跨企业采用证据。因此，本专题将前半句标为跨案例分析推断，将后半句落实为企业 operating-model 建议，不写成劳动力市场事实。

## 关键结论

1. **使用层前台化。** WorkBuddy 和 ChatGPT Work 让用户提交目标、补充上下文、观察过程并审查产物；Codex 将该模式带到代码库和工程任务。
2. **协作层专业化。** WorkBuddy 的团长负责拆解与整合，ChatGPT Work/Codex 可调用专业 Subagent；“专家团”是任务编排机制，不是新的授权主体。
3. **供给层平台化。** Skill、专家定义、共享上下文、工具目录、权限、预算、评测和审计需要由发布、运维、SRE、平台和安全人员共同经营。
4. **接受层仍确定性。** Agent 生成计划、分析和候选动作；Test、Scan、Policy、Signature、Approval、SLO 与 Rollback 继续由 CI/CD 控制面和授权人员执行或判定。
5. **角色是重构，不是消失。** 可证明的是工作内容有上移条件；尚不能证明岗位数量、组织普及率或职责迁移幅度。

## 三层五角色

| 层 | 核心职责 | 主要角色 |
|---|---|---|
| 开发者工作台 | 提交目标与约束、连接项目上下文、观察任务、审查候选结果 | 开发者 / 服务 Owner |
| 通用 Agent Harness | 注册 Skill、专家与工具，拆解、调度、交接、评测、授权、记录和成本控制 | Harness 设计者、主 Agent、专业 Agent |
| 确定性 CI/CD 控制面 | 执行测试、扫描、策略、签名、审批、SLO 判定、发布与回滚 | 外部 Oracle / 批准者 |

五类角色不是五个固定岗位。一个平台团队可以同时承担 Harness 设计者和部分 Oracle Owner，但主 Agent、专业 Agent 不能因此获得默认生产授权。

## 主张分类

| 分类 | 本专题结论 | 置信度 |
|---|---|---|
| 产品直接事实 | 工作台、专家团/Subagent、Skill/项目上下文、Builder/Admin 权限和 CI/CD 外部门禁均已有一手产品机制 | high |
| 跨案例分析推断 | 开发者工作台、通用 Agent Harness、确定性 CI/CD 控制面正在形成三层分工 | medium-high |
| 企业 operating-model 建议 | 交付支持人员应更多经营 Skill、专家、评测、权限和升级路径，让开发者自助消费能力 | medium |
| 尚待验证的岗位趋势 | 上述迁移是否已跨企业普遍发生、影响多少岗位或工时 | unverified |

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/agent-workbench/00_charter|研究边界]] |
| Question Tree | 完成 | [[50_deepdives/agent-workbench/10_question-tree|问题树]] |
| Evidence Map | 完成 | [[50_deepdives/agent-workbench/20_evidence-map|证据矩阵]] |
| Case Map | 完成 | [[50_deepdives/agent-workbench/30_case-map|案例地图]] |
| Findings | 完成 | [[50_deepdives/agent-workbench/50_findings|分析发现]] |
| Playbook | 完成 | [[50_deepdives/agent-workbench/60_playbook|企业 operating-model Playbook]] |
| Fact Audit | 通过 | [[50_deepdives/agent-workbench/70_fact-audit|逐主张事实审计]] |
| Report | 完成 | [[50_deepdives/agent-workbench/90_report|专题报告]] |
| Research Notes | 完成 | [[00_sources/research-agent-workbench-expert-team-2026-08-08|一手证据研究]] |

## Presentation-ready 判断

- **当前值：** `true`
- **候选页面主张：** 交付能力正在通过 Agent 工作台前台化给开发者，发布与运维能力则在后台沉淀为 Skill、专家团与治理规则。
- **页面证据：** WorkBuddy 证明显式专家团，OpenAI 产品线证明工作台、项目上下文、Subagent 与 Builder/Admin 分离，Harness Inc. 与 GitHub 证明 Agent 候选动作仍受流水线和外部门禁约束。
- **必须随页保留：** 这是基于产品机制的组织演进推断和 operating-model 建议，不是已经普遍发生的岗位事实。
- **禁止表述：** “运维岗位消失”“开发者绕过生产审批”“多 Agent 自动提高质量”“会创建 Skill 即具备平台工程能力”。

## 上下游关系

- Source Brief：[[00_sources/briefs/2026-tencent-workbuddy-agent-workbench|Tencent WorkBuddy Agent 工作台]]
- Source Brief：[[00_sources/briefs/2026-openai-chatgpt-work-codex-workspace-agents|OpenAI 工作台与 Workspace Agents]]
- CI/CD 桥接：[[00_sources/briefs/2026-harness-worker-agents|Harness Inc. Worker Agents]]、[[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows]]
- 组织与平台研究：[[00_sources/briefs/2026-dora-platform-engineering-ai|DORA Platform Engineering and AI]]
- 相邻专题：[[50_deepdives/harness-company/README|Harness Inc. 公司专题]]、[[50_deepdives/github-agentic-workflows/README|GitHub Agentic Workflows]]、[[50_deepdives/cli-agent-interface/README|CLI 与 Agent-ready Interface]]

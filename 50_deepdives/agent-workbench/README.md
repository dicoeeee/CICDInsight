---
title: Agent 工作台产品功能与控制边界专题
aliases:
  - Agent Workbench Deep Dive
  - Agent 工作台产品功能
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
  - Claude Cowork
  - GitLab Duo Agent Platform
  - Harness Inc. Worker Agents
  - GitHub Agentic Workflows
companies:
  - Tencent
  - OpenAI
  - Anthropic
  - GitLab
  - Harness Inc.
  - GitHub
confidence: high
presentation_ready: true
refresh_after: 2026-09-08
---

# Agent 工作台产品功能与控制边界专题

> [!abstract] 文档用途
> 本专题逐项记录 Agent 工作台、企业 Agent 构建治理平台和 CI/CD 原生 Agent 工作流已经公开的产品功能。正文只陈述可回链的一手产品事实、生命周期和限制，不输出产品事实之外的企业方案或采购排名。

## 研究对象

### 六个核心产品面

| 产品面 | 本专题详写范围 | 截至 2026-08-08 的状态口径 |
|---|---|---|
| Tencent WorkBuddy | 任务、项目、专家/专家团、Skill、连接器、产物、自动化、企业管理 | WorkBuddy 4.5.0 已于 2026-03-04 正式发布；各细分能力页面未单独标 GA/Preview/Beta |
| ChatGPT Work / Codex / Workspace Agents | Work 本地与云端任务、Projects、Subagents、Skills/Plugins、权限与管理员能力 | Work 已公开发布；Workspace Agents 官方 Cookbook 标为 Research Preview；各产品面分开记录 |
| Claude Cowork | 本地/远程任务、Projects、Memory、Plugins、Subagents、定时任务、权限和监控 | 远程执行为 Beta；Computer Use 为 Research Preview；桌面 Projects 与远程能力分别记录 |
| GitLab Duo Agent Platform | Agents、Flows、Skills、CI/CD 执行、Runner、身份、沙箱、工具治理 | Agent Platform 自 GitLab 18.8 GA；具体 Flow 与治理功能仍按各自 GA/Beta/Experiment 状态记录 |
| Harness Inc. Worker Agents | Agent Definition、Pipeline Step、Inputs、Triggers、Outputs、身份和隔离 | 官方产品文档可用；页面未为全部子能力提供统一生命周期标签 |
| GitHub Agentic Workflows | Markdown 定义、编译、Engine、Tools、Safe Outputs、权限与外部仓库规则 | Public Preview |

状态来源见 [[50_deepdives/agent-workbench/20_evidence-map|功能证据矩阵]]。表中的“正式发布”或“GA”不自动覆盖未标状态的子能力。

### 五个补充对照

Microsoft Copilot Studio、Google Gemini Enterprise Agent Platform、Atlassian Rovo、Amazon Bedrock AgentCore 与 Octopus Claude Agent Step 只进入 [[50_deepdives/agent-workbench/30_case-map|产品功能矩阵]]。它们分别补充 Agent 构建与治理、运行时与评测、业务工作面，以及部署 Step 等功能事实；本专题不为其创建新的单产品 Deep Dive。

## 统一记录字段

每个产品按同一信息接口记录：

`产品/能力状态 → 使用入口 → 配置对象 → 输入与上下文 → 任务流程与状态 → Agent 协作 → Skill/工具/连接器 → 触发与自动化 → 产物与交接 → 身份/权限 → 管理与审计 → 明确限制`

该字段集只用于查漏和对照，不表示不同产品共享同一实现模型。

## 产品边界

- WorkBuddy 与 CodeBuddy 是不同产品；本专题只研究 WorkBuddy。
- ChatGPT Work、ChatGPT Projects、Codex 和 Workspace Agents 分节记录，不合并为一个“OpenAI 工作台”。
- Claude Cowork 与 Claude Code 分开；只有 Cowork 官方文档直接陈述的功能进入 Cowork 章节。
- “通用 Agent Harness”只作为通用概念；涉及 Harness 公司产品时始终写作 “Harness Inc. Worker Agents”。
- 工作台生成的文件、变更、Issue、评论或 PR 是产品产物，不自动等于测试通过、Required Check、合并或生产发布批准。

## 专题导航

| 交付物 | 状态 | 入口 |
|---|---|---|
| Charter | 完成 | [[50_deepdives/agent-workbench/00_charter|事实研究边界]] |
| Question Tree | 完成 | [[50_deepdives/agent-workbench/10_question-tree|功能问题树]] |
| Evidence Map | 完成 | [[50_deepdives/agent-workbench/20_evidence-map|功能证据矩阵]] |
| Case Map | 完成 | [[50_deepdives/agent-workbench/30_case-map|十一产品功能矩阵]] |
| WorkBuddy | 完成 | [[50_deepdives/agent-workbench/40_workbuddy|WorkBuddy 功能详章]] |
| OpenAI 产品边界 | 完成 | [[50_deepdives/agent-workbench/41_openai-product-boundaries|ChatGPT Work、Codex 与 Workspace Agents]] |
| Claude Cowork | 完成 | [[50_deepdives/agent-workbench/42_claude-cowork|Claude Cowork 功能详章]] |
| GitLab Duo | 完成 | [[50_deepdives/agent-workbench/43_gitlab-duo-agent-platform|GitLab Duo Agent Platform 功能详章]] |
| Harness Inc. | 完成 | [[50_deepdives/agent-workbench/44_harness-worker-agents|Worker Agents 功能详章]] |
| GitHub | 完成 | [[50_deepdives/agent-workbench/45_github-agentic-workflows|Agentic Workflows 功能详章]] |
| Findings | 完成 | [[50_deepdives/agent-workbench/50_findings|功能事实摘要]] |
| Fact Audit | 通过 | [[50_deepdives/agent-workbench/70_fact-audit|功能主张审计]] |
| Report | 完成 | [[50_deepdives/agent-workbench/90_report|产品功能事实报告]] |
| 非事实附录 | 隔离 | [[50_deepdives/agent-workbench/95_appendix-operating-model-hypotheses|Operating-model 假设附录]] |
| Research Notes | 完成 | [[00_sources/research-agent-workbench-expert-team-2026-08-08|一手证据日志]] |

## Presentation-ready 门禁

- **当前值：** `true`。功能证据矩阵、URL、状态、产品边界和候选页面主张已完成逐项审计；该值只表示可支撑事实型功能矩阵，不表示产品成熟度或页面已经制作。
- **允许的候选页类型：** 具名产品的入口、任务/协作、扩展、产物、权限和 CI/CD 接入功能矩阵。
- **不允许的候选页类型：** 脱离产品事实的企业方案或不同产品的总体优劣排名。

## 证据缺口

- WorkBuddy 未公开专家团的子任务依赖、失败补偿、隔离模型或成功率基准。
- ChatGPT Work、Codex、Projects 与 Workspace Agents 的能力和可用账户并不完全相同，不能跨产品补全功能。
- Claude Cowork 的部分远程、Computer Use 与审计能力仍处于 Beta/Research Preview 或存在明确缺口。
- GitLab Platform GA 不表示所有 Agent、Flow 和治理功能均 GA。
- Harness Inc. 与 GitHub 的 Agent 输出可以进入流水线或仓库工作流，但仍需独立验证和仓库/环境规则决定后续接受。

## 上下游关系

- Source Brief 索引：[[00_sources/README|一手资料与 Source Brief]]
- 相邻专题：[[50_deepdives/harness-company/README|Harness Inc.]]、[[50_deepdives/github-agentic-workflows/README|GitHub Agentic Workflows]]、[[50_deepdives/amazon-bedrock-agentcore/README|Amazon Bedrock AgentCore]]
- 汇报候选：[[80_presentations/agentic-cicd-company-workflows/10_slide-outline|Agentic CI/CD 页面目录]]

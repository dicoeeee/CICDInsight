---
title: Agent 工作台、专家团与交付角色重构 Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: medium-high
---

# Agent 工作台、专家团与交付角色重构 Claim—Evidence—Gap Matrix

## 证据标记

- **事实：** 一手产品文档、Release Notes、Changelog 或原始研究直接陈述；
- **分析：** 主 Agent 跨两个以上独立案例形成的机制判断；
- **建议：** 面向企业 operating model 的设计选择；
- **未验证：** 当前证据不足，不能进入事实性主张。

## 核心 Claim

| ID | 分类 | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计 |
|---|---|---|---|---|---|---|
| AWB-C01 | 产品直接事实 | WorkBuddy 专家团由团长自动拆解、分配、并行执行并整合交付 | [WorkBuddy 专家中心](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Expert-Center)；[[00_sources/briefs/2026-tencent-workbuddy-agent-workbench]] | 厂商产品机制，不证明质量优于单 Agent 或 CI/CD 采用 | high | passed |
| AWB-C02 | 产品直接事实 | WorkBuddy 将 Skill、专家、专家团分别定义为工具能力、单一角色和多专家协作流程；Enterprise 管理员可管理专家版本、发布状态、可见范围、启停和成员下发策略 | [专家中心](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Expert-Center)、[企业专家管理](https://cloud.tencent.com/document/product/1831/134421) | 分类是厂商产品语义，不是行业标准；专家外部服务配置仍需单独治理 | high | passed |
| AWB-C03 | 产品直接事实 | WorkBuddy 专家不主动获得系统权限，Skill/MCP 外部访问仍需用户授权 | 同上；[Permission Modes](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Permission-Modes) | Full Access 会改变逐步确认边界，不能写成始终人工审批 | high | passed |
| AWB-C04 | 产品直接事实 | ChatGPT Work 提供长任务工作面，用户可观察进度、补充信息、改方向并批准重要动作 | [ChatGPT Release Notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)；[[00_sources/briefs/2026-openai-chatgpt-work-codex-workspace-agents]] | 分批开放且受计划/工作区影响；“重要动作”不等于统一生产审批策略 | high | passed |
| AWB-C05 | 产品直接事实 | ChatGPT Work 与 Codex 可运行专业 Subagent 并行工作；本地 Codex 可定义不同模型和指令的自定义 Agent | [OpenAI Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) | ChatGPT Work 仅合资格账户；多 Agent 增加 Token 与写冲突风险 | high | passed |
| AWB-C06 | 产品直接事实 | ChatGPT Project 共享文件、项目指令和连接来源；Codex 使用目录/Workspace 作为代码上下文 | [OpenAI Projects](https://learn.chatgpt.com/docs/projects) | 共享项目上下文不等于跨项目全局记忆或无授权访问 | high | passed |
| AWB-C07 | 产品直接事实 | Workspace Agents 允许 Builder 配置 Apps、Skills、指令和计划任务，Admin 用 RBAC 控制启用、构建、分享和发布 | [Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep) | Research Preview；不得回填成 ChatGPT Work 全面 GA 治理能力 | high | passed |
| AWB-C08 | 产品直接事实 | Harness Inc. 可把 Agent 封装为流水线步骤，组合指令、模型、MCP、上下文和权限 | [[00_sources/briefs/2026-harness-worker-agents]]、[[00_sources/briefs/2026-harness-worker-agent-security]] | 权限文档存在 Principal/Token 口径冲突，目标账户需验证 | high for mechanism | passed |
| AWB-C09 | 产品直接事实 | GitHub Agentic Workflows 默认只读并使用 Safe Outputs、沙箱和受控 Token；Issue Approval 不替代 Ruleset/Required Check | [[00_sources/briefs/2026-github-agentic-workflows]] | Public Preview；不能证明大规模生产效果 | high | passed |
| AWB-C10 | 产品直接事实 | DORA 认为高质量内部平台可把 AI 的局部加速转化为组织结果，并提供自动化、标准化和安全路径 | [[00_sources/briefs/2026-dora-platform-engineering-ai]] | 研究面向整体 AI 辅助开发，相关关系不等于 CI/CD Agent 因果 | medium-high | passed |
| AWB-C11 | 跨案例分析推断 | 开发者工作台、通用 Agent Harness、确定性 CI/CD 控制面正在形成三层分工 | AWB-C01—C10 | 产品边界不统一，尚无行业标准架构 | medium-high | passed-as-analysis |
| AWB-C12 | 跨案例分析推断 | “更多给开发使用”可分为当前开发者自助入口与目标 operating model 两种状态 | WorkBuddy/ChatGPT Work/Codex 证明入口；Workspace Agents/Harness Inc./GitHub 证明供给与控制 | 当前入口存在不代表企业已经按此重组组织 | medium-high | passed-as-analysis |
| AWB-C13 | 企业 operating-model 建议 | 发布、运维、SRE、平台人员应更多经营 Skill、专家、上下文、评测、权限、预算和升级路径 | AWB-C07—C10；DORA 平台研究 | 适用程度受团队规模、平台质量、监管和任务频率影响 | medium | passed-as-recommendation |
| AWB-C14 | 企业 operating-model 建议 | Agent 只负责计划、分析和候选动作；最终生产接受仍由外部 Oracle/批准者负责 | AWB-C03、C08、C09 | 低风险动作可在预授权策略内自动执行，但授权仍来自外部系统 | high for boundary | passed-as-recommendation |
| AWB-C15 | 企业 operating-model 建议 | Skill/专家必须具备版本、测试、权限、成本、Owner、回滚和生命周期治理 | WorkBuddy 成本/权限事实、OpenAI Admin/Preview、Harness Inc./GitHub 治理机制 | 当前没有统一行业规范或成熟度基准 | medium-high | passed-as-recommendation |
| AWB-C16 | 尚待验证的岗位趋势 | 发布/运维岗位已经普遍从逐单执行迁移为 Harness 设计 | 未找到足够跨企业岗位或工时数据 | 产品机制只能证明迁移条件和方向，不能证明普及率 | unverified | blocked-as-fact |
| AWB-C17 | 尚待验证的岗位趋势 | 多专家一定比单 Agent 更可靠、更快或更便宜 | 无独立横向基准；WorkBuddy 反而提示更高积分消耗，OpenAI 提示更多 Token 和写冲突 | 必须按任务集、成功率、P95 时延和单位成功成本评测 | unverified | rejected |

## 关键证据链

### 链 A：开发者自助入口与供给控制相分离

1. WorkBuddy：用户召唤专家团并提交自然语言任务；专家/Skill/权限是独立配置资产。
2. OpenAI：ChatGPT Work/Codex 面向任务使用者；Workspace Agents 的 Builder/Admin 管理构建、分享、发布、Apps 和 RBAC。
3. 结论：至少两个独立产品族证明“使用入口”和“能力供给/治理”可以分层。

### 链 B：Agent 候选动作与 CI/CD 接受相分离

1. Harness Inc.：Agent 是 Pipeline 中受上下文、权限和运行环境约束的步骤。
2. GitHub：Agentic Workflow 使用 Safe Outputs 和 Actions；最终合并继续依赖 Ruleset/Required Check 等宿主控制。
3. 结论：Agent 可以规划和生成候选动作，不能自行成为生产授权来源。

## 未关闭的证据缺口

- 跨企业采用率、岗位职责和工时迁移的纵向数据；
- 多专家与单 Agent 的独立成功率、成本和时延基准；
- 工作台到生产发布的完整身份、证据、审批和回滚数据；
- Skill/专家版本兼容、供应链安全、废弃和事故责任的行业标准；
- 中国与全球企业在权限、数据驻留和监管下的可比部署样本。

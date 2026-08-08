---
title: Agent 工作台、专家团与交付角色重构一手证据研究
tags:
  - research/agentic-cicd
  - research/source
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: medium-high
---

# Agent 工作台、专家团与交付角色重构一手证据研究

## 研究提纲

1. 工作台是否已从聊天框演进为目标、上下文、任务状态和产物的统一界面；
2. 是否已有显式的主 Agent/专家团拆解、并行、交接和整合机制；
3. 最终使用者与 Agent/Skill/权限设计者是否形成不同产品角色；
4. 如何桥接 CI/CD，而不把 Agent 工作面误写成发布控制面；
5. 哪些是产品事实、跨案例推断、企业建议和未验证岗位趋势。

## 范围与方法

- 观察时间：截至 2026-08-08；访问时间：2026-08-08。
- 核心对象：Tencent WorkBuddy、ChatGPT Work、Codex、ChatGPT Workspace Agents。
- CI/CD 桥接：Harness Inc. Worker Agents、GitHub Agentic Workflows、DORA Platform Engineering and AI。
- 证据优先级：官方文档、官方 Release Notes/Changelog、官方研究；不使用模型记忆或厂商之外的二手概述作为事实证据。
- 产品消歧：“通用 Agent Harness”指承载上下文、Agent、Skill、工具、权限、评测和审计的运行与治理层；Harness Inc. 始终写公司名。

## 一手证据摘要

| 对象 | 直接证明的机制 | 产品状态 / 时间点 | 不能证明 |
|---|---|---|---|
| Tencent WorkBuddy | 专家为人设、方法论和工具链；专家团由团长拆解、分配、并行执行并整合；Skill/MCP 需用户授权；企业管理员可管理专家版本、发布状态、可见范围、启停和下发策略 | 2026-03-04 正式发布；截至 2026-07-30 为 5.3.8；专家团单项阶段未标 | 不证明内建 CI/CD Required Checks、发布签名、生产审批或跨企业岗位变化 |
| ChatGPT Work | 长任务工作面；跨连接 App/文件；过程可观察、可改方向、可批准重要动作；合资格账户支持 Subagent workflow | 2026-07-09 正式发布并分批开放 | 不证明所有账户可用，不证明每次重要动作都有同一强制审批策略 |
| Codex | 代码库/Workspace 上下文；专业 Subagent 并行；本地自定义 Agent；可检查子线程与摘要 | 截至 2026-08-08 的当前文档能力 | 不证明 Agent 输出自动满足仓库合并或生产发布标准 |
| Workspace Agents | 可配置 Apps、Skills、指令、计划任务；Workspace Admin 以 RBAC 控制启用、构建、分享和发布 | Research Preview；Business、Enterprise、Edu 合资格工作区 | 不应写成 ChatGPT Work 本体已 GA 的组织 Agent 发布平台 |
| Harness Inc. Worker Agents | Agent 被封装为流水线步骤，组合指令、模型、MCP、上下文和权限 | 平台总览列为 GA；部分细粒度路径受账户/Feature Flag 影响 | 不证明 Prompt 本身构成授权；权限文档仍有需要目标账户验证的冲突 |
| GitHub Agentic Workflows | 自然语言工作流编译为 Actions；默认只读、Safe Outputs、沙箱与受控 Token；外部 Ruleset/Required Check 继续构成接受边界 | Public Preview | Issue Approval 不是服务器端安全边界，不能替代仓库策略 |
| DORA | 高质量内部平台能够把 AI 带来的局部加速转化为组织结果；平台提供自动化、标准化和安全路径 | 2026-01-12 官方研究 | 研究讨论整体 AI 辅助开发，不能单独归因于多 Agent CI/CD |

## 产品边界审计

### WorkBuddy 与 CodeBuddy

本研究使用 WorkBuddy 的专家中心、Teams 与权限文档证明通用工作台和专家团。Tencent CodeBuddy 的 IDE、GitLab/Jenkins 等能力属于另一产品面，除非逐项注明，不回填为 WorkBuddy 的 CI/CD 原生能力。

### ChatGPT Work、Codex 与 Workspace Agents

- ChatGPT Work：面向复杂交付物的最终用户工作面；
- Codex：面向软件开发、代码库和工程任务的 Agent 工作面；
- Workspace Agents：面向组织复用的 Agent 构建、分享、发布和 RBAC 治理面，当前为 Research Preview。

三者共享部分项目、Skill、Plugin 或 Subagent 机制，但不合并成熟度、开放范围和授权边界。

### 通用 Agent Harness 与 Harness Inc.

“通用 Agent Harness”是架构概念；Harness Inc. Worker Agents 是其中一个 CI/CD 原生桥接案例。前者不能因为名称相同而继承后者的产品事实。

## 形成的四级判断

### 产品直接事实

1. 已存在面向最终用户的长任务/项目工作面，也存在显式的多专家或 Subagent 并行协作机制。
2. 已存在由 Builder/Admin 配置 Agent、Skill、App、项目上下文和权限的供给面。
3. CI/CD 原生产品仍把 Agent 放在流水线、Safe Output、短期身份和外部 Required Check/Policy 之内。

### 跨案例分析推断

**开发者工作台、通用 Agent Harness、确定性 CI/CD 控制面正在分化为三层。** 前台负责表达目标和审查候选结果；通用 Agent Harness 负责把专业知识与协作流程产品化；控制面负责判断候选动作是否被接受。

### 企业 operating-model 建议

发布、运维、SRE 与平台工程人员不应继续为每个开发团队手工执行重复步骤，而应共同经营 Skill、专家定义、上下文产品、评测集、权限、预算、升级路径和外部门禁，让开发者以自助方式消费交付能力。

### 尚待验证的岗位趋势

没有足够跨企业数据证明上述职责迁移已经普遍发生，也没有证据支持“发布或运维岗位会消失”“开发者将绕过生产责任分离”或“会创建 Skill 就等于具备可靠的平台工程能力”。

## 证据缺口

- 跨企业的 Agent 工作台采用率、任务成功率、单位成功成本和持续使用率；
- 发布/运维人员在 Skill、评测与 Agent 治理上的实际时间占比变化；
- 多专家相对于单 Agent 的独立质量、时延和成本对照实验；
- 工作台候选动作穿过 Test、Scan、Policy、Signature、Approval、SLO、Rollback 的端到端生产数据；
- Skill/专家定义的版本、依赖、回归测试、撤回、Owner 和生命周期治理行业基准。

## 主要来源

- [WorkBuddy 专家中心](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Expert-Center)
- [WorkBuddy Enterprise 专家管理](https://cloud.tencent.com/document/product/1831/134421)
- [WorkBuddy Enterprise 技能](https://cloud.tencent.com/document/product/1831/134432)
- [WorkBuddy Changelog](https://www.workbuddy.cn/docs/workbuddy/Changelog)
- [OpenAI ChatGPT Work Release Notes](https://help.openai.com/en/articles/6825453-chatgpt-release-notes)
- [OpenAI Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- [OpenAI Projects and chats](https://learn.chatgpt.com/docs/projects)
- [OpenAI Workspace Agents Cookbook](https://learn.chatgpt.com/cookbook/articles/chatgpt-agents-sales-meeting-prep)
- [DORA Platform Engineering](https://dora.dev/capabilities/platform-engineering/)
- [[00_sources/briefs/2026-harness-worker-agents|Harness Inc. Worker Agents]]
- [[00_sources/briefs/2026-harness-worker-agent-security|Harness Inc. Worker Agent Security]]
- [[00_sources/briefs/2026-github-agentic-workflows|GitHub Agentic Workflows]]
- [[00_sources/briefs/2026-dora-platform-engineering-ai|DORA Platform Engineering and AI]]

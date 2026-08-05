---
title: AWS DevOps Agent 如何使用 Memory 及其与 AgentCore Memory 的公开关系
tags:
  - research/agentic-cicd
  - evidence/source
  - platform/aws
  - technology/agent-memory
status: complete
as_of: 2026-08-04
source_scope: aws-primary-only
confidence: high-for-devops-agent-memory-medium-for-agentcore-implementation-link
---

# AWS DevOps Agent 如何使用 Memory 及其与 AgentCore Memory 的公开关系

## 研究问题

1. AWS DevOps Agent 是否真实提供 Memory 功能？
2. 它如何从历史调查生成并在新调查中使用 Memory？
3. 用户能够直接使用和管理哪些功能？
4. 这些公开机制能否等同于 AgentCore Memory 的 Event、Strategy、Record、namespace 与 Harness？

访问日：2026-08-04。全部事实优先采用 AWS User Guide、What's New、API 文档和 AWS 官方工程博客。

## 核心判断

**AWS DevOps Agent 已把跨会话记忆做成明确的产品能力：历史调查先由 Learning agent 提炼为 Agent Space 内的版本化 Markdown memory；新调查开始时，Agent 先得到 memory store 目录，判断当前问题是否相关，再按需读取具体 memory。**

对外最明确的用途有两类：

- `monitors`：按 alarm / metric 保存重复根因类型与调查证据，供同一监控项再次触发时先行参考；
- `directives`：保存团队长期约定、环境名称和用户偏好，使后续调查遵守这些约定。

AWS 官方博客明确说明 DevOps Agent **built on Amazon Bedrock AgentCore**，并具有面向 memory、policy、evaluation 和 observability 的专用基础设施；但公开资料没有说明 DevOps Agent 的 Markdown memory file 就是 AgentCore `MemoryRecord`，也没有公开 `CreateEvent`、strategy、namespace、`RetrieveMemoryRecords` 或 Harness 的直接绑定。因此可以写“DevOps Agent 基于 AgentCore，并产品化了专用 Memory”，不能写成“DevOps Agent 的每个 memory 对象就是 AgentCore Memory API 对象”。

## 一、DevOps Agent 对外公开的 Memory 机制

### 1. 输入：Agent Space 内的历史调查与用户指令

DevOps Agent Memories 是 Agent Space 特定的高信号信息知识。AWS 将其与 Skills、Agent instructions 分开：Memory 保存“发生过什么、环境有什么特点、团队长期怎么称呼或处理”；它不新增工具或执行能力。

`monitors` store 的输入来自近期调查。存在过去两周调查时，Learning agent 每天分析这些调查，按 monitor 提取重复根因类型和每次调查证据。`directives` store 的输入来自用户在 Chat 中写入的长期指令。

来源：[DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)。

### 2. 提炼：Learning agent 生成信息型记忆

AWS 公开的是产品级学习周期，而不是底层 Memory strategy：

- Learning agent 每日分析近期调查并更新 `monitors`；
- 每个 memory 是 Markdown 文件；
- 每次更新产生新的不可变版本，可查看历史版本并回滚；
- `monitors` 中两周没有更新的 memory 会被删除，容量满时删除最旧项。

这说明 DevOps Agent 不是把完整调查原样塞入下一轮上下文，而是先提炼为更短、可浏览的历史模式。

来源：[DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)、[What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)。

### 3. 组织：Agent Space → memory store → memory file

公开组织层级是：

`Agent Space → memory store → versioned Markdown memory`

memory store 以名称和描述标识主题；具体 memory file 保存对应主题的信息。AWS 公布的配额为每个 Agent Space 50 个 store、每个 store 200 个 memory、单个 memory 100 KB。

这套结构是 DevOps Agent 的产品合同，不应直接改写成 AgentCore namespace / Record 合同。

来源：[DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)、[Quotas](https://docs.aws.amazon.com/devopsagent/latest/userguide/quotas.html)。

### 4. 使用：新调查按需逐级读取

每次 Agent session 开始时，Agent 只收到 memory stores 的名称与描述。调查过程中，Agent 判断某个 store 是否与当前问题相关；相关时再列出其中的 memory，并读取具体文件。

这是一种 progressive disclosure：Memory 不会全部塞入每次上下文，而是先暴露目录，再按任务读取具体内容。

来源：[DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)。

## 二、用户实际获得哪些功能

| 对外功能 | 产品行为 | 用户价值 | 边界 |
|---|---|---|---|
| 重复根因回忆 | `monitors` 按 alarm / metric 保存根因类型和调查证据；同一监控项再次触发时，Agent 在 triage 前读取对应 memory | 减少重复排查，从已发生过的根因开始验证 | Memory 只提供历史候选；当前根因仍需用当前 telemetry、部署和代码证据确认 |
| 环境特性记忆 | 保存 noisy alarm、基础设施特点、组件关系等 Agent Space 特定信息 | 新 session 不必重新解释环境特例 | 这是提炼信息，不等于当前配置或拓扑事实 |
| 长期指令 | 用户通过 Chat 写入 `directives`，例如服务更名或技术栈迁移 | 后续调查保持团队约定和表达一致 | directives 影响上下文，不新增工具权限，也不是发布 Policy |
| 可视化知识管理 | Knowledge → Memories 浏览 store、memory、版本、创建和修改时间 | 运维人员能检查 Agent 实际记住了什么 | 查看能力不证明 memory 内容正确或完整 |
| 启用 / 停用 | 可以停用整个 store 或单个 memory，停用后 Agent 不再读取 | 错误或暂不可信的记忆可退出运行 | 停用不等于删除历史版本或相关调查数据 |
| Chat 维护 | Chat 可创建、更新或删除 memory | 不必通过底层 API 管理日常记忆 | 当前正式文档未给出与 AgentCore Memory API 等价的公开 Memory 数据面 |
| 版本审计与回滚 | 更新生成新的不可变版本，并可浏览旧版本 | 能追踪知识变化并恢复较早内容 | AWS 未公开底层提炼模型、prompt 或完整 lineage |

## 三、与 AgentCore Memory 的关系：能映射到哪里，不能映射到哪里

| AgentCore 通用机制 | DevOps Agent 最接近的公开对象 | 结论 |
|---|---|---|
| Event | Agent Space 中的 chat / investigation session | 只能做输入层类比。DevOps Agent 没有公开 `memoryId`、`actorId`、`sessionId`、Event payload 或 `CreateEvent` 调用 |
| Strategy extraction / consolidation | Learning agent 每日分析近期调查并更新 monitor memories | 机制方向相似，但没有公开 strategy ID、类型、prompt、模型或配置 API，不能称为 AgentCore Memory Strategy |
| Long-term Record | 版本化 Markdown memory file | 都属于派生长期信息，但公开 schema 和管理合同不同，不能把 memory file 等同于 `MemoryRecord` |
| namespace / retrieval | Agent Space、memory store 目录与按需读取 | 都有分域和选择，但 DevOps Agent 未公开 namespace path、query、topK 或 `RetrieveMemoryRecords` |
| Harness 自动持久化 / 注入 | DevOps Agent 自己的 session 启动、store 目录暴露和按需读取 | 没有官方资料说明 DevOps Agent 运行在 AgentCore Harness 上，不能使用 Harness 默认行为解释 DevOps Agent |

AWS 官方能够支持的关系是：**DevOps Agent 建在 AgentCore 上，并有专门的 memory 基础设施；DevOps Agent Memories 是它对用户暴露的产品层合同。底层是否直接使用 AgentCore Memory service/API 未公开。**

来源：[AWS DevOps Agent engineering blog](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)、[AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)、[DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)。

## 四、哪些 DevOps Agent 功能不能直接归因于 Memory

### 1. Topology

Topology 来自资源发现、配置、CloudFormation、Tag、代码 / 部署映射和可观测行为。它是环境关系图，不是 Monitor memory。

来源：[DevOps Agent Topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html)。

### 2. Learned Skills

Learned Skills 是结构化的程序性 / 环境知识文件，包括 Agent Space Understanding、Code Dependencies、Pipeline Topology 与 Tool Use Best Practices。它们有自己的生成和刷新周期，并在任务开始时加载。Memory 是信息型知识，在调查中按需读取。二者可以共同服务调查，但不是同一种对象。

来源：[Learned Skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)。

### 3. Proactive recommendations

DevOps Agent 会分析 investigation history 生成预防建议，但 AWS 文档没有说明该分析必须通过 `monitors` memory store 完成。因此可以说“历史调查驱动预防建议”，不能把建议功能直接归因于 Memory。

来源：[Production operations](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-production-operations-index.html)、[Proactive incident prevention](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-proactive-incident-prevention.html)。

### 4. Release readiness 与 release testing

Release Management 公开依赖 repository knowledge graph、Topology、Agent instructions、Skills 和验证环境。当前文档没有说明 `monitors` / `directives` memories 会直接参与 Release Readiness 或 Release Testing。

Production operations 的 incident patterns、Topology 和 learned skills 会回流发布评审，但不能把这整条反馈链都写成 Memory 的作用。Release Management 截至 2026-08-04 仍为 `us-east-1` Preview。

来源：[Release management](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)、[Production operations feedback loop](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-production-operations-index.html)、[Release Management Preview](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)。

## 五、对 PPT 右半区的结论

原来的 AgentCore 通用 Memory 图可以作为平台机制背景，但下半区不宜只写抽象 CI/CD 启发。更清楚的产品化解释是：

`历史调查 / 用户指令 → Learning agent 提炼 → monitors / directives → 新调查按需读取 → 更快进入验证`

建议对外能力写三项：

1. **记住重复根因。** 同一 alarm 再次触发时，先读取过去的根因类型和证据，再用当前数据验证。
2. **记住环境约定。** 保存 noisy alarm、服务名称、技术栈迁移和团队长期指令，跨 session 继续使用。
3. **让记忆可检查、可停用、可回滚。** 用户能查看具体 Markdown memory 和版本，停用错误内容，并通过 Chat 更新或删除。

必须保留的页面边界：

> DevOps Agent Memories 是调查上下文，不是当前生产事实；AWS 未公开它与 AgentCore Memory API 的逐对象映射，也未证明 Release Management 直接读取这些 memories。

## 产品状态

- AWS DevOps Agent Production operations：2026-03-31 GA；
- DevOps Agent Memories：2026-06-12 写入 What's New，2026-06-19 `monitors` 默认启用；当前页面未单独标 Preview；
- AWS DevOps Agent Release Management：2026-06-17 Preview，仅 `us-east-1`；
- Amazon Bedrock AgentCore：2025-10-13 GA。

## 一手来源

- [DevOps Agent Memories](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-devops-agent-memories.html)
- [DevOps Agent What's new](https://docs.aws.amazon.com/devopsagent/latest/userguide/whats-new.html)
- [DevOps Agent built on AgentCore — AWS engineering blog](https://aws.amazon.com/blogs/devops/leverage-agentic-ai-for-autonomous-incident-response-with-aws-devops-agent/)
- [What is a DevOps Agent Web App?](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-web-app.html)
- [Learned Skills](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-learned-skills.html)
- [DevOps Agent Topology](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent-what-is-a-devops-agent-topology.html)
- [Production operations](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-production-operations-index.html)
- [Release management](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)
- [DevOps Agent quotas](https://docs.aws.amazon.com/devopsagent/latest/userguide/quotas.html)
- [AgentCore Memory](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/memory.html)
- [AgentCore CreateEvent API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_CreateEvent.html)
- [AgentCore RetrieveMemoryRecords API](https://docs.aws.amazon.com/bedrock-agentcore/latest/APIReference/API_RetrieveMemoryRecords.html)

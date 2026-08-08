---
title: Agent 工作台产品功能与控制边界研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台产品功能与控制边界研究边界

## 研究问题

本专题回答四类事实问题：

1. 各产品提供哪些用户入口、配置对象和任务状态？
2. 各产品如何接收上下文、组织 Agent 协作并调用 Skill、工具或连接器？
3. 各产品交付什么产物，采用什么身份、权限、管理员控制和审计机制？
4. 哪些功能直接接入 CI/CD，哪些只是在通用工作台或 Agent 运行平台中存在？

## 研究对象

### 核心详写

- Tencent WorkBuddy。
- ChatGPT Work、Codex、ChatGPT Projects 和 Workspace Agents，保持产品面分离。
- Claude Cowork。
- GitLab Duo Agent Platform。
- Harness Inc. Worker Agents。
- GitHub Agentic Workflows。

### 功能矩阵对照

- Microsoft Copilot Studio。
- Google Gemini Enterprise Agent Platform。
- Atlassian Rovo。
- Amazon Bedrock AgentCore。
- Octopus Claude Agent Step。

## 比较字段

| 字段 | 需要回答的事实 |
|---|---|
| 状态 | 产品整体和单项能力分别处于 GA、Preview、Beta、Alpha、Research Preview、Experiment 还是未声明 |
| 入口 | Web、桌面、CLI、IDE、Pipeline、Repository、Runbook 或 API |
| 配置对象 | Project、Agent、Expert、Skill、Flow、Workflow、Pipeline Step、Connector 等 |
| 上下文 | 文件、项目指令、仓库、知识源、任务历史、变量或前序日志如何进入任务 |
| 任务流程 | 创建、规划、执行、暂停、继续、失败、完成、归档等公开状态与交互 |
| 协作 | 专家团、Subagent、Custom Flow、多 Agent 委派或顺序串接的公开机制 |
| 扩展 | Skill、Plugin、MCP、Connector、CLI、Tool 与 Engine 的配置方式 |
| 触发 | 手动、定时、Webhook、Artifact、Manifest、Issue、Pull Request 或 Pipeline 事件 |
| 产物 | 文件、变更、预览、变量、Issue、评论、PR、报告或日志 |
| 权限 | 用户身份、服务身份、凭据、工具范围、审批、沙箱和网络边界 |
| 管理审计 | 版本、发布、可见范围、清单、日志、Trace、成本或合规记录 |
| 限制 | 官方明确限制，以及文档未公开但不能自行补全的内容 |

## 不包含

- 不判断哪一个产品总体最好，也不形成采购排序。
- 不从产品功能推导企业人员配置或采用效果。
- 不把厂商案例数据外推为行业平均效果。
- 不把 Agent 可调用工具、生成产物或给出结构化结果写成生产发布授权。
- 不制作 PPTX，也不复述传统 CI/CD 的完整流程。

## 证据规则

- 功能、日期、版本、生命周期和限制优先使用官方产品文档、官方发布说明和官方仓库。
- 每个原子功能主张必须精确回链；主题相关但不能证明具体功能的页面不进入 Evidence Map。
- 产品整体状态与单项能力状态分开；单项未标状态时写“未声明”。
- 厂商案例、厂商性能数字和推荐语保留其来源口径，不改写成跨产品事实。
- 找不到一手证据时记录 `unverified` 或证据缺口，不从相邻产品推断。

## 完成标准

- 六个核心产品均覆盖统一记录字段，并有独立功能详章。
- 五个补充产品进入事实矩阵，至少记录入口、配置、协作、权限、状态和边界。
- Evidence Map 的每一行包含官方 URL、页面日期或核验日期、能力状态和不可外推边界。
- 正文不出现人员职责表或企业落地建议；此类旧内容只保存在隔离附录。
- 下游索引、横向洞察、主报告和候选页不再传播旧的组织主张。

## 时间窗口

- `as_of`: 2026-08-08。
- 重点核验 2026 年公开产品文档；仍有效的早期架构或规范页面可作为补充。
- `refresh_after`: 2026-09-08，或任一 Preview/Beta/Alpha 能力状态变化时提前刷新。

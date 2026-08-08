---
title: Tencent WorkBuddy 产品功能与权限边界
source_id: tencent-workbuddy-agent-workbench-2026
organization: Tencent
source_type: official-docs-and-changelog
published: 2026-03-04
verified: 2026-08-08
availability: released-with-feature-stages-not-stated
confidence: high
geography:
  - China
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-runtime-orchestration
  - developer-workflow
  - governance
company_topics:
  - Tencent WorkBuddy
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Tencent WorkBuddy 产品功能与权限边界

## 一手来源

| 页面 | 页面日期/核验 | 直接支持的功能 |
|---|---|---|
| [更新日志](https://www.workbuddy.cn/docs/workbuddy/Changelog) | 4.5.0 于 2026-03-04 正式发布；访问 2026-08-08 | 产品版本状态 |
| [新建任务栏](https://cloud.tencent.com/document/product/1831/134391) | 更新 2026-07-20；访问 2026-08-08 | 模式、工作目录、模型、Skill、连接器、权限、并行任务 |
| [任务管理](https://www.workbuddy.cn/docs/workbuddy/Task-Management) / [任务对话](https://www.workbuddy.cn/docs/workbuddy/Conversation) | 页面未标日期；访问 2026-08-08 | 任务状态、补充上下文、中间步骤、停止/继续、产物和变更 |
| [项目](https://www.workbuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Project) | 页面未标日期；访问 2026-08-08 | Project 指令、资料、专家、Skill、Connector、资产库、授权方式 |
| [专家](https://cloud.tencent.com/document/product/1831/134393) | 更新 2026-07-20；访问 2026-08-08 | Expert 与 Expert Team、团长拆解和并行整合 |
| [技能](https://cloud.tencent.com/document/product/1831/134432) | 更新 2026-07-20；访问 2026-08-08 | Skill 脚本/工作流包、导入/查找/创建/启停 |
| [连接器](https://cloud.tencent.com/document/product/1831/134525) / [企业连接器](https://cloud.tencent.com/document/product/1831/134453) | 更新 2026-07-20；访问 2026-08-08 | MCP/CLI、OAuth/API Key、工具过滤和超时 |
| [自动化](https://cloud.tencent.com/document/product/1831/134399) / [右侧栏](https://cloud.tencent.com/document/product/1831/134400) | 更新 2026-07-20；访问 2026-08-08 | 定时任务、运行日志、文件/变更/预览产物 |
| [企业专家管理](https://cloud.tencent.com/document/product/1831/134421) | 更新 2026-07-20；访问 2026-08-08 | ID、版本、启用、可见范围、包校验和分发策略 |

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 状态 | WorkBuddy 4.5.0 正式发布；专家团、Project、Skill、Connector 等页面未分别声明 GA/Preview/Beta |
| 入口 | 新任务栏支持默认、Plan、Ask 模式，可设置工作目录、模型、Skill、Connector 和权限 |
| 上下文 | Task 使用工作目录和上传文件；Project 可注入指令、资料、专家、Skill 和 Connector |
| 任务 | 官方列出进行中、完成、失败、待处理、规划中、归档等状态；对话可补充需求、查看步骤和停止/继续 |
| 协作 | Expert Team 由团长拆解任务、协调多位 Expert 并行执行并整合交付 |
| 扩展 | Skill 封装脚本/工作流；Connector 通过 OAuth/API Key、MCP/CLI 接入并可过滤工具 |
| 自动化 | 可设置个人定时 Prompt 任务、模型、Skill、工作目录、通知和运行状态 |
| 产物 | 右侧栏区分生成文件、Workspace File、Change 和 Preview；产物可保存到 Project 资产库 |
| 权限 | Expert 本身不主动取得系统权限；Skill/Connector 在用户授权及外部账户权限内工作 |
| 管理 | 企业专家记录唯一 ID、版本、草稿/启用、可见范围、MD5/SHA256 校验和白/黑名单策略 |

## 状态和不可外推边界

- 产品版本正式发布不代表每一个后续功能均 GA。
- 团长机制未公开任务依赖图、调度算法、失败补偿、隔离模型或成功率基准。
- Skill、MCP 和 Connector 的可调用性不等于外部业务动作已经获批。
- 项目资料库、任务日志和包校验不自动满足企业变更审计或内容质量要求。
- 官方资料没有直接证明 WorkBuddy 原生执行 CI/CD Required Check、合并或生产发布批准。

## 专题入口

[[50_deepdives/agent-workbench/40_workbuddy|WorkBuddy 功能详章]]

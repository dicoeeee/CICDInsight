---
title: Claude Cowork 产品功能、执行位置与审计边界
source_id: anthropic-claude-cowork-2026
organization: Anthropic
source_type: official-help-center
published: 2026-04-09
verified: 2026-08-08
availability: beta-and-research-preview-components
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - agent-runtime-orchestration
  - knowledge-work
  - governance
company_topics:
  - Claude Cowork
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Claude Cowork 产品功能、执行位置与审计边界

## 一手来源

| 页面 | 日期/状态 | 直接支持的功能 |
|---|---|---|
| [Architecture overview](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview) | 页面未标日期；访问 2026-08-08；Remote Beta | Local/Remote 架构、权限、隔离、管理员控制、OTel 与审计缺口 |
| [Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork) | 发布 2026-04-09；访问 2026-08-08 | 文件、Instructions、Context、Memory、Scheduled Task 与限制 |
| [Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude) | 发布 2026-05-29；访问 2026-08-08 | Skills、Connectors、Hooks 和 Subagents 打包 |
| [Plugin management](https://support.claude.com/en/articles/13837433-manage-plugins-for-your-organization) | 页面未标日期；访问 2026-08-08 | Marketplace、ZIP/GitHub Sync、组织分发 |
| [Scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork) | 页面未标日期；访问 2026-08-08 | 周期/按需任务、远程运行、独立 Session 和本地文件限制 |
| [Computer use](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork) | 发布 2026-04-24；访问 2026-08-08；Research Preview | 应用许可、屏幕操作和风险边界 |
| [Safety](https://support.claude.com/en/articles/13364135-use-claude-cowork-safely) | 页面未标日期；访问 2026-08-08 | 文件/浏览器/应用权限和删除确认 |

## 功能事实

| 字段 | 官方公开事实 |
|---|---|
| 执行位置 | Remote Session 在 Anthropic 服务端隔离环境运行；Local Session 在设备运行 Agent Loop，并在隔离 VM 执行代码 |
| Project | 使用本地文件、Instructions、Context 和 Project-scoped Memory 组织任务 |
| Project 限制 | 当前 Desktop-only、无 Cloud Sync；Team/Enterprise Project 不支持分享；Claude Code 当前不支持 Cowork Project |
| 扩展 | Plugin 可打包 Skills、Connectors、Hooks 和 Subagents；Hooks/Subagents 只在 Cowork 运行 |
| 自动化 | Scheduled Task 按周期或按需运行，每次建立独立 Session，并使用已配置扩展 |
| 产物 | 可生成文件、报告和任务结果；Scheduled 页面保存历史运行 |
| 权限 | Remote 访问设备文件需要 Desktop 在线、目录已连接并通过本地工具权限；管理员可控制远程开关、网络和批准模式 |
| Computer Use | 访问应用前请求许可；当前为 Research Preview |
| 监控/审计 | Cowork Activity 当前不进入 Audit Logs、Compliance API 或 Data Export；可经 OpenTelemetry 输出事件 |

## 不可外推边界

- Remote execution 为 Beta，Computer Use 为 Research Preview。
- Plugin/Subagent 存在不证明公开了通用多 Agent 调度或质量模型。
- OTel 事件流不等于 Compliance API 覆盖。
- 官方文档没有直接证明 Cowork 原生运行 CI/CD Required Check、合并或发布批准。

## 专题入口

[[50_deepdives/agent-workbench/42_claude-cowork|Claude Cowork 功能详章]]

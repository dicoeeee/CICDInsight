---
title: Claude Cowork 产品功能详章
tags:
  - research/agentic-cicd
  - research/product-facts
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Claude Cowork 产品功能详章

## 产品状态与入口

- Claude Cowork 面向付费计划，提供 Desktop、Web 和 Mobile 等 Surface；远程执行在官方架构与安全文档中标为 Beta，并按计划逐步开放。[安全说明](https://support.claude.com/en/articles/13364135-use-claude-cowork-safely)
- Desktop 同时支持既有本地执行与远程任务。Remote session 默认在 Anthropic 服务端隔离环境运行；Local session 的 Agent loop 在设备上运行，代码在本地隔离 VM 中执行。[架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)
- Computer Use 可在 Cowork 和 Claude Code 中操作桌面应用，当前为 Pro/Max 的 Research Preview；它是独立能力状态。[Computer Use](https://support.claude.com/en/articles/14128542-let-claude-use-your-computer-in-cowork)

## 配置对象

| 对象 | 官方公开的配置或行为 |
|---|---|
| Cowork task/session | Prompt、文件、连接器、Plugin/Skill、运行 Surface 与权限 |
| Cowork Project | 本地文件夹、Instructions、Context、Memory 与项目定时任务 |
| Skill | 可复用指令与配套资源，可自动匹配或由用户选择 |
| Connector | 远程或桌面连接器；远程连接器经 Anthropic Cloud 调用 |
| Plugin | 将 Skills、Connectors、Hooks 和 Subagents 打包；Hooks/Subagents 只在 Cowork 运行 |
| Scheduled task | 保存 Prompt、运行频率、连接器/Plugin 和每次独立 Session 的结果 |

来源：[Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)、[Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)、[Scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork)

## 输入、上下文、Project 与 Memory

- Cowork Project 将相关任务组织到带有文件、上下文、Instructions 和 Memory 的本地 Workspace；可以从本地文件夹、Chat Project 或 URL 建立上下文。[Projects](https://support.claude.com/en/articles/14116274-organize-your-tasks-with-projects-in-claude-cowork)
- Project Memory 只在该 Project 内使用，不跨 Project 继承。
- 截至页面日期，Cowork Project 仅桌面可用、项目数据本地保存、不支持 Cloud Sync；Team/Enterprise 的 Cowork Project 不支持项目分享。
- Claude Code 尚不支持 Cowork Projects；官方只表示计划在未来更新中支持，不能写成当前能力。

## Subagents、Skills、Plugins 与连接器

- Plugin 将 Skills、Connectors 和 Subagents 打包；Skills 可用于 Chat 和 Cowork，Hooks 与 Subagents 只在 Cowork 运行。[Plugins](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)
- 组织管理员可通过 Marketplace 分发 Plugin；支持手动 ZIP 或 GitHub 同步，成员在下一次 Session 或刷新时获得更新。[组织 Plugin 管理](https://support.claude.com/en/articles/13837433-manage-plugins-for-your-organization)
- 统一目录将 Skills、Connectors 和 Plugins 分开展示；Team/Enterprise 的组织级 Skill 分享默认关闭，可由 Owner 开启。[统一目录](https://support.claude.com/en/articles/14328846-browse-skills-connectors-and-plugins-in-one-directory)
- 远程 Connector 由 Anthropic Cloud 访问外部服务；本地 MCP 或桌面扩展在设备上运行。远程 Cowork Session 不能直接访问企业私网中的 Connector，除非该服务从 Anthropic Cloud 可达。[连接器边界](https://support.claude.com/en/articles/11725091-when-to-use-desktop-and-web-connectors)

## 触发与自动化

- Scheduled task 可按固定频率或按需运行，每次运行形成独立 Cowork Session，并使用已配置的 Connectors、Skills 和 Plugins。[Scheduled tasks](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-claude-cowork)
- 远程 Scheduled task 可在电脑休眠或 Desktop 关闭时运行；它只能使用保存到 Claude 账户的文件和 Connector，不能绑定电脑本地文件夹。
- Project 可包含项目级 Scheduled task；普通 Scheduled task 与项目任务的上下文范围应按各自配置区分。

## 产物与交接

- Cowork 可读取和修改已连接文件夹中的文件、运行代码、查询 Connector 并生成报告或其他文件；具体能力取决于 Session Surface、权限和 Plugin。
- Scheduled task 的每次结果保存在独立 Session 中，用户可在 Scheduled 页面查看历史和后续运行。
- Plugin 中的 Skill 可根据 Surface 生成不同产物，例如在 Cowork 中创建文档，在 Microsoft 365 Add-in 中操作对应文件。[Skills](https://support.claude.com/en/articles/12512180-use-skills-in-claude)

## 权限、隔离和管理员控制

- Remote session 在短期隔离环境中运行，Session 结束后环境删除；Connector Token 不进入 Sandbox，由服务端代为调用。Tenant 数据按组织和账户隔离。[架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)
- Remote session 只有在 Desktop 在线、目录已连接且权限允许时才能访问设备文件；每次本地 Tool 调用按成员权限检查。
- Local session 的 Agent loop 在设备运行，代码在 Linux VM 中执行；VM 有网络过滤、系统调用限制和每 Session 用户隔离。
- Team/Enterprise 管理员可关闭 Remote session、设置网络访问策略、要求每次权限调用重新批准，以及要求可信设备和近期登录。
- 删除文件需要明确批准；Computer Use 在访问每个应用前请求权限，但官方安全文档提示 Computer Use 的操作风险与其他权限门控工具不同。[安全说明](https://support.claude.com/en/articles/13364135-use-claude-cowork-safely)

## 监控与审计

- Cowork Activity 当前不进入 Audit Logs、Compliance API 或 Data Export。
- Team/Enterprise Owner 可通过 OpenTelemetry 将 Cowork 事件流向 SIEM 或观测工具。该能力是监控出口，不等于 Compliance API 已覆盖 Cowork。[架构概览](https://support.claude.com/en/articles/14479288-claude-cowork-architecture-overview)
- Host EDR 无法检查隔离 VM 内或远程 Session 内部活动；依赖 Endpoint 可见性的组织需要把这一限制纳入部署判断。

## 端到端功能流程

`选择本地/远程 Surface 或 Cowork Project → 连接本地文件、账户文件、URL 或 Connector → 应用 Project Instructions、Memory、Skill/Plugin → Cowork 执行文件、Web、代码或应用任务，可调用 Plugin Subagents → 用户批准受控工具或应用访问 → 查看文件/报告等结果 → 将后续任务留在同一 Project 或建立 Scheduled task`

## 明确限制

- Remote execution 是 Beta，Computer Use 是 Research Preview。
- Cowork Project 当前不支持 Cloud Sync、Team/Enterprise 项目分享或 Claude Code 使用。
- Cowork Activity 当前不进入 Compliance API/Audit Logs/Data Export。
- Cowork 是通用工作台；官方文档没有证明它原生执行 CI/CD Required Check、合并或生产发布批准。

## 主要证据入口

- [[00_sources/briefs/2026-anthropic-claude-cowork|Claude Cowork Source Brief]]
- [[00_sources/research-agent-workbench-expert-team-2026-08-08|产品景观证据日志]]

---
title: MCP 业界案例比较
tags:
  - research/agentic-cicd
  - research/case-map
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP 业界案例比较

| 案例 | 核心能力 | 成熟度信号 | 局限 | 对企业的启示 |
|---|---|---|---|---|
| GitHub Remote MCP | OAuth 2.1+PKCE、短期 Token、远程 GitHub Tool | 2025-09 GA | 具体 Tool 仍受 GitHub 权限和企业策略约束 | 远程服务适合统一升级、身份和策略 |
| GitHub Toolset 精简 | Tool 配置、项目 Tool 合并、Scope 过滤 | 2025-12—2026-01 持续演进 | 23k/50% 是厂商自报的特定上下文结果 | Tool 数量本身是质量和成本问题 |
| GitHub internal registry/allowlist | 企业批准目录和 Server 控制 | 2025-11 Public Preview；2026-02 控制平面部分 GA | MCP allowlist 仍为 Preview；本地执行治理较弱 | Registry 必须与身份、执行位置和签名共同工作 |
| Official MCP Registry | Namespace 所有权验证、元数据、发布资产 | Preview | 目录出现不等于企业批准或安全 | 内部 Catalog 应叠加 Owner、风险和撤回状态 |
| Copilot CLI MCP 安装 | 在 Agent CLI 内搜索、安装和启停 MCP | 2026-06 GA 界面能力 | 便捷安装扩大本地代码执行与供应链风险 | 用户体验与企业治理需要同步设计 |
| Copilot Extensions → MCP | GitHub 弃用旧 GitHub Apps 扩展路线 | 2025-09 官方迁移信号 | 只代表 GitHub 生态选择 | MCP 已成为主流 Agent 工具互操作方向之一 |

## 共性判断

行业采用正在从“能否连接 Server”迁移到“哪些 Tool 对哪些任务身份可见、如何减少上下文、如何远程升级和撤回”。这说明 MCP 的竞争重点已经由连接协议上移到运营控制面。

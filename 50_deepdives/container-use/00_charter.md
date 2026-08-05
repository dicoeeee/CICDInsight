---
title: Dagger Container Use 独立研究章程
tags:
  - research/agentic-cicd
  - research/charter
status: active
as_of: 2026-08-03
confidence: open
---

# Dagger Container Use 独立研究章程

## 研究决策

判断 Container Use 是否提供了一种适用于多 Coding Agent、并能与 CI 交接的独特执行环境模型，以及这一机制是否足以形成面向 CTO、研发效能负责人和平台工程负责人的单页洞察。

## 纳入范围

- 计算、存储、网络、缓存和 Secret 的资源来源；
- Environment 的 branch/worktree、container、history、configuration 数据模型；
- MCP、CLI、Dagger Engine 与 Agent 客户端之间的调用链；
- 创建、恢复、观察、接管、合并、应用、删除和清理生命周期；
- 并行环境、服务、状态、缓存、故障恢复与资源成本；
- 从开发机进入 CI 的复用机制及不可保证的差异；
- 版本状态、维护活跃度、采用证据和替代路径。

## 不纳入范围

- 以 Claude Code 的企业采用替代 Container Use 自身采用证据；
- 把 MCP 可配置写成客户端厂商背书；
- 把环境复用等同于测试、扫描、制品、策略或审批通过；
- 未经实验宣称零环境漂移、无限并发或普遍成本收益。

## 成功条件

1. 用一手来源画清资源与生命周期；
2. 区分 Git 持久化、容器状态、命令历史和 Dagger cache；
3. 解释与 worktree、devcontainer、Nix/Codespaces、通用 sandbox 的增量差异；
4. 形成至少一个可证伪的页面主张、一个强反例和明确成熟度标签；
5. 完成逐主张事实审计并给出 Presentation-ready Go / No-Go。

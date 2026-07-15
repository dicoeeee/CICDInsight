---
title: MCP 专题分析发现
tags:
  - research/agentic-cicd
  - research/findings
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP 专题分析发现

## F1：MCP 的核心产品价值是复用半径

同一 Tool/Resource 能被多个 Host 发现和调用，Server 可独立升级。单客户端本地任务难以体现这项收益，因此不能用相同标准评价所有集成。

## F2：协议互操作不等于语义互操作

`tools/list` 和 JSON Schema 能让客户端知道“怎样调用”，不能保证不同 Server 对部署、批准、回滚或成功采用同一业务语义。

## F3：远程 MCP 才真正触发企业控制面

OAuth、短期 Token、集中策略、Registry 和 Allowlist 在远程多租户中有明确收益；本地 stdio 更接近受控子进程，其主要风险是安装和执行任意代码。

## F4：Tool 数量是新的上下文预算和权限预算

更多 Tool 不等于更强。每增加一个 Tool 都会增加模型选择空间、描述 Token、授权面和维护责任。企业应按任务、阶段和风险动态组装 Toolset。

## F5：MCP Server 是供应链软件，不是无害配置

本地一键安装可能执行任意命令；远程 Server 可以变化 Schema、Description 和返回内容。Server、包、元数据和更新渠道都需要 Owner、签名、审查和撤回。

## F6：2026-07 RC 暴露了早期架构的规模化压力

无状态核心、标准 HTTP 基础设施和扩展框架说明会话、负载均衡、演进兼容和能力膨胀已成为现实问题。由于正式规范尚未发布，企业现在应做迁移准备而非立即以 RC 为生产基线。

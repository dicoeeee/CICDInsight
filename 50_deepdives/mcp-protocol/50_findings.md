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

## F7：渐进式加载属于上下文平面，不属于权限平面

Anthropic `defer_loading` 决定 Tool Definition 何时进入 Claude 上下文，`enabled`/Allowlist/Policy 决定 Tool 是否存在并可执行。生产系统必须先裁剪 Enabled Toolset，再在低风险长尾能力中做搜索和延迟加载。

## F8：Tool Catalog 正转变为可检索索引

Tool 的 Name、Description、参数名和 Server Instructions 变成检索字段。接口设计不仅要“对人可读、对模型可调用”，还要具备稳定命名空间、任务关键词、风险元数据和可测试的 Search Recall。

## F9：Definition 优化与 Result 优化必须分开

Tool Search 减少定义 Token，却不解决日志、SBOM、测试结果和制品元数据的返回体膨胀。后者需要分页、字段投影、Resource Link、执行环境聚合和持久化产物。

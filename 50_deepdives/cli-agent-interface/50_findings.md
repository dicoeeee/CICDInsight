---
title: CLI 专题分析发现
tags:
  - research/agentic-cicd
  - research/findings
  - tool/cli
status: complete
as_of: 2026-07-27
---

# CLI 专题分析发现

## F1：CLI 的价值来自进程契约，不来自终端界面

Agent 不需要“像人一样使用终端”。真正有价值的是一个可以版本锁定、在容器内启动、接收显式参数、返回结构化结果并由 OS 强制超时或终止的进程边界。

## F2：Agent-ready 是比 machine-readable 更高的要求

JSON 只是其中一项。一个 CLI 即便返回 JSON，如果依赖当前目录、默认账户、全局登录态、交互式确认或非幂等副作用，仍不适合作为高自治工具。

## F3：直接 CLI 的优势在局部最优，MCP 的优势在组织复用

直接 CLI 减少适配层、容易调试和重放；MCP 通过统一发现、Schema、远程授权和目录管理降低多客户端重复接入。选择点不是“新旧技术”，而是复用半径与治理半径。

## F4：2026 年 CLI 与 MCP 正在收敛为上下两层

终端 Agent 可以安装 MCP Server、加载 Skill、调用本地 CLI；MCP Server 也经常把已有 CLI/API 封装为 Tool。二者不是同一维度，但会形成一个调用栈。

## F5：最危险的 CLI 不是功能多，而是边界隐式

当前 Account、Region、Cluster、Namespace、Branch、Working Directory 和凭据来源如果没有进入调用参数与审计事件，Agent 很容易“命令正确、对象错误”。

## F6：CI/CD 采用单位应是受限命令集合

企业不应对整个 `aws`、`kubectl` 或内部发布 CLI 一次性授权。可评测和授权的单位应该是“某身份在某环境调用某组参数约束下的命令，并由某 Oracle 验证”。

## F7：工具选择权与执行授权必须分离

模型可以看见哪些工具，决定它会规划什么；当前任务身份获准执行哪些命令，决定真实副作用能否发生。两者合并时，要么模型反复选择最终会被拒绝的工具，要么为了减少提示而扩大执行权限。企业控制面应分别维护 Tool Visibility 与 Execution Permission，并让拒绝规则优先。

## F8：CLI 把判断变成行动，不能把行动变成证明

CLI 可以把 Agent 的概率性判断压缩为显式命令、目标、退出状态和产物，但命令成功只证明调用链返回了成功。Test、Scan、Policy、Signature、SLO 或人工 Review 必须在 Agent 和 CLI 之外决定业务结果是否成立；这也是 CLI 能否从 L2 提案进入有界 L3 执行的真正分水岭。

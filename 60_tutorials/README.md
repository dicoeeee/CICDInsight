---
title: Agentic CI/CD 配置与实践教程
aliases:
  - 配置教程
  - Tutorial Index
tags:
  - research/agentic-cicd
  - tutorial
status: active
as_of: 2026-07-17
---

# Agentic CI/CD 配置与实践教程

> [!summary] 定位
> 本目录是 Deep Dive 的可选实践衍生物，不构成第四条主作业流，也不重复专题报告。每篇只解决一个问题：**一份配置怎么填、关键字段是什么意思、哪里最容易配错、如何确认它生效**。

## 首批教程

| 教程 | 配置状态 | 适合解决的问题 |
|---|---|---|
| [[60_tutorials/github-agentic-workflows-config|GitHub Agentic Workflows 配置速查]] | 可执行；需替换任务内容 | `.github/workflows/*.md` 的 Frontmatter 怎么写，如何限制权限、工具、网络和写操作 |
| [[60_tutorials/harness-worker-agent-config|Harness Worker Agent YAML 配置速查]] | 官方结构；需替换 Connector ID | Worker Agent Catalog 中的 YAML 怎么写，输入、模型、MCP 和运行参数分别做什么 |
| [[60_tutorials/agent-pipeline-control-reference|Agent Pipeline 安全配置骨架]] | 厂商无关参考，不能直接执行 | 在选型或设计评审时，如何检查身份、权限、工具、运行时、审批、验证和回退是否齐全 |

## 阅读约定

- **可执行**：字段名来自当前官方 Schema，可复制后按说明替换。
- **需替换**：结构有效，但 Connector ID、仓库名、Label、模型或任务说明必须换成自己的值。
- **参考骨架**：用于架构设计和评审，不属于任何厂商的可执行 Schema。
- 默认先给最小示例，再解释关键字段；完整原理、复杂案例和实验仍下钻到 [[50_deepdives/README|专题深研层]]。

## 每篇教程固定包含

1. 配置放在哪里；
2. 一份可复制的最小示例；
3. 字段路径、是否必填、含义和风险；
4. 常见错误；
5. 最小验证步骤；
6. 官方参考和对应专题报告。

新增教程时使用 [[60_tutorials/_templates/Config Quick Reference Template|配置速查模板]]。

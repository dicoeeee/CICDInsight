---
title: Sonatype Guide 将供应链情报前移到 Agent 决策
source_id: sonatype-guide-2025-12-09
organization: Sonatype
source_type: official-announcement
published: 2025-12-09
verified: 2026-07-14
availability: available
confidence: high
geography:
  - united-states
  - global
lifecycle_stages:
  - stage-2
  - stage-5
tool_categories:
  - dependency-intelligence
  - software-supply-chain
  - mcp
company_topics:
  - sonatype
autonomy_levels:
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Sonatype Guide 将供应链情报前移到 Agent 决策

## 来源

- 标题：Introducing Guide for Secure Agentic Development
- 发布日期：2025-12-09；官方帮助文档更新至 2026-06-18
- 链接：[Sonatype 官方公告](https://www.sonatype.com/press-releases/introducing-guide-for-secure-agentic-development)
- 来源类型：产品公告与官方文档
- 能力状态：可用；官方未使用 GA 术语

## 一句话结论

供应链安全正从构建后的扫描门禁前移到 Agent 选择依赖和版本的当下，但它仍只是决策输入，不是发布授权。

## 可核验事实

- 通过 MCP 提供实时组件、漏洞、恶意包、许可证和维护者健康信息。
- 可以给出推荐版本与合规检查，引导 Agent 在引入依赖前先查询供应链情报。
- 官方提供免费开始方式，但没有公开长期企业效果对照。

## CI/CD 相关性

- 涉及阶段：依赖/安全/合规检查，制品与版本管理。
- 工具类别：依赖情报、组件版本治理、MCP。
- 自主等级：L1—L2。
- 涉及角色：开发者、AppSec、供应链安全、平台工程。

## 对洞察的价值

与 JFrog、Cloudsmith 共同表明，Agentic 软件供应链会形成“选择前情报 + 构建后扫描 + 制品策略”的多层 Oracle，而不是让模型自行判断哪个包可信。

## 限制与待验证项

- 重点在依赖选择，不是制品签名、晋级或版本发布。
- 产品效果和生态规模数据来自厂商，不能外推为因果收益。

## 可引用判断

- 可靠的依赖 Agent 应先查询外部供应链事实，再生成或修改依赖，而不是只凭模型训练记忆选择版本。

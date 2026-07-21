---
title: Agentic CI/CD 实践案例库
aliases:
  - Case Library
  - Agentic CI/CD 案例库
tags:
  - research/agentic-cicd
  - evidence/case
status: active
as_of: 2026-07-14
---

# Agentic CI/CD 实践案例库

> [!summary] 定位
> 本目录保存可跨批量洞察和专题复用的外部实践案例。案例卡从 [[00_sources/README|L0 信息源与 Source Brief]]提取事实，但不复制完整 Brief；它重点回答“谁在什么场景中，用什么架构和控制边界做了什么，以及能证明到什么程度”。案例库是共享资产，不是三条作业流的必经步骤。

## 三类材料的边界

| 类型 | 保存位置 | 回答的问题 | 证据要求 |
|---|---|---|---|
| 外部实践案例 | 本目录 | 业界已经做了什么？ | 必须链接一手来源和 Source Brief |
| 自有实验 / 复现 | `50_deepdives/<topic>/40_labs/` | 我们亲自验证出了什么？ | 固定版本、环境、步骤、原始结果和限制 |
| 企业采用 Playbook | `50_deepdives/<topic>/60_playbook.md` | 企业应该如何试点和治理？ | 明确哪些是建议、哪些来自案例或实验 |

## 案例证据等级

| 等级 | 定义 |
|---|---|
| E1 | 产品公告、文档或开源仓库证明能力存在 |
| E2 | 有明确工作流、架构、权限或测试细节 |
| E3 | 有第一方生产规模、周期、效果或失败数据 |
| E4 | 有独立、可复现或多组织长期效果验证 |

证据等级只表示案例材料的充分程度，不等于自治等级或产品成熟度。

## 当前案例

| 案例 | 类型 | 主要课题 | 证据等级 | 关键边界 |
|---|---|---|---|---|
| [[05_case_library/2026-cli-anything-interface-factory|CLI-Anything 接口工厂]] | 开源参考实现 | CLI、Skill、接口生成 | E2 | 无企业 CI/CD 结果证据 |
| [[05_case_library/2025-github-remote-mcp-server|GitHub Remote MCP]] | 产品架构 | 远程 MCP、OAuth、治理 | E2 | MCP 不是授权本身 |
| [[05_case_library/2026-terraform-mcp-autonomy-boundary|Terraform MCP 自治边界]] | 基础设施实践模式 | Plan、Apply、显式扩权 | E2 | 不是完整部署决策 Agent |
| [[05_case_library/2026-cloudsmith-cli-to-mcp|Cloudsmith CLI 到 MCP]] | 制品仓工具面 | CLI、MCP、制品动作 | E2 | 无自动晋级证据 |
| [[05_case_library/2026-jfrog-agent-component-supply-chain|JFrog Agent 组件供应链]] | 供应链平台 | Skill、MCP、Agent 资产治理 | E2 | 状态混合，效果数据有限 |

## 使用规则

1. 每个案例只维护一个规范文件，专题通过 Wikilink 引用。
2. 厂商数字保留“第一方声明”标签，不转写为行业结论。
3. `autonomy`、`product_status`、`evidence_level` 分开记录。
4. 新事实先更新 Source Brief，再更新案例卡；后续回流位置由主作业流决定。
5. 批量洞察可以让案例直接支撑分类视图和主报告，但必须回链 Source Brief 并说明证据等级，不强制先建立 Deep Dive。
6. 深度洞察使用案例时，必须在专题 Claim—Evidence Matrix 或 Case Map 中说明它支撑哪个论点。
7. Presentation 不直接从案例卡生成结论；案例应先由对应 Deep Dive 解释，再进入页面叙事。

## 模板

- [[05_case_library/Case Template|Case Template]]

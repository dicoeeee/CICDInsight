---
title: Huawei CodeArts Agent Team
source_id: huawei-codearts-agent-2026
organization: Huawei Cloud
source_type: official-product-page
published: null
verified: 2026-07-14
availability: ga
confidence: medium
geography:
  - china
lifecycle_stages:
  - code-review
  - testing-gates
tool_categories:
  - coding-agent
  - testing
  - agent-runtime-orchestration
company_topics:
  - Huawei Cloud
  - CodeArts
autonomy_levels:
  - L0
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Huawei CodeArts Agent Team

## 来源

- 标题：华为云码道 CodeArts 代码智能体
- 组织或项目：华为云
- 核验日期：2026-07-14
- 链接：[CodeArts 代码智能体](https://codearts.huaweicloud.com/index.html)、[CodeArts 产品介绍](https://support.huaweicloud.com/productdesc-devcloud/devcloud_pdtd_00001.html)
- 来源类型：官方产品页与产品文档
- 能力状态：体验版、基础版和专业版可用；企业版预告

## 一句话结论

CodeArts 代码智能体强调 Agent Team 多 Agent 并发以及代码、单元测试和知识问答，但其与 CodeArts 全生命周期流水线之间的 Agentic 闭环尚缺少公开细节。

## 可核验事实

- 产品页称 Agent Team 可自动组建研发团队并由多 Agent 并发执行。
- 支持项目级代码生成、续写、知识问答和单元测试用例生成。
- CodeArts 平台本身覆盖需求、代码提交、代码检查、编译、验证、部署和发布。
- 公开页面当前主要描述 IDE 与终端中的编码智能体体验。

## CI/CD 相关性

- 涉及阶段：代码评审和测试生成；平台具备更广阶段但 Agent 接入证据不足。
- 工具类别：编码 Agent、Agent Team、测试生成和 DevOps 平台。
- 自主等级：L0—L2。
- 涉及角色：开发者、测试工程师和研发平台负责人。

## 对洞察的价值

该样本能帮助区分“公司同时拥有 Agent 产品和全生命周期 DevOps 平台”与“Agent 已经贯通平台流程”这两个不同成熟度状态。

## 限制与待验证项

- 未找到足够一手资料证明 Agent 可操作 CodeArts Pipeline、门禁、制品或部署。
- 应在公司维度总结中明确标为证据缺口，而非推断已实现端到端 Agentic CI/CD。

## 可引用判断

- 评估厂商时不能因为其同时拥有编码 Agent 和 DevOps 平台，就默认两者已经形成 Agentic 交付闭环。


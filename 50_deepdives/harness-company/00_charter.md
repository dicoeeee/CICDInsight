---
title: Harness 公司 Agentic Software Delivery 研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - company/harness
status: complete
as_of: 2026-07-16
---

# Harness 公司 Agentic Software Delivery 研究边界

## 决策目标

回答 Harness 公司在 2025H2—2026 年发布了哪些 Agent/AI 能力、这些能力如何嵌入 CI/CD 和软件交付平台、技术架构是否足以支撑企业试点，以及企业应如何区分可用产品、受控开放能力和厂商愿景。

## 研究对象

本专题中的 **Harness** 专指 [Harness Inc.](https://www.harness.io/) 及 Harness Software Delivery Platform，不指通用的 Agent Harness、Harness Engineering 方法论或同名第三方项目。

## 研究范围

- 公司产品战略、平台边界及 2025H2—2026 发布节奏；
- Harness AI、DevOps Agent、Worker Agents、Agent Marketplace；
- Code Quality、AI Test Automation、AI SRE、Release Agent 等专项 Agent；
- Knowledge Graph/HQL、MCP Server、Unified CLI、Skills 和 IDE 入口；
- Pipeline、Delegate、模型连接器、LLM/MCP Gateway、RBAC、OPA、审批和审计；
- Worker Agent 的隔离、凭据代理、网络出口、委托身份和工具授权；
- 公开客户案例、第一方测试、可用性、计费、部署和数据边界；
- 与 GitHub、GitLab、AWS、云平台和专业测试/SRE 工具的边界。

## 非目标

- 不重述 Harness 传统 CI/CD 的全部功能；
- 不把 Harness 公司能力与“Agent Harness 工程框架”混为一谈；
- 不把路线图、产品页示意和 Feature Flag 能力写成普遍 GA；
- 不把 Harness 自测或客户引语外推为行业基准；
- 不实际开通 Harness 账户、配置云凭据或执行生产变更。

## 观察口径

- 观察日：2026-07-16；
- 以官方文档、Release Notes、开源仓库、法律条款和署名客户案例为主；
- “产品状态”和“账户可获得性”分别标记；
- “机制存在”和“实际效果已被证明”分别判断；
- 实验设计与已执行结果严格分开。

---
title: Agentic CI/CD 信息源精简审计
aliases:
  - Source Pruning 2026-07-14
tags:
  - research/agentic-cicd
  - evidence/curation
status: complete
as_of: 2026-07-14
---

# Agentic CI/CD 信息源精简审计

> [!summary] 精简结果
> L0 核心信息源由 107 条精简为 80 条。删除 27 条：13 条被更新版本或更强来源覆盖，8 条仍处于路线图、小样本原型或信息量不足状态，6 条与“编码完成后的 Agentic CI/CD”边界关联较弱。保留项已重新编号为 S01—S80。

> [!info] 后续增补
> 2026-07-14 按专项研究需求新增 S81 CLI-Anything。它补充了现有来源未覆盖的“把遗留/内部软件生成 Agent 可调用 CLI 与 Skill”的接口工厂形态，当前核心集为 81 条，仍在建议的 75—85 条范围内。

## 保留标准

一条来源优先保留，需要满足以下一项或多项：

1. 2025 年下半年或 2026 年的一手资料，且 2026 年仍有效；
2. 能证明 Agent 实际进入代码评审、门禁、构建、制品、部署、发布或恢复流程；
3. 来自大型公司真实生产实践，包含规模、架构、流程或效果证据；
4. 是高影响力开源项目、协议规范、原始研究或可复现实验；
5. 为制品、发布、身份、授权、评测等公开证据稀缺的领域提供独特信息；
6. 能支持成熟度、自治边界、人员变化或经济性判断，而不只是产品宣传。

## 删除标准

- 已被同一产品的 GA、Public Preview、正式文档或更晚版本替代；
- 与同一厂商另一条来源高度重复，未增加新的状态、架构或实践证据；
- 只提供趋势观点、路线图或简短 Preview，缺少可验证工作流；
- 研究样本过小且已有更强 Benchmark 覆盖；
- 主要讨论编码前设计、传统 CI/CD 更新、通用 Agent 平台或 AI Agent 运维，与本报告边界联系较弱；
- 只能证明“存在一个产品”，不能支撑上层分析判断。

## 删除的 27 条来源

### 一、被更新或更强来源覆盖：13 条

| 原编号 | 来源 | 删除原因 | 保留的主要替代证据 |
|---|---|---|---|
| S01 | GitHub Agentic Workflows technical preview | 已被 Public Preview 和开源仓库覆盖 | 当前 S01、S34 |
| S03 | Agentic workflows no longer need a PAT | 单项认证更新，已可并入 Workflows 与 GitHub MCP 判断 | 当前 S01、S40 |
| S07 | Azure DevOps and GitHub strategy blog | 与 Microsoft 迁移案例和 Azure DevOps MCP 深度 Brief 重复 | 当前 S04、[[00_sources/briefs/2026-azure-devops-mcp-open-source|Azure DevOps MCP]] |
| S13 | GitLab agentic engineering announcements | GA、Beta 和路线图混合，核心能力已由版本资料覆盖 | 当前 S07—S09 |
| S14 | GitLab Duo getting-started guide | 产品对象清单与 GA/版本资料重复 | 当前 S07—S09 |
| S18 | Harness AI overview | 总览粒度过粗，模块资料更可验证 | 当前 S10—S12 |
| S19 | Harness June 2026 roundup | 与 Worker、Quality、Test 三条模块资料重复 | 当前 S10—S12 |
| S21 | AWS DevOps Agent public preview | 已被 Production Operations GA 取代 | 当前 S13 |
| S24 | AWS DevOps Agent What's New | 与 GA、Release Management 和架构文章重复 | 当前 S13—S15 |
| S33 | Bitbucket Agentic Pipelines documentation | 与正式发布文章高度重复 | 当前 S20 |
| S37 | What AI is teaching us about CI | 战略观点被 Buildkite 实践案例和 CircleCI 基础设施文章覆盖 | 当前 S23—S24 |
| S39 | Semgrep Custom Workflows | 安全 Workflow 架构已被更新的 Guardian 和 Snyk/JFrog 资料覆盖 | 当前 S25—S28 |
| S45 | Datadog Bits Agent Builder | 示例性功能与 Datadog 架构和 DASH 能力重复 | 当前 S29—S30 |

### 二、证据仍弱或过早：8 条

| 原编号 | 来源 | 删除原因 |
|---|---|---|
| S20 | Harness Investigator Agent Pipelines | Early Access，缺少独立工作流和效果证据 |
| S47 | Dynatrace foundation for agentic AI | 多项状态混合，主要是平台定位与产品宣布 |
| S48 | PagerDuty Spring 2026 release | 以路线图和 Upcoming 能力为主 |
| S49 | New Relic Agentic Platform | Preview 公告较短，缺少具体控制与结果证据 |
| S56 | AIOpsLab | 研究框架，与本报告的生产 CI/CD 实践距离较远 |
| S59 | AutoPipelineAI | 仅两个仓库的小样本原型，已有 SWE-CI/SWE-EVO 等更强研究覆盖 |
| S60 | Agentic Coding Needs Proactivity | 概念性 Position Paper，缺少 CI/CD 实证 |
| S85 | Agent Audit | 22 个样本的小型预印本原型，暂不足以支撑生产判断 |

### 三、与研究边界关联较弱：6 条

| 原编号 | 来源 | 删除原因 |
|---|---|---|
| S27 | Google Application Design Center | 重点偏编码前应用设计，不是编码完成后的 CI/CD |
| S31 | Cloud Build release notes | 主要是传统构建与制品更新，缺少 Agent 变化 |
| S87 | Broadcom Automic Automation V26 | 通用工作负载自动化，缺少 CI/CD 场景和效果证据 |
| S88 | LaunchDarkly AgentControl | 重点是运行 AI Agent 本身，不是 Agent 操作传统 CI/CD |
| S93 | Microsoft Entra Agent ID | 通用 IAM Preview；身份原则已有 NIST、Uber、Google 和 MCP 规范覆盖 |
| S97 | Google Cloud Online Monitors | 通用 Agent 监控 Preview；评测与遥测已有 AgentCore 和 OpenTelemetry 覆盖 |

## 保留集的平衡原则

- 没有只按厂商数量平均裁剪；GitHub、GitLab、Harness、AWS 等平台保留多条，是为了区分 GA、具体工作流和架构实践。
- 制品、发布和生产恢复证据较少，即使部分来源仍处于 Beta/Preview，只要提供独特行动面和控制边界就予以保留。
- 中国厂商和大型企业内部实践全部优先保留，以避免报告只反映欧美 SaaS 产品发布。
- 理论文章没有全部删除；保留能直接解释 CI/CD 自治、发布智能或长期可维护性的研究。
- 开源项目按技术独特性和可验证代码保留，Star 只作为关注度信号，不作为成熟度证据。

## 精简后的使用口径

- [[00_sources/agentic-cicd-source-landscape|81 条来源]]是主报告的当前核心证据登记；其中 S01—S80 是精简基线，S81 为后续增补。
- [[00_sources/README|独立 Source Brief]]是对高频主题的深入分析，可能引用登记之外的补充材料；未登记的补充材料不计入 81 条核心源。
- 上层报告的重要产品状态、实践和成熟度判断应至少能下钻到一个保留的核心来源或独立 Source Brief。
- 后续增加新来源时，应优先替换较弱条目，而不是持续扩大总量；建议将核心集长期控制在 75—85 条。

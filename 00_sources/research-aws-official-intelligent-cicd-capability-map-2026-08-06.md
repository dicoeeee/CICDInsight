---
title: AWS 官方"智能化 CI/CD 能力全景图"核研报告
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/aws
status: complete
as_of: 2026-08-06
accessed: 2026-08-06
confidence: high-for-absence-of-single-official-panorama
---

# AWS 官方"智能化 CI/CD 能力全景图"核研报告

## 1. 检索范围与访问时间

- **检索时间：** 2026-08-06（全部页面为当日直接访问，HTTP 200 或 404 均已记录）
- **覆盖官方来源类型：** 产品营销页、功能页、FAQ、官方文档（User Guide + TOC）、AWS News Blog、AWS Cloud Operations Blog、What's New、官方参考架构站点、Kiro 官方站点
- **主要访问页面：**
  - `aws.amazon.com/devops/`、`/devops-agent/`、`/devops-agent/features/`、`/devops-agent/faqs/`
  - `docs.aws.amazon.com/devopsagent/latest/userguide/`（About、Getting started、Release management、Release readiness、Release testing、Autonomous incident response、Learned Skills、Topology、TOC）
  - 博客：`/blogs/aws/aws-devops-agent-helps-you-accelerate-incident-response-and-improve-system-reliability-preview/`（2025-12-02）、`/blogs/aws/aws-devops-agent-adds-release-management-capabilities-.../`（2026-06-17）、`/blogs/mt/announcing-general-availability-of-aws-devops-agent/`（2026-03-31）、`/blogs/aws/new_deployment_pipelines_reference_architecture_.../`（2023-01-30）
  - What's New：`/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/`、`/about-aws/whats-new/2026/06/aws-devops-agent-release-management/`、`/about-aws/whats-new/2026/07/kiro-opus-sonnet-monitoring-launch-aws-govcloud-us/`
  - 其他：`/codepipeline/`、`/codepipeline/features/`、`/transform/`、`/q/developer/`、`/q/developer/features/`、`/ai/frontier-agents/`、`kiro.dev`、`pipelines.devops.aws.dev`

## 2. 结论摘要

**未检索到单一官方全景图。** AWS 官方**没有**发布过一张覆盖"代码审查→门禁→构建→发布→部署→运行调查/恢复"多阶段、并标注 AWS 智能能力分布的 capability panorama / landscape / framework 图。

已核验的官方页面上只有三类东西：
1. **官方文字框架（无图）**：AWS DevOps Agent 的"release management（Preview）+ production operations（GA）"双域结构；Amazon Q Developer features 页按 SDLC 阶段（Design/Build → Test/Secure → Review/Deploy → Manage → Diagnose）组织的文字能力清单。
2. **单功能官方图（截图/示意）**：release readiness 图、release testing 图、incident response dashboard 截图等，均为单功能 UI 或演示截图，非全景。
3. **一张非智能的多阶段 CI/CD 官方架构图**：Deployment Pipeline Reference Architecture（2023），覆盖 CI/CD 全流程但不含智能能力分布，是"最接近的官方图"而非"智能能力全景图"。

## 3. 找到的官方图清单表

> 所有图均经页面 HTML `<img>` 枚举核验；"官方图"指 AWS 官方页面内嵌的架构图/示意图；截图类单独标注。

| # | 图名 | 来源 URL | 覆盖阶段/产品 | 发布/更新 | 缺失 |
|---|---|---|---|---|---|
| 1 | DevOps Agent 产品页 release-readiness 图（功能截图） | https://aws.amazon.com/devops-agent/（页面内 `release-readiness.*.jpg`） | 仅 Release readiness review 单功能 | 2026-06 起 | 单功能，非全景 |
| 2 | DevOps Agent 产品页 release-testing 图（功能截图） | https://aws.amazon.com/devops-agent/（页面内 `release-testing.*.jpg`） | 仅 Release testing 单功能 | 2026-06 起 | 单功能，非全景 |
| 3 | GA 博客 3 张图：investigation journal / prevention dashboard / SRE chat（UI 截图） | https://aws.amazon.com/blogs/mt/announcing-general-availability-of-aws-devops-agent/ | 生产运维 3 个单功能 | 2026-03-31 | 均为单功能截图 |
| 4 | Release Management 博客控制台截图（4–8 张） | https://aws.amazon.com/blogs/aws/aws-devops-agent-adds-release-management-capabilities-to-assess-code-changes-before-production-preview | release readiness 控制台流程 | 2026-06-17 | 单功能流程截图 |
| 5 | 文档 Autonomous incident response 图（Incident Response Dashboard 截图，已 OCR 确认非架构图） | https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html | 单功能 | 2026 | 单功能截图 |
| 6 | **Deployment Pipeline Reference Architecture 图**（官方架构图 x2） | https://aws.amazon.com/blogs/aws/new_deployment_pipelines_reference_architecture_and_-reference_implementations/ + https://pipelines.devops.aws.dev | 源码→构建→测试→beta/gamma/prod 多环境→审批→多波次部署 | 2023-01-30 | **非智能**，无 AI 能力分布 |
| 7 | 预览博客（2025-12-02）系列 UI 截图 | https://aws.amazon.com/blogs/aws/aws-devops-agent-helps-you-accelerate-incident-response-and-improve-system-reliability-preview/ | 引导式演示 | 2025-12-02 | 无全景图 |

**明确"缺图"的页面（只有文字、无图）：**
- User Guide `about-aws-devops-agent.html`（官方"框架"页，仅有 1 个警告图标，无架构图）
- User Guide Release readiness / Release testing / Learned Skills / Topology / Getting started 页（各仅 1 个警告图标）
- `/devops-agent/features/`、`/devops-agent/faqs/`、`/codepipeline/`、`/codepipeline/features/`、`/transform/`、`/q/developer/`、`/q/developer/features/`、`/ai/frontier-agents/`、`kiro.dev` —— 均为文字/装饰性插画，无 CI/CD 能力分布图

## 4. 最接近的候选图及可用边界

- **候选 A（文字框架，非图）**：AWS DevOps Agent 的官方产品结构 = **Production operations（GA，事件调查/预防/SRE/拓扑）+ Release management（Preview，release readiness + release testing）**。最接近"智能 CI/CD 能力地图"的官方框架，但以文字 + 单功能截图呈现，无一份总图。可用边界：可引用于"能力分域"主张，不可引用于"存在官方全景图"。
- **候选 B（唯一官方多阶段 CI/CD 图）**：Deployment Pipeline Reference Architecture（https://pipelines.devops.aws.dev，2023）。覆盖 source→build→test→多环境晋级→手动审批→部署波次，且含 CodeGuru 代码检查。可用边界：可证明"AWS 官方有 CI/CD 阶段架构图"；**不可**用于证明"智能化/LLM 能力分布"（图中无 AI 能力）。
- **候选 C（官方生命周期文字框架）**：Amazon Q Developer features 页把 AI 能力按 SDLC 阶段组织（Design/Build / Test/Secure / Review/Deploy / Advice / Manage / Diagnose）。这是 AWS 官方把 LLM 能力映射到交付生命周期的**最系统文字表述**，但仍是功能清单，无图。

## 5. 证据缺口与排除项

- 未检索到任何官方页面（营销/文档/博客/What's New）标题含 **capability map / landscape / panorama / framework** 且为 CI/CD 多阶段全景图者。
- **第三方图已全部排除**：社区、分析师、媒体整理图均不作为 AWS 官方证据。
- **待核验**：re:Invent 2025 演讲资料（`content.reinvent.awsevents.com` 为 JS 页面，未逐 session 核验）可能存在 session 内"how it works"图，标记 **unverified**。
- 营销页存在大量 `Missing alt text` 图片，本轮对文档 incident response 图做了 OCR 确认（为 Dashboard 截图）；其余营销插图未逐一 OCR，若需绝对完整需补一轮。
- 结论"缺失"的证据链：Product page / Features / FAQs / User Guide 全部逐页枚举 `<img>`，仅上表所列；What's New 与博客文字均未宣称存在全景图。

## 6. 复核记录

- 引用 URL 均实际访问，404 的来源（如 `/devops/agent/`、`/kiro/`、What's New 月份归档页）已排除。
- 产品状态复核：Production operations **GA**（2026-03-31）；Release management **Preview**（2026-06-17，仅 `us-east-1`）；与项目既有 evidence map 一致。
- 页面表述抽查：产品页声称 "from code creation to production / spans release management and production operations"，但**无对应全链路图**——文字主张与图证缺口并存，已如实记录。
- 冲突说明：无来源间冲突；唯一需注意是营销"从码到产线"表述与"仅两域功能"的边界差异，报告按官方状态字段处理。

**一句话结论：** AWS 官方迄今只有"文字能力框架 + 单功能截图"，唯一一张多阶段 CI/CD 官方架构图为 2023 年非智能的 Deployment Pipeline Reference Architecture；不存在一张标注 AWS 智能能力分布的 CI/CD 能力全景图。
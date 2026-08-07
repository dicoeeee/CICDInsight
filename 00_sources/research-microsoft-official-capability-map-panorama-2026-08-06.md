---
title: Microsoft/GitHub/Azure 官方“智能化 CI/CD 能力全景图”核验
date: 2026-08-06
as_of: 2026-08-06
scope: 检索 Microsoft Learn、GitHub Docs、github.blog、Azure DevOps 文档与 release notes 中是否存在单张覆盖多阶段（计划→编码→PR→构建→测试→发布→部署→运行/恢复）并标注 Microsoft 智能/AI 能力的官方全景图；仅官方来源。
status: complete
confidence: high-for-no-single-official-panorama; medium-for-image-alternative-text-only-verification
related:
  - research-microsoft-intelligent-cicd-capabilities-2026-08-06
  - research-aws-official-llm-product-diagrams-2026-08-03
---

# Microsoft/GitHub/Azure 官方“智能化 CI/CD 能力全景图”核验

## 结论摘要

**截至 2026-08-06，未检索到 Microsoft、GitHub 或 Azure 发布的单一官方图，同时覆盖计划→编码→PR→构建→测试→发布→部署→运行/恢复的多阶段，并把 Copilot coding agent、Agentic code review、Agentic Workflows、Code Quality、Azure SRE Agent 等智能化能力标注在阶段上。**

“未检索到”是对本次官方文档、官方博客、官方 Changelog 和 Learn 搜索 API 的定向检索结果，不等同于证明 Microsoft 从未在会议演讲或未索引材料中使用过类似图。现有官方图形材料分为三种性质：

1. **通用 DevOps 生命周期图**（`devops-lifecycle.png`）：`learn.microsoft.com/en-us/devops/what-is-devops` 的官方概念图，覆盖 Plan/Develop/Deliver/Operate 四阶段，但不含任何 AI/Agent 能力；
2. **Agent 生命周期示意图**（`agent-lifecycle-diagram.png`）：Learn 培训模块 “Foundations of Agentic AI in GitHub” 中的官方 slides 图，Plan→Act→Evaluate 环形，是 Agent 运行循环而非 CI/CD 阶段能力地图；
3. **服务协作 ASCII 图**（Azure DevOps “What is Azure DevOps?” 页）：Plan→Code→Build→Test→Deploy→Monitor→Repeat 的官方文本流程，映射 Azure Boards/Repos/Pipelines/Test Plans/Artifacts/Dashboards，但未画任何智能能力。

单产品页面（GitHub Copilot features、Copilot for Azure、Azure SRE Agent、Azure MCP）均为文字分类或单产品流程，不构成跨阶段能力地图。

### 对当前叙事/页面的直接判断

- **不能引用“Microsoft 官方智能化 CI/CD 全景图”作为主图，因为该图不存在。**
- 最接近的官方图是 **Agent Lifecycle（Plan→Act→Evaluate）**，可用于证明“Microsoft 官方把 Agent 运行描述为规划—行动—评估循环”，但它不是阶段能力分布图。
- 若要画能力分布，必须自绘并标注“基于 Microsoft 官方产品文档整理，非 Microsoft 原图”，每个能力点回链到单产品一手来源（见 `research-microsoft-intelligent-cicd-capabilities-2026-08-06`）。

## 核验口径

### 纳入条件

- 图或同页至少覆盖两个以上 CI/CD 阶段；
- 图内或正文明确标注 Microsoft/GitHub/Azure 的智能化（Copilot/Agent/MCP/AI）能力；
- 来源限定 Microsoft Learn、GitHub Docs、github.blog（Changelog/博客）、Azure DevOps release notes、官方 GitHub 仓库 README。

### 不把以下内容当作“智能化全景图”

- 只有文字分类、无图的页面（如 GitHub Copilot features 的 Assistive/Agentic/Customization/Admin 分类）；
- 营销页的招牌/图标罗列（如 github.com/features、github.com/enterprise）；
- 单产品工作流或单产品 overview 图（如 Azure SRE Agent overview 的流程图）；
- 通用 DevOps 生命周期图（无 AI 标注，如 `devops-lifecycle.png`）；
- 会议录像/keynote 中未以官方文档或官方博客形式发布的画面。

## 最接近的官方图

| 图名 / 类型 | 页面与发布日期 | 覆盖阶段 | 智能能力标注 | 可否直接用于全景叙事 | 准确使用方式 |
|---|---|---|---|---|---|
| **Agent Lifecycle 图（Plan→Act→Evaluate 环形）** | [Foundations of Agentic AI in GitHub — Unit 3](https://learn.microsoft.com/en-us/training/modules/foundations-agentic-ai/3-explain-agent-lifecycle-plan-act-evaluate)，Learn 培训模块，ms.date **2026-04-24**（模块 updated 2026-05-04）；图片 alt 文本为 “Copilot slide showing an agent lifecycle diagram with three steps—Plan, Act, and Evaluate—connected in a circular loop”；图片资源已在 Learn CDN 核验存在（`/en-us/training/github/foundations-agentic-ai/media/agent-lifecycle-diagram.png`） | 不映射 CI/CD 阶段；是 Agent 运行循环 | 是（官方 Agent 运行模型） | **否，作为阶段能力地图；可** 作为“Microsoft 官方 Agent 运行模型”的机制图 | 证明 Microsoft 官方把 Agent 行为描述为 规划→行动→基于系统信号评估的循环；评估信号明确点名 workflow runs/status checks/code review/security signals。 |
| **DevOps 生命周期概念图（devops-lifecycle.png）** | [What is DevOps?](https://learn.microsoft.com/en-us/devops/what-is-devops)，Microsoft Learn；alt 文本为 “Conceptual diagram that illustrates the DevOps application lifecycle”，覆盖 Plan/Develop/Deliver/Operate 四阶段 | 四阶段（粗粒度） | 无 | **否**（无 AI 标注） | 只能作为通用 DevOps 生命周期背景，不能作为智能化能力地图。 |
| **Azure DevOps 服务协作 ASCII 图** | [What is Azure DevOps?](https://learn.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops)，ms.date **2026-02-17**（updated 2026-05-08）；正文代码块 ASCII 图：Plan→Code→Build→Test→Deploy→Monitor→Repeat，映射 Boards/Repos/Pipelines/Test Plans/Artifacts/Dashboards | 六阶段文本流程 | 无（页面下方以文字/MCP 表单列 AI assistance，不入图） | **否**（文本图、无智能能力） | 只能作为 Azure DevOps 服务如何协作的官方示意；AI assistance 以文字小节（Azure DevOps MCP Server、Copilot with Azure Boards）单独列出。 |
| **GitHub Copilot features 文字分类** | [GitHub Copilot features](https://docs.github.com/en/copilot/get-started/features)，GitHub Docs | 不以阶段划分 | 是（Assistive / Agentic / Customization / Admin 四类） | **否**（无图） | 作为“能力分类”文字证据，不是阶段地图。 |
| **Copilot for Azure Primary scenarios 表** | [What is GitHub Copilot for Azure?](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/introduction)，Learn | Learn / Design and develop / Deploy / Troubleshoot 四类场景 | 是 | **否**（表格，非图） | 作为“跨编码—部署—排障”文字框架证据。 |

## 证据缺口

1. **模型无法查看图片内容**：`devops-lifecycle.png`、`agent-lifecycle-diagram.png` 只能依据 alt 文本与页面上下文间接核验；图片内容层级未逐像素审计。
2. **Learn API 搜索覆盖有限**：`learn.microsoft.com/api/search` 对部分查询返回 400（含空格/斜杠），且语义检索结果噪声大；未发现以 “capability map / landscape / panorama / solution overview” 命名的多阶段官方图页面。
3. **GitHub Copilot for Azure 发布博客未定位**：`azure.microsoft.com/en-us/blog/introducing-github-copilot-for-azure-...` 返回 404；未能确认其是否正确 URL 或已下线，未从中核验发布图。
4. **GitHub Well-Architected Framework 未定位**：`github.com/github/well-architected-framework` 与 `docs.github.com/.../well-architected/overview` 均 404；若该框架存在，其内容也以架构支柱为主，不构成智能化 CI/CD 能力地图（未纳入结论）。
5. 通用搜索引擎（DuckDuckGo/Mojeek/Bing）不可靠（超时/403/中文重定向），检索主要依赖直接抓官方 URL + Learn API，可能遗漏未索引的官方活动材料。

## 已核验的排除项

| 页面 / 资源 | 结论 |
|---|---|
| [learn.microsoft.com/en-us/devops/](https://learn.microsoft.com/en-us/devops/)（DevOps 资源中心 hub） | 文字导航，无图。 |
| github.com/features/ai、/features、/features/copilot、/solutions/devops、/enterprise | 营销页，无阶段化能力图。 |
| [GitHub Copilot features](https://docs.github.com/en/copilot/get-started/features) | 文字分类（Assistive/Agentic/Customization/Admin），无图。 |
| [What is GitHub Copilot for Azure?](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/introduction) | Primary scenarios 表格，非图。 |
| [Azure AI developer tools overview](https://learn.microsoft.com/en-us/azure/developer/ai-developer-tools/overview) | 文字框架（Copilot for Azure / Azure MCP / Azure Skills）。 |
| [Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview) | 单产品文字+流程，非跨阶段能力地图。 |
| [Introducing Agent HQ（github.blog）](https://github.blog/news-insights/company-news/welcome-home-agents/) | 愿景叙事，无阶段能力图。 |
| [github.com/microsoft/GitHub-Copilot-for-Azure](https://github.com/microsoft/GitHub-Copilot-for-Azure) | 单产品 repo README。 |
| [What is Azure DevOps?](https://learn.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops) | 服务协作 ASCII 流程图，无智能能力标注。 |
| [What is DevOps?](https://learn.microsoft.com/en-us/devops/what-is-devops) | `devops-lifecycle.png` 为通用生命周期图，无 AI 能力。 |
| Microsoft Learn 培训模块批量检索（GitHub Copilot fundamentals / AI-assisted development / Azure Well-Architected） | 无跨阶段智能化能力地图；最接近的 Agent 生命周期图见上表。 |

## 来源与访问记录（均访问于 2026-08-06）

| ID | 官方来源 | 类型 | 发布/更新时间 | 采用理由 |
|---|---|---|---|---|
| S1 | [Foundations of Agentic AI in GitHub — Unit 3（Agent Lifecycle）](https://learn.microsoft.com/en-us/training/modules/foundations-agentic-ai/3-explain-agent-lifecycle-plan-act-evaluate) | Learn 培训模块 | ms.date 2026-04-24；模块 updated 2026-05-04 | 官方 Agent 运行循环图（Plan→Act→Evaluate）及评估信号；图片资源已在 CDN 核验存在。 |
| S2 | [Foundations of Agentic AI in GitHub — Unit 4（系统记录与控制平面）](https://learn.microsoft.com/en-us/training/modules/foundations-agentic-ai/4-describe-github-system-record-control-plane) | Learn 培训模块 | 同上 | “GitHub 是系统记录与控制平面”的官方文字框架；控制点表格（PR/required reviews/status checks/CODEOWNERS/rulesets/environments）。 |
| S3 | [What is DevOps?](https://learn.microsoft.com/en-us/devops/what-is-devops) | Learn 文档 | 未标注发布日期（访问日核验） | `devops-lifecycle.png` 官方概念图，四阶段、无 AI。 |
| S4 | [What is Azure DevOps?](https://learn.microsoft.com/en-us/azure/devops/user-guide/what-is-azure-devops) | Learn/ Azure DevOps 文档 | ms.date 2026-02-17；updated 2026-05-08 | 官方 ASCII 服务协作流程（Plan→Code→Build→Test→Deploy→Monitor）；AI assistance 仅以文字列出。 |
| S5 | [GitHub Copilot features](https://docs.github.com/en/copilot/get-started/features) | GitHub Docs | 访问日核验 | Assistive/Agentic/Customization/Admin 文字分类。 |
| S6 | [What is GitHub Copilot for Azure?](https://learn.microsoft.com/en-us/azure/developer/github-copilot-azure/introduction) | Learn 文档 | 访问日核验 | Primary scenarios 表格（Learn/Design and develop/Deploy/Troubleshoot）。 |
| S7 | [Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview) | Learn 文档 | ms.date 2026-07-30 | 单产品概述，确认无跨阶段能力地图。 |
| S8 | Learn API 检索（GitHub Copilot fundamentals / AI-assisted development / GitHub Well-Architected Framework） | Microsoft Learn 搜索 API | 访问日 | 确认无 “capability map/landscape/panorama” 命名的官方多阶段图。 |

## 结论的置信度与限制

- **“无单一官方智能化 CI/CD 能力全景图”**：高置信度的定向检索结论（官方文档 + 官方博客 + Changelog + Learn API）；保留未索引活动材料或后续发布的可能，如需对外断言需限定为“截至 2026-08-06 官方文档检索未见”。
- **`Agent Lifecycle（Plan→Act→Evaluate）`**：高置信度，官方 Learn 模块有图与正文双重证据；图片内容细节仅经 alt 文本间接核验。
- **`devops-lifecycle.png` 无 AI 标注**：中置信度（依赖 alt 文本，未像素审计）。
- **GitHub Copilot for Azure 发布博客**：`unverified`（候选 URL 404，未找到正确公告页）。
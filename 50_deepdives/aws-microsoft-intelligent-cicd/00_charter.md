---
title: AWS 与 Microsoft 智能化 CI/CD 对比研究章程
tags:
  - research/agentic-cicd
  - research/charter
  - company/aws
  - company/microsoft
  - company/github
  - company/azure
status: complete
as_of: 2026-08-06
confidence: high
---

# AWS 与 Microsoft 智能化 CI/CD 对比研究章程

## 研究决策

判断 AWS 与 Microsoft（含 GitHub、Azure DevOps、Azure）在智能化 CI/CD 领域分别提供哪些功能/特性、以什么机制工作、处于什么产品阶段，并回答四个决策问题：

1. 两家厂商分别把 Agent / AI 放入软件交付链的哪些位置（审查、门禁、构建、发布、部署、运行调查、恢复）？
2. 每项可核验能力的机制是什么、用户可见产物是什么、目前处于什么生命周期？
3. 哪些能力已 GA、哪些是 Preview、哪些效果只是厂商自述、哪些是证据缺口？
4. 合并、部署、恢复的最终授权是否仍由确定性 Gate 与人工控制面持有？

## 研究范围

### 纳入

- AWS：AWS DevOps Agent（Production Operations、Release Management、Learned Skills、Topology、Custom Agents、Sandbox、MCP/A2A）、Amazon Q Developer 代码审查、AWS Transform continuous modernization、CodeGuru/CodeCatalyst 状态；
- Microsoft：GitHub Copilot coding agent、Copilot code review、GitHub Code Quality、Dependabot→AI agent 修复、Agentic autofix、GitHub Agentic Workflows、Azure Repos Copilot code review、Azure DevOps Autofix、Azure SRE Agent、GitHub Copilot for Azure / Azure MCP / Azure Skills、Remote Azure DevOps MCP、Defender for Cloud code-to-cloud；
- 每项能力的机制、用户可见产物、产品状态与日期、授权/副作用边界、一手来源链接；
- 生命周期标注（GA / Preview / limited public preview / unverified）；
- 与确定性 CI/CD 门禁（Required Status Check、Ruleset、Branch Policy、Environment Protection、RBAC、IAM、人工审批）的关系。

### 不纳入

- 对两家公司全部开发者工具做产品线盘点；
- 传统 CodePipeline / CodeBuild / Azure Pipelines / GitHub Actions / CodeQL / Dependabot 版本更新的确定性能力本身（只作为 Agent 输出的约束或输入）；
- Amazon Bedrock AgentCore 作为通用 Agent 平台的能力（不列作原生 CI/CD 编排）；
- 基础模型、提示词、planner 或重试算法的无来源推测；
- 实际连接客户账号、仓库、VPC、目标环境或生产系统；
- 把厂商发布材料中的收益数字外推为行业平均；
- 直接制作 PPT 或越过现有 Presentation 的语义、结构和视觉审批门。

## 观察窗口与证据政策

- **事实截止：** 2026-08-06；
- **访问时间：** 2026-08-06（Asia/Shanghai），部分条目在 2026-08-05 访问并复核；
- **主来源：** AWS User Guide、AWS What's New、AWS 官方博客、GitHub Changelog、GitHub 官方文档与仓库、Microsoft Learn、Azure DevOps release notes / roadmap；
- **来源限制：** 产品与效果资料几乎都来自厂商方，机制事实可为高置信度，客户效果与跨组织适用性最多为中低置信度；
- **状态口径：** 明确区分 GA / Preview / limited public preview / roadmap；文档存在性不自动等于 GA；路线图日期是"预期"而非承诺；
- **写作口径：** 区分官方事实、厂商自述、分析推断和证据缺口。

## 核心术语

| 术语 | 本专题定义 | 不等于 |
|---|---|---|
| 智能化 CI/CD 能力 | 用户可配置、调用或消费、面向 CI/CD 阶段的 Agent/AI 可见功能 | 底层模型、通用 MCP 协议、确定性流水线规则 |
| Release readiness | AWS Release Management 对标准、跨仓依赖、访问控制和验证结果形成的发布建议 | 默认阻断合并或批准发布 |
| Release testing | AWS 对客户提供的已部署 Web/API 目标生成并执行变更相关测试 | 无副作用测试或自动发布 |
| Code Quality | GitHub 组合 CodeQL 确定性检测 + AI 检测 + Autofix + 覆盖率 + Ruleset 的合并前闭环 | 模型结论本身构成门禁 |
| Agentic autofix | 对 code scanning 告警探索代码、提出修复并重跑扫描验证的 Agent | 只生成建议而无需验证的普通修复 |
| Azure SRE Agent | Azure 连接可观测性、事件平台与代码仓库的运维 Agent（Review/Autonomous run modes） | 文档未证明其生命周期或默认自动恢复生产 |

## 成功条件

- 建立 AWS 与 Microsoft 两套"阶段—能力—机制—产物—状态—边界"对照表；
- 每项关键 Claim 有一手来源、限制、置信度与访问时间；
- 生命周期、区域、写权限、目标环境和宿主 Gate 边界被逐项校准；
- 给出一个不依赖"谁领先"或"自动发布"夸张表述的双公司比较主张；
- 对文档矛盾与生命周期缺失不做擅自拼接，记录冲突并采用保守表述（unverified）。

## 停止条件

- 若主张必须依赖 Release Management 已 GA、Azure SRE Agent 已 GA 或已有独立生产效果才成立，则保持阻塞；
- 若必须把 BLOCK、Check Run、Autofix 修复、Draft PR、缓解建议或 Autonomous mode 直接等同于生产写权限/Required Gate，则停止；
- 若需要连接真实账号、仓库、VPC 或测试目标才能补证据，则另行获得授权，不在本研究内操作；
- 若 Presentation 要求成熟客户 ROI 或端到端自主发布，当前专题不能提供支持。
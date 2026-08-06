---
title: AWS 与 Microsoft 智能化 CI/CD Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - company/aws
  - company/microsoft
  - company/github
  - company/azure
status: complete
as_of: 2026-08-06
confidence: high-for-mechanism-and-status medium-for-outcomes
---

# AWS 与 Microsoft 智能化 CI/CD Claim—Evidence—Gap Matrix

## 证据标记

- **A（AWS 一手）：** AWS User Guide、AWS What's New、AWS 官方博客；可证明产品机制、接口、限制和明确状态。
- **G（GitHub/Microsoft 一手）：** GitHub Changelog、GitHub 官方文档与仓库、Microsoft Learn、Azure DevOps release notes / roadmap。
- **分析：** 主 Agent 基于多项 A/G 事实形成的架构、比较或采用判断，不冒充厂商原话。
- **缺口：** 当前未找到足以核验的独立客户结果、跨产品基准或第三方复现。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| C01 | AWS Production operations 于 2026-03-31 GA | [AWS GA 公告](https://aws.amazon.com/about-aws/whats-new/2026/03/aws-devops-agent-generally-available/)明确发布时间；[[00_sources/research-aws-intelligent-cicd-capabilities-2026-08-06\|AWS Source Brief]] | 文档历史记 03-30，What's New 页记 03-31；GA 公告中的 MTTR 改善是厂商自述 | high | passed |
| C02 | AWS Release Management 截至 2026-08-06 仍是 us-east-1 Preview | [Release management 索引](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)明确 "Preview ... available only in us-east-1" | AWS 称 GA 时扩区，属 Roadmap 表述；What's New 公告 URL 未定位（unverified） | high | passed |
| C03 | AWS Release readiness 四透镜审查：标准、跨仓依赖、访问控制 + 托管验证 build/run/test | [Release readiness code review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | 自然语言 Skills 不等于确定性 Policy-as-Code；只有 CloudFormation 访问控制检查被明确说明 | high | passed |
| C04 | AWS 审查 Agent 内置护栏：阻断凭据、阻断敏感文件+出网、阻断 mutative AWS API、强制阶段顺序 | [Release readiness code review](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html) | 自动触发仅限私有仓库；私有依赖需显式授予 runtime role 与 VPC 访问 | high | passed |
| C05 | AWS Release testing 会对客户部署目标发真实写请求（POST/PUT/DELETE） | [Release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html) 明确警告 | 仅公网可达端点，不支持私有网络；文档建议仅用于可容忍探索性写操作的环境（如 staging） | high | passed |
| C06 | AWS 内建生产工具默认不修改基础设施或应用，缓解建议由人应用 | [Autonomous incident response](https://docs.aws.amazon.com/devopsagent/latest/userguide/production-operations-autonomous-incident-response.html) | Sandbox 沙箱内 AWS API 被代理为只读；外部 MCP/webhook 的写副作用是客户授权面 | high | passed |
| C07 | AWS 自定义 SRE Agent 支持 cron/rate 调度与 MCP 工具 | [Custom agents executing](https://docs.aws.amazon.com/devopsagent/latest/userguide/custom-agents-executing-custom-agents.html) | 单次一个 invocation；MCP 工具可能写外部系统，需逐 Agent 最小权限治理 | high | passed |
| C08 | AWS 于 2025-11-07 停止 CodeGuru Reviewer 新关联与 CodeCatalyst 新客户 | [CodeGuru availability change](https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/codeguru-reviewer-availability-change.html)、[CodeCatalyst 文档](https://docs.aws.amazon.com/codecatalyst/latest/adminguide/managing-generative-ai-features.html) | 存量关联仍可用；属供应商产品生命周期变化，不证明替代品成熟度 | high | passed |
| C09 | GitHub Copilot coding agent 已 GA（2025-09-25），异步交付 Draft PR | [GA changelog](https://github.blog/changelog/2025-09-25-copilot-coding-agent-is-now-generally-available) | Draft PR 是候选变更，合并仍由人决定；企业需管理员启用 | high | passed |
| C10 | agentic Copilot code review 已 GA（2026-03-05），支持 skills/MCP（2026-07-29 GA） | [Agentic architecture changelog](https://github.blog/changelog/2026-03-05-copilot-code-review-now-runs-on-an-agentic-architecture)、[skills/MCP GA](https://github.blog/changelog/2026-07-29-copilot-code-review-agent-skills-and-mcp-now-generally-available) | 2026-04-27 起消耗 Actions minutes；评论不是 Required Review，不阻断合并 | high | passed |
| C11 | GitHub Code Quality 于 2026-07-20 GA，组合 CodeQL + AI 检测 + Autofix + 覆盖率 + Ruleset | [Code Quality GA changelog](https://github.blog/changelog/2026-07-20-github-code-quality-is-now-generally-available) | 仅 Enterprise Cloud + Team；GHES 暂不支持；67.3% 解决率为 GitHub 内部数据（厂商自述） | high | passed |
| C12 | Dependabot 告警可指派 coding agent 生成修复 Draft PR（GA 2026-04-07） | [Dependabot → AI agents changelog](https://github.blog/changelog/2026-04-07-dependabot-alerts-are-now-assignable-to-ai-agents-for-remediation) | 需 Code Security + 含 coding agent 的 Copilot 计划；仅 GitHub.com | high | passed |
| C13 | Agentic autofix 于 2026-07-10 进入 public preview，修复后重跑 CodeQL 验证 | [Agentic autofix changelog](https://github.blog/changelog/2026-07-10-agentic-autofix-for-code-scanning-alerts-in-public-preview) | 消耗 AI credits + Actions minutes；修复是候选 PR，需人工评审 | high | passed |
| C14 | GitHub Agentic Workflows 于 2026-06-11 进入 public preview，Markdown 编译为 Actions | [Agentic Workflows changelog](https://github.blog/changelog/2026-06-11-github-agentic-workflows-is-now-in-public-preview) | 默认只读，受 sandbox/firewall 约束；非 GA Pipeline 替代物 | high | passed |
| C15 | Azure Repos Copilot code review 与 Azure DevOps Autofix 为 limited public preview | [Sprint 275](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-275-update)、[Sprint 276](https://learn.microsoft.com/en-us/azure/devops/release-notes/2026/sprint-276-update) | 路线图称 2026 Q3 转 public preview，截至访问日未落地；Copilot review 只 Comment、不阻断 | high | passed |
| C16 | Azure SRE Agent 连接可观测性、事件平台与代码仓库，Review（默认）/Autonomous 双 run mode | [Azure SRE Agent overview](https://learn.microsoft.com/en-us/azure/sre-agent/overview)、[run modes](https://learn.microsoft.com/en-us/azure/sre-agent/run-modes) | 文档未显式标注 Preview/GA（unverified）；Autonomous 受权限门控与响应计划约束 | high for mechanism; low for lifecycle | passed-with-gap |
| C17 | Remote Azure DevOps MCP Server 为 public preview（Sprint 271），GA 目标 2026 Q3 未落地 | [Remote MCP docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server)、[roadmap](https://learn.microsoft.com/en-us/azure/devops/release-notes/features-timeline) | DevOps 索引页仍标 preview；不能写成 GA | high | passed |
| C18 | AWS 与 Microsoft 都把 Agent 放在发布前审查、合并前门禁、发布后恢复三个位置，但保留确定性 Gate | C01—C17 的机制链 | **分析推断。** 没有公开 benchmark 证明哪种路线必然更高效或更安全 | medium-high | passed-as-analysis |
| C19 | 两家路线差异：AWS 收敛"交付—运行上下文"，Microsoft 收敛"仓库内修复与门禁闭环" | C02-C07（AWS 上下文+发布/运行）vs C09-C14（Microsoft 仓库内闭环） | 这是机制层面的方向归纳，不是成熟度或效果排名 | medium-high | passed-as-analysis |
| C20 | 本专题可支撑能力对比页，但不能支撑普遍 ROI 或端到端自治结论 | C01—C19；未找到可独立复核的效果数据 | "未找到"是证据缺口，不是证明客户不存在或产品无效 | high for boundary | passed |

## 生命周期与状态审计

| 对象 | 截至 2026-08-06 的状态 | 可安全表述 | 不能写成 |
|---|---|---|---|
| AWS Production operations | GA（2026-03-31） | 事件调查、预防建议、按需 SRE、自定义 Agent | 整个产品所有能力均 GA；自动修复生产 |
| AWS Release Management | Preview；仅 us-east-1（文档记录 2026-06-11） | readiness review、托管验证、release testing | GA、全球可用、自动发布 |
| AWS Sandbox | Preview（2026-07-23） | 调查隔离、只读 AWS API 代理、出站 allowlist | 已 GA、可用于生产写操作 |
| Amazon Q Developer IDE 审查 | GA | SAST/secrets/IaC/质量/部署风险/SCA 六类 IDE 内检测 | 是 CodeGuru 的完全等价物 |
| Amazon Q Developer for GitHub | Preview | 私有仓库 PR 自动审查 | GA、覆盖公开仓库 |
| AWS Transform continuous modernization | GA（2026-08-03） | 技术债分析、自主生成修复 PR | 修复 PR 自动合并部署 |
| GitHub Copilot coding agent | GA（2025-09-25） | 异步 Draft PR、测试执行 | 自动合并、自动发布 |
| agentic Copilot code review | GA（2026-03-05） | 全仓上下文行级评论、skills/MCP | 评论构成 Required Review 或阻断 |
| GitHub Code Quality | GA（2026-07-20） | 检测+修复+覆盖率+Ruleset 闭环 | 门禁来自模型结论（门禁在 Ruleset） |
| Dependabot → AI agent | GA（2026-04-07） | 告警→修复 Draft PR | 修复自动合并 |
| Agentic autofix | Public Preview（2026-07-10） | 探索+修复+重跑验证 | 已 GA；验证即证明修复 |
| GitHub Agentic Workflows | Public Preview（2026-06-11） | Markdown 定义、只读默认、sandbox/firewall | GA、可替代生产 Pipeline |
| Azure Repos Copilot review / Autofix | limited public preview | Azure DevOps 侧候选修复与评审 | GA、public preview 已落地 |
| Azure SRE Agent | 文档未显式标注 → unverified | 调查/根因/缓解建议、Review/Autonomous 模式 | GA、默认自动恢复生产 |
| Remote Azure DevOps MCP | Public Preview（Sprint 271） | MCP tools、Entra ID 认证 | GA（目标 2026 Q3 未落地） |

## 主要证据缺口

- 独立客户的根因正确率、误报率、修复正确率、MTTR 对照与预防效果；
- Release testing 的缺陷发现率、覆盖率、稳定性与失败降级行为；
- Azure SRE Agent 的正式生命周期标注（Preview/GA）与区域可用性扩展到官方公告；
- Azure DevOps 侧 Copilot review / Autofix / Remote MCP 的 public preview 与 GA 落地时间；
- 上下文地图在缺失 Tag、非 CloudFormation、多云、遗留 Pipeline 环境中的准确率；
- 两家厂商具体基础模型、planner、tool-selection、context 管理与模型更新策略；
- 跨厂商、同口径、可独立复核的效果基准。
---
title: 各公司智能化 CI/CD 演进趋势证据地图
aliases:
  - Intelligent CI/CD Evolution Evidence Map
tags:
  - research/agentic-cicd
  - research/deep-dive
  - evidence-map
topic_id: cicd-trends-2024-2026
status: complete
as_of: 2026-08-07
---

# 证据地图：各公司智能化 CI/CD 演进趋势（2024—2026）

> 证据来源统一访问时间 2026-08-07。证据强度：`高`=官方一手来源直接核验；`中`=官方来源但仅标题/列表页，或仓库既有交叉核验；`厂商自述`=仅单一厂商来源。完整事实表见各研究底稿。

## 一、按演进维度组织的 Claim—Evidence—Gap 矩阵

### 维度 1：能力形态演进（AI 建议 → Agent 执行 → 自治编排）

| # | Claim（主张） | 环节 | 证据（研究底稿+事实编号） | 产品状态 | 证据强度 | Gap |
|---|---|---|---|---|---|---|
| C1 | GitHub 智能化能力从"Copilot 助手建议"演进为"Agent 在 Actions 内执行并护送 PR 至合并"：gh-aw 把 Markdown 编译为 Actions，Agent 只读推理 + Safe Outputs 独立写；Copilot 从助手变 CI 内一等执行者（CLI 免 PAT、修 check、Code Review Agent GA）；官方明确"PR 永不自动合并"，合并决定仍在确定性门禁/人 | 跨阶段 | research-github-microsoft-cicd-trends-2026-08-07（gh-aw 2026-02-13 技术预览→2026-06-11 Public Preview；2026-07-02/07-23/07-29） | Public Preview / GA 混合 | 高 | 部分 changelog 仅列表页标题 |
| C2 | AWS 智能化能力从"事件调查（只读）"演进为"发布前审查 + 发布测试（受控执行）"：DevOps Agent production operations GA（2026-03-31）→ release management Preview（2026-06-17）→ Sandbox Preview（2026-07-23） | 检查/门禁/发布/恢复 | research-aws-cicd-trends-2024-2026（F1-F9） | GA / Preview | 高 | 效果数值厂商自述（G5） |
| C3 | Harness 智能化能力从"2024-2025 的传统 CI 基础设施优化"演进为"2026 的 Agent 执行 + AI 原生 DLC"：Worker Agents GA（2026-06-30）、AI Test/AI Evals/AI SRE、Agent DLC | 构建/跨阶段 | harness-company/fact-table-2024-2026（2024 逐月、Worker Agents GA、Agent DLC 2026-07） | GA / Beta 混合 | 高 | Agent DLC 逐项状态未逐项核验 |
| C4 | OpenAI 智能化能力从"Codex 终端 Agent 补代码"演进为"Symphony 常驻编排器控制 CI"：issue 追踪器变控制平面，Agent watch CI/rebase/重试 flaky/护送合并 | 跨阶段/门禁 | research-openai-anthropic-cicd-trends（O5-O8） | Experimental | 高（存在性）/厂商自述（500%） | 效果独立验证缺失 |
| C5 | Anthropic 智能化能力从"沙箱内执行"演进为"审批自动化 + 可恢复执行环境"：sandbox（2025-10-20 Beta）→ auto mode（2026-03-25 GA）→ Managed Agents（2026-04-08 GA） | 门禁/构建/恢复 | research-openai-anthropic-cicd-trends（A1-A5、A9-A10） | GA / Beta 混合 | 高（存在性）/厂商自述（指标） | auto mode headless 数据未公开 |
| C6 | 字节智能化能力从"传统流水线 + 测试工具"演进为"AI 应用部署 + Agent 运行时部署"：火山引擎 CP AI 应用部署（2024-04 邀测→2025-03 GA）、AgentKit 部署任务（2026-03）；SE Lab 研究从 Fastbot2 转向 Trae/MarsCode Agent | 部署/检查/测试 | research-bytedance-cicd-2024-2026-trends（F8-F9、F16-F19） | GA / 研究混合 | 高（产品）/厂商自述（研究数值） | 内部平台无披露（G2） |
| C7 | Microsoft 智能化能力从"确定性扫描门禁"演进为"Copilot 评审/修复进入 Azure Repos"：GHAzDO 三件套 GA → Copilot Code Reviews（2026 Q2 Limited Preview）→ Copilot Autofix（Limited Preview） | 检查/门禁 | research-github-microsoft-cicd-trends-2026-08-07（Sprint 271/275） | Limited/Public Preview 为主 | 高 | MCP 开源属性 unverified |

### 维度 2：环节扩散（智能化先覆盖哪、后覆盖哪）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C8 | 智能化最先成熟于"检查/评审"环节，随后扩散到"构建修复/门禁"，最后才是"发布准备/恢复"：2026 年 GA 的智能化能力集中在检查/门禁与受控执行（Copilot Review GA、DevOps Agent production ops GA、Worker Agents GA、auto mode GA）；发布准备多 Preview（AWS release readiness、Azure Copilot Reviews）；恢复闭环最少 | 各底稿状态列 | 分布证据 | 高 | "环节密度"是证据数量而非成效证明 |
| C9 | 发布端自治在 2026 年仍被"边界拦截"：GitHub "PR 永不自动合并"、AWS "审查不等于合并/部署授权"、Anthropic auto mode 拦截"直接推 main/绕过 pre-check 部署"、OpenAI 证据只到 PR 合并 | 各底稿 | 官方明确边界 | 高 | 无 |
| C10 | 恢复环节智能化最薄：AWS 事件调查 GA 但默认非突变（只读为主，可建 ticket/support case）；Anthropic Managed Agents 提供可恢复执行环境（cattle）但属 Agent 运行层而非发布恢复；无一家公开"生产发布端到端自治" | 各底稿 | 分布证据 | 高（缺口部分） | 发布/恢复端到端证据缺失（G7） |

### 维度 3：自治等级演进（只读 → 受控执行 → 受控合并）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C11 | 自治等级演进有共同路径：只读/调查（2024-2025 主流）→ 沙箱内受控执行（2025-2026）→ 边界内受控合并（2026，仅部分公司） | gh-aw 只读默认+Safe Outputs；AWS Sandbox 只读代理；Anthropic 双边界沙箱；OpenAI 护送合并 | 混合 | 高（机制）/中（演进推断） | 演进路径属分析推断 |
| C12 | 沙箱/凭据外置是获得执行权的共同前提：GitHub gh-aw sandbox+零密钥、AWS Sandbox（Lambda MicroVM+只读代理）、OpenAI Codex 默认无网络、Anthropic 凭据永不进沙箱 | 各底稿 | GA/Preview 混合 | 高 | 无 |
| C13 | 审批机制从"人工逐个审批"演进为"模型分类器/策略门禁"：Anthropic auto mode（93% 批准率背景下）、GitHub Ruleset+required checks、AWS required status check | auto mode 全文；AWS release readiness 文档；GitHub Ruleset 见仓库既有证据（github-agentic-workflows / aws-microsoft-intelligent-cicd） | GA/Preview | 高（机制）/厂商自述（指标） | auto mode 指标厂商自述 |

### 维度 4：产品状态转正轨迹（Research → Preview → GA）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C14 | 2026 年是智能化 CI/CD 密集转正年：gh-aw 转 Public Preview（2026-06-11）、Worker Agents GA（2026-06-30）、AgentCore Managed Harness GA（2026-06-17）+ Runtime instances GA（2026-08-06）、Copilot Code Review GA（2026-07-29） | 各底稿 | 转正证据 | 高 | 部分 changelog 仅列表页 |
| C15 | AI 公司产品多停留在 Experimental/Pilot/Research：Symphony Experimental、Tax AI Pilot、sandbox Beta、harness 研究 Research | research-openai-anthropic-cicd-trends | 状态证据 | 高 | 无 |
| C16 | 智能化产品线同样存在生命周期收窄：字节构建加速下线（2025-09-25）、AWS CodeGuru 停新（2025-11-07）、Q Developer IDE EoS（2027-04-30）、Harness DLite 撤销 | 各底稿 | 生命周期证据 | 高 | 无 |

### 维度 5：执行形态演进（终端 CLI → 编译进流水线 → 常驻编排器）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C17 | 执行形态出现三条演进路线：① 编译进流水线（GitHub gh-aw Markdown→Actions、Harness Worker Agents 作 Pipeline Step）；② 旁路 Agent 服务（AWS DevOps Agent 不内建 CI 引擎）；③ 常驻编排器（OpenAI Symphony issue 追踪器即控制平面、Anthropic Managed Agents 常驻 harness） | 各底稿 | GA/Preview/Experimental 混合 | 高（机制） | 无 |
| C18 | 工作流策略入库是共同演进方向：OpenAI WORKFLOW.md、GitHub gh-aw Markdown+frontmatter、Anthropic AGENTS.md/Rules/Hooks、Harness Pipeline YAML Git 化 | 各底稿 + 仓库既有 Claude Code 文档证据（code.claude.com/docs：Hooks/Rules/AGENTS.md） | 混合 | 中高（方向推断） | Anthropic 侧来自既有专题（claude-code-container-use）非本次 2026-08-07 研究；格式无统一标准 |

### 维度 6：治理演进（从治理流程 → 治理 Agent 身份与持续行为）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C19 | 治理对象从"流程/权限"演进为"Agent 身份与持续行为"：2024 年治理的是分支保护/required checks/人工审批；2025-2026H1 治理 Agent 的凭据与执行边界（scoped token、凭据外置、只读代理）；2026 治理 Agent 的持续行为与状态化动作（temporal policies、rate limiting、复合身份、AI 生成资产标记、AgentTrace） | AWS F14（temporal/rate limiting）+ F6（权限护栏）；Harness 事实表（Runtime Token=grant∩RBAC、OPA 生成标 ai_generated:true、AgentTrace）；gh-aw（只读默认+Safe Outputs+零密钥）；仓库 llm-era-cicd 专题（Agent Audit/Composite Identity） | GA/Preview 混合 | 高（机制）/中（阶段归因） | 复合身份/AgentTrace 的审计级证据未逐项核验 |
| C20 | Agent 凭据模型演进：从共享/长生命 PAT 到"免 PAT 自动化身份 + scoped token + 凭据外置"：Copilot CLI 在 Actions 免 PAT（2026-07-02）、Entra workload identity（2026-06-17 Preview）、Anthropic 凭据永不进沙箱+scoped git token、Harness Runtime Token=声明 grant∩触发人 RBAC 交集 | GitHub-Microsoft 底稿；Anthropic A3/A9；Harness 事实表 | GA/Preview/GA 混合 | 高 | Entra workload identity 仍 Preview |
| C21 | 状态化授权成为 2026 新增治理原语：AWS temporal policies 要求工作流顺序、tool 参数恰等于先前调用输出、特权操作前人工批准、数据新鲜度；rate limiting 按 OAuth/IAM 作用域限制 requests/tokens/concurrent | AWS F14 | 2026-08-06 公告 | 强（公告存在性） | 公告未给独立 GA/Preview 标签 |

### 维度 7：验证机制演进（权威保持确定性，前置验收智能化）

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C22 | 验证最终权威未转移：确定性 gate 仍是放行者，智能化演进的是"前置验收"：GitHub Ruleset/Required Check 持有合并权威；AWS BLOCK 需映射 required check 才阻断合并；Harness OPA/Approval 是硬 Gate、AI Rules 只是软引导 | GitHub 底稿（"PR 永不自动合并"）；AWS F8/F 边界；Harness 事实表（OPA 硬门禁） | GA/Preview | 高 | 无 |
| C23 | 前置验收从"人工逐个审批"演进为"模型分类器 + eval 门禁 + 确定性重跑"三层组合：Anthropic auto mode（单 token 快速过滤+CoT 复核）→ OpenAI Tax AI（targeted eval+回归套件+grader）→ AWS release readiness（托管验证环境 build/run/test+护栏）→ GitHub Agentic Autofix（修复+重跑验证） | Anthropic A4、OpenAI O14、AWS F8、GitHub autofix 条目 | GA/Preview/Pilot 混合 | 高（机制存在性） | eval 门禁成效均为厂商自述 |
| C24 | "Agent 不得自证"成为共同约束：Anthropic 强化 harness 自评/eval 分离是改善关键杠杆；Tax AI 的 grader 独立于被修的 Codex；GitHub Safe Outputs 由独立 Job 持最小写权限执行；AWS 审阅护栏由外部规则执行 | Anthropic A8、OpenAI O14、gh-aw Safe Outputs、AWS 护栏 | 混合 | 中高 | 部分为厂商自述（A8 成本/时长） |

### 维度 8：生命周期收窄——"入口/单点收窄"vs"能力死亡"须区分

| # | Claim | 证据 | 状态 | 强度 | Gap |
|---|---|---|---|---|---|
| C25 | 2024-2026 的下线/收窄多为"独立单点工具型"或"入口型"，而非"平台控制面能力"：字节构建加速（独立分布式编译，2025-09-25 下线）、AWS CodeGuru Reviewer（独立代码评审单点，2025-11-07 停新关联）、Amazon Q Developer IDE 插件（IDE 入口，EoS 2027-04-30，Q Developer 未整体下线）、Harness DLite（执行引擎实现替换）、字节 CP V1（版本迁移）、WIF Azure DevOps issuer（身份机制迁移） | 字节 F4/F3；AWS F21/F22；Harness 事实表（HOSTED_BUILDS_SUNSET_DLITE）；GitHub-Microsoft 底稿（WIF） | 已下线/收窄 | 高（事件本身） | 下线原因多数官方未详述；样本小 |
| C26 | 同期转正/存活的智能化能力多为"嵌入平台控制面/治理闭环"型：gh-aw（编译进 Actions）、Worker Agents（Pipeline Step）、Code Quality（Ruleset 门禁闭环）、auto mode/Managed Agents（产品内建）、AgentCore（平台底座）——它们改变调度/治理/验证而不只是单环节效率 | 各底稿 + 仓库既有 Code Quality GA（2026-07-20） | GA/Preview | 高 | Code Quality 来自仓库既有证据非本次底稿 |

## 二、状态更新标注（相对仓库既有结论）

| 既有结论 | 复核结果 | 状态 |
|---|---|---|
| GitHub Agentic Workflows 2026-06-11 Public Preview | 成立；补充 2026-02-13 起由 GitHub+Microsoft Research+Azure Core 联合开发 | 状态更新 |
| AWS DevOps Agent Production Operations GA（2026-03-31） | 成立；Release Management 公告 URL 修复为已核验 | 状态更新 |
| AgentCore Managed Harness GA（2026-06-17） | 成立；新增 Runtime instances GA、temporal policies | 状态更新 |
| Harness Worker Agent 权限文档冲突 | 冲突已消除（7/23 更新），核心结论仍成立 | 状态更新 |
| CodePipeline/CodeBuild 为确定性机制 | 成立；追加 CodeDeploy——作为"智能化走旁路"的背景约束引用，非主线 | 状态更新 |
| Transform → AgentCore 构建关系 unverified | 仍 unverified，禁止画边 | 保持阻塞 |
| Microsoft 2025-2026 仓库迁移到 GitHub | 无一手来源复核 | 证据缺口 |

## 三、各公司智能化演进快照（2024-01 → 2026-08）

| 公司 | 2024 起点 | 2025 中间态 | 2026-08 状态 | 演进主线 |
|---|---|---|---|---|
| GitHub | Copilot 助手 + 供应链签名起步 | gh-aw 技术预览（2026-02）、Copilot Agent Mode GA | gh-aw Public Preview、Code Review GA、Agentic Autofix Preview | 把 Agent 编译进 Actions 控制面 |
| Microsoft | GHAzDO 确定性扫描 | Remote MCP Server Preview（2026-03） | Copilot Code Reviews Limited Preview、Autofix、Entra workload identity | 把 GitHub 系智能能力移植进 Azure DevOps |
| AWS | DevOps Agent 调查（只读） | Production ops GA（2026-03） | Release Management Preview、Sandbox Preview、AgentCore 平台 GA | 从只读调查向发布前审查/受控执行前移 |
| Harness | 传统 CI 基础设施优化 | Test Intelligence 转 GA | Worker Agents GA、DAG、Agent DLC | 编排与智能同平台 |
| 字节 | 传统流水线 + 测试工具论文 | AI 应用部署 GA（2025-03）、Trae 开源 | YAML/OAM、AgentKit 部署、SE Lab 研究转向 Agent | 产品化交付 + 研究资产双轨 |
| OpenAI | Codex 终端 Agent | Symphony 开发中 | Symphony Experimental、Windows sandbox GA、Tax AI Pilot | 任务追踪器即控制平面 |
| Anthropic | Claude Code 终端 Agent | sandbox Beta（2025-10） | auto mode GA、Managed Agents GA | 审批与执行环境自动化 |

**判断（分析推断）**：智能化 CI/CD 在 2024-2026 沿"能力形态深化 + 环节扩散 + 自治抬升 + 状态转正 + 形态迁移 + 治理演进 + 验证前置智能化 + 生命周期分界"八条线同时演进，其中"检查/门禁先成熟、发布/恢复被边界拦截"是跨公司最稳定的演进事实。
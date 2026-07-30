---
title: 大模型时代的 CI/CD 基础设施案例地图
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/case-map
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
confidence: high
---

# 案例地图

## 跨基础设施案例

| 基础设施 / 案例 | 截至 2026-07-28 的状态 | 可证实变化 | 变化类型 | 不能推出 |
|---|---|---|---|---|
| GitHub Copilot Cloud Agent | 核心 Coding Agent GA；部分 API/第三方 Agent 为 Public Preview | Issue/Agents UI/PR 评论成为任务入口；Actions 临时环境运行；以 Branch/PR 交付并请求 Review | **新增 Agent 工作负载与 Repository 控制面** | 所有 Agent 能力均 GA；Agent 内部测试等于 Required Checks |
| GitLab Duo Agent Platform / Custom Flows | 平台 18.8 GA；Custom Flows 19.2 GA；部分 Audit/Governance 仍 Beta/Experiment | Issue/MR/评论/Pipeline Event 触发 Flow；Runner 执行；Composite Identity 与服务账号归因 | **新增 Agent Flow 与委托身份** | Prompt/Tool 全量审计已成熟；所有版本与套餐可用 |
| GitHub Agentic Workflows | Public Preview（2026-06-11） | 自然语言 Markdown 编译为标准 Actions YAML；默认只读、Safe Output、Firewall、Threat Detection；Actions+AI 双计量 | **新增 Repository Automation 决策层** | 取消 Actions/YAML/Runner/Policy；可直接安全合并或发布 |
| Buildkite AI Agents in Pipelines | 已发布文档能力；Model Provider 当前仅 Anthropic | MCP 读取 Build/Log、触发 Run；Model Provider 和 Plugin 将 Agent 放入 Pipeline Step | **Agent 接口与执行层** | MCP 即授权；所有模型与安全边界一致 |
| CircleCI Chunk Sidecar / Microbuild | Sidecar 已可用；效果数字为厂商工程自述 | 快照、增量同步、直接反馈给 Agent 的低延迟验证内环 | **新增验证运行时** | 取代正式 CI；具备完全 Hermetic Reproducibility；厂商效率数字可外推 |
| Harness Autonomous Worker Agents / CI Autofix | 厂商称已可用；部分细粒度能力需按账户/Feature Flag 核验 | Agent 作为 Pipeline Step；失败日志→修复→重跑到成功或 Max Turns；非 Root、Broker、Egress、短期 Token | **新增 Agent Step 与安全执行壳** | “Autonomous”意味着自动合并/部署；厂商红队结果等于独立验证 |
| Nx Self-Healing CI | 正式文档能力；具体套餐与部署需核验 | Project Graph/Task Metadata 提供上下文；按 Task/分支限制修复；验证后建议或白名单 Auto-apply 到 PR Branch | **Build Graph 上的 Agent 闭环** | 全仓生产自愈；重跑失败 Task 等于完整业务验证 |
| Nx Distributed Execution / Bazel RBE & Remote Cache | 既有、已发布能力 | 按影响范围和任务图分发；跨机器复用产物；提供统一执行环境 | **既有能力升值** | Agent 时代的新发明；缓存天然正确或无污染 |
| Cloudsmith MCP | 2026-03 初始版本可用，未声明 GA | CLI/API 暴露为本地 MCP；查询漏洞/版本和管理部分制品；多 Profile 隔离 | **制品仓 Agent 接口** | 允许破坏性动作；可由 MCP 激活 Policy；已证明自动晋级 |
| JFrog MCP / Agent Packages | MCP Server Beta；Skills/Agent Packages 部分 Open Beta | Repository/Xray/SBOM/Evidence/Release/Identity 工具；Skills/Plugins/Prompts/MCP 本身进入私有仓 | **供应链行动面与新资产类型** | 全部能力 GA；Tool 可见即可授权；Agent 资产已形成统一跨厂商标准 |
| Sonatype MCP / Firewall API | MCP 可用；Firewall API 为既有能力 | 依赖风险与推荐版本前移到 Agent 选包时点；Firewall 可 Quarantine/Waive | **实时供应链情报前移** | MCP 已能发布/删除/豁免；Bulk Waiver 有内置后续审批 |
| GitHub Artifact Attestations + SLSA v1.2 | Attestations GA；SLSA v1.2 Approved | Builder 生成绑定 Repo/Workflow/Commit/Digest 的 Provenance，可关联 SBOM并在下游验证 | **既有信任原理强化与产品化** | Attestation 等于安全；启用一个 Action 自动达到 SLSA Build L3 |

## 变化类型归纳

| 变化类型 | 代表案例 | 成熟度判断 |
|---|---|---|
| Agent 原生任务与协作控制面 | GitHub Cloud Agent、GitLab Custom Flows | **已进入 GA，但扩展能力仍混合 Preview。** |
| Agent 原生 Pipeline/Workflow 层 | GitHub Agentic Workflows、Buildkite、Harness | **可用与 Preview 并存，机制真实，行业效果不足。** |
| 高频验证运行时 | CircleCI Sidecar、Harness CI Autofix、Nx Self-Healing | **产品化开始，需严格区分内环与正式外环。** |
| 机器可调用供应链接口 | Cloudsmith、JFrog、Sonatype | **查询最成熟，写入/治理动作边界差异大。** |
| Agent 资产供应链 | JFrog Agent Packages / Skills Repository | **早期，单厂商样本，标准与治理模型未稳定。** |
| 构建与信任基本功升值 | Nx/Bazel Graph/Cache/RBE、SLSA/Attestation | **能力成熟，但必须重新按 Agent 负载与威胁模型配置。** |

## 横向判断

1. **最先成熟的是“Agent 产生候选、平台保留接受权”。** 代码仓中的 PR/MR、流水线的 Required Check、制品链的 Attestation/Policy 都沿用这个结构。
2. **最清晰的新能力是 Interface 与 Runtime。** MCP/Skill/Agent API 解决可发现和上下文，Sandbox/Runner/Sidecar 解决执行。
3. **最不成熟的是高风险行动授权。** Promotion、Policy Activation、Waiver、Delete、Token/OIDC/Role 管理尚无跨平台统一批准语义。
4. **最容易被误判的是既有构建能力。** Cache、RBE、Incremental Build、Provenance 不是大模型发明，但没有它们，Agent 的并发、反馈和可信性成本会迅速失控。

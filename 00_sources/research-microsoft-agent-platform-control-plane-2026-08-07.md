---
title: Microsoft 侧 Agent 生产控制面对等能力研究报告（与 Amazon Bedrock AgentCore 对标）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/microsoft
  - company/azure
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
date: 2026-08-07
confidence: high-for-mechanism-and-status; medium-for-Agent-Service-exact-GA-announcement-date-unverified
source_policy: 仅使用 Microsoft Learn、Azure / GitHub 官方文档与官方博客（Microsoft Learn search API、GitHub docs search API 作为检索手段）；所有链接于 2026-08-07 访问；无法核验项标记 unverified。
---

# Microsoft 侧 Agent 生产控制面对等能力研究报告（与 Amazon Bedrock AgentCore 对标）

> 研究任务：核验 Microsoft（Azure/GitHub）侧是否存在与 Amazon Bedrock AgentCore 对等的"Agent 生产控制面"能力，按 AgentCore 六域口径（编排与运行 / 工具与行动 / 状态与上下文 / 观测与质量 / 资产与经济 / 平台级）做映射。AgentCore 六域参考：Harness/Runtime、Gateway/Identity/Policy、Memory、Observability/Evaluations/Optimization、Agent Registry/Payments、平台级。

## 1. 检索范围与访问时间

- 访问时间：**2026-08-07**（完整会话，单日完成；Agent 365 许可与 GA 细节在 2026-08-07 追加核验）
- 来源范围：仅 Microsoft / GitHub 官方一手来源
  - Microsoft Learn 官方文档（`learn.microsoft.com`，含 Foundry Agent Service、Foundry Control Plane、Microsoft Agent 365、Copilot Studio、Entra Agent ID、Agent Framework、Observability、RBAC、AI Gateway/APIM 等 20+ 页面）
  - Microsoft Security Blog 官方公告（Agent 365 GA，2026-05-01）
  - Foundry 官方 what's-new 页面、Agents hub 页面
  - GitHub 官方文档（`docs.github.com/copilot`，MCP 管理、Agentic Workflows、AI Controls、Copilot SDK）与官方仓库（`github/github/copilot-sdk`）
- 检索方法备注：使用 Microsoft Learn search API（`learn.microsoft.com/api/search?search=...&locale=en-us&$top=N`）与 GitHub docs search API 定位新路径；旧 URL（如 `/azure/ai-foundry/agent-service/overview`、`/azure/foundry/whats-new`）已 404，通过 search API 找到新路径。
- 重要前置事实：Azure AI Foundry 已更名为 **Microsoft Foundry**（文档域 `/en-us/azure/foundry/`），原文档中大量产品名已相应变化（如角色名 Azure AI User → Foundry User）。

## 2. 结论摘要

**结论：Microsoft 侧存在与 AgentCore 高度对等的统一 Agent 生产控制面，但不是"单一产品"，而是"三层收敛"的分散—收敛混合结构（是，但分散分层）。**

- **运行/构建控制面**：`Microsoft Foundry Agent Service`（GA，精确公告日期未核验）+ `Foundry Control Plane`（多数特性 preview）—— 与 AgentCore 的 Runtime/版本端点、工具身份、观测评估优化闭环一一对应，是**最接近 AgentCore 的对象**。
- **组织治理控制面**：`Microsoft Agent 365`（GA，2026-05-01）—— 官方营销与产品口径即 **"The Control Plane for Agents"（Agent 控制平面）**，提供 Observe/Govern/Secure 三支柱，支持将外部平台（含 **Amazon Bedrock**、Google Cloud）的 agent 同步进统一注册表。
- **身份控制面**：`Microsoft Entra Agent ID`（GA，2026-04）—— agent identity + blueprint 体系，为上述两层提供身份地基。
- **GitHub 侧**：`GitHub AI Controls`（企业 agent 治理）+ `GitHub Copilot SDK`（GA，agent 运行时）+ `GitHub Agentic Workflows`（public preview，Actions 作为 agent 运行环境）—— 是**编码类 agent 的独立治理面**，与 Foundry 平台相对分离。

**与 AgentCore 六域的对应关系**：六域全部能找到 Microsoft 侧映射，但"质量闭环"（trace→eval→optimize→immutable version）完整存在于 Foundry Agent Service；"行动闭环"由 Agent Service 运行隔离 + Entra Agent Identity + MCP 工具授权组成。**相对最弱的两点**：① Agent 应用发布后不支持流量灰度/版本分流（100% 切流量）；② 不存在与 AgentCore Payments（Agent 变现/订阅计费）直接对等的机制，以资源消耗计费 + 按用户许可代替。

## 3. 按六域映射的能力对照表

### 3.1 域一：编排与运行（Harness / Runtime / version-endpoint）

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| Agent Harness | ① Foundry Agent Service：Prompt agents（配置化、全托管）+ Hosted agents（容器化 BYO 代码）② Agent Framework Harness（HarnessAgent/create_harness_agent）③ Copilot Studio 三种 harness（GitHub Copilot / standard / Copilot chat） | ① 托管运行、自动扩缩、会话生命周期 ② SDK 内建 plan/execute、工具审批、内存、OTel ③ 低代码托底 | GA / 开源 GA / GA | [Foundry Agent Service overview](https://learn.microsoft.com/en-us/azure/foundry/agents/overview)、[Agent Framework Harness](https://learn.microsoft.com/en-us/agent-framework/agents/harness)、[Copilot Studio overview](https://learn.microsoft.com/en-us/microsoft-copilot-studio/fundamentals-what-is-copilot-studio) |
| Runtime 隔离 | Hosted agents 每会话 **VM 隔离沙箱**；BYO VNet；prompt agents 支持虚拟网络 | 每会话独立 sandbox + 持久 `$HOME`/`/files`；15 分钟空闲回收、30 天会话上限；CPU/内存 0.5vCPU-2vCPU | GA | [Hosted agents](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/hosted-agents) |
| 运行协议 | Responses / Invocations / Invocations(WS) / Activity / A2A | 单一超类端点，多协议可并存 | GA / A2A（preview） | [Hosted agents](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/hosted-agents) |
| immutable version | 每次保存/创建即产生**不可变 agent version**；环境变量逐版本不可变；版本历史、回滚、对比 | 端点一次只服务一个版本，100% 流量路由，**不支持流量拆分/灰度** | GA | [Dev lifecycle](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/development-lifecycle)、[Hosted agents](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/hosted-agents) |
| version-endpoint / 发布 | **Agent Application** 资源：稳定 endpoint + Deployment 子资源（引用版本）+ start/stop + 独立 Entra agent identity | ARM 资源、Azure Policy 可治理、发布后 identity 需重新授权；Agent Applications 不注册进 Entra agent registry | GA（部分 API 用 preview 版本） | [Agent applications](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/agent-applications) |

### 3.2 域二：工具与行动（Gateway / Identity / Policy / Browser / Code Interpreter / Web Search）

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| Gateway | 无同名对象；对等物为 **Toolbox（统一 MCP 端点）** + **Foundry MCP Server（mcp.ai.azure.com）** + **AI Gateway（APIM）** | Toolbox 可版本化、单 MCP 端点供多 agent；MCP 工具经 APIM 网关做限流/IP 过滤/审计 | Toolbox GA；Foundry MCP Server preview；AI gateway preview | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog)、[Foundry MCP](https://learn.microsoft.com/en-us/azure/foundry/mcp/get-started)、[Govern MCP via AI gateway](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/governance)、[AI gateway capabilities](https://learn.microsoft.com/en-us/azure/api-management/genai-gateway-capabilities) |
| Identity | **Microsoft Entra Agent ID**：agent identity + agent identity blueprint；OAuth2.0 OBO（代表用户）+ client credentials（自主行动）；联邦凭据（managed identity） | 四步 token 交换（blueprint 认证→agent token→scoped token→调用）；每 agent 独立身份；发布后换新身份需重授 RBAC | **GA（2026-04）** | [Agent identity concepts](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/agent-identity)、[Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/)、[Entra what's-new（GA 公告）](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new) |
| Policy | ① MCP 工具 `require_approval`（never/always）② AI Gateway 策略（限流/IP 过滤/敏感头清除）③ Azure Policy + RBAC ④ guardrail policy（内容安全、XPIA 防护）⑤ Conditional Access for agents | 工具连接级、网关级、资源级、身份级多层 | 工具审批 GA；网关策略 preview | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog)、[Govern MCP](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/governance)、[Control Plane](https://learn.microsoft.com/en-us/azure/foundry/control-plane/overview) |
| Browser | Browser Automation（preview）、Computer Use（preview） | 内置托管工具 | preview | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog) |
| Code Interpreter | Code Interpreter（沙箱 Python）、Custom Code Interpreter（preview） | 内置托管执行 | GA / preview | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog) |
| Web Search | Web search / Grounding with Bing | 内置工具、带内联引用 | GA | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog) |
| 工具接入（MCP/OpenAPI） | MCP servers（key / Entra managed identity / OAuth 透传）、OpenAPI 工具、A2A（preview）、private tool catalog、Toolbox | 连接级凭证管理；结构化输入可在运行时覆盖工具参数；curate 意图化 toolbox（preview） | MCP GA；A2A preview | [Tool catalog](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/tool-catalog)、[Toolbox（preview）](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/toolbox) |

### 3.3 域三：状态与上下文（Memory / session filesystem）

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| Memory | Foundry **Memory Store（preview）**：user profile / chat summary / procedural 三类长期记忆 | 提取→合并→检索；item 级 CRUD、store 级 TTL、remember/forget 命令；需 Azure OpenAI chat+embedding 模型 | **preview** | [What is Memory](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-memory) |
| session filesystem | Hosted agents 每会话持久 `$HOME` + `/files` 端点 | 会话级状态跨 idle 恢复；20GiB 磁盘预算（1vCPU+）；conversation ID 为持久会话记录 | GA | [Hosted agents](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/hosted-agents) |
| 跨会话记忆（GitHub 侧） | Copilot Memory（仓库/个人事实） | 补充说明，非平台级 | 未核验具体状态 | [GitHub Copilot concepts](https://docs.github.com/en/copilot/concepts/agents) |

### 3.4 域四：观测与质量（Observability / Evaluations / Optimization / Insights）

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| Observability | OTel tracing → Application Insights；多 agent OTel 语义约定（与 Cisco 联合）；fleet 监控 dashboard | trace 捕获输入/输出/工具调用/延迟/token；展示在 Foundry 门户与 App Insights | **tracing 对 prompt/hosted agents GA**；Trace Replay（preview） | [Agent tracing overview](https://learn.microsoft.com/en-us/azure/foundry/observability/concepts/trace-agent-concept)、[Trace setup](https://learn.microsoft.com/en-us/azure/foundry/observability/how-to/trace-agent-setup)、[Trace Replay（preview）](https://learn.microsoft.com/en-us/azure/foundry/observability/how-to/trace-agent-replay) |
| Evaluations | Foundry evaluation：rubric evaluator（LLM judge）+ agent evaluators（task adherence、tool call success、intent resolution）+ safety + 自定义；合成数据集、trace 转数据集（preview）；**集成 GitHub Actions 作 CI/CD 质量门** | 测试集→运行→逐行评分；continuous evaluation 用于生产监控 | 评估 preview（部分功能按区域受限）；traces-to-dataset preview | [Evaluate AI agents](https://learn.microsoft.com/en-us/azure/foundry/observability/how-to/evaluate-agent)、[traces-to-dataset（preview）](https://learn.microsoft.com/en-us/azure/foundry/observability/how-to/traces-to-dataset) |
| Optimization | **Agent Optimizer（preview）**：评估驱动闭环（baseline→candidates→rank→apply） | 自动改进 instructions/skills/工具描述/模型选择；prompt agent 可提升为新版本 | **preview** | [Agent optimizer](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/agent-optimizer-overview) |
| 质量闭环（trace→eval→optimize→version） | 完整闭环存在于 Agent Service 生命周期（Create→Test→Trace→Evaluate→Optimize→Publish→Monitor） | 与 AgentCore"质量闭环"叙述同构 | 部分 preview | [Dev lifecycle](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/development-lifecycle) |
| Insights | Control Plane：cluster analysis、AI Red Teaming Agent、fleet 健康分/成本/合规 KPI、推荐（prompt/模型升级） | 关联 trace+eval+告警 | preview | [Control Plane](https://learn.microsoft.com/en-us/azure/foundry/control-plane/overview) |

### 3.5 域五：资产与经济（Agent Registry / Payments）

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| Agent Registry | ① **Foundry Control Plane Assets**（订阅级 agent/model/tool 清册）② **Microsoft 365 admin center Agent Registry**（租户级清册，含风险/ownerless/shadow 检测）③ **Agent 365 Registry Sync（preview）**：把 **Amazon Bedrock / Google Cloud / 其他平台** agent 同步进统一注册表 ④ Entra Agent ID 全租户 agent identity 清单 | 多级登记；风险聚合（Entra/Defender/Purview）；block/unblock；导出 | Registry sync preview | [Control Plane](https://learn.microsoft.com/en-us/azure/foundry/control-plane/overview)、[M365 Agent Registry](https://learn.microsoft.com/en-us/microsoft-365/admin/manage/agent-registry)、[Agent 365 Registry sync](https://learn.microsoft.com/en-us/microsoft-agent-365/admin/agent-registry)、[Agent 365 GA 公告](https://www.microsoft.com/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/) |
| Payments | **无直接对等**。Microsoft 以资源消耗计费（inference+tools+container compute）+ 按用户许可（Agent 365 按用户 $15/user/month，或含于 M365 E7；Copilot Studio Copilot Credits 按用量/许可；GitHub 用 AIC，1 AIC=$0.01） | publisher-pays 模型（Agent Application 基础资源 publisher 承担）；无 Agent 变现/订阅计费产品 | — | [Agent applications（计费 FAQ）](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/agent-applications)、[Agent 365 overview](https://learn.microsoft.com/en-us/microsoft-agent-365/overview)、[Agent 365 GA 公告](https://www.microsoft.com/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)、[GH Agentic Workflows](https://docs.github.com/en/copilot/concepts/agents/about-github-agentic-workflows) |

### 3.6 域六：平台级

| AgentCore 能力 | Microsoft 对等能力 | 机制 | 状态 | 来源 |
|---|---|---|---|---|
| 平台级统一控制面 | **Microsoft Agent 365**（Observe/Govern/Secure）+ **Foundry Control Plane**（Overview/Assets/Compliance/Quota/Admin）+ GitHub **AI Controls** | Agent 365 用于全组织跨平台 agent 治理；Foundry Control Plane 用于订阅内 build→production fleet；AI Controls 用于 Copilot coding agents 企业策略 | **Agent 365 GA（2026-05-01）**；Foundry Control Plane 特性 preview；AI Controls 已发布（具体 GA 日期未核验） | [Agent 365 overview](https://learn.microsoft.com/en-us/microsoft-agent-365/overview)、[Control Plane](https://learn.microsoft.com/en-us/azure/foundry/control-plane/overview)、[GH Agent management](https://docs.github.com/en/copilot/concepts/agents/enterprise-management) |
| 关键里程碑 | Entra Agent ID GA 2026-04；Agent 365 GA 2026-05-01；Foundry Agent Service 文档无 preview 横幅（隐含 GA，精确公告日期未核验） | 与 AgentCore GA 2025-10-13 相比，Microsoft 侧"平台级控制面"产品化晚约 6-7 个月 | — | [Entra what's-new](https://learn.microsoft.com/en-us/entra/fundamentals/whats-new)、[Agent 365 overview](https://learn.microsoft.com/en-us/microsoft-agent-365/overview) |
| 本地/终端 agent 治理 | Agent 365 + Defender + Intune：发现本地 agent（OpenClaw 先行，GitHub Copilot CLI、Claude Code 随后）、Shadow AI 页、Intune 策略阻断、Defender 运行时阻断（编码 agent） | 设备级发现 + 策略 + 运行时防护 | 2026-06 起 public preview；OpenClaw 首支持 | [Agent 365 GA 公告](https://www.microsoft.com/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/) |
| 受管运行环境 | **Windows 365 for Agents**（public preview，仅美国） | 面向 agent 的 Cloud PC，Intune 管理，与员工同级身份/安全/管理控制 | public preview | [Agent 365 GA 公告](https://www.microsoft.com/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)、[Windows 365 for Agents](https://learn.microsoft.com/en-us/windows-365/agents/introduction-windows-365-for-agents) |

## 4. 分散方案的治理边界

Microsoft 无单一产品承担全部 AgentCore 职责，其架构是"**身份统一、运行分散、治理两级收敛**"：

**（1）身份如何统一落地**
- 所有代理身份收敛到 **Entra Agent ID**：Foundry 自动为每 agent 创建 identity + blueprint；Copilot Studio 与第三方（含 AWS Bedrock、n8n 等）agent 也可接入 Agent ID（[Entra Agent ID](https://learn.microsoft.com/en-us/entra/agent-id/)）。
- 权限落地链路：agent identity 需在**下游资源**按 RBAC 单独授权（如 Storage Blob Data Contributor）；发布后 identity 更换必须重新授权（项目共享 identity → 应用专属 identity）。调用方权限走 Agent Application 的 ARM RBAC（`/applications/invoke/action`）。身份级再加 Conditional Access / ID Protection / ID Governance。
- 许可边界：Entra Agent ID 适用于所有 Entra 客户；将 Entra 安全能力扩展到 agent 需 **M365 E7**（含 Agent 365 + Entra Suite）或 **E5 + Agent 365 许可**；无 E5/E7 时配 Agent 365 许可可单购 Conditional Access for agents（Entra P1）、ID Protection（P2）、ID Governance（P1）、Network controls（Entra Internet Access）（[Entra licensing](https://learn.microsoft.com/en-us/entra/fundamentals/licensing)）。

**（2）运行与质量边界**
- **Foundry Agent Service** 负责"行动闭环"（运行隔离、工具授权、身份委托）与"质量闭环"（trace→eval→optimize→immutable version→publish）。这是与 AgentCore 口径最对齐的层。
- **低代码层** Copilot Studio 负责业务 agent/harness；**SDK 层** Agent Framework（Semantic Kernel/AutoGen 合并）负责开源构建，可部署到 Foundry Hosted Agents 或自托管——治理仍由 Foundry/M365 平台承接。

**（3）组织治理两级收敛**
- **订阅/工程级**：Foundry Control Plane + AI Gateway（APIM）—— agent/model/tool 资产清册、Azure Policy 策略、Defender/Purview 信号、可**注册外部 agent**（run anywhere，经 APIM 代理并 block/unblock）。
- **租户/组织级**：Agent 365（M365 admin center）—— 全租户 agent 注册表、风险聚合（shadow/excessive permissions/prompt injection/sensitive data/Conditional Access 违规等）、ownerless 治理、Registry Sync（public preview）把 **Amazon Bedrock / Google Cloud** 的 agent 拉入统一治理，后续支持 start/stop/delete 生命周期治理（[Agent 365 GA 公告](https://www.microsoft.com/security/blog/2026/05/01/microsoft-agent-365-now-generally-available-expands-capabilities-and-integrations/)）。
- **GitHub 侧独立边界**：企业可通过 AI Controls 对 Copilot cloud agent/custom agents/third-party agents 做独立策略；MCP 策略+注册表（allowlist）控制工具访问；Copilot SDK 权限处理器控制工具调用；Agentic Workflows 在 Actions 中以**只读 token + safe outputs + 威胁检测**运行。GitHub 的治理与 Foundry 的治理**不自动连通**。

**（4）工具授权边界**：三层——工具连接级（key/Entra/OAuth 透传 + `require_approval`）、网关级（APIM 限流/IP/审计）、身份级（agent identity 的 RBAC/Conditional Access）。

## 5. 证据缺口与复核记录

| 缺口/待复核项 | 说明 | 处理 |
|---|---|---|
| Foundry Agent Service 明确的 GA 公告日期 | 本批页面未见"GA on 2026-XX-XX"字样；仅文档无 preview 横幅、含区域清单与定价页，隐含 GA。SDK 状态表显示大量包为 Stable。Foundry what's-new 页（`/azure/foundry/whats-new-foundry`）仅覆盖 2026-06，无历史归档 | 标注为"GA（公告日期未核验）"；如需精确日期需查 Foundry 发布公告/博客归档 |
| Azure AI Foundry → Microsoft Foundry 更名时间 | 未找到官方更名公告日期 | 未核验，仅记录现状 |
| "Entra Agent Registry"与"Agent Applications 不注册入 Entra agent registry" | Agent Service overview 将 Entra Agent Registry 列为分发渠道；agent-applications 页明确注明 Agent Applications 不注册其中 | 保留冲突，视为两套登记机制（分发 vs. 治理） |
| Payments 对等物 | 未检索到与 AgentCore Payments（agent 订阅/变现）直接对等的功能 | 判定为"无直接对等"，仅付费用量/许可 |
| GitHub 侧多项状态 | Copilot SDK 自述 GA（厂商自述）；Agentic Workflows 明确 public preview；AI Controls 具体 GA 日期未核验；GitHub MCP server GA 状态未核验 | 分别标注 |
| 工具审批 UX 细节 | `require_approval` 字段已确认存在，但未深挖审批交互文档 | 机制部分确认 |
| Copilot Studio harness 明细 | 仅基于 overview 页对 harness 的描述，未展开 harnesses-overview 文档 | 状态未核验 |
| Control Plane 多数特性 | 文档明确标注"（preview）"且仅限 Foundry（新）门户 | 全部按 preview 记录 |
| 流量分流 | Agent Application/Hosted endpoint 均只支持 100% 单版本路由，不支持分流 | 作为与 AgentCore 的差异点记录 |
| Agent 365 运行覆盖分级 | delegated-access agents（GA）、own-credentials agents（GA）、team workflow agents（public preview） | 已核验（GA 公告），按分级记录 |
| Agent 365 生态伙伴 | n8n、Kore.ai、Kasisto、Adobe、NVIDIA、Zendesk、Celonis 等为"Agent 365 可管理"生态 agent | 厂商自述，未验证第三方兑现程度 |

## 复核建议（面向主 Agent）

- 将"Microsoft 侧存在三层控制面（Foundry 运行控制面 / Agent 365 组织治理面 / Entra 身份面）"与 AgentCore"单产品双闭环"作为双公司对比的核心差异点。
- 特别留意 **Agent 365 Registry Sync 已原生支持 Amazon Bedrock**——这是微软在"跨云 agent 治理"上直接对标 AgentCore 生态的信号，可作为观点素材。
- Agent 365 定价 $15/user/month 或含于 M365 E7，可作为"治理能力按用户许可"的定价对照。
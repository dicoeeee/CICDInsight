---
title: 大模型时代的 CI/CD 基础设施企业演进手册
tags:
  - research/agentic-cicd
  - research/deep-dive
  - research/playbook
status: complete
as_of: 2026-07-28
topic_id: llm-era-cicd-infrastructure
confidence: medium-high
---

# 企业演进手册

> [!note] 性质
> 本文是基于 [[50_deepdives/llm-era-cicd-infrastructure/50_findings|Findings]] 的企业建议，不是厂商产品事实。阶段顺序表示依赖关系，不代表所有企业必须采用相同时间表。

## 一、路线原则

1. **先补确定性基础，再接 Agent。** 不稳定测试、不可复现构建、共享高权限 Token 和无 Digest 发布不能靠模型修复。
2. **先读后写，先候选后执行。** Agent 先查询日志/制品/策略，再创建 Issue/PR/Plan，最后才讨论自动写回。
3. **把自治授权到任务，不授权给产品名。** 授权单位应是“任务类型 × 仓库/分支 × 环境 × Tool × 动作 × TTL”。
4. **内环优化反馈，外环决定接受。** Agent 内循环可以快、增量、可复用；Required Checks、Policy 与发布门禁必须完整。
5. **让平台强制边界。** Prompt、`AGENTS.md` 和 Approval UI 是上下文或交互，不是权限、签名、策略或职责分离。

## 二、五阶段演进

### 阶段 0：建立可信基线

**目标：** 在没有 Agent 时，基础设施已能稳定回答“什么变了、由谁构建、验证了什么、发布了哪个制品”。

**必须完成：**

- Repository Ruleset、CODEOWNERS、Required Checks、Environment Approval；
- 构建输入/输出声明、干净 Runner、Cache Writer 隔离、可复现性基线；
- Artifact Digest、SBOM/Provenance/Attestation 生成与实际验证；
- 工作负载短期身份，去除共享长期 PAT/Registry Admin Key；
- CI/制品/部署事件使用统一 Run、Commit、Digest 和 Environment 标识；
- Flaky、Queue Time、Cache Hit、Test Duration、Deployment Failure 基线。

**停止条件：** 测试不稳定、构建无法重现、制品用 Tag 而非 Digest、门禁可由普通开发身份绕过时，不进入自动修复。

### 阶段 1：只读 Agent Context

**目标：** 让 Agent 能检索 Repository、CI 与 Artifact 数据，但没有直接副作用。

**开放：**

- 代码、Issue/PR/MR、Build State、结构化日志、Test Result；
- Package Version、Vulnerability、License、SBOM、Provenance；
- 只读 MCP/CLI/API 与受限网络。

**控制：**

- 专用只读 Principal；
- Source/Tool Allowlist 与审计；
- 响应保留原始证据链接、时间、Digest 和 Policy Version；
- 禁止把模型摘要当作唯一证据。

**验收指标：**

- 调查时间、证据命中率、人工纠正率；
- 无越权 Tool Call、无 Secret 进入模型上下文；
- 每个结论可回链原日志、Commit 或 Digest。

### 阶段 2：候选变更与 PR/MR 缓冲

**目标：** Agent 可以生成 Patch、Suggestion、Draft PR/MR、Rerun Request 或 Waiver Draft，但不能决定最终接受。

**开放：**

- 临时分支/Worktree 写；
- Issue/PR/MR/Comment Safe Output；
- 有限 CI Rerun、非生产验证环境；
- 不可执行的 Promotion/Waiver/Policy Change Plan。

**控制：**

- Agent、Validator、Merger/Publisher 三身份分离；
- Agent 不得修改 Required Check、Scanner、Test Threshold、Policy、Signer；
- PR/MR 附带任务、Diff、验证范围、未覆盖风险和 Agent Session；
- 新 Commit 到来、超过最大轮次、证据冲突立即停止。

**验收指标：**

- PR 接受率不是唯一指标；同时看人工改动量、缺陷逃逸、复发、回滚和每个 Verified Change 成本；
- 记录按故障类别的修复成功率，而不是一个总体“自愈率”。

### 阶段 3：低延迟验证内环与任务级 Auto-apply

**目标：** 对低风险、可重复、固定 Oracle 的任务允许 Agent 多轮验证或在 PR Branch 自动写回。

**候选白名单：**

- Formatting、Lint、生成代码同步、固定 Schema/Conformance；
- 已知瞬态故障的有限重试；
- 明确 Task Graph 的局部 Build/Test；
- 无生产 Secret 的依赖升级与 Scanner 复验。

**基础设施：**

- 可创建/快照/销毁的隔离环境；
- 受影响任务与增量同步；
- 结构化失败反馈；
- 并发、Turn、Token、Runner 与 Wall-clock 预算；
- Cache 按 Trust Domain 分区，只有可信 Builder 写；
- 通过后仍运行完整 Required Checks。

**停止条件：**

- Agent 改动 Test/Policy/Workflow/Security Config；
- 失败类型从已知变为 Unknown；
- 两轮相同失败、一次权限请求升级、成本超预算；
- Validator 与 Agent 使用同一可修改成功判据；
- 产生跨仓、数据迁移、认证/授权或生产环境影响。

### 阶段 4：有界的制品与发布动作

**目标：** 只对预定义、可逆、低爆炸半径动作开放自动执行。

**可考虑：**

- 查询与验证 Attestation/SBOM；
- 在同一 Trust Domain 内复制/标记制品；
- 非生产环境晋级已验证 Digest；
- 撤销已知泄漏的短期 Token；
- 执行参数受限、带回退的类型化 Runbook。

**默认保持人工/独立服务批准：**

- Production Promotion/Deploy；
- Policy Activation/Disable、Fail-open；
- Waiver、Quarantine Release；
- Artifact/Attestation Delete；
- Token/OIDC/Role/Admin 修改；
- Signer、Trust Root、Admission Policy 修改。

**执行凭证必须绑定：**

```text
Plan Hash + Artifact Digest + Source Commit + Environment
+ Action Type + Parameters + Approver + TTL + Max Uses
```

任一字段变化使批准失效。

## 三、动作风险分层

| 风险层 | 典型动作 | 默认模式 | 最低控制 |
|---|---|---|---|
| R0 读取 | 查代码、日志、版本、漏洞、SBOM、Attestation | 自动 | 只读身份、审计、数据边界 |
| R1 建议 | 摘要、Issue、Comment、Plan、Waiver Draft | 自动 | Safe Output、来源链接、去重 |
| R2 可逆写 | PR Branch、非生产 Tag/Copy、有限 Rerun | 策略白名单或人批 | 短期 Token、Task/Branch Scope、回退 |
| R3 接受性写 | Merge、Policy Activate、Waiver、Promotion | 独立批准 | 四眼原则、Artifact/Plan 绑定、完整 Gate |
| R4 破坏/生产 | Delete、Role/OIDC、Signer、Prod Deploy | 默认禁止 Agent 自主执行 | 专用执行器、强审批、实时观察、回退/熔断 |

## 四、四身份模型

| 身份 | 允许 | 禁止 |
|---|---|---|
| Observer | 读 Repo/CI/Artifact/Telemetry，形成 Evidence Pack | 任何写入 |
| Worker | 写临时 Workspace/PR Branch，触发白名单验证 | Merge、改 Gate、生产 Secret、Policy/Waiver |
| Validator | 运行固定 Build/Test/Scan/Policy，生成签名证据 | 修改候选代码和成功判据 |
| Publisher | 对已批准 Plan/Digest 执行 Promotion/Deploy | 自由修改 Plan、选另一个 Artifact、开放式 Shell |

小型团队可以由同一平台承载四个身份，但 Token、Policy、Audit Principal 必须分开。

## 五、最低事件与证据契约

| 字段 | 用途 |
|---|---|
| Task/Issue/Incident ID | 去重和任务边界 |
| Initiator、Agent、Session、Model/Skill/Tool Version | 归因与回放 |
| Base/Head Commit、Repository、Branch | 确定候选对象 |
| Pipeline/Job/Task、Runner Image、Toolchain | 重现验证环境 |
| Artifact Name + Digest、Dependency Lock | 绑定输出与输入 |
| Test/Scan/Policy Version 与结果 | 证明由哪个 Oracle 判定 |
| Tool Call + Target + Parameters + Result | 副作用审计 |
| Approval + Plan Hash + TTL | 防止批准漂移 |
| Token/Runner/Cache/Wall-clock Cost | 成本归因 |
| Deployment Environment + SLO/Outcome | 连接交付结果 |

## 六、度量

### 质量

- Verified Change Rate；
- 人工改动比例；
- 缺陷逃逸、回滚、复发；
- Agent 修改 Gate/Test/Policy 的拦截数；
- Unknown/Abstain 比例。

### 反馈与容量

- Agent 内环 P50/P95；
- 外环 Required Checks P50/P95；
- Queue Time、Runner Utilization；
- Cache Hit/Miss、错误 Cache Hit 和写入主体；
- 每任务尝试次数与并发峰值。

### 经济性

- 每个 Verified Outcome 的 Runner、Cache、Inference 与人工成本；
- 被拒绝候选的总成本；
- 因 Flaky/环境问题浪费的循环；
- 自动化节省时间与新增审核时间分开记录。

### 治理

- 长期凭据数量与平均 TTL；
- 读/写/删除/管理权限拆分覆盖率；
- 审批绑定 Digest/Plan 的覆盖率；
- Agent Session 到 Commit、Artifact、Deployment 的可追溯率。

## 七、采购与平台评估问题

1. Agent Runtime 与普通 CI Runner 在 Secret、Network、Filesystem、Process 上如何隔离？
2. 哪些能力 GA、Preview、Beta、Open Beta、Roadmap？
3. MCP/CLI Tool 的有效权限如何由平台计算，能否按 Tool、参数、环境和 Run 限制？
4. 是否支持委托人身份、Service Account、短期 Token 和完整归因？
5. Agent 可否修改 Test、Policy、Workflow、Signer 或保护规则？
6. 内环验证是否有独立外环复验，Artifact 是否绑定同一 Commit/Digest？
7. Cache Writer 谁拥有，如何防污染，是否能按 Trust Domain 分区？
8. 是否保存 Tool Call、Session、批准、成本和失败停止原因？
9. Promotion、Waiver、Delete、Role/OIDC 是否默认关闭或要求独立批准？
10. 厂商效果数字的样本、基线、失败定义和第三方验证是什么？

## 八、推荐的近期优先级

1. **Repository Controls + Workload Identity**
2. **结构化 CI/Artifact Read Interface**
3. **PR/MR Candidate Workflow**
4. **Build Graph、Cache、隔离验证与成本遥测**
5. **Task-level Auto-apply**
6. **Attestation Verification 与 Artifact Promotion Policy**
7. **最后才是生产高风险动作**

顺序的核心逻辑是：每一次扩大 Agent 能力之前，先确保相应的证据、身份、外部 Gate 和回退已经存在。

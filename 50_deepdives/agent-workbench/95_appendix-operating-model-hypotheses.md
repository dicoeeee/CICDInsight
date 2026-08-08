---
title: Agent 工作台 operating-model 假设附录
tags:
  - research/agentic-cicd
  - research/hypothesis
  - topic/agent-workbench
status: isolated-hypothesis
as_of: 2026-08-08
confidence: low
presentation_ready: false
---

# Agent 工作台 operating-model 假设附录

> [!warning] 非事实源
> 本文只保存上一版专题中的跨案例分析推断、企业建议和尚待验证的岗位假设。它不是产品功能事实源，不进入主报告、Source Map 或正式页面主张，`presentation_ready: false`。

## 假设一：三层五角色

上一版专题提出三层 operating model：开发者工作台、通用 Agent Harness 和确定性 CI/CD 控制面；角色包括开发者、Harness 设计者、主 Agent、专业 Agent与外部 Oracle/批准者。

这是跨产品功能形成的架构抽象，不是任何厂商的标准定义，也没有跨企业采用率证据。

## 假设二：职责迁移

上一版提出发布、运维、SRE 和平台人员的价值重心可能从逐单执行迁移到 Skill、专家、上下文、权限、评测、异常升级和生命周期治理。

当前没有一手研究证明该迁移已经普遍发生，也没有足够数据量化岗位数量、工时比例或组织覆盖率。该内容保持为尚待验证的岗位趋势。

## 企业建议草案

### RACI 草案

| 工作对象 | R | A | C | I |
|---|---|---|---|---|
| 任务验收条件与候选产物审查 | 应用开发团队 | 业务/代码所有者 | QA、领域专家 | 平台团队 |
| 专家、Skill、MCP/CLI 契约 | 平台工程 | 平台负责人 | 安全、领域专家、开发团队 | 使用者 |
| 凭据、权限、工具 Scope、Runner/沙箱 | 平台/SRE | 安全或平台负责人 | 应用所有者 | 审计/合规 |
| 测试、扫描、Required Checks、策略 | QA/安全/CI Owner | 发布负责人 | 开发团队、平台团队 | 相关干系人 |
| 生产晋级、回滚与例外审批 | 发布/SRE | 变更授权人 | 安全、应用所有者 | 平台团队 |

该表是企业方案草案，不是产品事实或行业通用 RACI。

### 分阶段草案

1. 定义任务输入、候选产物和外部接受条件。
2. 在单 Agent、只读或低风险写入条件下试点。
3. 只把边界清晰、可并行的子任务拆给专业 Agent。
4. 对 Skill、专家和 Connector 增加版本、测试、权限和下架机制。
5. 在保留外部 Test、Scan、Policy、Approval 和 Rollback 的条件下扩大使用范围。

这是建议顺序，未由本专题产品案例证明为最佳实践。

## Skill / 专家生命周期建议

上一版建议为 Skill 和专家维护 Owner、版本、适用范围、依赖、权限、评测集、成本预算、变更记录和下架条件。产品事实只证明部分厂商提供其中一些字段；完整列表是工程建议。

## 试点指标草案

- 任务完成率、人工介入率和错误修复率。
- 后续测试或 Required Check 通过率。
- 缺陷逃逸、变更失败和回滚情况。
- 权限拒绝、审计完整性和异常升级。
- 每个成功任务的 Token、Runner 和外部服务成本。

这些指标尚无本专题统一基线，不可用于产品排名。

## 证伪条件

- 如果企业实际采用仍主要依赖人工逐单执行，职责迁移假设不成立。
- 如果 Skill/专家资产无法稳定版本化、测试或治理，“能力供给层”假设需要收缩。
- 如果多 Agent 的错误、成本或冲突高于单 Agent，专家团不应作为默认形态。
- 如果开发者入口无法在权限、审计和独立 Gate 下运行，自助 operating model 不应扩大。

## 与事实型专题的隔离

- 产品功能事实只维护在 [[50_deepdives/agent-workbench/90_report|事实报告]]、[[50_deepdives/agent-workbench/20_evidence-map|Evidence Map]] 和六个产品详章。
- 本附录不得作为 `presentation_ready` 判断依据。
- 后续若要恢复任何组织观点，需要单独研究跨企业采用、岗位活动和治理成效，并建立新的证据矩阵。

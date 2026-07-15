---
title: Agent-ready CLI 企业 Playbook
tags:
  - research/agentic-cicd
  - research/playbook
  - tool/cli
status: complete
as_of: 2026-07-15
---

# Agent-ready CLI 企业 Playbook

## 一、接口验收

每个进入 Agent Tool Catalog 的命令至少登记：

- Owner、版本、支持周期和撤回方式；
- `--help`、机器可读输出及稳定字段；
- 输入约束、退出码、错误类型和重试语义；
- 读/写分类、幂等性、dry-run、超时和取消；
- Account、Region、Cluster、Environment 等目标 Scope；
- 所需任务身份、秘密来源和最小权限；
- 外部 Oracle、审计字段和预期产物。

## 二、按风险拆分 Toolset

| Toolset | 默认能力 | 典型自治 |
|---|---|---|
| Observe | 查询状态、日志、拓扑、历史 | L1—L2 |
| Propose | 生成 Patch、Plan、配置或命令建议 | L2 |
| Verify | 运行测试、扫描、策略和签名验证 | 沙箱内 L3 |
| Change-nonprod | 测试环境写操作、可回滚变更 | 逐次批准 L3 |
| Change-prod | 预批准、低风险、Runbook 绑定动作 | 极少数 L3/L4 |

## 三、四种运行模式

1. **PR-bound：** CLI 写入只允许生成 Branch 或 Draft PR，原有检查决定合并。
2. **Pipeline-bound：** 在临时 Runner 中执行，限制网络、凭据、时间、重试和输出。
3. **Plan-and-Approval：** 批准绑定 Plan 哈希、制品、目标环境和有效期，执行时重新校验。
4. **Runbook-bound：** 只开放幂等、可回滚、Blast Radius 可证明的小范围操作。

## 四、优先级

先接入结构化只读查询、CI 诊断和 PR 生成；再接入测试/扫描和非生产动作；最后才评估部署、制品晋级和恢复动作。任何阶段都不允许 Agent 同时修改 Gate、批准自己的计划并解释自己的成功。

## 五、度量

- 任务首次成功率、无效命令率、误目标率；
- 结构化解析失败、非预期交互和超时；
- 人工修改、拒绝、接管和回滚；
- 权限拦截、秘密泄露和审计完整率；
- 每成功任务的 Token、Runner 时间、重试和维护成本。

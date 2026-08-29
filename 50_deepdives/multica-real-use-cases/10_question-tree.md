---
title: Multica 与业界真实使用场景问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - company/multica
status: complete
as_of: 2026-08-29
---

# Multica 与业界真实使用场景问题树

## Q1：Multica 当前是什么

- Q1.1 Issue、Agent、Task、Runtime、Project、Squad、Skill、Autopilot 各自负责什么？
- Q1.2 服务端、连接机器、Agent CLI、Git host 与 CI/CD 的边界在哪里？
- Q1.3 什么会触发 Agent？Task 完成与 Issue 完成是否等价？
- Q1.4 “Review gate”是硬控制还是工作流约定？
- Q1.5 当前产品状态、许可证与安全边界是什么？

## Q2：Multica 有哪些公开真实使用

- Q2.1 是否存在明确环境和运行链路的社区案例？
- Q2.2 多 Agent handoff、Autopilot、消息入口分别有何证据？
- Q2.3 是否存在具名生产客户、规模、质量、稳定性或 ROI 证据？
- Q2.4 哪些失败和请求揭示当前产品边界？

## Q3：业界真实使用集中在哪些任务

- Q3.1 Issue/需求如何变成可审阅 PR？
- Q3.2 大规模迁移如何拆并行 Agent 并保留人审？
- Q3.3 CI 失败、夜间日志分诊与候选修复如何闭环？
- Q3.4 Code Review 如何用历史事故或真实反馈验证，而非只看 comment 数？
- Q3.5 多 Agent/多项目平台如何共享上下文、Skills 与治理？

## Q4：企业如何落地 Multica

- Q4.1 哪些任务清晰、低风险、易验证，适合先异步执行？
- Q4.2 哪些任务需要 Planner、Reviewer、Tester 或外部 Oracle？
- Q4.3 哪些权限必须从 daemon 用户移除？
- Q4.4 Squad 与 Autopilot 何时可启用，何时必须使用确定性工作流？
- Q4.5 成功指标、停止条件和扩围门是什么？

## 研究假设与验收

| 假设 | 验收标准 | 结果 |
|---|---|---|
| H1 Multica 的差异化在协调/记录，而非单个 Agent 的代码能力 | 官方架构能明确分开服务端、Runtime 和 CLI | 支持 |
| H2 最有证据的场景是有界、重复、可验证任务 | 至少三个独立组织公开保留独立测试/Review/观测 | 支持 |
| H3 Multica 已有真实多 Agent 使用，但生产成熟度未获证明 | 有社区运行自述，同时缺具名客户与质量指标 | 支持 |
| H4 Squad 不能替代强状态机 | 官方机制依赖 leader + mention，且公开请求仍要求 step enforcement | 支持 |
| H5 Review/安全必须外置 | 官方 Security Model 明确默认按 daemon 用户权限执行 | 支持 |

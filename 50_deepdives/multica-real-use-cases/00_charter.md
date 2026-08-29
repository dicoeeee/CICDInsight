---
title: Multica 与业界真实使用场景研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - company/multica
status: complete
as_of: 2026-08-29
---

# Multica 与业界真实使用场景研究边界

## 决策目标

回答三个决策问题：

1. Multica 当前到底解决什么问题，与模型、Coding Agent、Git/CI/CD 和工作流引擎的边界是什么？
2. 公开材料中，哪些 Multica 工作流确实有人使用，哪些只是官方示例或用户愿望？
3. 参照业界已公开的生产案例，企业应该先把哪些任务交给 Multica，哪些任务必须保留外部验证与人工批准？

## 研究范围

- Multica 当前 main、v0.4.36 Release、官方文档、License、Vision；
- Multica Issues/Discussions 中带明确环境、流程、次数、日志或维护者回应的公开用户自述；
- 业界至少覆盖 Issue→PR、迁移/重构、CI 失败、代码评审/安全、多 Agent/跨项目协作、周期任务；
- 只纳入 GitHub、OpenAI、GitLab、Factory 等官方工程文章或具名客户案例；
- 输出场景适配分级、控制边界和可执行试点方案。

## 非目标

- 不比较基础模型代码能力或做 SWE-bench 排名；
- 不把 GitHub stars、forks、发布频率当采用或成熟度指标；
- 不把产品文档、官方教程、Vision 或 Feature Request 当真实客户案例；
- 不做采购价格与 TCO 对比；
- 不进行 Multica 安装、运行或生产权限实验；
- 不给出法律意见，只如实记录许可证附加条件。

## 时间与来源口径

- **观察日：** 2026-08-29；
- **Multica 基线：** 最新公开 Release v0.4.36（2026-08-28）与 2026-08-28 的 main 快照；
- **访问范围：** 官方站点、官方 GitHub 仓库、官方产品文档、公开 Issues/Discussions、官方工程博客、具名客户案例；
- **证据等级：** E1 官方自用闭环、E2 具名供应商案例、E3 社区自述、E4 文档/教程、E5 愿景/请求；
- **数字口径：** 所有效果数字标明“官方自述”或“客户/厂商自述”，不外推为行业均值。

## 完成条件

- Multica 机制、触发、权限、工作区隔离与 Review 边界均有直接官方来源；
- 每个“真实使用”案例都说明来源等级、实际动作、产出、控制边界和不可外推范围；
- 明确记录 Multica 具名客户、SLA、质量指标和安全认证的证据缺口；
- 给出适用、条件适用、不适用三档场景；
- 试点方案包含进入门、独立验证、人工批准、运行隔离、度量与停止条件。

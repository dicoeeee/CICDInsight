---
title: CLI-Anything 专题实验计划
tags:
  - research/agentic-cicd
  - research/lab
  - tool/cli
status: proposed
as_of: 2026-07-15
---

# CLI-Anything 专题实验计划

本轮未在具体企业工具上执行 CLI 生成。下面的实验用于下一阶段验证项目主张，不把计划结果视作证据。

## 最小可行实验

选择一个有源码、可离线运行、能产生结构化产物、没有生产凭据的内部构建或测试工具。

| 阶段 | 操作 | 必须保存的证据 |
|---|---|---|
| Baseline | 人工完成 20 个固定任务 | 时间、步骤、错误、产物 |
| Generate | 按七阶段 SOP 首次生成 | 模型/Prompt、代码 Diff、依赖、耗时 |
| Review | Owner 审查接口和风险 | 缺口、危险命令、修复量 |
| Refine | 运行 refine 补齐 | 迭代次数和新增能力 |
| Test | 单元、E2E、隐藏任务 | 通过率、失败类型、真实产物 |
| Agent Run | CLI+Skill 完成固定任务 | 首次成功、调用数、Token、误操作 |
| Compare | 对比人工封装/GUI 自动化 | 成本、可靠性、维护与安全 |

## 放行阈值建议

- 20 个固定任务和至少 10 个隐藏任务全部无越权；
- 只读任务首次成功率达到企业基线，写任务必须 100% 命中正确目标；
- 所有写命令具有 dry-run、幂等/并发保护和可验证产物；
- 无秘密进入参数、stdout、Skill、测试夹具或 Preview；
- 依赖锁定、扫描、签名、Owner 和撤回流程齐备；
- 未达到阈值时只能用于本地辅助或 Draft PR。

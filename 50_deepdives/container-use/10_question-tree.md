---
title: Dagger Container Use 独立专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
status: active
as_of: 2026-08-03
confidence: open
---

# Dagger Container Use 独立专题问题树

## Q0：Container Use 到底新增了什么可复用的工程能力？

### Q1：资源从哪里来？

- 本地 Docker、Dagger Engine 与可选 Cloud Engine 各承担什么？
- 计算、镜像、volume、cache、网络、service 和 Secret 分别存在哪里？
- 多 Environment 并发时，资源是否独占、共享或按内容复用？

### Q2：Environment 是什么对象？

- branch/worktree、container state、history、configuration 如何关联？
- 哪些状态进入 Git，哪些只存在本地运行时或 cache？
- 恢复 Environment 时恢复的是源码、文件系统、进程、service 还是执行定义？

### Q3：生命周期如何工作？

- 创建、配置、运行、观察、terminal 接管、merge/apply/delete 的真实调用链是什么？
- 并行任务如何隔离？Agent 如何发现或复用已有 Environment？
- 异常退出、Engine 重启、分支冲突、资源泄漏和垃圾回收如何处理？

### Q4：为什么需要 Dagger？

- Container Use 如何映射到 Dagger Function、DAG、container/service、cache 与 Engine？
- 内容寻址缓存复用什么，不能复用什么？
- 本地到 CI 的“同一定义”与“相同结果”有何差别？

### Q5：与现有方案差异多大？

- 相比 Git worktree + devcontainer，新增的是状态、历史、生命周期还是只是封装？
- 与 Nix、Codespaces、CDE、通用 Agent sandbox 的责任边界是什么？
- 哪些团队已有等价能力，采用 Container Use 反而增加复杂度？

### Q6：成熟度和认可度是否足够？

- 当前 release、维护与 breaking-change 风险如何？
- 公开使用信号属于教程、项目级采用还是企业生产？
- 能否支撑方向性机制页、选型页或成熟实践页？

---
title: Agent 生成验证的运行时 Gate 问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - capability/testing-gates
status: complete
as_of: 2026-08-03
---

# Agent 生成验证的运行时 Gate 问题树

## Q1：流水线运行时新增了什么对象

- Agent 输出是自然语言建议、测试代码、验证计划、可执行作业，还是带判定的证据包？
- 该对象是否只服务本次变更，是否需要进入仓库长期维护？
- 没有 Agent 时，哪些验证只能由静态配置、固定测试或人工选择完成？

## Q2：验证如何被规划

- 触发器来自 PR/MR、Pipeline stage、IDE、聊天还是抽样调度？
- 规划输入包括哪些变更上下文、依赖、风险、运行环境和组织标准？
- 谁限制 Agent 可选择的验证范围、成本、最长时间和允许的副作用？
- 计划是否结构化到足以由流水线审查、缓存、重放和审计？

## Q3：验证如何被执行

- 执行发生在客户提供的 staging、CI runner、隔离环境还是内部测试基础设施？
- 身份、Secret、网络、目标 URL、测试数据和写请求如何约束？
- 超时、重试、并发、环境不可用和外部依赖失败如何处理？
- Agent 是否能修改执行环境或跳过既有 Required Checks？

## Q4：结果如何成为证据

- 原始结果如何压缩为可消费的 verdict、原因、剩余风险与回链？
- 规则、LLM assessor、对照运行和人工复核分别承担什么角色？
- 如何区分被测变更失败、验证自身失败和执行基础设施失败？
- 证据是否能关联 Commit、计划版本、环境、权限和运行时间？

## Q5：证据如何转化为 Gate

- 哪个外部控制面把结果映射成通过、阻断、降级或人工审批？
- 阈值和失败策略由谁预先授权，Agent 能否修改？
- 生成式验证失败时，是 fail-closed、fail-open，还是回退固定测试与人工 Review？
- 哪些场景只能 advisory，哪些场景可进入 Required Check？

## Q6：AWS 与 Meta 分别证明什么

- AWS 是否证明了变更感知验证可产品化为 Pipeline stage？
- AWS Preview 是否证明自动合并、自动部署或生产成熟度？
- Meta 是否证明了临时验证可在内部生产工作流规模化生成和筛选？
- Meta 是否公开证明所有 PR、所有语言或同步强制 Gate？

## Q7：Presentation-ready 的证明标准是什么

- 标题是否在讲 CI/CD 控制面变化，而不是测试算法？
- 页面能否把 planning、execution、evidence、gate 四层区分清楚？
- AWS 和 Meta 是否分别承担不同证明任务，而不是重复案例？
- 页面是否保留 Preview、内部生产、非自动发布和非全量阻断边界？

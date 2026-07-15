---
title: CLI-Anything 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - tool/cli
status: complete
as_of: 2026-07-15
---

# CLI-Anything 专题问题树

## Q1：项目究竟生成什么

- 输入必须是源码、API 还是可执行程序？
- 七阶段流程如何从分析进入设计、实现、测试和发布？
- CLI、REPL、状态、undo/redo、JSON、Skill 与 Hub 如何组合？

## Q2：生成结果为什么可能比 GUI 自动化可靠

- 是否调用真实后端而不是模拟界面？
- 如何验证退出状态、状态变更、导出和最终制品？
- Preview Protocol 能否把视觉/复合结果变成机器反馈？

## Q3：证据能证明什么

- Harness、公共 CLI、Skills、Preview 集成和测试数量分别说明什么？
- 论文是否包含受控 Agent 任务成功率对照？
- 哪些适配器是 Experimental/Community？

## Q4：适用与不适用场景是什么

- 开源桌面/创意/科学软件与企业内部工具是否适合？
- 闭源、强视觉、移动端或生产高危动作为什么不适合？
- 一次生成能否直接进入生产？

## Q5：如何落到 Agentic CI/CD

- 哪些内部诊断、测试、构建、制品、部署工具值得优先试点？
- 如何做只读/写分离、任务身份、供应链、版本和 E2E 质量 Gate？
- 如何判断自建、人工封装、CLI-Anything 或 MCP Wrapper 的投入产出？

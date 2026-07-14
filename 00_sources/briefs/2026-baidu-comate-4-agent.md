---
title: Baidu Comate 4.0 Agent
source_id: baidu-comate-4-2026-03-24
organization: Baidu
source_type: official-release-notes
published: 2026-03-24
verified: 2026-07-14
availability: ga
confidence: high
geography:
  - china
lifecycle_stages:
  - code-review
  - testing-gates
tool_categories:
  - coding-agent
  - code-review
  - agent-runtime-orchestration
company_topics:
  - Baidu
  - Comate
autonomy_levels:
  - L0
  - L1
  - L2
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# Baidu Comate 4.0 Agent

## 来源

- 标题：Comate 4.0 版本正式发布
- 组织或项目：百度智能云文心快码
- 更新日期：2026-03-24
- 链接：[Baidu Comate 文档](https://cloud.baidu.com/doc/COMATE/s/xmm4hx69k)
- 来源类型：官方版本说明
- 能力状态：4.0 正式发布

## 一句话结论

Comate 4.0 从单一编码助手演进为带工具、专业 Subagent、Spec 执行和代码评审命令的 Agent 工作台，但公开证据仍主要集中在编码与评审前端。

## 可核验事实

- Agent 可编辑文件、搜索代码库、运行终端命令、调用 Subagent 和网络检索。
- 4.0 增加专注检索的 Subagent，并提供 `/code-review` 对变更进行评审和修复建议。
- 自定义 Subagent 可以面向代码安全审计、代码评审和文档等专业场景。
- Agent 配置可保存到 `.comate/agents/` 并进入版本控制。
- Spec Mode 先生成可审阅方案，再分步执行并支持结果回溯。

## CI/CD 相关性

- 涉及阶段：代码评审、测试与变更验证。
- 工具类别：编码 Agent、代码 Review、Subagent 和可版本化指令。
- 自主等级：L0—L2。
- 涉及角色：开发者、代码评审者、安全审计人员和研发平台团队。

## 对洞察的价值

它提供中国头部厂商从 IDE 助手向可配置 Agent Team 和版本化 Agent 资产演进的样本。

## 限制与待验证项

- 公开文档没有证明 Agent 与 CI/CD Pipeline、门禁、制品和部署控制面的深度连接。
- 需要寻找百度内部或客户的发布后实践和量化效果。

## 可引用判断

- 中国编码 Agent 已开始引入专业 Subagent、Spec 与版本化配置，但从编码端走向完整 CI/CD 控制面仍需更多证据。


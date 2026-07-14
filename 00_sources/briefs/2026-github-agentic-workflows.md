---
title: About GitHub Agentic Workflows
source_id: github-agentic-workflows-docs-2026
organization: GitHub
source_type: official-docs
published: null
verified: 2026-07-14
availability: preview
confidence: high
geography:
  - global
lifecycle_stages:
  - code-review
  - testing-gates
  - build
tool_categories:
  - source-control
  - ci-pipeline
  - agent-runtime-orchestration
  - governance
company_topics:
  - GitHub
autonomy_levels:
  - L1
  - L2
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# About GitHub Agentic Workflows

## 来源

- 标题：About GitHub Agentic Workflows
- 组织或项目：GitHub
- 链接：[GitHub Enterprise Cloud Docs](https://docs.github.com/en/enterprise-cloud%40latest/copilot/concepts/agents/about-github-agentic-workflows)
- 来源类型：官方产品文档
- 能力状态：Public Preview

## 一句话结论

GitHub 将 Agentic Workflows 定位为运行在 GitHub Actions 上的仓库级智能自动化层，用自然语言描述需要判断的任务，但继续依靠编译后的 Actions 工作流、最小权限和受控输出执行。

## 可核验事实

- 工作流以 Markdown 编写，由 YAML frontmatter 定义触发器、权限和安全输出，再编译为 `.lock.yml` GitHub Actions 工作流。
- 支持 GitHub Copilot、Anthropic Claude、OpenAI Codex 和 Google Gemini 等不同执行引擎。
- 默认只读；写操作只能通过声明的 `safe-outputs`，敏感凭证不暴露给 Agent 运行时。
- 运行环境包含沙箱、网络限制和威胁检测，可产出 Issue、评论或 PR 供人审。
- 官方列出的场景包括 CI 失败调查、测试覆盖改进、文档维护和仓库状态报告。

## CI/CD 相关性

- 涉及阶段：代码评审、测试与门禁、CI 故障诊断。
- 工具类别：代码仓、流水线、Agent 编排与治理。
- 自主等级：L1—L3，取决于允许的安全输出和人工审批。
- 涉及角色：开发者、仓库维护者、平台工程师、安全治理人员。

## 对洞察的价值

这是“确定性 CI/CD 之外增加 Continuous AI 层”的代表模式，说明 Agent 任务可以自然语言化，但执行边界仍需要编译期策略和确定性控制。

## 限制与待验证项

- 仍为公开预览，接口和计费可能变化。
- 官方文档证明能力存在，但尚不能证明大规模生产效果。

## 可引用判断

- Agentic Workflow 并不等于取消确定性流水线，而是在流水线上增加能够理解上下文和提出行动的受控决策层。


---
title: GitHub Agentic Workflows 配置速查
aliases:
  - gh-aw 配置教程
tags:
  - research/agentic-cicd
  - tutorial
  - company/github
status: complete
as_of: 2026-07-17
---

# GitHub Agentic Workflows 配置速查

> [!info] 配置状态
> 下例使用 GitHub Agentic Workflows 当前 Frontmatter 字段，可作为 `.github/workflows/ci-health.md` 的起点。首次运行保留 `safe-outputs.staged: true`，确认输出后再开放真实写操作。

## 配置放在哪里

Agentic Workflow 的源文件是 `.github/workflows/<name>.md`：顶部 `---` 之间是 YAML Frontmatter，后面是自然语言任务。`gh aw compile` 会生成同目录的 `<name>.lock.yml`，后者是 GitHub Actions 实际执行文件，**不要手工编辑**。

## 最小示例

```markdown
---
description: 每周检查 CI 失败和慢任务
on: weekly on monday

permissions:
  contents: read
  issues: read
  actions: read
  copilot-requests: write

network:
  allowed:
    - defaults
    - github

tools:
  github:
    toolsets: [repos, issues, actions]

safe-outputs:
  staged: true
  create-issue:
    title-prefix: "[ci-health] "
    labels: [automation, ci]
    close-older-issues: true
    max: 1

max-ai-credits: 500
max-daily-ai-credits: 2000
timeout-minutes: 15
---

# Weekly CI Health Review

Analyze failed and slow workflow runs from the last seven days.
Create one issue containing evidence, recurring causes, prioritized actions,
and the checks maintainers should run before accepting a proposed change.
Do not modify code, workflow files, repository settings, or existing issues.
```

## 关键字段怎么理解

| 字段路径 | 必填 | 示例 | 含义与注意事项 |
|---|---|---|---|
| `description` | 否 | `每周检查 CI` | 给维护者看的说明，也会进入生成文件的注释；它不是 Agent Prompt。 |
| `on` | 是 | `weekly on monday` | 触发条件。既支持 GitHub Actions 事件，也支持 gh-aw 的自然语言 Schedule。试点时也可先用 `workflow_dispatch` 手动触发。 |
| `permissions.contents` | 建议显式写 | `read` | Agent 读取仓库内容所需的 GitHub Token 权限。默认遵循最小权限，不要因为要创建 Issue 就把 Agent Job 改成全仓写权限。 |
| `permissions.actions` | 场景需要 | `read` | 读取 Workflow Run、Job 和日志元数据；做 CI 诊断时通常需要。 |
| `permissions.copilot-requests` | 使用 Copilot Engine 时需要 | `write` | 允许 Workflow 调用 GitHub Copilot。这里的 `write` 是“发起 Copilot 请求”，不是代码写权限。 |
| `network.allowed` | 否，但生产建议显式写 | `defaults`, `github` | Agent 容器可以访问的网络生态或域名。只允许任务真正需要的目标；`blocked` 规则优先于 `allowed`。 |
| `tools.github.toolsets` | 场景需要 | `[repos, issues, actions]` | 暴露给模型的 GitHub 工具集合。它决定“模型能调用什么工具”，与 `permissions` 决定的“Token 能做什么”是两层控制。 |
| `safe-outputs.staged` | 否 | `true` | 预览所有写操作：只在 Actions Summary 展示计划结果，不调用写 API。新 Workflow 建议从 `true` 开始。 |
| `safe-outputs.create-issue` | 需要创建 Issue 时 | 对象 | 只允许 Agent 通过受类型约束的 Safe Output 创建 Issue；写入发生在独立 Job，不让 Agent 直接持有通用写 Token。 |
| `title-prefix` | 否 | `[ci-health]` | 给自动创建的 Issue 加固定前缀，方便搜索、去重和治理。 |
| `labels` | 否 | `[automation, ci]` | 自动添加的 Label。目标仓库应先创建这些 Label。 |
| `close-older-issues` | 否 | `true` | 新 Issue 创建成功后，关闭同一 Workflow 之前创建的旧 Issue；默认 `false`，适合“只保留最新周报”的场景。 |
| `max` | 否 | `1` | 单次运行最多创建多少个同类输出，防止循环或模型异常造成批量写入。 |
| `max-ai-credits` | 否 | `500` | 单次运行的 AI Credits 上限；当前省略时默认 `1000`。试点应显式给出较小预算。 |
| `max-daily-ai-credits` | 否 | `2000` | 同一 Workflow 滚动 24 小时总预算；当前省略时不启用。它可限制事件风暴导致的累计成本。 |
| `timeout-minutes` | 否 | `15` | 主 Workflow 超时；当前默认 `20` 分钟。它不能代替模型预算和工具调用次数限制。 |

> [!important] 三个边界不要混淆
> `permissions` 限制 Token，`tools` 限制模型可见的调用入口，`safe-outputs` 限制最终允许外化的写操作。企业配置要同时收紧三层，而不是只看其中一层。

## 从预览切换到真实写入

确认预览内容、Label、数量上限和审计信息正确后，只修改这一处：

```yaml
safe-outputs:
  staged: false
  create-issue:
    title-prefix: "[ci-health] "
    labels: [automation, ci]
    close-older-issues: true
    max: 1
```

如果希望仅让某一种输出处于预览状态，也可以把 `staged: true` 放到该输出类型内部。

## 常见错误

- **直接修改 `.lock.yml`**：下次编译会覆盖，且源文件与执行文件失去一致性。只改 `.md`，然后重新编译。
- **把 `tools` 当权限系统**：工具存在不代表 Token 有权执行；反过来，Token 有权限也不应意味着模型看到所有工具。
- **在顶层 `env` 放 Secret**：这些变量会直接进入 Agent 容器，可能被模型看到；Secret 应使用 Engine 或 MCP 的专用 Secret 配置。
- **一开始就真实写入**：先使用 `staged: true`，人工审查几次 Summary 后再开放。
- **省略成本上限和日上限**：事件触发错误或循环调度可能放大模型与 Runner 成本。
- **关闭严格模式**：`strict: false` 只适合本地开发/测试；生产使用 `compile --strict`，公共仓库也不能运行非严格编译产物。

## 如何验证

```bash
gh aw validate --strict
gh aw compile --strict --actionlint --zizmor --poutine
gh aw run ci-health
gh aw logs ci-health
gh aw audit <run-id>
```

验证时重点检查 `.md` 与 `.lock.yml` Diff、实际 Token 权限、网络访问记录、Safe Output 预览、模型花费和超时。通过后再开放 Schedule/Event Trigger。

## 下钻阅读

- [Frontmatter 官方参考](https://github.github.com/gh-aw/reference/frontmatter/)
- [Safe Outputs 官方参考](https://github.github.com/gh-aw/reference/safe-outputs/)
- [[50_deepdives/github-agentic-workflows/90_report|GitHub Agentic Workflows 深度报告]]
- [[50_deepdives/github-agentic-workflows/40_labs/README|复杂场景实验方案]]

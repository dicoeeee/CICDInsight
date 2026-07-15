---
title: CLI 案例比较
tags:
  - research/agentic-cicd
  - research/case-map
  - tool/cli
status: complete
as_of: 2026-07-15
---

# CLI 案例比较

| 案例 | Agent-ready 机制 | 强项 | 关键限制 | 对 CI/CD 的启示 |
|---|---|---|---|---|
| GitHub CLI `gh` | `--json` 字段选择、`--jq`、Go template、认证与仓库上下文 | PR/Issue/Run 的机器查询与组合 | 命令间字段和权限仍需逐项治理 | 代码仓操作可直接形成 PR-bound Toolset |
| Terraform CLI | 非交互、保存 Plan、应用同一 Plan、状态和锁、JSON 格式 | IaC 变更有明确计划—批准—执行边界 | 状态漂移、Provider 副作用、敏感输出 | 是 Plan-and-Approval 的代表，不应让 Agent 自批自执 |
| `kubectl` | JSON/YAML/JSONPath、自描述资源、namespace/context | Kubernetes 查询和受控操作覆盖广 | 当前 Context 易误用；写命令 Blast Radius 大 | 只读与写 Toolset 分离，显式 Cluster/Namespace |
| AWS CLI | 多种输出格式，`off` 可让 CI 只依赖退出状态 | 云服务覆盖面广，适合脚本和 Runner | API 面庞大、IAM 与 Region/Profile 隐式 | 任务身份、Region、Account 和命令白名单必须显式 |
| Agent CLI/Harness | 终端会话、代码仓上下文、CLI/MCP/Skill 组合 | 人与 Agent 在同一工作区协作 | Harness 本身不是安全边界 | 把 Harness 与业务 CLI、执行沙箱、外部 Gate 分层 |
| CLI-Anything | 从源码/后端生成 CLI、测试和 Skill | 补齐长尾软件机器接口 | 生成质量、源码可用性和供应链需逐工具验收 | 适合内部工具 Agent 化，详见 [[50_deepdives/cli-anything/README]] |

## 共性模式

成熟案例都不只提供“一个命令”：它们同时定义可发现性、机器输出、凭据定位、状态、失败和版本。差异主要在于真实副作用是否可计划、可回滚，以及外部系统是否能独立证明成功。

---
title: Dagger Container Use 独立专题证据矩阵
tags:
  - research/agentic-cicd
  - research/evidence-map
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Dagger Container Use 独立专题证据矩阵

## 证据标记

- **A：** 当前官方源码、官方文档、Release/API；
- **B：** 官方产品/工程文章；
- **C：** 外部官方生态文档或公开项目实操；
- **分析：** 基于多项来源的边界化推断，不冒充厂商原话。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计 |
|---|---|---|---|---|---|
| CU-C01 | Container Use 是 Agent-neutral 的本地 stdio MCP server / CLI，而非 Claude Code 附属能力 | [README](https://github.com/dagger/container-use/blob/main/README.md)、[agent integrations](https://github.com/dagger/container-use/blob/main/docs/agent-integrations.mdx)、[tools.go](https://github.com/dagger/container-use/blob/main/mcpserver/tools.go) | Dagger 维护的配置不等于各 Agent 厂商联合支持 | high | passed |
| CU-C02 | 默认计算路径是 Agent → Container Use → Dagger client → 主机 OCI runtime 中的 Dagger Engine | [stdio.go](https://github.com/dagger/container-use/blob/main/cmd/container-use/stdio.go)、[Dagger runtimes](https://docs.dagger.io/reference/container-runtimes/) | 当前 release 与 `main` 使用的 Engine 版本可能不同；运行时兼容性需按实际版本验证 | high | passed |
| CU-C03 | Container Use 本身没有自动为每个 Environment 申请 Dagger Cloud 托管计算的控制器 | stdio 只调用 `dagger.Connect`；[custom runner](https://docs.dagger.io/next/reference/configuration/custom-runner/)、[Cloud](https://docs.dagger.io/reference/configuration/cloud/) 是独立配置面 | 可以改接 custom runner；不能把“未发现自动路径”写成 Dagger Cloud 永远不能承载 | medium-high | passed with boundary |
| CU-C04 | Environment 是 branch + 本地 bare remote/worktree + ContainerID state + notes + config 的组合对象 | [environment README](https://github.com/dagger/container-use/blob/main/environment/README.md)、[repository.go](https://github.com/dagger/container-use/blob/main/repository/repository.go)、[state.go](https://github.com/dagger/container-use/blob/main/environment/state.go) | 不等于 VM 快照；各层生命周期不同 | high | passed |
| CU-C05 | 命令即使非零退出也会形成新的 Dagger Container state，并把命令/输出记录到 notes | [environment.go](https://github.com/dagger/container-use/blob/main/environment/environment.go)、[git.go](https://github.com/dagger/container-use/blob/main/repository/git.go) | 只记录容器内已执行命令；不验证 LLM 解释或容器外副作用 | high | passed |
| CU-C06 | 项目配置有 baseline 与 Agent adaptation 两层，配置写入 `.container-use/environment.json`，只影响新环境 | [configuration docs](https://github.com/dagger/container-use/blob/main/docs/environment-configuration.mdx)、[config.go](https://github.com/dagger/container-use/blob/main/environment/config.go) | 交互式临时变更不会自动成为团队基线；需显式 import | high | passed |
| CU-C07 | setup 在源码进入前运行，使相同工具链层更容易命中 Dagger 内容缓存 | [environment.go](https://github.com/dagger/container-use/blob/main/environment/environment.go)、[function caching](https://docs.dagger.io/extending/function-caching/) | 源码、外部网络、随机性和 cache GC 仍影响命中；缓存不是质量结论 | high for mechanism | passed |
| CU-C08 | 服务配置可以创建和重新声明，但运行中 service、Host Tunnel、内存态不能写成跨 Engine 自动恢复 | [service.go](https://github.com/dagger/container-use/blob/main/environment/service.go)、`Load` 中的 `Services: ?` [environment.go](https://github.com/dagger/container-use/blob/main/environment/environment.go) | 当前源码没有持久卷配置或完整 service 恢复证明 | high for gap | passed |
| CU-C09 | 每个任务的 branch/worktree/container 可减少源码与文件冲突 | [repository lifecycle](https://github.com/dagger/container-use/blob/main/repository/repository.go)、[workflow docs](https://github.com/dagger/container-use/blob/main/docs/environment-workflow.mdx) | 共享 CPU、Engine、外部数据库/API、账号和 bucket 仍可互相干扰 | high | passed with counterexample |
| CU-C10 | log/diff/watch/terminal/checkout 让人能观察并接管 Agent 环境 | [workflow docs](https://github.com/dagger/container-use/blob/main/docs/environment-workflow.mdx)、[launch post](https://dagger.io/blog/agent-container-use/) | “完整审计”是厂商措辞；Git notes 不是不可篡改审计账本 | high for operations | passed |
| CU-C11 | merge、apply、delete、prune 把候选环境变成可接受、定制或丢弃的生命周期对象 | [repository.go](https://github.com/dagger/container-use/blob/main/repository/repository.go)、[CLI sources](https://github.com/dagger/container-use/tree/main/cmd/container-use) | Git 冲突、未提交本地变更与删除后的 Engine cache 生命周期仍需处理 | high | passed |
| CU-C12 | Dagger Function / cache 可让被声明的构建测试语义在本机与 CI 复用 | [Functions](https://docs.dagger.io/core-concepts/functions/)、[caching](https://docs.dagger.io/extending/function-caching/)、[launch post](https://dagger.io/blog/agent-container-use/) | Container Use 不把任意 shell 历史自动转译为 Function，也不自动迁移活体 Container 到 CI | medium-high | passed with inference boundary |
| CU-C13 | `environment_checkpoint` 可将当前容器显式发布到 registry，形成内容寻址的跨 Engine 工件 | [tools.go](https://github.com/dagger/container-use/blob/main/mcpserver/tools.go)、[environment.go](https://github.com/dagger/container-use/blob/main/environment/environment.go) | checkpoint 不是默认 CI 交接路径；服务、外部状态和审批不随镜像自动迁移 | high | passed |
| CU-C14 | Container Use 仍处 experimental / early development，最新可核验 Release 为 v0.4.2（2025-08-19） | [README](https://github.com/dagger/container-use/blob/main/README.md)、[release API](https://api.github.com/repos/dagger/container-use/releases/latest) | 2026 `main` 仍维护，不能写已停止；`main` 也不能当稳定 release 合同 | high | passed |
| CU-C15 | 已有 Goose 官方生态教程与公开开发者实操，但没有具名企业生产采用证明 | [[00_sources/research-container-use-adoption-endorsement-2026-08-03|采用审计]]、[Goose docs](https://goose-docs.ai/docs/mcp/container-use-mcp/) | 未找到证据不等于无人使用；不可用 GitHub stars 替代生产证据 | medium-high | passed with gap |

## 状态分层

| 状态层 | 主要落点 | 可恢复性 | 页面允许写法 |
|---|---|---|---|
| 代码与提交 | Container Use 本地 bare Git remote + per-ID worktree/branch | 可通过 Git 查看、checkout、merge/apply | “候选代码有独立版本历史” |
| 命令与元数据 | `container-use` / `container-use-state` Git notes | 可读取；不是不可篡改 | “保留容器内操作记录” |
| 容器文件系统 | Dagger `ContainerID` 与 Engine state/cache | 同一可解析 Engine 状态下可 load；长期/跨 Engine 受限 | “可恢复的 Dagger Container 状态引用” |
| 运行中进程/服务 | Dagger Service、Host Tunnel、内存态 | 当前源码不证明跨 Engine 无缝恢复 | 不写“完整运行现场永久恢复” |
| 团队环境基线 | `.container-use/environment.json` | Git 可版本化；新环境重建 | “环境配方可评审和共享” |

## 仍需实测的缺口

- ContainerID 在 Engine 重启、GC、跨 runner 和不同架构下的恢复率；
- 大规模并发 Environment 的 CPU/内存/磁盘、cache 命中和排队行为；
- 有状态服务与外部系统命名空间隔离；
- Container Use 与 `worktree + devcontainer/Nix + CI` 的真实增量收益；
- checkpoint、Dagger Function 与具体 CI 平台的交接操作和失败恢复。

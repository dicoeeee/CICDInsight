---
title: Dagger Container Use 独立专题报告
tags:
  - research/agentic-cicd
  - research/deep-dive-report
status: complete
as_of: 2026-08-03
confidence: medium-high
presentation_ready: true
---

# Dagger Container Use 独立专题报告

## 执行摘要

Container Use 不是 Dagger Cloud 托管的 Agent 平台，也不是“Claude Code 的 Docker 插件”。它是一个本地 stdio MCP server / CLI，把 Coding Agent 的每个任务组织成由 Git branch、本地 worktree、Dagger Container 状态、命令 notes 和环境配置共同组成的 Environment。

这个组合的工程价值不只是防止两个 Agent 修改同一目录。它让失败命令仍留下可检查的容器文件系统状态和执行记录，让操作者能够进入 terminal、查看 diff/log、继续任务，再选择 merge、apply 或 delete。由此，Agent 输出从一次性代码 diff 变成一个有生命周期的候选 Environment。

但状态必须分层理解：Git 可以保存代码、配置和 notes；Dagger ContainerID 指向的文件系统状态受 Engine/cache 生命周期影响；运行中的 service、Host Tunnel、数据库内存态和外部副作用没有无缝跨 Engine 恢复证据。Container Use 与 CI 的正确连接，是把环境 setup 和 build/test 等步骤声明为 Dagger Function，在 CI 中按同一执行语义独立重跑，而不是把任意 Agent 会话直接搬进 CI。

因此本专题对“候选 Environment 的对象模型与 CI 交接机制”标记 `presentation_ready: true`；不支持“企业成熟采用”“自动跨机快照”或“替代 CI 门禁”。

## 一、资源从哪里来

```text
Coding Agent
    │ MCP stdio
    ▼
Container Use process
    │ dagger.Connect
    ▼
Dagger Engine
    │
    ├─ Container / Directory / File
    ├─ Service / Host Tunnel
    ├─ content-addressed cache
    └─ Secret object
```

默认计算来自主机 OCI runtime 中启动的 Dagger Engine；Container Use 自身不拥有 runner fleet、队列或资源调度器。企业可以为 Dagger 配置 custom runner，但不能由此推导每个 Container Use Environment 会自动获得 Dagger Cloud 托管计算。

本地持久数据主要位于 `~/.config/container-use/` 的 bare repo 和 worktrees；原项目只新增一个指向本地 bare repo 的 `container-use` remote，不会自动把 Environment branch 推送到 GitHub origin。

## 二、Environment 的真实数据模型

| 层 | 对象 | 作用 |
|---|---|---|
| 逻辑层 | Environment ID / title | 标识一个 Agent 任务或尝试 |
| 版本层 | branch + local remote + worktree | 保存候选代码、提交与合并关系 |
| 执行层 | Dagger ContainerID | 指向当前容器文件系统与执行定义 |
| 证据层 | Git notes | 保存 command、exit code、stdout/stderr 与 state JSON |
| 配方层 | `.container-use/environment.json` | 保存 base image、setup/install、env、secret reference 与 services |

创建时，Container Use 从一个 Git ref 生成环境分支与 worktree，将源码装入 Dagger Directory，先执行 setup，再复制源码并执行 install。每次命令通过 `WithExec` 产生新 Container state；即使退出码非零，代码仍会尝试保存 state 并记录命令输出。

因此 Environment 不是长期运行的 Docker container。它更像“Git 版本对象 + Dagger 内容对象 + 操作记录”的组合句柄。

## 三、生命周期为什么重要

```text
create(ref)
   ↓
setup → service → source → install
   ↓
run / file operation / add service
   ↓
log · diff · terminal · checkout
   ↓
merge ── apply ── delete/prune
```

这个生命周期给研发效能平台三项可借鉴能力：

1. **每次 Agent 尝试都有独立候选对象。** 不同方案可以并行存在、比较和丢弃；
2. **失败后保留可处置上下文。** 平台工程师不是只拿到失败日志，还可以进入同一容器状态继续定位；
3. **环境适配可以被选择性沉淀。** Agent 的 setup 改动默认只在当前任务生效，经过判断后再 import 为团队 baseline。

## 四、并行能力的边界

branch、worktree 与 container 隔离了文件修改和容器文件系统，但没有自动隔离：

- 宿主 CPU、内存、磁盘和 Engine cache；
- 共享 Docker/OCI daemon 与镜像下载带宽；
- 外部数据库、S3 bucket、API、测试账号和配额；
- 端口之外的业务命名空间和全局状态。

所以“多个 Agent 不冲突”只能写成“降低工作区冲突”。大规模并行仍需要资源配额、队列、外部依赖命名空间和垃圾回收策略。

## 五、服务与恢复不是同一件事

Container Use 可以按配置启动 service container、建立 service binding，并通过 Host Tunnel 暴露端口。服务配置可以被保存和重建，但源码 `Load` 未证明运行中的 service、tunnel 或内存态会随 ContainerID 无缝恢复；当前配置也没有通用持久 volume 模型。

因此有状态测试环境需要把 schema、fixture、seed 和健康检查写成可重建步骤，不能依赖数据库进程仍然活着。对外表达应使用“可重建服务配置”，而不是“完整运行现场永久恢复”。

## 六、Dagger 如何连接 CI

Dagger Function 把 typed input、容器化操作和 typed output 组成可缓存执行图。Container Use 的 base、setup、install 和 service 配置可以与这套执行语义对齐；团队进一步把 build/test/generate 编码为 Function 后，Agent 和 CI 可以调用同一声明。

正确链路是：

```text
Agent 候选 Environment
    │ 代码 + 配置 + history
    ▼
Dagger Function / CI 独立重跑
    │
    ▼
test / scan / policy / artifact / approval
```

三条边界必须保留：

- Container Use 不会把任意 shell history 自动编译为 Function 或 CI pipeline；
- ContainerID 不等于可无限期、跨 Engine、跨架构恢复的快照；需要固化当前容器时可显式 checkpoint 到 registry；
- cache hit 只表示输入和执行图可复用，不表示结果已经通过质量裁决。

## 七、相邻方案与反例

| 已有能力 | Container Use 的增量 | 增量可能不成立的情况 |
|---|---|---|
| Git worktree | 绑定 Dagger container state、notes、MCP lifecycle | 任务只需源码隔离 |
| Dev Container | 每任务 branch/history、Agent 工具面、可接管生命周期 | 已有 worktree + devcontainer + compose 命名空间 |
| Nix | Agent 候选对象与服务/container API | 主要目标是严格工具链闭包与确定性构建 |
| Codespaces / CDE | 本地自控、任务粒度候选环境 | 更需要统一远程 VM、身份、配额和 IDE 工作台 |
| 通用 sandbox | Git-backed lifecycle 与 Dagger execution graph | 只需要强隔离或 VM/microVM 网络策略 |

强反例是：已经拥有 `worktree + devcontainer/Nix + 稳定 CI + 日志/远程调试` 的团队，Container Use 可能只是新增 MCP、Engine 和 Git plumbing。它必须通过仓库级试验证明能降低环境漂移或失败定位成本。

## 八、成熟度与认可度

- README 标记 `experimental` 和 `early development`；
- latest release 为 `v0.4.2`（2025-08-19）；
- 2026 `main` 仍有维护，但不能当成稳定 release 合同；
- Goose 官方教程、公开项目配置和开发者实操构成早期生态验证；
- 没有可核验的具名企业生产客户、规模稳定性或量化成本数据。

## 九、Presentation-ready 判断

### Go：方向性机制页

> **并行 Coding Agent 的工程交接单元，正在从代码 diff 升级为带执行上下文与操作历史的候选 Environment；Dagger Container Use 给出了一种对象化实现，但 CI 仍需对可声明步骤独立重跑和裁决。**

### No-Go：成熟实践或平台选型页

- 不写 Dagger × Anthropic/OpenAI 联合方案；
- 不写 Container Use 已成为企业标准；
- 不写完整容器状态自动进入 CI；
- 不写环境隔离已经解决安全、质量和外部副作用。

## 主要来源

- [Container Use repository](https://github.com/dagger/container-use)
- [Environment implementation](https://github.com/dagger/container-use/tree/main/environment)
- [Repository lifecycle](https://github.com/dagger/container-use/blob/main/repository/repository.go)
- [MCP tools](https://github.com/dagger/container-use/blob/main/mcpserver/tools.go)
- [Dagger: Agent Container Use](https://dagger.io/blog/agent-container-use/)
- [Dagger Functions](https://docs.dagger.io/core-concepts/functions/)
- [Dagger Function caching](https://docs.dagger.io/extending/function-caching/)
- [[00_sources/research-container-use-standalone-deepdive-2026-08-03|独立研究底稿]]

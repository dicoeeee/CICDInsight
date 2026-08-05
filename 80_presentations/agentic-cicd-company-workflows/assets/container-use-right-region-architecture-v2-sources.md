---
asset: container-use-right-region-architecture-v2
status: fact-audited-proposed-for-visual-review
as_of: 2026-08-03
supersedes: container-use-right-region-environment-v1
---

# Container Use 右半区 V2：事实来源与图形审计

## 资产

- SVG：`container-use-right-region-architecture-v2.svg`，900 × 840，SHA-256 `630e320f91b3e888f2501eb6cf203875d1062c93fff0c889f8f72d72e2144b83`。
- PNG：`container-use-right-region-architecture-v2.png`，1800 × 1680，SHA-256 `58bbaa8e641e412d48cd03941ea31dbdefae86faa1a7e7b8e6261d6b2ca801fc`。
- V1 保留作评审基线，但退出当前候选；V2 不再复刻 Dagger 左图的“调用方 → Engine → API → Operation Graph”层级。

## 架构选择

V2 将 Container Use 画成一个协调进程，而不是第二个执行引擎。一个 `Environment ID` 关联两类状态：

1. **Git state plane：** 本地 bare repository、`container-use` remote、Environment branch、独立 worktree，以及保存 history / environment state 的 Git notes；
2. **Dagger execution state plane：** Container Use 持有 `dagger.Client`，通过 Dagger Engine 执行 `WithExec` 等容器操作，并以 `ContainerID` 引用命令后的文件系统状态；该 ID 写入 environment state；
3. **Project baseline：** `.container-use/environment.json` 描述 base image、setup / install、environment variables、secret references 与 services；它是新 Environment 的默认基线，既有 Environment 需要显式 config import 才采用新的项目基线。

## 连线逐项审计

| 图中关系 | 事实含义 | 当前官方一手来源 | 审计结果 |
|---|---|---|---|
| Coding Agent → Container Use process | `container-use stdio` 启动面向 Agent 集成的 stdio MCP server | https://github.com/dagger/container-use/blob/main/cmd/container-use/stdio.go | pass |
| Container Use holds `dagger.Client` | stdio 启动路径先 `dagger.Connect(...)`，再把 client 传给 MCP server；Environment 对象保存 `dag *dagger.Client` | https://github.com/dagger/container-use/blob/main/cmd/container-use/stdio.go；https://github.com/dagger/container-use/blob/main/environment/environment.go | pass |
| Project baseline → create / config import | Environment config 位于 `.container-use/environment.json`；创建时读取项目配置，既有 Environment 通过显式 config import 更新基线；`buildBase()` 依当前 config 构造 base container、setup、source、install 与 services | https://github.com/dagger/container-use/blob/main/environment/config.go；https://github.com/dagger/container-use/blob/main/environment/environment.go；https://github.com/dagger/container-use/blob/main/repository/repository.go | pass with label boundary |
| Environment ID → Git branch / worktree / notes | repository 按 Environment ID 建立 local branch/worktree，并在 Git notes 中保存 history 和 serialized state | https://github.com/dagger/container-use/blob/main/repository/repository.go | pass |
| `dagger.Client` → Engine → ContainerID | Environment 用 client 通过 `LoadContainerFromID` 读取原状态，执行 `WithExec` 后 `Sync` 新 Container，并把 ID 写回 state | https://github.com/dagger/container-use/blob/main/environment/environment.go；https://github.com/dagger/container-use/blob/main/environment/state.go | pass |
| non-zero exit → state update | `Run()` 使用 `ReturnTypeAny` 获取非零退出码，并在记录命令结果后仍调用 `apply()` 更新 ContainerID | https://github.com/dagger/container-use/blob/main/environment/environment.go | pass |
| lifecycle strip | Update 同步 worktree、history 与 state notes；log/diff/terminal/checkout 支持观察和继续；merge/apply/delete/prune 提供处置 | https://github.com/dagger/container-use/blob/main/repository/repository.go；https://github.com/dagger/container-use/blob/main/docs/environment-workflow.mdx | pass |
| Engineer CLI → lifecycle disposition | merge、apply、delete 属于工程师可调用的 CLI / Git 处置动作，不是 Agent MCP Environment tools | https://github.com/dagger/container-use/tree/main/cmd/container-use；https://github.com/dagger/container-use/blob/main/mcpserver/mcpserver.go | pass |

## 没有画出的能力与边界

- `ContainerID` 是 Dagger 容器状态引用，不是完整 VM snapshot，也不是与 Git notes 平行的独立业务数据库；图中已在 Git notes 标注 `state(ContainerID)`。
- 当前 `Load()` 对 service 恢复没有与 Container state 同等明确的实现保证；Host Tunnel、运行中进程、外部数据库内存态和其他副作用不写成可恢复状态。
- Environment config 是新环境的默认项目基线；既有 Environment 的修改保留在 State.Config，需显式 config import 才更新项目基线。图中不把它误画成每次命令后的完整 runtime snapshot。
- 前台命令的非零退出会记录结果并更新 Container state，但不代表通过了独立 CI gate；后台任务的 workdir 变化也不宣称会自动提交。
- Container Use 是 MCP / CLI 协调层，不是 Dagger Engine 的替代品；真正的容器计算仍由 Dagger 执行。
- 图中 “Environment ID 同时关联两类状态” 是对 repository、EnvironmentInfo 和 serialized State 关系的 high-level 归纳，不表示代码中存在单独的双状态数据库或事务协议。

## 视觉差异检查

- 左区的主体是纵向执行链：调用入口 → Engine → Module API → Operation Graph；
- 右区的主体是中心身份 + 双状态面：Environment ID 分别关联 Git 工作现场与 Dagger Container state；
- 两图仅共享全页配色、标题和能力卡片规范，不共享主图拓扑，避免把 Container Use 误读成另一个 Dagger 式执行引擎。
- 红色只标注两个页面重点：Environment ID 的双状态绑定，以及失败后仍保留现场；普通组件、状态面与执行路径均使用黑灰色。

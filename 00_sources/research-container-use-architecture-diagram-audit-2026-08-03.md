---
title: "Container Use 右区技术图：源码级架构审计"
tags:
  - research/cicd
  - company/dagger
  - agent-environment
  - diagram-audit
status: complete
as_of: 2026-08-03
confidence: high
product_status: experimental-early-development
---

# Container Use 右区技术图：源码级架构审计

## 写作提纲

1. 先还原实际运行、代码和状态的三条路径；
2. 审查当前右区 V1 的对象与箭头是否会造成错误理解；
3. 给出一张可直接绘制、且明显区别于 Dagger DAG 图的高层架构图；
4. 划清命令失败、后台服务、配置、合并和删除的边界。

## 结论先行

**Container Use 的真实结构不是“Agent 进入一个长期运行的容器”，而是“Agent 经 MCP 驱动本地 Container Use 进程；该进程用 Dagger Engine 产生/更新容器状态，并把代码变更、状态引用与命令记录同步进本地 Git 分支和 Git notes；工程师再通过 CLI 决定接管、合并、应用或删除”。**

因此右图不应复刻左图的 `Engine → API → Function` 垂直层级。更准确且差异明显的表现是：**上方画请求/执行路径，下方画 Git 状态归档路径，右侧单列画工程师的审阅与处置入口。**

Container Use README 明确标为 `experimental`，并说明项目仍处 early development；本审计以 `main` 的当前源码为准，只说明访问日实现，不能当作发布版的稳定契约。 [README](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/README.md)

## 1. 已核验的实际架构

### 1.1 调用和执行路径

```text
Coding Agent
     │ MCP over stdio
     ▼
container-use stdio（本地 MCP server）
     │ dagger.Connect()
     ▼
Selected Dagger Engine
     │ creates a NEW foreground execution container
     ▼
Dagger Container state
     │ Sync + Container.ID
     ▼
State.Container = ContainerID
```

- `container-use stdio` 是 MCP 入口：它先 `dagger.Connect`，再把 client 交给 `RunStdioServer`；连接失败即退出，源码并没有把 Agent 直接连到 Dagger Engine。 [stdio.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/cmd/container-use/stdio.go#L13-L40)
- MCP server 使用 stdin/stdout，并注册 create/open/config/list/run/file/add-service/checkpoint 等 13 个 Environment 工具。`merge`、`apply`、`delete` 并不在该 MCP tool 列表中。 [tools.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/mcpserver/tools.go#L104-L153)
- 环境操作通过 `LoadContainerFromID` 取得 Dagger Container；每次 `apply` 先 Sync 再将 `ContainerID` 写到 `State.Container`。持久化的是 Dagger 对容器**状态**的 ID 引用，不是一个保证长期运行的 Docker/VM 实例。 [environment.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L82-L87) · [apply](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L128-L146)
- `dagger.Connect` 本身没有在 Container Use 源码中指定本地或云端 Engine。Dagger 的默认行为是由主机可用 OCI runtime 拉起与 CLI 匹配的 Engine 镜像，也允许自定义 runner。因此图中可以画“selected Dagger Engine”，不能擅自画成 Dagger Cloud 或每个 Environment 独占的 Engine。 [Dagger container runtimes](https://docs.dagger.io/reference/container-runtimes/)

### 1.2 Git 分支、worktree 和 notes 才是持久化控制面

```text
user repository @ ref
     │ local remote named "container-use"
     ▼
local bare repository ~/.config/container-use/repos/...
     │ branch <env-id>
     ▼
per-environment worktree ~/.config/container-use/worktrees/<env-id>
     ▲                                           │
     └──── export Dagger workdir → commit ───────┘
                                                 │
                                     State JSON → refs/notes/container-use-state
                                  command notes → refs/notes/container-use
                                                 │
                                                 ▼
                               fetched back as user repo ref container-use/<env-id>
```

- Unix 默认基路径是 `~/.config/container-use`；`Repository.Open` 为用户仓库建立本地 bare repo，并将用户仓库的 remote 命名为 `container-use`。因此这不是自动创建 GitHub remote branch。 [repository.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go#L30-L150)
- `Create` 将指定 ref（默认 `HEAD`）推入 local remote 的 `<env-id>` branch，创建对应 worktree，并从该 commit 构造 Dagger Directory。 [repository.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go#L184-L257) · [git.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/git.go#L119-L230)
- 正常前台命令和文件操作后的 `repo.Update` 将 Dagger workdir 导出至该 worktree、提交代码改动、将 State JSON 写入 `refs/notes/container-use-state`，并将操作记录写入 `refs/notes/container-use`；随后把 branch 与 notes fetch 回用户仓库。 [git.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/git.go#L233-L289) · [state/note persistence](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/git.go#L344-L470)

### 1.3 Environment 的实际对象组成和配置关系

`State` 只有创建/更新时间、`Config`、`Container`、标题和 submodule paths。它不是独立数据库，也不包含“完整活体服务会话”。 [state.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/state.go#L9-L52)

```text
project .container-use/environment.json
     │ baseline config for new environments
     ▼
Environment State.Config snapshot
     ├─ base image / workdir
     ├─ setup commands → source mounted before?  No: before source
     ├─ install commands → source mounted before? Yes: after source
     ├─ env / secret references
     └─ service definitions
             │
             ▼
Dagger builds base container, starts configured services, then binds them
```

- 项目配置文件是 `.container-use/environment.json`，包括 base image、workdir、setup/install commands、env、secrets 和 service definitions；默认写入的是用户项目根目录，并作为**新 Environment**的基线。 [config.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/config.go#L14-L42) · [CLI config](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/cmd/container-use/config.go#L20-L66)
- 创建时先运行 setup commands，再挂载 source，最后运行 install commands；配置中的 service 由 Dagger `AsService().Start()` 创建并绑定到环境容器。此顺序才是可复用工具链层的来源。 [buildBase](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L170-L236) · [service.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/service.go#L31-L118)
- 对一个既有 Environment 的 MCP config 修改会更新 `State.Config`、重建 base 并保存到该 Environment；要成为项目的 `environment.json` 默认配置，需要显式执行 `container-use config import <env>`。因此不能将 `environment.json` 误画为每次 Agent 命令自动写入的同级状态对象。 [UpdateConfig](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L238-L252) · [config import](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/cmd/container-use/config.go#L104-L130) · [configuration workflow](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/docs/environment-configuration.mdx)

## 2. 命令、失败、服务和处置的真实边界

### 2.1 前台命令的失败路径

`environment_run_cmd` 的前台分支在一个 **new container** 中运行。`Run` 通过 `Expect: ReturnTypeAny` 获取 exit code、stdout 和 stderr；非零退出不会被 Dagger SDK 自动转为 `runErr`。随后代码添加 command note，**无论 exit code 是否非零都 apply 新 Container state**，工具 handler 再执行 `repo.Update`。因此最准确的文案是：

> 前台命令完成后，exit code、输出和新 ContainerID 被保存；非零退出是可检查的运行结果，不是 Container Use 自动实施的 CI gate。

[MCP run handler](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/mcpserver/tools.go#L521-L610) · [Environment.Run](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L254-L297)

### 2.2 后台命令和服务不是“完整可恢复现场”

后台命令将 container `AsService().Start()`；启动错误/超时会记 notes 并返回 tool error。即使 handler 仍尝试 `repo.Update`，工具回包也明确说明：后台进程的 workdir changes **不会**提交到 environment branch，且后台命令不受后续 filesystem/state changes 影响。配置化服务可以绑定和暴露 endpoint，但 `Load` 源码中 `Services: ?`，没有证据证明跨 Engine 重启可恢复运行中服务、Host Tunnel 或服务内存状态。

[RunBackground](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L299-L352) · [MCP handler message](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/mcpserver/tools.go#L560-L609) · [Environment.Load](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go#L89-L110)

### 2.3 接受或丢弃是工程师通过 CLI 的 Git 操作

- `checkout` 建立/切换本地 `cu-<env-id>` tracking branch，以审阅 `container-use/<env-id>`；`log` 和 `diff` 读取 branch 与 notes。 [checkout/log/diff](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go#L451-L549)
- `merge` 是当前用户分支上的 `git merge --no-ff --autostash container-use/<id>`；`apply` 是 squash merge。两者都是 CLI / Git 处置路径，不是 MCP Environment tool。 [merge/apply](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go#L551-L566)
- `Delete` 移除 local worktree、local bare branch，并 prune user repo remote；源码没有额外的 Dagger Engine garbage-collection 或“停止所有相关运行服务”操作。因此图中只能说“删除候选 Git 环境/解除状态引用”，不能保证“立即销毁全部底层计算资源”。 [Delete](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go#L414-L426) · [delete implementation](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/git.go#L82-L117)

## 3. 当前右区 V1 的审计结论

| V1 表达 | 审计结论 | 容易造成的误解 | 应如何修正 |
|---|---|---|---|
| `Claude Code / Codex / Goose → MCP / CLI → Container Use` | 部分正确 | 把 MCP 与 CLI 混为同一条 Agent 调用箭头。 | 拆成两位调用者：Coding Agent 通过 **MCP stdio**；工程师通过 **CLI**。 |
| `Container Use → manage → Environment lifecycle surface` | 方向过于抽象 | `merge/apply/delete` 看似由 Agent 经 MCP 直接调用。 | 上半只放 MCP 可用的 create/open/run/file/config/service；将 inspect/merge/apply/delete 迁至工程师 CLI 处置列。 |
| `Environment state = branch/worktree + ContainerID + notes + environment.json` | 组成方向正确，但层级混淆 | 误以为四者等价、都在同一处自动写入，且 `environment.json` 是本轮实时状态。 | 将 Git branch/worktree/notes 放入 **Git 持久化面**，ContainerID 放入 **Dagger state**，`environment.json` 放在 **项目默认配置**，以箭头连到 State.Config。 |
| `ContainerID / filesystem state` | 有事实基础 | 容易被读成永驻可登录的整机容器或完整 snapshot。 | 保留 `ContainerID → Dagger-managed container state`，旁注“运行中 service / tunnel 不保证恢复”。 |
| `失败后仍保存 Container state 与 command notes` | 前台命令基本成立 | 容易被读成“失败被系统门禁拦截”或后台命令同样保存文件改动。 | 标注 `foreground non-zero: logged + state applied; not a CI gate`；将后台限制列为小字边界。 |
| `merge · apply · delete · prune` 放在同一生命周期条 | CLI 表面存在这些命令 | 看似是一个自动连续流程，也未说明 delete 的资源边界。 | 画成工程师的三个分叉处置：`inspect → merge / apply / delete Git environment`。 |
| 图形整体是“调用方 → 管理层 → Environment 方框” | 不够贴近实现，也与 Dagger 左图相似 | 两张图都像纵向技术栈，无法突出 Container Use 的差异。 | 改为**双泳道状态流**：执行状态向下归档到 Git；右侧人工审阅/处置。 |

## 4. 可直接落图的高层架构图

### 图的标题和一句话

> **Container Use｜把 Agent 的一次执行归档为可审阅的 Git 候选环境**

副标题：`ContainerID 管理执行状态；branch、worktree 与 notes 管理可交接的代码和记录。`

### 图形结构：三列、双泳道，不使用 Dagger 左图的层级塔

```text
┌──── 调用与执行 ───────────────────────────────────────────────────────┐
│ Coding Agent ── MCP stdio ──▶ Container Use local server ──▶ Dagger Engine │
│                                     │                    │                │
│                                     └──── create/run ───▶ NEW exec container│
│                                                          │                 │
│                                             exit/output + ContainerID      │
└──────────────────────────────────────────────────────────┼───────────────┘
                                                           │
                                                           ▼
┌──── Git 持久化 ───────────────────────────────────────────────────────┐
│ base ref ─▶ local bare remote ─▶ env branch + worktree                 │
│                                 ▲                │                      │
│                                 └── export code ──┘                      │
│                   State JSON (ContainerID/config) ─▶ state note          │
│                   command / exit / output          ─▶ command note        │
│                                 └──────── fetch ──▶ container-use/<id>   │
└────────────────────────────────────────────────────────────────────────┘
                                                  │
                                                  ▼
                         Engineer via CLI: log / diff / terminal / checkout
                                                  │
                                  ┌───────────────┼────────────────┐
                                  ▼               ▼                ▼
                               merge            apply       delete Git env
```

#### 应画出的对象、边和标签

| 位置 | 对象 | 箭头标签 | 视觉作用 |
|---|---|---|---|
| 上泳道左 | `Coding Agent` | `MCP stdio` | 强调 Agent 是调用方，不是 Environment 所在层。 |
| 上泳道中 | `Container Use local MCP server` | `dagger.Connect()` | 体现真实的本地 process / Dagger client 边界。 |
| 上泳道右 | `Dagger Engine` 与小型 `NEW exec container` | `run / file operation` | 体现操作发生在 Dagger 内；不用画 Function DAG。 |
| 执行→归档的竖箭头 | `ContainerID + exit/output` | `apply + update` | 唯一跨泳道箭头，形成“执行结果被归档”的主视觉。 |
| 下泳道 | `local bare remote`、`env branch + worktree`、`Git notes`、`container-use/<id>` | `export → commit → notes → fetch` | 这是 Container Use 有别于普通容器沙箱的主体。 |
| 右侧独立列 | `Engineer via CLI` | `inspect`, `merge / apply / delete` | 明确人工处置与 MCP tool 的边界。 |
| 顶部或底部小标 | `.container-use/environment.json` | `baseline → State.Config → build services` | 只画为配置输入，不与每轮状态并列。 |

#### 图中必须保留的两个边界标记

1. `foreground non-zero → recorded + state applied; not a CI gate`
2. `background service: workdir changes are not committed; live service/tunnel recovery is not guaranteed`

这两个标记既避免夸大，也把 Container Use 和正式 CI gate 清晰分开。

## 5. 对右区文案的可用调整

建议将图下提示条从“失败后仍可继续处理”改为更精确的一句：

> **前台命令的 exit code、输出和 ContainerID 会归档到候选分支；工程师据此查看、继续或决定是否接受代码。**

下方能力卡片也应避免把所有操作都描述为 MCP 自动能力：

1. **任务执行与代码归档分离**：Agent 在 Dagger Container 中操作，代码导出并提交至独立 Environment branch。
2. **执行状态可回到同一环境**：前台命令后保存 ContainerID、exit code 和输出；下一轮可从该状态继续。
3. **配置成为候选环境的一部分**：项目 `environment.json` 定义新环境基线，Environment State 保存本次配置快照和服务定义。
4. **工程师通过 Git 决定去留**：CLI 的 log/diff/terminal/checkout 支持审阅；merge/apply/delete 是明确的人工处置动作。

## 6. 结论的适用边界

这张图能够支持的洞察是：

> Agent 任务的交付物不只是 diff；Container Use 将 Dagger 的执行状态与 Git 可审阅的候选分支、notes 连接起来，使一次尝试具备“继续、审阅、接受或丢弃”的工程生命周期。

它不能支持以下说法：

- 任意 Agent 都会直接控制 Dagger Engine；
- 非零退出被 Container Use 当作 CI 质量门禁；
- Environment 是可跨任何 Engine 永久恢复的活体容器；
- merge/apply/delete 都是 MCP server 内自动完成的 Agent 工作流；
- delete 会立即回收所有 Dagger cache、后台服务或宿主资源。

## 来源与状态审计

| 来源 | 类型 / 状态 | 支撑事实 | 访问时间 |
|---|---|---|---|
| [Container Use README](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/README.md) | 官方仓库 README；experimental / early development | 产品定位、MCP/CLI、当前成熟度 | 2026-08-03 |
| [stdio.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/cmd/container-use/stdio.go) / [tools.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/mcpserver/tools.go) | 当前 `main` 源码，commit `7461f71` | stdio MCP、Dagger client 注入、工具集合、command handler | 2026-08-03 |
| [environment.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/environment.go) / [state.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/state.go) / [config.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/environment/config.go) | 当前 `main` 源码 | ContainerID、配置、command、前台/后台与 service 行为 | 2026-08-03 |
| [repository.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/repository.go) / [git.go](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/repository/git.go) | 当前 `main` 源码 | bare repo、worktree、branch、notes、merge/apply/delete | 2026-08-03 |
| [Environment workflow](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/docs/environment-workflow.mdx) / [configuration](https://github.com/dagger/container-use/blob/7461f71f9f0e6cbfe7a6310b11a910f4701cb649/docs/environment-configuration.mdx) | 官方文档，当前 `main` | 观察、继续、CLI 处置和配置导入语义 | 2026-08-03 |
| [Dagger container runtimes](https://docs.dagger.io/reference/container-runtimes/) / [Container type](https://docs.dagger.io/getting-started/types/container/) | Dagger 官方文档，当前页面 | Engine/runtime 与 Container state 的边界 | 2026-08-03 |

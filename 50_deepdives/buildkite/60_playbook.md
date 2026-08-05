---
title: Buildkite 企业试点与采用 Playbook
tags:
  - research/agentic-cicd
  - research/playbook
  - company/buildkite
status: complete
as_of: 2026-08-02
confidence: medium-high
---

# Buildkite 企业试点与采用 Playbook

## 一、先判断是不是 Buildkite 问题

| 当前痛点 | 优先尝试 | 原因 |
|---|---|---|
| Runner 机型、网络、OS、GPU、Mac 很多，现有 CI 难以统一调度 | Buildkite Queue/Fleet PoC | 这是 Buildkite 的核心适配区 |
| Monorepo 静态 Pipeline 过大、跳过任务多、配置生成复杂 | Dynamic Pipeline PoC | 可将本次任务图变成运行时程序输出 |
| 测试耗时不均、Flaky 无 Owner、重试占用关键路径 | Test Engine 独立 PoC | 可先接收其他 CI 的结果，不必先迁完整 Pipeline |
| Build 本身无法增量、依赖图错误、缓存经常失效 | 先评估 Bazel/Nix/Dagger/Build System | CI Scheduler 不能替代构建语义建模 |
| Pipeline 只有少量固定 Job，GitHub Actions 已稳定 | 暂不迁移 | Provider 替换收益可能不足以覆盖复杂度 |
| 必须完全自托管 Control Plane | Jenkins/GitLab 自托管等路径 | Buildkite Pipelines Control Plane 是 SaaS |

## 二、6—8 周试点路径

### 第 0 周：冻结基线与工作负载

选择一个“复杂但可回退”的代表 Pipeline，最好同时包含：

- 2—3 类执行资源；
- 10 个以上可并行 Job；
- 可识别的 Checkout/Cache/Test 时间；
- 不承担唯一生产发布路径；
- 当前有 4 周以上基线数据。

记录：P50/P95 排队、Agent 启动、Checkout、执行、最慢分片、重试、Infra Failure、单位成功 Build 成本和平台人工时间。

### 第 1—2 周：只验证 Control Plane 与 Queue

1. 建立一个 Cluster；
2. Queue 数保持最小，例如 `linux-general`、`macos`、`private-large`；
3. 固定 Agent Image 和 Toolchain；
4. 保留原 CI 并行运行或可一键回切；
5. 不启用 Dynamic Pipeline、Test Engine、Agentic Step 等邻接能力。

验收：相同 Commit 的关键结果一致；Queue 路由可解释；日志/Artifact 可定位；平台团队能处理 Agent lost、Queue backlog 和 Image rollback。

### 第 3—4 周：引入 Dynamic Generator

将静态 Pipeline 缩成 bootstrap + generator：

```text
changed files / dependency graph / target platform
                         ↓
                  generator program
                         ↓
       typed steps + dependencies + queue routing
                         ↓
                  pipeline upload
```

强制要求：

- Generator 有单元测试和黄金输出；
- 每个 Step 有稳定 `key`；
- 每次生成 YAML/JSON 均作为 Artifact 保存；
- 本地 `--dry-run` 与 CI 校验；
- 上传次数、Job 数和生成时间有阈值；
- Retry 不产生重复 Step；
- 任务选择逻辑有全量回归模式作为 Oracle。

### 第 5—6 周：验证 Agent Portfolio 与成本

对照三种资源策略：

1. Warm self-hosted VM：高利用、强 Cache；
2. Kubernetes ephemeral Pod：突发与隔离；
3. Hosted Agent：零基础设施或峰值 Burst。

不要只比较单分钟价格。统一计算：

```text
单位成功变更成本 = Compute + Hosted Fee + Storage/Network + 平台人工 + 失败重试
```

### 第 7—8 周：可选接入 Test Engine

先上传测试结果并观察，不立即自动 Mute。达到稳定数据量后：

- 用历史耗时分片，对比固定分片的 P95；
- 建立 Test Owner；
- Flaky Workflow 先通知，再自动 Label；
- Mute 必须带到期时间、Issue 和恢复阈值；
- Skip 只用于明确无法执行的测试，不作为常规降噪手段。

Agentic CI、Headless MCP 和 Model Providers 不应与首个迁移里程碑绑定，可在基础 Pipeline 稳定后独立试点。

## 三、通过与停止条件

### 通过条件

- P95 关键路径至少改善一个内部设定门槛，或在相同性能下降低总成本；
- Queue 等待与 Agent 启动的 P95 可解释、可告警；
- 生成任务数明显小于静态最大图，且无漏跑回归；
- Infrastructure Failure Rate 不高于基线；
- 单位成功 Build 成本和平台人工投入都有可接受趋势；
- 应用团队能在不理解底层 Agent 实现的情况下选择标准 Queue；
- 原 CI 回退演练成功。

### 停止条件

- 大部分收益来自 Build System/Cache 修复，与迁移 Buildkite 无关；
- Dynamic Generator 复杂度超过被替代的静态配置，且无法建立测试合同；
- Self-hosted Fleet 运维时间抵消控制面收益；
- Hosted Agent 无法满足必要的区域、网络、硬件或成本约束；
- Test Engine 数据不足、测试标识不稳定或自动隔离掩盖缺陷；
- 关键能力依赖 Preview/未明确状态接口且无替代路径。

## 四、推荐平台产品边界

| 平台团队负责 | 应用团队负责 | Buildkite 负责 |
|---|---|---|
| Cluster/Queue 设计、Agent/Stack、Image、容量、Cache、Generator SDK、标准 Plugin、SLO | 业务测试、依赖声明、资源需求、Step 组合、Flaky 修复、Owner | SaaS 编排、Build 状态、Job 调度、UI/API、Hosted Agent（选用时）、Test/Registry 产品能力 |

平台团队应提供少量稳定的“能力合同”，例如：

- `queue=linux-general|macos|gpu|private-large`；
- `generateAffectedServices(commitRange)`；
- `splitTests(suite, partitions)`；
- 标准 Checkout/Cache/Artifact/Secret Plugin；
- Generator 与 Agent Image 的兼容矩阵。

不要向应用团队暴露完整基础设施标签组合，否则 Queue 会演化成新的隐式平台 API。

## 五、与 Dagger 组合时的采用顺序

如果两者都考虑采用，先明确哪个问题更痛：

- **Job 内构建/测试逻辑重复、本地不可复现：** 先抽 Dagger Function；
- **Job 图巨大、异构资源与排队复杂：** 先建立 Buildkite Queue/Dynamic Pipeline；
- **两者都存在：** 保留 Buildkite 负责事件与 Job/Fleet，Dagger 只进入少量高复用 Job；分别测量 Generator 与 Dagger Engine/Cache 的价值，避免一次性双重迁移导致无法归因。

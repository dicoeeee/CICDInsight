---
title: Dagger 采用案例与替代关系地图
tags:
  - research/agentic-cicd
  - research/case-map
  - tool/dagger
status: complete
as_of: 2026-08-02
---

# Dagger 采用案例与替代关系地图

## 采用架构模式

| 模式 | 触发与控制面 | Dagger 位置 | 适合场景 | 核心风险 |
|---|---|---|---|---|
| 本地开发 | 开发者终端与本地身份 | CLI 启动/连接本地 Engine，运行 Function/Check | Pipeline 开发、失败复现、提交前检查 | 本地凭据、Docker 权限和网络可能比 CI 更宽 |
| Hybrid CI | GitHub/GitLab/Jenkins 等处理事件、Job、Runner、Token 与门禁 | Runner 内 CLI 调 Engine | 渐进迁移、保留现有治理、多 CI 复用 | 把逻辑可移植误作身份、Cache 与审批可移植 |
| 自管远程 Engine | CI 控制面 + 企业 K8s/VM/裸机 | Engine 作为共享或专用执行节点 | 高性能 Cache、受监管基础设施、集中运维 | 特权 Engine、多租户、Cache、网络与升级责任 |
| Dagger Cloud Trace/Cache | 现有 CI 继续触发 | 自管 Engine 连接 Dagger Cloud | 集中 Trace、协作、共享 Cache | SaaS Token、Trace/Cache 数据与角色粒度 |
| Cloud Engines EA | 本地或 CI Client | 托管 Engine 执行与分布式 Cache | 不想自管 Engine、需要弹性计算 | Early Access、数据/隔离/SLA/退出未充分公开 |
| Cloud Checks EA | Dagger Cloud GitHub App | Git Event + 托管 Engine + Commit Status | 希望取消单独 CI 外壳的 Check 流程 | 当前能力面窄、EA、不能外推为完整 CD 控制面 |
| Agent Runtime | 现有 CI、开发者或自定义应用 | LLM/Env 调 Dagger Functions 与 MCP | 失败诊断、修复候选、评测、受控工具循环 | 未稳定、模型错误、工具授权和数据外发 |

## 署名案例

| 案例 | 公开架构与结果 | 可以证明 | 不能证明 | 证据等级 |
|---|---|---|---|---|
| Airbyte | 轻量 GitHub Action → 自定义 Dagger CLI → Kubernetes Engine Fleet；Cloud 分布式缓存；Karpenter 扩缩。案例称 CI 平均快 2—5 倍、部分时段成本约为此前四分之一 | 混合 CI、远程 Engine、共享 Cache 和自动扩缩可组合；客户公开报告方向性收益 | Dagger 单因素因果、行业平均 ROI、当前 Cloud Checks 成熟度 | C；[[00_sources/briefs/2023-dagger-airbyte-ci-case-study|Source Brief]] |
| Puzzle | GitLab/OpenShift/ArgoCD；因特权 Engine、SCC 和 Cache 运维问题改为 KubeVirt VM 内运行 Engine | Engine 权限会真实改变企业部署拓扑；VM 可成为更强隔离与自主管理边界 | VM 必然更安全/更便宜；完成态性能和长期稳定性 | C；[[00_sources/briefs/2025-dagger-puzzle-kubevirt-ci|Source Brief]] |
| Discern | 每个 PR 建完整临时应用；同一 Pipeline 本地/远端；2023 案例称热缓存把 15 分钟构建降至 2 分钟 | PR 临时环境与本地复现是实际用法；热缓存可能显著缩短重复运行 | 当前版本性能、冷缓存收益、独立实验与长期成本 | C；[官方案例](https://dagger.io/blog/discern-use-case/) |
| Flipt | Dagger 承载构建与集成测试；2023 案例称冷缓存约 10 分钟、后续运行 17 秒 | 冷/热缓存差异可能极大，适合验证 Cache 价值 | 不能用热缓存数字代表首次运行、其他项目或当前版本 | C；[官方案例](https://dagger.io/blog/flipt-use-case/) |
| Dagger 自用 Cloud Checks | Changelog 称 Dagger 已将自身 CI 从 GitHub Actions 切到 Cloud Checks | 产品团队正在 Dogfood 其托管 CI 路径 | 外部客户成熟度、SLA、广泛 Git Provider 与企业治理 | B；[Changelog](https://dagger.io/changelog/) |

## 案例共同模式

1. 收益不是由“把 YAML 改成代码”单独产生，而是同时重构 Module 边界、Cache、Runner 与平台接口。
2. 最有价值的路径通常先保留既有 CI 事件与门禁，把 Dagger 引入执行数据面。
3. 热缓存收益容易被营销放大；冷启动、Cache 传输和失效必须单独测量。
4. Engine 特权与 Cache 运维可能抵消开发体验收益，并把成本转移给平台/安全团队。
5. 现有案例都由 Dagger 发布，适合证明采用形态，不适合证明行业因果和普遍 ROI。

## 相邻方案：替代与互补

| 方案类别 | 它最擅长什么 | Dagger 能替代的部分 | Dagger 不能自然替代的部分 | 推荐关系 |
|---|---|---|---|---|
| GitHub Actions / GitLab CI / Jenkins | Event、Job/Runner、权限、审批、生态集成、UI | Job 内 Shell/YAML/Groovy 执行逻辑、跨平台复用层 | SCM 事件、Branch/Environment Gate、平台 IAM、组织治理 | 先 Hybrid；按 Cloud Checks 成熟度再评估控制面迁移 |
| Tekton / Argo Workflows | Kubernetes 原生 Task/Workflow 调度与资源编排 | Task 内构建测试逻辑、共享 Module | Cluster 调度、CRD 状态机、Operator/Admission 生态 | 控制面 + Dagger 执行 |
| Make / npm scripts / Taskfile | 简单、本地、低开销命令组合 | 容器隔离、类型输入输出、跨语言 Module、自动 Cache | 极低复杂度与无 Engine 成本 | 简单项目保留 Task Runner；复用扩大后再迁 |
| BuildKit / Dockerfile | 容器镜像构建与 Layer Cache | Dagger 可直接调用并扩展到测试、服务和发布 | Dockerfile 生态和底层镜像构建语义 | Dagger 在其上编排，不视为同类替代 |
| Bazel / Nix | 精细构建依赖图、增量/远程 Cache、确定性或可复现构建机制 | 外围测试、服务、制品与发布编排 | 语言规则、精确依赖分析、可复现性语义 | Dagger 调用 Bazel/Nix，避免重写 Build Graph |
| Earthly | 容器化、本地/CI 一致的构建定义 | 功能重叠较大；Dagger 提供类型化 Module/API 与 Agent/Cloud 路径 | 迁移成本、语法/生态偏好 | 新选型优先验证 Dagger；存量按维护与 TCO 决策 |
| 通用 Agent Harness | 推理、上下文、规划、工具循环与人机交互 | Dagger 可提供受控 Function/Container/Trace 执行环境 | 完整身份委托、评测控制面、人机审批与业务 Oracle | Harness 负责判断，Dagger 负责确定性执行 |

## 决策矩阵

| 条件 | 采用倾向 | 原因 |
|---|---|---|
| 多 CI Provider 或频繁更换 Runner/平台 | 强 | 抽离执行逻辑能降低 Provider YAML/Groovy 重复 |
| Monorepo、多语言、复杂集成测试与临时服务 | 强 | Module、Service、类型对象和缓存具有组合价值 |
| 平台团队准备把交付逻辑当内部产品维护 | 强 | Module API、版本、Owner 与复用半径匹配 |
| 需要本地复现 CI 失败 | 强 | 同一 Module/Engine 模型缩短反馈回路 |
| 简单单仓、少量稳定 Step、原生 CI 已易维护 | 弱 | Engine、SDK、Module 与容器依赖可能增加 TCO |
| 必须 Rootless、运行不可信多租户代码 | 弱 / 阻塞 | 当前 Engine 特权与 Rootless 限制需额外隔离 |
| 工作负载依赖专用硬件、宿主状态或非 Linux 能力 | 先实验 | 逻辑可移植性可能被运行基础设施打破 |
| 目标是完整替代 Jenkins/GitHub Actions | 谨慎 | Cloud Checks 仍 EA，控制面与 CD 能力尚未充分证明 |
| 目标是生产自治 Agent | 阻塞 | LLM/Env 未稳定，外部授权与 Oracle 仍缺一不可 |

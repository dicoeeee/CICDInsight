---
title: Dagger 专题问题树
tags:
  - research/agentic-cicd
  - research/question-tree
  - tool/dagger
status: complete
as_of: 2026-08-02
---

# Dagger 专题问题树

## Q1：Dagger 究竟是什么层

- Dagger 是 Pipeline-as-Code DSL、Build Engine、CI Orchestrator，还是软件交付运行时？
- CLI、Client、Session、Engine、Runner、GraphQL/DagQL 分别负责什么？
- Module、Function、Object、Core Type、Check、Service 如何组成执行图？
- Dagger 与容器运行时、BuildKit、Kubernetes、GitHub Actions/Jenkins 的职责边界是什么？
- Cloud Checks 出现后，“Dagger 不是 CI 平台”的旧判断需要怎样修正？

## Q2：可移植性为什么成立，又在哪里失效

- 宿主目录、秘密、Socket、Service 和环境变量如何显式进入 Function？
- 相同 Function 在本地和 CI 中能保持哪些语义一致？
- Linux 容器运行时、CPU 架构、网络、DNS、证书、外部 API、Secret Provider 和特权能力会造成哪些差异？
- 从一个 CI Provider 迁到另一个时，哪些 Dagger 逻辑可复用，哪些 Trigger、身份、Runner 和制品集成必须重做？
- Dagger Module 自身是否形成新的 API、Engine 版本与生态锁定？

## Q3：性能机制与成本模型是什么

- 内容寻址、惰性求值、增量执行、并发和 Layer/Volume/Function Cache 如何协同？
- Cache Key 包含哪些输入，外部时间与远程状态如何处理？
- 哪些发布、部署、通知、随机数或远程读取 Function 必须使用 TTL、Session 或 `never`？
- 本地持久 Engine、临时 Runner、Kubernetes DaemonSet、分布式缓存和 Cloud Engine 的成本/命中率差异是什么？
- 官方客户性能数字能证明什么，是否混入 Runner 架构、代码重构和基础设施变化？

## Q4：安全与治理边界是什么

- “Function fully sandboxed”精确指什么，是否意味着 Engine 可无特权运行？
- 默认 `insecureRootCapabilities`、特权 Engine、rootless 不支持对多租户意味着什么？
- `Directory`、`Secret`、`Socket`、`Service` 类型如何限制隐式宿主访问？
- Secret Scrubbing、Cache Key 和用户把明文写入 File/Log 之间有什么边界？
- Module 的 Git 来源、版本 pin、传递依赖、Daggerverse 索引与 Engine 镜像如何治理供应链？
- CI 身份、OIDC、Cloud Token、GitHub App、网络 Egress、Trace 可见性和删除策略由谁控制？
- Dagger 能否生成 Provenance、执行 Policy 或签名，还是仍需外部系统完成最终证明？

## Q5：Dagger Cloud 的真实产品边界是什么

- Trace、Module Catalog、分布式缓存、Cloud Engines、Cloud Checks 分别解决什么问题？
- 哪些是 GA/正式文档能力，哪些仍为 Early Access 或 In development？
- 使用 Dagger Cloud 会上传哪些 Trace、Module、Git 元数据、Cache 或执行内容？
- Admin/Member、GitHub App、Repository 扫描和公共 Trace 默认行为是否满足企业治理？
- Cloud Checks 当前支持哪些 Git Provider、事件、Secret Provider、身份联邦和 Commit Status？
- 开源 Engine 的可退出性是否抵消 Cloud Cache/Compute/Control Plane 的商业锁定？

## Q6：LLM 与 Agent 能力是否改变核心价值

- `LLM`、`Env` 和 Dagger Functions 如何构成 Tool-use Loop？
- Dagger Module 如何暴露为 MCP Server，又如何连接外部 MCP Server？
- 模型能访问哪些资源，谁决定工具可见性与执行权限？
- Agent 输出如何被 Check、Test、Scan、Policy 或人工 Oracle 验证？
- Dagger 在 Agentic CI/CD 中更像通用 Agent Harness、受控执行沙箱，还是可组合工具运行时？
- 现有公开材料是否证明比通用 Agent Runner 更高的成功率、安全性或单位成本？

## Q7：何时应该采用

- 多 CI Provider、复杂集成测试、Monorepo、多语言平台团队是否构成强适配？
- 简单单仓、成熟原生 Actions、严格 rootless、多架构硬件或高度事件驱动流程是否构成弱适配？
- 与原生 CI Reusable Workflow/Actions、Task Runner、Bazel/Nix、Earthly、Tekton/Argo、BuildKit/Dockerfile 如何划界？
- 应从单个 Check、共享 Module、完整 Pipeline 还是 Cloud Checks 开始？
- 如何定义 4—8 周 PoC、基线、退出条件、回滚和停止条件？

## Q8：Presentation-ready 的证明标准是什么

- 单页主张能否由执行机制直接证明，而不是营销口号？
- Engine、Cloud 与 AI 的产品状态是否分开标注？
- 安全图是否同时画出 Function 沙箱和特权 Engine？
- 客户数字是否标记为厂商署名案例，并保留共同变化因素？
- 是否存在足以推翻“可移植执行层”主张的反例，或仅限制其适用范围？

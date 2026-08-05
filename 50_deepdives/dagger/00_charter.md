---
title: Dagger 专题研究边界
tags:
  - research/agentic-cicd
  - research/deep-dive
  - tool/dagger
status: complete
as_of: 2026-08-02
---

# Dagger 专题研究边界

## 决策目标

回答 CTO、研发效能负责人和平台工程负责人在以下决策中的真实问题：

1. Dagger 应被放在现有 CI/CD 架构的哪一层，它替代什么、不替代什么；
2. “本地与 CI 一致”“自动缓存”“沙箱化”“无平台锁定”分别在什么条件下成立；
3. 何种团队、仓库和工作负载值得采用 Dagger，何种场景应继续使用原生 CI、Build System、Task Runner 或容器构建工具；
4. 开源 Engine、Dagger Cloud、Cloud Engines、Cloud Checks 和 LLM Primitive 的产品状态与控制边界；
5. 企业如何以可回退、可量化和最小权限方式验证采用价值。

## 纳入范围

- Dagger CLI、Engine、Session、Runner、GraphQL/DagQL 执行模型；
- Module、Function、核心类型、Checks、Services、Secrets、Cache 与 OpenTelemetry；
- Go、Python、TypeScript、PHP、Java、.NET、Elixir、Rust 等 SDK 的跨语言契约；
- GitHub Actions、GitLab CI、Jenkins、CircleCI、Argo、Tekton 等现有 CI 集成；
- Dagger Cloud 的 Trace、Module、分布式缓存、托管 Engine 与 Cloud Checks；
- LLM 类型、Tool Use、Environment、MCP 输入输出和 Agent 运行边界；
- 署名客户案例、官方自用信号、已公开限制、版本演进和替代方案；
- 从代码检查、测试、构建、制品、部署到发布的适用性。

## 时间窗口

- **主观察窗口：** 2024-01-01 至 2026-08-02；
- **历史基线：** 2022 年公开发布材料只用于解释最初定位，不作为当前状态；
- **版本基线：** GitHub Latest Release `v0.21.8`（2026-07-29）；文档与 Release 差异必须单独标记；
- **未来能力：** `next` 文档、Changelog 的 In development / Early Access 内容不得写成稳定现状。

## 非目标

- 不编写完整 Dagger 入门教程或逐 SDK API 手册；
- 不把 Dagger 与所有 CI/CD 产品做采购级功能清单横评；
- 不以 GitHub Star、营销引语或厂商案例数字证明行业平均收益；
- 不把“容器内可复现”写成跨 CPU、网络、秘密、外部服务和宿主能力的绝对一致；
- 不把 `Secret` 类型、Function 沙箱或 LLM Environment 写成完整 IAM、Policy、审批或供应链证明；
- 不通过本轮研究直接修改现有 Presentation 或主报告；只有专题结论通过审计并改变既有观点时再回流。

## 核心术语

- **Dagger Engine：** 执行 Dagger API 调用、容器操作、服务和缓存的运行引擎。
- **Dagger Function：** 接收类型化输入、组合 Dagger API 操作并返回类型化输出的计算与复用单元。
- **Dagger Module：** 一组 Functions、对象、依赖和元数据组成的版本化能力包。
- **Session：** Client/CLI 与 Engine 之间的一次连接生命周期，承载 GraphQL API、宿主资源同步、秘密和服务代理。
- **逻辑可移植性：** 相同 Dagger Module/Function 可由不同环境调用；不等于运行基础设施和外部依赖完全相同。
- **Cloud Engines：** Dagger 托管的 Engine 计算与缓存能力，当前官方标记为 Early Access。
- **Cloud Checks：** 监听 Git 事件并在托管 Engine 上运行 `dagger check`、回写 Commit Status 的托管 CI 能力，当前官方标记为 Early Access。
- **Oracle：** 在 Dagger 和 Agent 之外判定结果是否可接受的 Test、Scan、Policy、Signature、SLO 或人工审批。

## 证据口径

| 等级 | 来源 | 可支持 | 不可直接支持 |
|---|---|---|---|
| A | 当前官方文档、版本化文档、源码、Release、许可证 | 接口、默认值、版本、限制、实现存在性 | 普遍效果与独立安全有效性 |
| B | 官方 Changelog、工程博客、设计文档 | 架构意图、发布轨迹、产品状态 | GA 承诺、第三方复现 |
| C | Dagger 发布的署名客户案例或客户引语 | 该客户公开采用与厂商记录的结果 | 独立对照、行业平均、因果外推 |
| D | 产品页、愿景文章、未量化演示 | 定位与研究线索 | 正式采购结论、成熟度与 SLA |

## 验收标准

- 必需交付物 `README`、Charter、Question Tree、Evidence Map、Findings、Report 全部完成；
- 至少形成 10 个可审计 Claim，每个关键 Claim 有一手证据、限制和置信度；
- 明确拆分开源 Engine、现有 CI 集成、Dagger Cloud 与 AI/LLM 能力；
- 对“可移植、缓存、安全、锁定、性能”五类高风险主张分别给出成立条件和反例；
- 至少比较 4 类替代/互补方案，不用单一功能表得出结论；
- 客户数字全部标记为厂商发布的署名案例，不外推；
- `presentation_ready` 前完成逐主张链接、状态、时间和引用边界审计。

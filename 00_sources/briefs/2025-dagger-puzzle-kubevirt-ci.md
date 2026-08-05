---
title: Puzzle 以 KubeVirt 隔离 Dagger 特权 Engine
source_id: dagger-puzzle-kubevirt-ci-2025
organization: Dagger
source_type: vendor-published-customer-case
published: 2025-02-24
verified: 2026-07-28
availability: vendor-case-study
confidence: medium
geography:
  - Europe
lifecycle_stages:
  - testing-gates
  - build
tool_categories:
  - ci-orchestration
  - virtualization
  - kubernetes
  - case-study
company_topics:
  - Dagger
  - Puzzle
autonomy_levels:
  - L0
tags:
  - research/agentic-cicd
  - evidence/source-brief
  - tool/dagger
---

# Puzzle 以 KubeVirt 隔离 Dagger 特权 Engine

## 来源

- [Streamlining CI Infrastructure at Puzzle with Dagger, KubeVirt, and ArgoCD](https://dagger.io/blog/puzzle-case-study)，Dagger 发布的署名客户案例，2025-02-24。

## 一句话结论

Puzzle 案例不是“Dagger 天然适配 Kubernetes”的简单成功故事，而是一个重要反例：由于 Engine 需要特权能力、CI 团队缺少集群管理权并遇到缓存运维问题，该团队最终把 Engine 放入 KubeVirt 虚拟机，以 VM 作为更强的权限与运维边界。

## 可核验事实

- Puzzle 使用 GitLab CI/CD、OpenShift Kubernetes Runner 和 ArgoCD。
- 初始 Dagger Engine 以 DaemonSet 运行；Engine 需要 Privileged Container，CI 团队必须向运维团队申请额外 Security Context Constraints。
- Cache 过快占满时，CI 团队也需要运维团队人工清理节点磁盘。
- 新架构使用 KubeVirt 管理临时 Ubuntu VM，在 VM 中安装 Docker 与 Dagger。
- VM 挂载持久卷保留 Docker 与 Dagger 数据；GitLab Kubernetes Executor 创建的 Job Pod 通过 Kubernetes Service 连接 VM 内 Engine。
- 案例称 VM 提高可扩展性、隔离、升级与测试便利性；团队仍计划评估共享缓存、资源规格和 TCP/Unix Socket 性能。

## 对洞察的价值

- 证明 Dagger 可以嵌入 GitLab、OpenShift、KubeVirt 与 ArgoCD 的组合架构。
- 证明 Engine 特权、平台团队权限分工与 Cache 运维会实质改变部署拓扑。
- 支持“Function 沙箱化不能替代 Engine 级隔离”的关键反例。

## 限制与待验证项

- 来源由 Dagger 发布，效果描述没有独立量化对照。
- KubeVirt 方案增加 VM、持久卷、Service 路由和镜像维护成本。
- 案例仍在测试资源配置、共享缓存和连接性能，不能视为完成态参考架构。
- 未公开攻击面、租户隔离、安全审计或长期故障率。

## 可引用判断

- 对受监管或多租户平台，Dagger Engine 的特权需求可能迫使企业把它部署到独立节点或 VM；这项成本必须进入采用决策。

## 专题映射

- [[50_deepdives/dagger/30_case-map|Dagger Case Map]]
- [[50_deepdives/dagger/60_playbook|Dagger 企业采用 Playbook]]

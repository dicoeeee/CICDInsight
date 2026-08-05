---
title: Dagger Function 沙箱、Secret 与 Engine 特权边界
source_id: dagger-sandbox-secrets-engine-security-2026
organization: Dagger
source_type: official-docs
published: null
verified: 2026-07-28
availability: released
confidence: high
geography:
  - global
lifecycle_stages:
  - testing-gates
  - build
  - security-compliance
  - deployment
tool_categories:
  - workflow-runtime
  - sandbox
  - secrets
  - governance
company_topics:
  - Dagger
autonomy_levels:
  - L0
  - L1
  - L3
tags:
  - research/agentic-cicd
  - evidence/source-brief
  - tool/dagger
---

# Dagger Function 沙箱、Secret 与 Engine 特权边界

## 来源

- [Sandboxed Runtime](https://docs.dagger.io/features/sandbox/)，无发布日期，2026-07-28 访问。
- [Secrets](https://docs.dagger.io/extending/secrets/)，无发布日期，2026-07-28 访问。
- [Engine Configuration](https://docs.dagger.io/reference/configuration/engine/)，无发布日期，2026-07-28 访问。
- [Container Runtimes](https://docs.dagger.io/reference/container-runtimes/)，无发布日期，2026-07-28 访问。

## 一句话结论

Dagger 通过类型化参数让 Function 默认看不到宿主文件、环境、Socket、服务和 Secret，但这一 Function 级能力边界不能掩盖 Engine 仍需特权运行、默认允许不安全 Root Capability 且当前不支持 Rootless 的事实。

## 可核验事实

- Function 默认不能直接访问宿主环境、服务、文件系统或 SSH Agent；顶层调用必须显式传入 `Directory`、`Socket`、`Service`、`Secret` 等资源。
- 资源可由顶层 Module 继续传给依赖 Module，因此最上层的参数与依赖关系决定实际能力传播。
- `Secret` 默认从日志和输出中清除，不写入构建文件系统或缓存；但用户若主动把明文写入普通 File，仍可能被缓存。
- Secret 明文会参与相关操作的 Cache Key；相同明文可能共享相关缓存条目，`cacheKey` 可改变等价判断。
- Engine 默认安全策略允许 `insecureRootCapabilities`，效果类似向相关操作开放特权能力；该选项可关闭。
- Dagger Engine 当前不能以 Rootless 容器运行；官方自定义 Engine 示例使用 `--privileged`。

## CI/CD 相关性

- **能力输入：** 显式目录、Socket 与 Secret 参数减少传统脚本对宿主全局状态的隐式依赖。
- **身份与秘密：** Dagger 保护 Secret 的传递形式，但凭据的颁发、Scope、轮换与撤销仍由 CI、Cloud IAM 和 Secret Manager 决定。
- **多租户：** Function 级沙箱不足以单独证明共享特权 Engine 可以安全运行互不信任的 Tenant。
- **Agent：** 传给 Agent Env 的 Function、Socket、Secret 和写对象定义其真实能力上限。

## 限制与待验证项

- “Fully sandboxed”是官方对 Function 默认宿主访问的描述，不应扩展成 Engine 无特权、无逃逸风险或已通过第三方隔离审计。
- 默认开放 `insecureRootCapabilities` 与安全产品页的概括性措辞存在理解张力，企业必须显式核验实际 Engine 配置。
- 公开文档不足以证明 Dagger Cloud 托管 Engine 的租户隔离实现、Rootless 状态或第三方安全验证。

## 可引用判断

- Dagger 改善的是资源显式传递与 Function 能力边界；生产授权、网络隔离和 Engine 租户隔离仍需外部控制面完成。

## 专题映射

- [[50_deepdives/dagger/20_evidence-map|Dagger Evidence Map]]
- [[50_deepdives/dagger/60_playbook|Dagger 企业采用 Playbook]]

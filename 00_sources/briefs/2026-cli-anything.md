---
title: CLI-Anything：Agent 原生接口生成与 CLI-Hub
aliases:
  - HKUDS CLI-Anything
  - CLI-Anything Brief
tags:
  - research/agentic-cicd
  - evidence/source
  - tool/cli
  - tool/skill
  - open-source
status: complete
as_of: 2026-07-15
source_status: available-open-source
confidence: medium-high
---

# CLI-Anything：Agent 原生接口生成与 CLI-Hub

> [!summary] 一句话判断
> CLI-Anything 不是 CI/CD 平台或通用 Agent Harness，而是“Agent 原生接口工厂”：它把有源码或后端 API 的软件转成结构化、可测试、可安装的 CLI 与 `SKILL.md`。对 CI/CD 的意义在于让长尾测试、构建、制品、部署和运维工具进入 Agent 工具面；但生成物仍必须经过供应链、权限和外部 Gate 治理。

## 基本信息

| 项目 | 内容 |
|---|---|
| 组织 / 项目 | HKUDS / `HKUDS/CLI-Anything` |
| 官方仓库 | [HKUDS/CLI-Anything](https://github.com/HKUDS/CLI-Anything) |
| 原始论文 | [CLI-Anything: Towards Agent-Native Computer Use](https://arxiv.org/abs/2606.03854) |
| 当前版本信号 | 项目 [v0.4.0](https://github.com/HKUDS/CLI-Anything/releases/tag/v0.4.0)，2026-06-25；CLI-Hub PyPI 0.4.1，2026-07-09 |
| 许可证 | Apache-2.0 |
| 项目状态 | 活跃开源；不同生成 harness 和 Agent 适配器成熟度不一 |
| CI/CD 相关阶段 | 跨阶段 2—8，最直接影响测试、构建、制品工具、部署和运维接口 |
| 建议自治定位 | 接口生成本身 L1—L2；使用生成 CLI 的自治上限由运行权限、环境与外部 Gate 决定 |

项目获得了显著的开源关注，但 Star/Fork 会快速变化且不同页面缓存可能不一致，本报告不再用精确快照支撑成熟度判断。关注度不能证明安全性、企业支持、CI/CD 成功率或生产自治成熟度。

## 它解决的核心问题

CLI-Anything 本身不是一个输入源码后按固定算法输出 CLI 的编译器；`/cli-anything` 是由 Claude Code、Codex、Pi、OpenCode 等宿主 Coding Agent 执行的方法规范与 Skill。项目的前提是：很多桌面、GUI 或历史软件虽有可用能力，却没有为 Agent 设计的机器接口。依赖屏幕坐标、视觉识别和交互时序的 GUI 操作较脆弱；而 CLI 能提供：

- 结构化、可组合的命令；
- 通过 `--help` 自描述；
- JSON 等机器可读输出；
- 明确退出码和可预测反馈；
- 适合本地终端、容器和 CI Runner 的轻量执行面。

CLI-Anything 的论文将其概括为 Agent-native computer use：软件向 Agent 暴露结构化命令、显式状态、确定性反馈和机器可读协议，而不是要求 Agent 模拟人类点击 GUI。

## 七阶段生成流程

官方仓库描述的生成过程包括：

1. 分析源码结构与后端 API；
2. 设计命令组、状态模型与输出格式；
3. 用 Python Click 实现 CLI，并按需提供 REPL、JSON、undo/redo；
4. 规划测试；
5. 编写单元测试与端到端测试；
6. 记录实现和测试结果；
7. 发布并安装到可执行路径。

生成的 harness 包含 `SKILL.md`。CLI-Hub 则提供注册和包管理能力，Agent 可以搜索、安装和启动 CLI harness。仓库列出的适配目标包括 Claude Code、Codex、OpenCode、Pi、OpenClaw、GitHub Copilot CLI 等，其中部分仍带 Experimental 或 Community 标记。

v0.3.0 启动 CLI-Hub；v0.4.0 进一步引入 CLI-Matrix、30 个新 CLI、17 项修复和 4 类安全加固。CLI-Matrix 把任务映射到 capability/provider，并提供发现、preflight、dry-run 和批量安装；它是策展与安装层，不是 DAG/Pipeline Workflow Engine。2026-07-09 主分支快照的注册表包含 79 个 harness CLI、22 个公共 CLI 和 5 个 Matrix。论文更早的快照报告 65 个 harness、18 个公共 CLI、61 个 companion skills 和 5 个 preview integrations；这些都是项目自报的覆盖数据，不应外推为任务成功率。

CLI-Hub 0.4.1 的 PyPI 包带 Trusted Publishing Attestation，是发布来源的正面信号，但不能传递为所有下游 harness 的供应链保证。当前公共 Registry 中许多 harness 从未固定 Commit 的默认分支安装，安装器还允许执行 Registry 提供的 Shell 命令；公共 Hub 因此适合发现和实验，不应直接作为生产 Runner 的信任根。

## 对 Agentic CI/CD 的具体价值

### 1. 补齐长尾工具的 Agent 行动面

企业 CI/CD 不只有主流云 API，还存在大量内部构建器、桌面工具、测试工具、制品处理器和运维软件。若这些工具有源码或后端能力，接口生成器可降低把它们接入 Agent Harness 的成本。

### 2. 让接口本身可进入 CI

生成流程同时产出 CLI、测试和文档，意味着接口可以像代码一样版本化并在流水线中验证。相比让 Agent 直接点击 GUI，这更容易重放、固定版本、审计命令和定位错误。

### 3. 连接 CLI、Skill 与 MCP

CLI-Anything 支持的模式不是“CLI 替代 MCP”。更合理的分层是：

```text
软件源码 / 后端 API
  → 生成并测试 CLI
  → 生成或维护 Skill，说明何时和如何使用
  → 必要时通过 MCP 暴露给多个 Agent 客户端
  → 在沙箱 / Runner 中以任务身份执行
```

CLI 是可执行能力面，Skill 是使用方法，MCP 是可选的发现与互操作适配；身份、授权和审批仍属于外部控制面。

### 4. 形成内部 Agent Tool Catalog

平台团队可以借鉴 CLI-Hub，建设经过审核的内部 CLI/Skill 目录：登记 Owner、版本、风险级别、只读/写范围、支持环境、测试证据、签名和撤回状态。这样“工具可被发现”与“工具已获生产授权”可以明确分开。

## 可落地实践案例设计

### 案例 A：把内部构建诊断工具接入 CI 修复 Agent

1. 对内部构建工具生成只读 CLI，仅开放查询配置、依赖图和失败详情；
2. 为 JSON Schema、退出码和典型失败编写固定测试；
3. 将 CLI 与 `SKILL.md` 锁定版本并放入隔离 Runner；
4. Agent 读取失败日志并调用 CLI 形成诊断和 Patch；
5. 输出限定为 Draft PR，由原有 Build/Test Gate 复验。

适合的指标是诊断准确率、首次修复成功率、无效命令调用、Runner 成本和人工修改量。该案例可控制在 L2。

### 案例 B：为制品处理工具建立只读/写分离接口

生成两个命令域：只读域允许查询版本、签名和 SBOM；写域只能在批准后对指定测试仓执行上传或标记。生产晋级、签名策略和删除操作不放入 Agent 默认 Toolset。批准必须绑定制品哈希、目标环境和有效期。

该案例验证的不是“Agent 能否执行命令”，而是生成接口能否表达最小权限、幂等、dry-run 和不可绕过的 Policy。

### 案例 C：把 GUI 运维工具用于发布后只读调查

先生成查询状态、导出诊断和读取历史的 CLI，不开放恢复动作；在事故回放数据上评测 Agent 能否通过 CLI 收集证据并输出时间线。只有在稳定达到阈值后，才考虑将少量预批准、可回滚命令置于 Runbook-bound L3。

## 主要风险与验证清单

| 风险 | 为什么重要 | 最低控制 |
|---|---|---|
| 命令覆盖不完整或语义错误 | 一次生成可能遗漏关键状态和边界条件 | 工具 Owner 评审、固定任务集、单元与 E2E 测试 |
| 危险动作被直接暴露 | 包装 GUI/API 可能扩大删除、发布或凭据动作 | 默认只读、命令白名单、环境 Scope、批准后执行 |
| `SKILL.md` 与 CLI 漂移 | Agent 可能按过期说明调用新权限面 | Skill 与 CLI 同版本发布、回归测试、变更审查 |
| 生成链供应链风险 | 生成代码、依赖和 Skill 都可被投毒 | 来源记录、锁定依赖、扫描、签名、内部 Registry |
| 公共 Hub 安装链 | 可变 Registry、默认分支和 Shell 安装扩大代码执行面 | 内部镜像、固定 Commit/制品 Hash、禁止任意 Shell、SBOM/Provenance |
| 源码与模型数据边界 | 生成依赖源码分析和较强模型 | 私有执行、敏感代码策略、模型/Prompt 审计 |
| 结构化输出被误当成安全证明 | JSON/退出码只提高可解析性 | 外部 Test、Policy、Signature、SLO 和 Approval |
| 开源热度被当成成熟度 | Star 不反映企业可靠性 | 以自有场景成功率、误操作、成本和维护性验收 |

## 证据边界

- 官方仓库明确提示依赖 frontier-class 模型和源码可用性；一次生成不保证覆盖全部能力，可能需要迭代完善。
- 仓库测试和论文能证明方法与系统存在，不能替代独立企业 CI/CD 结果研究。
- 当前仓库没有对所有 Harness 运行真实后端 E2E 的统一 CI；README 聚合测试数存在内部不一致，因此不能把“100% pass”外推到全平台、全真实后端。
- CLI-Anything 本身不提供企业任务身份、集中授权、沙箱、审计、发布审批或确定性 Oracle。
- 生成 CLI 是否能进入生产，必须按具体工具、命令、环境和风险重新评测，不能给项目整体授予 L3/L4。

## 对本报告的修正

CLI-Anything 使 Tool 层从五类能力进一步细化出“Agent 原生接口生成”：

- 它位于软件能力源与 CLI/MCP 之间，不属于 Harness；
- 它对所有 CI/CD 阶段横向生效，不应归到单一 Stage；
- 它说明 CLI 在 2026 年不是遗留形态，而是 Agent 工具面的一种关键确定性底座；
- 它同时要求平台团队运营内部 CLI/Skill 供应链，而不只是部署更多 MCP Server。

## 相关下钻

- [[00_sources/agentic-cicd-source-landscape#S81. CLI-Anything Agent-native interface generator|L0 S81]]
- [[10_summaries/tools/README|Agent 工具与技术栈总结]]
- [[20_summaries/companies/README|公司维度总结]]
- [[30_summaries/stages/README|八阶段总结]]
- [[40_summaries/crosscutting/README|横向变化总结]]

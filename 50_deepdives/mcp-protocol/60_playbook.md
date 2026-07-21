---
title: MCP 企业采用 Playbook
tags:
  - research/agentic-cicd
  - research/playbook
  - tool/mcp
status: complete
as_of: 2026-07-15
---

# MCP 企业采用 Playbook

## 一、先证明需要协议层

出现以下任意两项，再优先建设 MCP：多个 Agent 客户端共享能力、远程多租户服务、动态发现、集中 OAuth、统一目录/撤回、Resources/Prompts/通知或长任务扩展。否则先复用成熟 CLI/API 并验证任务价值。

## 二、Server 入库最小元数据

- Owner、来源、版本、规范版本、部署位置；
- Tool/Resource/Prompt 清单和风险等级；
- OAuth Audience/Scope、任务身份和对象级授权；
- 数据分类、外发边界、日志和留存；
- 包签名/SBOM/依赖、更新和撤回；
- SLO、超时、速率、成本和审计字段；
- 评测集、已知失败和允许的 CI/CD 阶段。

## 三、按任务裁剪 Toolset

不要给所有 Agent 暴露所有工具。Review Agent 只需要仓库只读、评论与 Draft PR；CI 修复 Agent 需要日志、Runner 和测试；发布 Agent 需要 Evidence/Plan，但生产写 Tool 默认不可见。

### 渐进式加载策略

1. 先通过 `enabled`/Allowlist 决定该任务真正有权使用的 Tool；
2. 在 Enabled Tool 中保留 3—5 个高频低风险 Tool 常驻；
3. 将 Lint/Test/Build/Artifact/Telemetry 等长尾查询 Tool 设为 Deferred；
4. Production Deploy、Sign/Promote/Delete、修改 Gate 等高风险 Tool 默认 Disabled，不能只做 Deferred；
5. Tool 命名采用 `stage.service.verb` 或等价稳定命名空间，Description 前部写清任务关键词、读写属性和目标对象；
6. 对每个 Scenario 维护 Search Query—Expected Tool 集，做 Recall@5 和误命中回归；
7. 将 Tool Reference、Schema 版本、搜索 Query、最终调用和 Policy 决策纳入同一 Trace。

## 四、身份与批准

- 每任务短期身份，不共享个人 Token 或高权限机器人；
- OAuth Scope 只是第一层，Server 仍要做仓库、制品、环境和动作级授权；
- 高风险 Tool 调用展示目标、参数、制品/Plan 哈希和影响范围；
- 批准后再次校验上下文，防止批准与执行之间发生漂移。

## 五、部署策略

| 方式 | 适合 | 关键控制 |
|---|---|---|
| stdio | 本地工具、单用户、低延迟 | 固定包、精确启动命令、沙箱和环境凭据 |
| Remote HTTP | 共享服务、多租户、集中升级 | TLS、OAuth、Origin/Host 防护、速率和隔离 |
| Gateway | 多 Server、企业策略和审计 | Tool 过滤、身份映射、Schema 缓存、Kill Switch |

Tool Search 可以在 Host、Gateway 或 Anthropic 服务端实现。企业若不能把完整 Tool 定义发送给外部服务，或需要自定义 Embedding、租户/数据域过滤，应在 Gateway 做自有检索并返回受控 Tool Reference；无论部署在哪里，搜索前都先做身份与 Policy 过滤。

## 六、版本策略

当前生产以 2025-11-25 正式规范为基线；对 2026-07-28 RC 建立兼容分支和回归集，待正式版本发布后核对握手/会话、扩展、Schema 和弃用项，再制定迁移。

Anthropic Tool Search 当前为 Claude API GA，但 `mcp_toolset` Connector 仍使用 Beta 版本；生产清单应分别固定 Host/Claude Code、Model、Tool Search Type、MCP Connector Header、Server 与 MCP 规范版本。升级时同时回归 Search Recall、Prompt Cache、Schema 展开和高风险 Tool 不可见性。

---
title: Slide 06 - CLI and MCP Selection
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide
  - tool/cli
  - protocol/mcp
status: complete
slide: 6
content_status: fact-audited
visual_status: complete
primary_deep_dive: "[[50_deepdives/cli-agent-interface/README]]"
as_of: 2026-07-28
---

# 软件交付的 Agent 接口走向互补：CLI 走向结构化输出与 Skill 复用，MCP 接入走向渐进发现与按需加载

> [!abstract] 页面任务
> 面向 CTO、研发效能和平台工程负责人，用一页展示 CLI 如何贯穿软件交付作业链，并完成 CLI / MCP 的机制定位、优缺点、适用场景和共同发展趋势判断。

## 上半层：CLI 贯穿软件交付作业链

1. **Headless Agent CLI：** Codex CLI、Claude Code、Copilot CLI 与 OpenCode 读取工程上下文，选择下层领域 CLI 并以非交互模式执行。
2. **编码与协同：** 代码托管与协作平台通过 `gh`、`glab` 暴露 PR、MR、Issue 与 Actions 等能力。
3. **检查与门禁：** `sonar-scanner`、`snyk`、`trivy` 将质量、安全与依赖检查变成可调用命令。
4. **构建与验证：** `circleci`、`docker` 连接 CI 与构建环境。
5. **制品与供应链：** `jf`、`docker`、`cosign` 处理制品上传、镜像分发与签名。
6. **部署、发布与恢复：** `terraform`、`kubectl`、`helm`、`argocd`、`sentry-cli` 与 `datadog-ci` 承接环境变更、交付与观测动作。

## 左下：代表性 CLI 与能力覆盖

| 作业阶段 | 公司 | CLI | 功能方向 | MCP 形态 / 成熟度 |
|---|---|---|---|---|
| 编码与协同 | GitHub | `gh` | Repo / PR / Review；Issue / Actions / Release | 远程托管 / 可本地 |
| 检查与门禁 | Snyk | `snyk` | 依赖 / 代码 / 容器 / IaC；监测 / 忽略 / 修复建议 | stdio · EA |
| 构建与验证 | CircleCI | `circleci` | validate / process；local execute / diagnostic | stdio · CLI v1 Preview |
| 制品与供应链 | JFrog | `jf` | 上传 / 复制 / 发布；Xray / SBOM | SaaS / 自托管 Beta |
| 部署与发布 | HashiCorp | `terraform` | init / plan / apply；state / workspace | 本地 Server · v1.0.x |
| 观测与恢复 | Sentry | `sentry-cli` | Release / Deploy / Commit；Source Map / Event | 远程可用 / stdio 开发中 |

## 右下：CLI 与 MCP 的机制对比

| 对比维度 | CLI｜直接执行面 | MCP｜标准接入面 |
|---|---|---|
| 定位 | 复用成熟工具的完整执行语义；命令、文件与进程进入 Agent 工作循环 | 统一工具发现与调用契约；Host 接入本地或远程服务 |
| 核心优势 | 能力保真、易重放，Shell 组合成本低；复用脚本、Runner 与运维经验 | 工具描述与参数契约标准化；适合远程授权与集中治理 |
| 主要代价 | 接口与版本差异由 Agent / Skill 消化；输出不稳定会降低自动化可靠性 | 新增 Server、协议生命周期与权限治理；全量工具定义会占用上下文 |
| 适用场景 | 成熟命令、本地工程动作、CI Runner | 运行时工具发现、统一参数、授权治理 |

## 趋势

1. **CLI 趋势：** 结构化输出与 Skill 封装增强 Agent 的稳定调用与可重放执行。
2. **MCP 趋势：** Tool Search、渐进发现与按需加载避免一次性注入全部工具定义。
3. **整体趋势：** 主流终端 Agent 同时保留 CLI 与 MCP，并按任务边界组合使用。

## 页底洞察

1. 已有成熟命令，任务依赖本地文件、进程、管道或完整执行语义时，优先使用 CLI；通过结构化输出与 Skill 封装提升 Agent 调用稳定性。
2. Agent 需要在运行时查找工具、获取调用参数，并由平台统一控制权限时，优先使用 MCP；通过渐进式发现与按需加载降低上下文开销。

## 事实口径与边界

- “业界主流”只表示多个独立组织出现同构机制，不表示市场份额、企业采用率或 CI/CD 成功率。
- GitHub Copilot CLI 于 2026-02-25 GA；2026-06-23 是新终端界面 GA，二者不混用。
- MCP `latest` 在 2026-07-28 访问时仍指向 2025-11-25；当日后续版本切换未核实，不写成已发布最终版。
- OpenAI Codex CLI / MCP、Terraform CLI / MCP 的活文档未给出可直接对应的单项 GA / Preview 标签。
- CLI-Anything 是论文与开源参考实现，Codex 集成标记为 Experimental / Community，仅作为“接口工厂”研究前沿，不作为本页主流产品案例。
- Tool 可见、CLI / MCP 可调用都不等于已获业务授权；接口不替代 Test、Scan、Policy、Signature、SLO 或人工审批。

## 可编辑成品

- [软件交付 Agent 接口洞察最终版](../../../outputs/cli-software-delivery-agent-interface-insight-final.pptx)
- 上半软件交付链作为已确认的 PNG 视觉插入；标题、左下表格、右下对比与趋势、页底洞察均为独立 PowerPoint 对象，可编辑和移动。
- 完整状态、时间、命令示例和证据边界见本页来源文件与对应 Deep Dive。

## 来源

- [[50_deepdives/cli-agent-interface/industry-mainstream-slide-evidence-refresh-2026-07-28|CLI / MCP 单页证据刷新]]
- [[50_deepdives/cli-agent-interface/industry-mainstream-evidence-2026-07-28|CLI 主流机制核验]]
- [[50_deepdives/cli-agent-interface/industry-mcp-evolution-and-progressive-discovery-2026-07-28|MCP 演进与渐进式发现]]
- [[50_deepdives/cli-agent-interface/presentation-page-research-2026-07-27|CLI 单页汇报研究]]
- [[50_deepdives/cli-vs-mcp-decision-guide|CLI vs MCP 决策指南]]
- [上半软件交付链命令来源](../../../outputs/cli-upper-half-c-refined-command-sources.md)
- [左下代表性 CLI 表格来源](../../../outputs/cli-lower-left-representative-table-sources.md)
- [右下 CLI / MCP 对比来源](../../../outputs/cli-lower-right-cli-mcp-coevolution-sources.md)
- [[80_presentations/agentic-cicd-company-workflows/source-map#Source Map|本页 Source Map]]

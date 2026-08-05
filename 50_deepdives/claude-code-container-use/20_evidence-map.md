---
title: Claude Code × Dagger Container Use Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
status: complete
as_of: 2026-08-03
confidence: medium-high
---

# Claude Code × Dagger Container Use Claim—Evidence—Gap Matrix

## 证据标记

- **A：** 当前官方文档、源码、仓库 API 或版本化 Release；
- **B：** 官方工程博客、产品文章和 Changelog；
- **C：** 厂商发布的署名客户案例或厂商使用研究；
- **分析：** 基于多项事实形成的比较与架构判断，不冒充来源原话。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计 |
|---|---|---|---|---|---|
| CCU-C01 | Container Use 是可接入 Claude Code 的开源 MCP server，由 Dagger 驱动 | [官方仓库](https://github.com/dagger/container-use) 给出 `claude mcp add container-use -- container-use stdio`，并明确 Powered by Dagger | 只证明技术接入，不证明联合产品、联合销售或 Anthropic 推荐 | high | passed |
| CCU-C02 | Container Use Environment 的核心对象是 Git branch、容器与完整执行历史 | [官方仓库](https://github.com/dagger/container-use)；[Dagger 产品文章](https://dagger.io/blog/agent-container-use/) | “完整历史”为厂商对自身记录模型的描述，不等于不可篡改审计 | high | passed |
| CCU-C03 | Claude Code 已原生提供并行 Agent 和 worktree 隔离 | [Worktrees](https://code.claude.com/docs/en/worktrees)、[Subagents](https://code.claude.com/docs/en/sub-agents)、[Agent Teams](https://code.claude.com/docs/en/agent-teams) | Agent Teams 仍标 experimental；worktree 隔离文件修改，但不会自动完成依赖和服务初始化 | high | passed |
| CCU-C04 | “并行、多 Agent、分支隔离和 MCP”不能再作为 Container Use 的独占价值 | CCU-C01—C03 的能力重叠；Claude Code [Hooks](https://code.claude.com/docs/en/hooks) 与 [GitHub Actions](https://code.claude.com/docs/en/github-actions) | Container Use 仍可为非 Claude Agent 提供一致接入，并将 worktree 与容器状态绑定 | high | passed |
| CCU-C05 | Container Use 相对原生 worktree 的剩余差异是项目化执行环境：依赖、服务、状态、历史和配置生命周期 | [[00_sources/research-claude-code-container-use-current-deepdive-2026-08-03\|研究底稿]]；官方 Environment workflow/configuration 文档 | 已使用 devcontainer、Nix、Bazel 或成熟平台环境的团队可能已有替代能力 | medium-high | passed |
| CCU-C06 | Dagger 可组合 Function 和内容寻址缓存，为本地与 CI 复用同一环境定义提供执行基础 | [Dagger 产品文章](https://dagger.io/blog/agent-container-use/)；[[50_deepdives/dagger/README\|Dagger Deep Dive]] | “identically in CI”是厂商主张；宿主、网络、Secret、架构和外部服务仍需实测 | high for mechanism; medium for outcome | passed |
| CCU-C07 | Container Use 截至 2026-08-03 仍是 early development，不能按成熟生产平台表达 | [官方仓库](https://github.com/dagger/container-use) 明确 early development；最新正式 [v0.4.2](https://github.com/dagger/container-use/releases/tag/v0.4.2) 发布于 2025-08-19 | 仓库未归档且 2026 年仍有维护提交；不能写“已停止” | high | passed |
| CCU-C08 | Claude Code 有公开企业采用信号 | Anthropic [Rakuten 客户案例](https://claude.com/customers/rakuten)；[Claude Code 使用研究](https://www.anthropic.com/research/claude-code-expertise?level=0) | 均为 Anthropic 第一方材料；收益不可外推行业平均 | medium-high | passed |
| CCU-C09 | 当前证据不足以证明 Container Use 的独立企业生产采用或二者的联合规模采用 | 限定检索未找到官方客户目录、具名企业生产工程文章或 Anthropic / OpenAI 联合背书 | “未找到证据”不等于“业界不存在”；需持续刷新 | medium | gap recorded |
| CCU-C10 | 最适合的页面主张是“Agent workspace 向 CI Candidate Environment 演进” | CCU-C03—C09；与现有五页的内容差异审计 | 这是分析推断，不是厂商原话；必须同页标注 Container Use 早期状态 | medium-high | passed |
| CCU-C11 | Container Use 不替代 CI 的测试、扫描、策略、制品和审批 Oracle | Container Use 公开能力集中在 Environment 管理和执行；没有对外部质量结论的独立证明 | Dagger 可以调用这些工具，但工具执行不等于获得独立结论或授权 | high | passed |
| CCU-C12 | Container Use 已出现第三方生态集成与公开开发者工作流信号 | Goose 官方发布完整扩展教程；Pigmalion Software 与 NearMe 技术分享公开复现；`thetom42/perplexica-mcp` 将其写入 Claude Code 开发规则；John Lindquist 公开实际工作流 | 这些证据分别证明平台接入、公开复现或个体/项目级采用，不能推导为企业生产采用、平台方联合支持或规模成效；Zed 旧第三方扩展已不可用，不能算当前背书 | medium-high | passed with scope |

## 能力重叠与剩余差异

| 能力 | Claude Code 原生 | Container Use / Dagger | 判断 |
|---|---|---|---|
| 并行任务与角色拆分 | Subagents、Agent Teams、动态并行工作流 | 多 Environment 并行 | 高度重叠，不能作为主张 |
| 源码隔离 | `--worktree`、subagent worktree isolation | 每个 Environment 绑定 Git branch/worktree | 高度重叠 |
| 会话与任务观察 | 会话、transcript、Agent 视图与 attach 路径 | 命令/输出历史、`watch/log/terminal` | 部分重叠；Container Use 更靠近运行环境操作面 |
| 依赖与服务准备 | Worktree 文档要求自行初始化环境 | 容器、setup/install、service、env 配置 | Container Use 的主要剩余差异 |
| 环境状态恢复 | 恢复 Claude 会话或保留 worktree | 恢复容器状态、依赖、历史和 branch | Container Use 更完整 |
| 本地与 CI 复用 | Actions 能在 CI 触发 Claude | Dagger Function/Engine 复用执行定义与缓存 | Dagger 的 CI/CD 差异化；结果需实证 |
| 多 Agent 客户端兼容 | Claude 生态内 | Claude、Cursor 等 MCP 客户端 | Container Use 的平台中立价值 |

## 采用与成熟度审计

| 对象 | 当前信号 | 认可度判断 | 页面表达 |
|---|---|---|---|
| Claude Code | 当前官方能力面广；有署名企业案例和大规模会话研究 | high，证据以厂商第一方为主 | “已有规模采用信号” |
| Dagger Core | 活跃开源 Engine、持续 Release、已有署名客户案例 | medium-high | 可作为成熟技术底座讨论，但不把成熟度自动转移给子项目 |
| Container Use | 约 3.9k GitHub Stars、未归档、仍有维护；官方 early development；已有 Goose 官方教程与公开开发者/技术分享复现 | low-medium，已获开发者生态验证，企业生产证据仍弱 | “早期实现/方向性机制；已有生态试用” |
| Coding Agent × Container Use | Dagger 为 Claude Code、Codex、Goose 等提供接入说明；Goose 有第三方官方教程；无联合客户与联合产品证据 | low-medium | “Agent-neutral 可组合”，不写“成熟联合实践” |

## 仍未关闭的证据缺口

- 具名企业在生产仓库中长期、规模化使用 Container Use 的案例；
- Anthropic、OpenAI 等主流 Coding Agent 厂商对 Container Use 的官方联合背书；
- Container Use 在大规模并行任务下的稳定性、成本、缓存命中与故障恢复数据；
- 与 Claude 原生 worktree + devcontainer/Nix/Bazel 的可比实验；
- “同一环境定义”在真实本地与 CI Runner 中的差异率和故障归因结果。

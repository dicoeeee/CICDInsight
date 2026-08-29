---
title: Multica 与业界真实使用场景案例地图
tags:
  - research/agentic-cicd
  - research/case-map
  - company/multica
status: complete
as_of: 2026-08-29
---

# Multica 与业界真实使用场景案例地图

## Multica 本身的公开使用案例

| 场景 | 使用者 / 形态 | 实际输入 → 输出 | 已公开结果 | 证据与边界 |
|---|---|---|---|---|
| 端到端软件交付 | xiaohei-info / oh-my-multica | Webhook Inbox 目标 → 5 节点 DAG → 多 Runtime 实现/Review/验收 → main | 5 个 PR 合并提交；作者报告 86 tests、97.18% coverage、11/11 flows | E3 public artifacts；可复核演示，不是客户生产 |
| 自托管多 Agent 交接 | Multica 社区 #1998 | Developer → Reviewer → Tester，sub-issue 前向、父 Issue 评论反馈 | 用户称已搭起且可运行 | E3；prompt-only correctness，非强工作流 |
| 八阶段 Squad 交付 | Multica 社区 #5856 | TDD→Contrarian→Implementer→Verifier→Review→PR，由 leader 串行 route | workaround 仍在使用；过密触发曾产生重复实现/PR | E3；证明运行和失败，不证明稳定流水线 |
| 完成证据门 | Multica 社区 #4804 | Issue 进入 done → 七类 evidence gate → warn/block/manual-review | 240 transitions、69 历史 Issues、42 fake-evidence variants | E3；本地验证，不是生产或 core 能力 |
| MCP 多项目编排 | Korkyzer/multica-mcp | Claude/Codex Chat → MCP → Issue/Agent/Autopilot/usage | 作者称 daily 10+ Agents、一个周末 470+ Issues | E3；实现公开，使用量与业务质量未审计 |
| 高频 Autopilot 运维 | Multica 社区 #4276 | 持续周期任务 → run directories / gc metadata | 523 run dirs、约 22GB；近 24h 新增 263 | E3；业务内容未知，只证明运行量与运维负担 |
| PR 对账 | Multica 社区 #6202 | GitHub webhook → Autopilot → LLM 执行确定性脚本 | 132 events/24h；20 run 中 8 次 quota 失败 | E3；应改为 command/rule-first |
| GitHub/Huly 与 PR 评论轮询 | Multica 社区 #2373 | Autopilot scan GitHub Issues/PR comments → Huly mirror / revision sub-issues | 两个轮询 workaround 在运行 | E3；空轮询仍唤醒模型、消耗 token |
| Slack 团队入口 | Multica 社区 #4073 | Slack thread → Bridge → Autopilot/Hermes → Multica work | 用户称已验证发送、history/thread read | E3；共享 Bridge 原型，非当前 native 一 App 一 Agent |
| 多云 Runtime、切换与 handoff | Multica 社区 #6150 / #3262 | 两个腾讯云 Hermes Runtime + 火山 Ark → continuation / ProjectManager→Lead；Hosted Desktop Codex→Hermes rerun | fresh task 与 Hermes smoke test 成功，resume/handoff 可重复失败 | E3；混合结果的实测，不证明真实代码任务或长期稳定性 |
| 分阶段写作/润色 | Multica 社区 #4929 | Leader → stage 6 polish child → cre-writer + Skill | 人工 rerun 后 task 成功完成 | E3；无公开最终文档，自动启动未成功 |
| 多 Agent 并发隔离 | Multica 社区 #7326 | 三角色三 Issue 并发 → 串行 rerun 对照 | 并发身份/workdir 污染；串行目录正确且完成 | E3；控制试验，无业务仓库 |

## 其他 Agent 平台的行业真实案例（非 Multica）

| 场景 | 组织 / 平台 | 实际输入 → 输出 | 已公开结果 | 对 Multica 的类比启示 | 证据与边界 |
|---|---|---|---|---|---|
| 有界 Issue → PR | GitHub Secret Protection / Copilot | 人工 endpoint 研究 + 结构化 Issue → validator PR → test/review → darkship → release | 数周新增近 90 种 validator | Issue、Agent、Task、PR 卡、Review 状态很匹配 | E1；研究与 live API 判断不能交给 Agent |
| 日常维护与 first pass | GitHub 核心仓 / Copilot | Issue → PR → 人审/迭代/关闭 | 覆盖 feature flag、bug、flaky test、API、迁移、安全门禁 | 最适合作为 Multica 首批 backlog 消化任务 | E1；低合并率不等于无价值，禁止盲合并 |
| 大规模迁移 | Asana / Codex | 五句话目标 → 4 个并行 Agent 独立代码副本 → 人每日两次检查 → 逐项 review | 厂商称两周移除 Enzyme | Project + 多 Issue + worktree + Planner/Reviewer 可映射 | E2；单案例，不外推时间/成本 |
| 系统级代码评审 | Datadog / Codex | 每个 PR 自动 review + thumbs feedback + 历史事故 replay | 约 22% 回放事故被判断会产生差异 | Reviewer Agent + 只读 Skill + Issue/PR 评论 | E2；必须建立独立回放/反馈 harness |
| CI 失败诊断 | Barclays / GitLab Duo | 失败 job log → RCA 解释 → 人解决 | 个案称数秒解决 | Autopilot webhook 或人工触发 Triage Agent | E2；不等于自动修复/重跑/上线 |
| 大规模夜间分诊 | AMD / Copilot | 百万级 simulation logs + 历史问题 → 分类与候选修复 | 客户/厂商称晨间人工分诊降为零 | 高级场景，可由 Autopilot 触发并回写 Issue | E2；日志入口应先确定性过滤，修复需验证 |
| 跨项目专业 Agent | Cube / GitLab Duo | Ticket / code / issue / MR / pipeline history → refinement、context、design reuse、upgrade risk | 客户/厂商称项目启动快 50% | Workspace Agent + Project resources + shared Skills | E2；Channel Partner，权限与合并细节缺失 |
| 长任务与共享规范 | You.com / Factory | 多模型、共享 Skills/MCP、复杂 spec → 后台 Mission / review / bug fix | 厂商称 24/7 运行并进入核心工程平台 | Multica 的 provider-neutral + Skills + Runtime 路线相近 | E2；无独立成功率、门禁或失败数据 |

## 跨案例共性

| 共性 | 直接案例 | 对 Multica 的要求 |
|---|---|---|
| 输入先结构化，再异步执行 | GitHub Secret Protection、GitHub 核心仓、Asana | Issue 必须包含目标、约束、验收、上下文与决策；Planner 只产出可确认计划 |
| 工作单元有界且可拆 | GitHub、Asana、Cube | 一 Issue 一个可验收目标；大项目用 Project + 多 Issue，不把长任务塞进单一 prompt |
| 产出是候选变更或评论，不是业务真相 | GitHub、Datadog、Barclays | Task completed 只能进入 review；外部 CI、扫描、policy 和人审决定是否接受 |
| 需要独立反馈 harness | Datadog、GitHub darkship、OpenAI agent-first | 收集 rejected/accepted、失败测试、事故回放、返工与部署后信号 |
| 并行前先建立隔离 | Asana、OpenAI agent-first、Multica worktree | 每 Task 独立 worktree；daemon 运行身份仍需容器/VM 隔离 |
| 确定性工作优先脚本 | Multica PR 对账、CI 状态、policy checks | Autopilot 入口可保留，执行层用规则/CLI；只有 ambiguous state 升级给 Agent |

## 真实案例与演示的排除表

| 材料 | 分类 | 处理 |
|---|---|---|
| Multica 官方 Tutorial 的网站开发、Reviewer、Squad、周检 | 官方演示（E4） | 用于说明功能，不进入采用结论 |
| Multica deploy-sentinel / triage-notify 示例插件 | 参考实现与测试合同（E4） | 可证明接口形态，不写成生产事故闭环 |
| Multica Vision 中研究、客服、内容、客户交付 | 未来方向（E5） | 不写成当前真实使用 |
| B2B 营销 Issue #2517 | 使用愿望（E5） | 只作为非研发场景边界，不列成功案例 |
| GitHub Agent HQ / 产品功能页 | 能力发布（E4） | 可作机制对比，不作客户成效 |

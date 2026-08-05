---
title: Slide 07 - AWS Release Management and Meta JiTTesting Decision History
tags:
  - research/agentic-cicd
  - deliverable/presentation/slide/history
status: active
as_of: 2026-08-03
---

# Slide 07 - AWS Release Management and Meta JiTTesting Decision History

## 2026-08-03 · 对象与 CI/CD 观察范围

- 用户明确选择 AWS DevOps Agent Release Management 与 Meta JiTTesting 作为页面对象。
- 用户明确要求页面洞察聚焦“Agent 生成的验证，如何在流水线运行时被规划、执行并转化为 Gate”，服务 CI/CD 专家；mutant、测试用例质量和 UI/API 测试类型不作为页面主轴。
- 原 `07-dagger-meta-jittest.md` 保留为历史候选，不控制当前 AWS + Meta 页面。

## 2026-08-03 · Evidence readiness

- 预检时两个对象均没有可直接复用的 `presentation_ready: true` Deep Dive，因此正式页面最初被项目规则阻塞。
- 基于已完成且截至 2026-08-03 的一手证据核验，建立 [[50_deepdives/runtime-generated-verification-gates/README|Agent 生成验证的运行时 Gate Deep Dive]]。
- Deep Dive 完成 Charter、Question Tree、Evidence Map、Findings、Report 与 12 项逐主张事实审计，当前 `presentation_ready: true`。
- AWS 保持 Release Management `Preview` 与 `us-east-1` 边界；Meta 保持“内部生产工作流 + 公开研究”边界。两者均不支持自动批准、自动发布或所有 PR 同步阻断的更强表述。

## 2026-08-03 · Alignment route gate

- 已按 `build-insight-pptx` 提供 top-down 与 bottom-up 两条页面分析路径。
- 用户明确回复 `bottom-up`；R1 记录为 `user_approved`，页面进入 Evidence round 1。
- 曾提出 E1（六项事实与边界）和 E2（三个内容簇）作为页面前台。用户反馈“不行，洞察页主要讲的是偏技术方案，可以引起启发的”，因此 E1/E2 未获批准并退出 active contract。
- 根据反馈提出 T1—T3：五段 Agentic Verification Gate 技术链、三项运行时合同，以及 AWS/Meta 作为架构锚点的比较方式。标题、最终主张和企业启示继续保持开放。

## 2026-08-03 · 技术方案事实审计

- 用户要求：“如果缺少这块的洞察请补充洞察，所有技术方案必须有事实依据，不能胡编”。
- T1—T3 尚未获批；其中 `Change Envelope`、`Execution Broker`、`EvidenceBundle`、`GatePolicy` 等名称没有 AWS/Meta 原生 schema 证据，已退出 active contract并等待审计后改写。
- 主审计新增确认：AWS release readiness 可输出 `BLOCK / Proceed with Caution / Safe to Release`；GitHub 可配置 required status check，GitLab 可配置 MR approval rule；release testing 可回写 GitHub Check Run pass/fail。这些事实为“Agent 结果如何转成 Gate”提供直接技术依据。
- 已按项目规则启动 `gpt-5.6-terra`、high reasoning 的研究 Subagent，进一步审计 AWS、Meta 与必要的 CI 原生门禁机制。
- 研究完成并写入 [[00_sources/research-agent-generated-verification-gate-technical-basis-2026-08-03|技术事实审计]]；主 Agent 复核 AWS、Meta 与 GitHub 关键事实。
- T1—T3 被新的 A1—A3 取代：只保留 `Revision/Diff → 变更感知生成 → 执行/评估 → SCM/CI 状态 → 宿主保护规则`；AWS 的 release testing Check Run 与 readiness required-check/approval-rule 分成两条事实链，Meta 止于工程师确认；CI 状态路径的技术启发改为“结果绑定 revision、状态绑定 required rule”。

## 2026-08-03 · 用户给定左右双栏

- 用户建议：“整体ppt分为左右两块，左边讲aws 右边讲meta”。
- 记录 S0 为用户给定结构方向；完整结构锁仍等待语义主张、标题、内容预算和比例确定。
- A1—A3 随之修订：页面主体分别呈现 AWS 与 Meta 的事实链，共同技术抽象只进入底部洞察，不再用一条横跨公司的流程覆盖两者差异。

## 2026-08-03 · 板块内容需要完整对齐

- 用户指出：“不应该是确定完页面布局之后，就开始对齐每块板块该写什么内容么，你就给个几个单词是什么意思？？”
- A1—A3 的标签级表达不足以构成板块内容合同，退出 active contract。
- 当前改为 B1—B3：分别给出左栏 AWS、右栏 Meta、底部共同洞察的栏目主句、完整可见技术内容、演讲解释和证据边界；标题继续按 bottom-up 路径后置。

## 2026-08-03 · 改为逐块对齐

- 用户要求：“一块一块对齐，不要一下子把三块内容一起给出来”。
- B2、B3 未获审批并退出 active contract；Meta 与底部洞察暂停。
- 当前只保留 B1：对齐左栏 AWS 是否必须同时保留 Release Testing 与 Release Readiness 两条互补事实链。B1 未批准前不进入下一块。

## 2026-08-03 · 可见文案语言

- 用户要求：“尽量都输出中文，除非这个英文单词比较普遍”。
- 记录 LANG1：页面默认使用中文；产品正式名称、平台原生对象或无法准确替代的术语首次出现时保留英文括注，之后使用中文简称。
- 当前只重写 AWS 左栏文案，不推进 Meta 或底部洞察。

## 2026-08-03 · AWS 左栏收敛为单一机制

- 用户反馈“左侧想体现啥呢，没太看懂”，说明双支线仍停留在功能罗列，没有单一证明任务。
- B1 修订为一条主链：代码变更 → 动态测试计划 → 已部署目标执行 → GitHub 检查结果 → 必需状态规则 → 合并决定。
- 发布就绪评审降为旁证，只证明 AWS 已直接支持 required-check / approval-rule Gate 接法；不再与发布测试并列成第二主流程。

## 2026-08-03 · AWS 左栏移除安全与权限内容

- 用户要求：“不讲这些偏安全或者权限的内容”。
- B1 再次收敛：删除 VPC、IAM、网络允许列表、仓库保护规则、审批规则和发布就绪评审旁证。
- AWS 左栏当前只讲发布测试的运行时链路：部署后触发 → 按变更生成测试计划 → 在已部署应用执行 → 回写通过/失败检查 → 成为后续 Gate 输入。

## 2026-08-03 · AWS 左栏批准，进入 Meta

- 用户明确回复“同意”，B1 记录为 `user_approved`。
- 按逐块对齐要求，当前只提出 B2：Meta 的 Diff-aware 一次性验证生成、父版本/Diff 对照、评估器与工程师报告链路。
- 底部洞察和标题继续暂停。

## 2026-08-03 · 统一比较问题，AWS 重新确认

- 用户澄清：“不是说不能一样，可以有异同，但是观点要清洗，表达要清晰”。
- 不再为了差异强造不同叙事；左右两栏统一回答“何时触发、生成什么、怎样执行、输出什么”。
- B1 在首次批准后经历了多次实质改写，按 Contract 规则重新打开为 `proposed`；B2 暂时退出 active contract，待 B1 重新批准后再按同一四问框架提出。

## 2026-08-03 · AWS 左栏由流程说明升级为技术机制

- 用户同意 AWS 左栏“上图、下文”的表现形式，但指出现有内容只描述流程，看不出可借鉴的技术机制和实现难点。
- 保留已批准的上下分区，B1 内容继续保持 `proposed`。
- 进一步核验 AWS 官方 `aws-actions/devops-agent-qa` README、`action.yml` 与 `src/index.ts`：Action 将 repository、head SHA、PR、test profile 和可选 test requirement 组成 Webhook 作业上下文；收到 2xx 后结束；Agent Space 创建并更新当前 commit 的 Check Run。
- B1 因此改为“revision 绑定的异步外部检查”架构：稳定配置与运行时计划解耦、Runner 与 Agent 执行解耦、Check Run 证据与 Required Check Gate 判定解耦。

## 2026-08-03 · AWS 示例图草案

- 用户确认 PR #482 / commit SHA 示例清晰，并要求参考该例子画图。
- 生成低保真技术图 `assets/aws-release-testing-async-check-example.svg` 及 PNG 预览；图按 GitHub Actions、AWS Agent Space、GitHub 状态与 Gate 三层组织。
- 首轮视觉检查发现“异步回写”箭头落点可能误读，已改为明确写回 Check Run，并修复深色泳道文字对比度。
- 该图是 AWS 区域内容对齐草案，不构成视觉锁批准；正式 PPT 生产时应按已批准内容重绘为原生可编辑对象。

## 2026-08-03 · AWS 示例图降为局部放大

- 用户指出 commit SHA、Webhook 与 Check Run 的机制点可能过小，并询问它是否只是 AWS DevOps Agent 的一个功能。
- 产品层级复核：Release Management 是 AWS DevOps Agent 的 Preview capability；Release Testing 是 Release Management 的两项核心能力之一；官方 Action/Check Run 又只是 Release Testing 的 GitHub 接入路径。
- 因此现有三层图降为 Release Testing 分支的局部实现放大，不再承担 AWS 左栏主观点。
- B1 新提案提升为 Release Management 的两条独立证据链：PR/MR 时的 Release Readiness，以及部署后的 Release Testing；两条链分别产生状态并由宿主 SCM/CI 决定 Gate 强度。该提案仍待用户批准。

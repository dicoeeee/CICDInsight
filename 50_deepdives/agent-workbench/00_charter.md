---
title: Agent 工作台、专家团与交付角色重构研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - topic/agent-workbench
status: complete
as_of: 2026-08-08
confidence: high
---

# Agent 工作台、专家团与交付角色重构研究边界

## 决策问题

面向 CTO、研发效能负责人和平台工程负责人，本专题回答三个决策问题：

1. WorkBuddy、ChatGPT Work、Codex、Workspace Agents 等产品是否能够佐证“Agent 工作台 + 专家团”成为新的工作入口；
2. 这种入口如何与编码完成后的构建、发布准备、诊断、恢复和生产门禁连接；
3. 发布、运维、SRE 与平台人员的职责应如何从逐单执行上移为通用 Agent Harness 的设计和运营。

## 核心主张

> **Agent 工作台把交付能力变成开发者可自助消费的服务；发布与运维人员则从流程执行者逐步转为 Agent 专家体系和安全边界的设计者。**

该主张拆成两种证据状态：

- **当前可证明：** 已有产品分别提供开发者/最终用户工作台、多专家或 Subagent、共享上下文、Skill/App、Builder/Admin 权限和外部 CI/CD 控制边界。
- **需要谨慎推断：** 企业应据此重构 operating model；但跨企业采用率、岗位时间分配和组织普及程度尚未验证。

## 术语锁定

- **Agent 工作台：** 用户提交目标、选择/连接上下文、观察计划和子任务、补充信息、审查产物并批准候选动作的交互面。
- **专家：** 具有特定角色、指令、方法、上下文和工具范围的 Agent 配置。
- **专家团：** 由主 Agent/团长按任务图协调多个专业 Agent 的协作机制。
- **通用 Agent Harness：** 管理 Agent、Skill、上下文、工具、身份、沙箱、权限、评测、交接、预算、审计和生命周期的运行与治理层。
- **Harness Inc.：** CI/CD 厂商。全文出现时写公司名，不与通用 Agent Harness 混淆。
- **外部 Oracle：** 对候选动作给出可接受/不可接受判定的确定性测试、扫描、策略、签名、SLO、审批者或生产控制系统。

## 研究范围

### 包含

- WorkBuddy 的专家、专家团、Skill/MCP、项目与权限机制；
- ChatGPT Work、Codex、ChatGPT Projects、Subagents、Workspace Agents 的产品边界；
- Harness Inc. Worker Agents、GitHub Agentic Workflows 对 CI/CD 控制边界的桥接；
- DORA 对高质量平台、AI 放大效应和安全路径的组织启示；
- 三层五角色架构、职责迁移 RACI 和企业试点门禁。

### 不包含

- WorkBuddy、ChatGPT Work 或 Codex 的完整采购对比；
- 将 Tencent CodeBuddy 的 IDE/CI 集成无条件归入 WorkBuddy；
- 将 Workspace Agents 的 Research Preview 治理能力写成 ChatGPT Work 全面 GA 能力；
- 预测岗位数量、裁员比例或发布/运维职能消失；
- 用 Agent 自我判断替代 Test、Scan、Policy、Signature、Approval、SLO 或人工 Oracle；
- 制作 PPTX 或开展生产写入实验。

## 验收标准

1. 至少两个独立一手来源证明最终用户入口与 Builder/Admin 供给控制相互分离；
2. 至少一个 CI/CD 原生案例证明 Agent 输出仍受确定性门禁或 Required Checks 接受；
3. 每个核心 Claim 分类为产品事实、跨案例推断、企业建议或未验证岗位趋势；
4. WorkBuddy、ChatGPT Work、Codex、Workspace Agents、Harness Inc. 的产品边界不混写；
5. RACI 不给主 Agent 或专业 Agent 默认生产审批责任；
6. 明确 Skill/专家的版本、测试、权限、成本、Owner 和生命周期治理；
7. 逐主张审计、URL、Frontmatter、Wikilink 与 `git diff --check` 通过后，才保持 `presentation_ready: true`。

## 证据与时间窗口

- `as_of`: 2026-08-08；
- 产品状态以当前官方文档、Release Notes、Changelog 为准；
- 厂商自述只能证明机制或厂商记录的场景，不外推为行业效果；
- “未检索到”只记录为证据缺口，不表述为行业不存在。

---
title: Agent 生成验证的运行时 Gate Claim—Evidence—Gap Matrix
tags:
  - research/agentic-cicd
  - research/evidence-map
  - capability/testing-gates
status: complete
as_of: 2026-08-03
confidence: high
---

# Agent 生成验证的运行时 Gate Claim—Evidence—Gap Matrix

## 证据标记

- **A：**当前官方公告、官方文档和原始论文，可证明公开机制、状态与边界。
- **B：**官方工程博客，可证明组织对机制和流程的第一方解释。
- **分析：**基于多个事实形成的 CI/CD 控制面判断，不冒充来源原话。

## 核心 Claim

| ID | 待验证论点 | 支持证据 | 反例 / 限制 | 置信度 | 审计状态 |
|---|---|---|---|---|---|
| RVG-C01 | AWS DevOps Agent Release Management 截至 2026-08-03 仍为 Preview，限 `us-east-1` | [AWS Release Management announcement](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/)；[About AWS DevOps Agent](https://docs.aws.amazon.com/devopsagent/latest/userguide/about-aws-devops-agent.html)；[[00_sources/research_ws-devops-agent-release-management_meta-jittesting#二、AWS DevOps Agent Release Management 时间线|时间线核验]] | Production operations 的 GA 不能外推给 Release Management | high | passed |
| RVG-C02 | AWS release readiness review 检查组织标准、跨仓依赖、访问控制与基础设施权限偏差 | [Release management user guide](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)；[AWS announcement](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) | 公开材料未证明所有组织策略都可表达，也未给出独立准确率 | high | passed |
| RVG-C03 | AWS release testing 会针对具体变更生成测试计划，并在客户提供的已部署应用环境中运行 | [Release testing user guide](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)；[AWS News Blog](https://aws.amazon.com/blogs/aws/aws-devops-agent-adds-release-management-capabilities-to-assess-code-changes-before-production-preview/) | 主要公开范围是 Web/API 应用；真实请求可能产生数据修改，应优先 staging | high | passed |
| RVG-C04 | AWS Release Management 可从 PR/MR 和 CI/CD Pipeline stage 触发 | [Release management user guide](https://docs.aws.amazon.com/devopsagent/latest/userguide/working-with-devops-agent-release-management-index.html)；[AWS announcement](https://aws.amazon.com/about-aws/whats-new/2026/06/aws-devops-agent-release-management/) | 普通触发不等于 required gate；Gate 配置需看 C11 | high | passed |
| RVG-C05 | AWS 的公开能力不证明自动合并、自动部署或 Agent 自主发布 | [[00_sources/research_ws-devops-agent-release-management_meta-jittesting#AWS 当前能力边界（截至 2026-08-03）|AWS 能力边界]] | AWS 使用“autonomous release testing”描述计划生成与执行，不能扩写为自主发布 | high | passed |
| RVG-C06 | Meta JiTTesting 是已披露的内部生产工作流与公开研究，不是对外产品 | [JiTTest paper](https://arxiv.org/abs/2601.22832)；[Meta Engineering](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)；[[00_sources/research_ws-devops-agent-release-management_meta-jittesting#三、Meta JiTTesting / JiTTest 时间线|时间线核验]] | 未发现公开产品、GA/Preview、下载或 API；这是检索边界，不是未来不存在的断言 | high | passed |
| RVG-C07 | Meta 的公开流程包含 Diff 意图/风险推断、生成临时验证、运行、规则与 LLM assessor、工程师候选报告 | [Meta Engineering](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)；[JiTTest paper](https://arxiv.org/pdf/2601.22832) | mutant 是生成验证的内部机制，不应成为 CI/CD 页面主轴 | high | passed |
| RVG-C08 | Meta 的 Catching JiTTests 服务当前变更，不要求作为长期测试资产进入代码库 | [Meta Engineering](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)；[[00_sources/research-large-company-llm-build-test-practices-2026-08-03#第一选择：Meta JiTTest|候选研究]] | 这不证明固定回归测试不再需要 | high | passed |
| RVG-C09 | 公开证据不足以证明 Meta JiTTesting 覆盖所有 PR 或作为同步强制阻断 Gate | [JiTTest paper](https://arxiv.org/pdf/2601.22832)；[[00_sources/research_ws-devops-agent-release-management_meta-jittesting#Meta 机制与成熟度边界|成熟度边界]] | 工程博客用 PR 提交时描述机制；论文披露的是两条选定 diff-aware production workflow | high | passed |
| RVG-C10 | 两个案例共同显示验证内容可在流水线运行时按变更生成 | RVG-C03、RVG-C04、RVG-C07、RVG-C08 | **分析推断：**两者工件、环境、状态和治理方式不同，不能写成同一实现 | high | passed |
| RVG-C11 | AWS readiness review 可配置为 GitHub required status check 或 GitLab MR approval rule；release testing 可回写 GitHub Check Run pass/fail | [AWS release-readiness code reviews](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-readiness-code-review.html)；[AWS release testing](https://docs.aws.amazon.com/devopsagent/latest/userguide/release-management-release-testing.html)；[GitHub protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) | 必须区分 readiness gate 与 release-testing Check Run；Check Run 只有被仓库规则设为 required 才阻断 merge；不等于自动发布 | high | passed |
| RVG-C12 | AWS 与 Meta 在页面上承担不同证明任务：AWS 证明动态验证可接宿主 merge gate，Meta 证明按 Diff 生成、对照和评估候选信号 | RVG-C01—C11；[[00_sources/research-agent-generated-verification-gate-technical-basis-2026-08-03#六、可安全进入技术洞察页的最小参考方案|技术事实审计]] | AWS 仍是 Preview；Meta 没有公开 Required Check 或自动阻断证据 | high | passed |
| RVG-C13 | AWS 官方 Release Testing Action 将 repository、head SHA、PR、test profile 与可选 test requirement 作为 Webhook 作业上下文提交；Agent Space 创建并更新关联 commit 的 Check Run | [AWS 官方 Action README](https://github.com/aws-actions/devops-agent-qa/blob/main/README.md)；[`action.yml`](https://github.com/aws-actions/devops-agent-qa/blob/main/action.yml)；[`src/index.ts`](https://github.com/aws-actions/devops-agent-qa/blob/main/src/index.ts)；[[00_sources/research-agent-generated-verification-gate-technical-basis-2026-08-03#2. 官方 Action 源码暴露的流水线接入机制|源码审计]] | 这是 GitHub Action 路径；AWS 未公开 GitLab release-testing 的等价状态回写实现，也未公开 test-plan schema | high | passed |

## 证据缺口

- AWS Release Management 的独立准确率、误报、覆盖率、典型耗时与生产客户成效；
- AWS release-testing 在 GitLab 的状态回写接口、Check Run 被设置为 required 的默认性，以及 fail-open/fail-closed 细节；
- Meta JiTTesting 的完整调度策略、仓库覆盖范围、运行时预算、权限模型和 Required Check 集成；
- 两个系统的证据 schema、可重放性、签名/证明、长期审计与异常降级实现；
- 任何跨公司、第三方复现实验或行业平均收益。

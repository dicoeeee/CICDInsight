---
title: CI/CD 问题自愈研究边界
tags:
  - research/agentic-cicd
  - research/charter
  - scenario/self-healing
status: complete
as_of: 2026-08-09
---

# CI/CD 问题自愈研究边界

## 决策目标

回答企业应优先让 Agent 自愈哪些 CI/CD 问题、怎样搭建可验证的闭环、什么时候可以自动执行，以及如何防止 Agent 通过弱化门禁制造“伪绿灯”。

## 研究范围

- 代码提交后的测试、门禁、编译、构建和流水线失败；
- 依赖、安全和配置修复；
- 测试脚本/定位器自愈与真实产品缺陷修复的边界；
- IaC、GitOps、部署和渐进发布恢复；
- 发布后调查、Runbook 处置、回退与预防性 Fix-forward；
- 检测、取证、分类、修复、验证、执行、观察、回退和学习闭环；
- 权限、身份、预算、审计、并发、重试和停止条件。

## 重点观察对象

本轮六家公司主审计对象为：GitHub Actions Rerun、Agentic Workflows/CI Doctor、Agentic Autofix；GitLab CI Retry 与 Fix CI/CD Pipeline Flow；CircleCI Automatic Rerun 与 Chunk；Harness Failure Strategy、Code Quality AutoFix 与 Worker Agents；Nx Flaky Retry 与 Self-Healing CI；Buildkite Retry、Test Engine、失败分析插件与 Agent/MCP 底座。Snyk、Dependabot、BrowserStack、Akuity、HolmesGPT 和 AWS DevOps Agent 继续作为专项对照。

## 非目标

- 不回顾传统 CI/CD 的发展史；
- 不把所有 AIOps、自动重试或 Kubernetes Reconcile 都重新包装为 Agent 自愈；
- 不把厂商“Autonomous”“Self-Healing”命名当作能力证明；
- 不把当次 CI 变绿等同于缺陷真正消除或长期零回归；
- 不实际连接生产账号、开放写权限或运行付费产品；
- 不以模型排行榜代替场景、控制面和 Oracle 的工程评估。

## 观察口径

- 观察日：2026-08-09；重点来源为 2025 年下半年以后，优先 2026 年材料；
- 产品事实优先官方文档、官方工程博客、官方仓库和原始研究；
- 厂商效果指标明确标记为第一方自述，不外推为行业因果结论；
- “实验已设计”和“实验已执行”严格分开；本专题实验尚未执行；
- 自愈完整度用 `SH0—SH4`，行动权限继续用仓库统一的 `L0—L4`，两者不混用。
- 平台、Flow、Agent、Endpoint、插件和参考实现分别记录生命周期；“已文档化”不自动等于 GA。
- 未检索到的能力只写“本轮公开一手材料未证明”，不写成产品不存在。

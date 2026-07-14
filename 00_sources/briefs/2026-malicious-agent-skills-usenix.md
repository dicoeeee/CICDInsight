---
title: USENIX 2026 恶意 Agent Skills 实证研究
source_id: malicious-agent-skills-2026-02-06
organization: International Academic Research
source_type: peer-reviewed-security-research
published: 2026-02-06
verified: 2026-07-14
availability: research
confidence: high
geography:
  - global
lifecycle_stages:
  - cross-cutting
tool_categories:
  - skill-supply-chain
  - security-analysis
  - threat-research
company_topics: []
autonomy_levels:
  - L1
  - L2
  - L3
  - L4
tags:
  - research/agentic-cicd
  - evidence/source-brief
---

# USENIX 2026 恶意 Agent Skills 实证研究

## 来源

- 标题：“Do Not Mention This to the User”: Detecting and Understanding Malicious Agent Skills in the Wild
- 首次提交：2026-02-06；v4：2026-06-10
- 链接：[原始论文](https://arxiv.org/abs/2602.06547)
- 来源类型：原始安全研究，已接受 USENIX Security 2026
- 能力状态：同行评审会议录用；数据集和检测流水线公开

## 一句话结论

Skill 供应链风险已经不是假设：自然语言说明和随附脚本可以共同操纵 Agent、窃取凭据并利用用户授予的工具权限。

## 可核验事实

- 研究分析 98,380 个 Skills，确认 157 个恶意 Skills。
- 确认样本包含 632 个漏洞，覆盖 13 种攻击技术。
- 主要策略包括通过代码执行窃取凭据，以及在文档中嵌入对抗指令操纵 Agent。
- 负责任披露后，注册中心移除了全部 157 个确认样本。

## CI/CD 相关性

- 涉及阶段：全部八阶段，尤其高权限仓库、流水线、制品和部署工具。
- 工具类别：Agent Skill 供应链、静态/动态检测、威胁建模。
- 自主等级：所有等级的共同风险。
- 涉及角色：AppSec、供应链安全、平台工程、Agent Control Plane 团队。

## 对洞察的价值

为 Skill 注册、签名、版本锁定、恶意行为扫描和最小权限提供实证依据；工具目录本身必须像依赖和制品一样治理。

## 限制与待验证项

- 生态和恶意样本会快速变化，快照比例不能直接外推到企业私有目录。
- 检测器不能替代沙箱、权限隔离和运行时策略。

## 可引用判断

- Agentic CI/CD 的软件供应链必须同时覆盖库、制品、模型、Prompt、Skill、MCP Server 和其随附脚本。

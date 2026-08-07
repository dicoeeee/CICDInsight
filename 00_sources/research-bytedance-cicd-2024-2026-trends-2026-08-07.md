---
title: 字节跳动与火山引擎 CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）
tags:
  - research/agentic-cicd
  - evidence/primary-source
  - company/bytedance
  - trend-update
status: complete
as_of: 2026-08-07
accessed: 2026-08-07
confidence: high-for-volcengine-CP-product-status-and-feature-timeline-from-official-docs; medium-for-SE-Lab-AI-research-status; low-for-Bytedance-internal-CI-platform-and-AI-code-review-current-state
---

# 字节跳动与火山引擎 CI/CD 变化趋势研究（2024 至今，截至 2026-08-07）

## 研究范围与口径

- **任务性质**：趋势更新与补充。仓库既有研究（`research-bytedance-alibaba-build-test-practices-2026-08-03`）已覆盖 Fastbot2、Hawkeye、SIT、NxtUnit 及内部 Monorepo/Bazel/Rspack 线索；本报告聚焦 2024+ 新材料，重点回答：(1) 可核验的 CI/CD 步骤变化；(2) 内部 CI/CD 平台披露；(3) 2024+ 新开源/论文（对比 Fastbot2/Hawkeye/SIT/NxtUnit）；(4) AI/LLM 重塑 CI/CD（AI 测试生成、AI 代码评审、AI 构建修复）；(5) 火山引擎研发效能产品（持续交付 CP、代码检查、制品仓库）2024+ 能力变化。
- **访问时间**：全部链接于 2026-08-07 实际访问；无法访问或未核验项显式标记 `unverified`。
- **一手来源**：火山引擎官方文档 `docs.volcengine.com/docs/6461`（持续交付 Code Pipeline, CP）、`se-research.bytedance.com`（字节 SE Lab）、GitHub `bytedance` 组织官方仓库、产品公告页面。
- **证据分级**：`事实`（来源明确陈述）/ `分析推断`（由证据推导，不写成已发生事实）/ `待验证假设`（无证据支撑的检索方向）。
- **产品名核对**：火山引擎研发效能 CI/CD 产品官方全名为**持续交付（Code Pipeline，CP）**，产品文档 ID 6461。历史资料中常被称为"Flow/流水线"，但官方文档当前以"持续交付"命名，其产品架构图与文档指南均使用 CP 简称。`Flow` 品牌名在火山产品导航中仍是研发效能大类的常称，但本报告以官方文档名称为准。

## 结论先行

1. **火山引擎持续交付 CP 是可核验的 GA 产品，2024-2026 有密集功能迭代，且 2024 年起向 AI 应用部署与 Agent 化方向扩张。** 2022-04-15 正式商用，2023-08-07 基础版 v2 对新用户开放，V1 于 2025-07-31 正式下线；2024 年新增 AI 应用部署（邀测 2024-04，正式发布 2025-03）、制品上传/下载、镜像源触发、应用托管、OpenAPI v2 开放等；2025-2026 新增 YAML 流水线、OAM 商用、Helm、AgentKit 部署任务、Nydus 加速镜像、多云容灾模板、ECS 镜像构建流水线等。构建加速服务（Sailfish/Bazel 生态 RBE）2023-12-14 商业化后于 2025-09-25 下线。
2. **字节 SE Lab 2024-2026 的 AI 软件工程研究显著转向 LLM Agent 方向，是"AI 重塑 CI/CD"最可核验的字节一手证据。** Trae Agent（SWE-bench Verified SOTA，已开源）、Repo2Run（NeurIPS 2025 Spotlight，自动生成可执行环境/构建脚本）、AEGIS（FSE 2025 Industry，从 issue 复现 bug）、MarsCode Agent（自动 bug 修复）等，直接覆盖"构建修复、测试生成、环境搭建"等 CI/CD 上游环节。但这些都是研究/开源项目，**不**等于字节当前生产 CI/CD 平台能力。
3. **未发现字节内部 CI/CD 平台（Monorepo/Bazel/Rspack 等）随时间演进的 2024+ 一手披露。** `codes.bytedance.com` 与 `json.bytedance.com` 均无法访问（`unverified`），既有线索仍停留在 2023 及更早的二次来源，不能据此绘制字节内部 CI/CD 架构。
4. **AI 代码评审在火山引擎侧以"代码扫描/质量门禁"为名义存在（SonarQube 集成、准入条件），但未找到 2024+ 的方舟/豆包 LLM 驱动的代码评审产品化证据**（`unverified`，见证据缺口）。
5. **GitHub `bytedance` 组织 2024+ 新开源中与 CI/CD 直接相关的增量有限**：flow-builder（2021 创建，流程画布）与 flowgram.ai（2025-02 创建，8.3k★，AI 工作流开发框架，被 Coze Studio 采用）不是 CI/CD 编排器；Jenkins 生态仅有 2019 年创建的 `jenkins-plugin-lark`（通知插件）。2024+ 无新的 CI/CD 编排/构建引擎级开源。

---

## 一、事实表

> 访问时间统一为 2026-08-07。证据强度：`强`=官方一手来源直接核验；`中`=官方来源 + 仓库既有交叉核验；`厂商自述`=仅单一厂商来源，不得外推为行业结论。

| # | 事实 | 一手来源 URL | 发布时间 | 产品状态 | 证据强度 |
|---|---|---|---|---|---|
| F1 | 火山引擎持续交付（Code Pipeline，CP）官方定位为"云原生应用交付平台 + 一站式流水线"，提供持续集成、持续测试、持续部署；正式商用时间为 2022-04-15 00:00，基础版仍免费 | https://www.volcengine.com/docs/6461/70636 + https://www.volcengine.com/docs/6461/107780 | 2025-03-18 更新 / 2022-04-15 | **GA** | 强（本次全文核验） |
| F2 | 持续交付基础版 v2 于 2023-06-19 邀测首次上线，2023-08-07 对新用户正式开放；V1 老用户继续使用 V1 控制台直至迁移 | https://www.volcengine.com/docs/6461/1767694 | 2023-06-19 / 2023-08-07 | **GA（v2）** | 强 |
| F3 | 持续交付 V1 版本下线规划：2025-07-10 起不支持新建 V1 服务，2025-07-31 正式下线存量 V1（`/cp/overview`）；存量用户均已完成迁移至 V2（`/cp/v2/overview`） | https://www.volcengine.com/docs/6461/1607393 | 2025-07 | **下线** | 强 |
| F4 | 构建加速服务（分布式云端编译 + 共享分布式缓存，精准增量与依赖识别）2023-12-14 正式商用；2025-09-25 因产品调整下线，无法再使用 | https://www.volcengine.com/docs/6461/1817282 + https://www.volcengine.com/docs/6461/1179013 | 2023-12-14 / 2025-09-25 公告 | **已下线** | 强 |
| F5 | 构建加速以 Bazel 生态为基础提供远端构建执行与远端缓存（Sailfish 实例类型），支持 AOSP 13+ 分布式构建（RBE）；2024-06-13 Sailfish 客户端升至 1.0.3 | https://www.volcengine.com/docs/6461/1767694 | 2023-09-07 / 2024-06-13 | 已下线 | 强 |
| F6 | 2024-07-03 计费方案变更：停售两档资源包；支持自助选配流水线任务资源规格（1:2/1:4/1:8 CPU 内存配比）；运行时按量计费改为核时计费（1c2g 0.0200 元/分钟、1c4g 0.0242 元/分钟、1c8g 0.0325 元/分钟） | https://www.volcengine.com/docs/6461/1281113 | 2024-07-03 | GA | 强 |
| F7 | 2024 年功能迭代（节选）：07-01 选择器任务、人工卡点部分会签、最终阶段；05-30 合并请求新建/更新触发、流水线运行状态自动同步至代码仓库 PR 评论；04-16 并发锁（细粒度并发度控制）；02-06 镜像源 Webhook 触发、应用托管正式开放、v2 OpenAPI 正式开放；01-11 制品上传/下载、流水线变量组 | https://www.volcengine.com/docs/6461/1767694 | 2024-01 至 2024-07 | GA | 强 |
| F8 | 2024-04-16 应用交付支持 AI 推理应用部署（邀测）；2024-07 AI 应用部署支持 ConfigMap 挂载、VCI 自定义系统盘；2025-03 AI 应用正式发布，支持 DeepSeek、Stable Diffusion、Qwen 等一键部署；2025-05 支持 mGPU 共享部署与大模型分布式部署；2026-10 支持滚动更新 | https://www.volcengine.com/docs/6461/1263454 + https://www.volcengine.com/docs/6461/1767694 + https://www.volcengine.com/docs/6461/70635 | 2024-04 至 2026 | 邀测 → **GA（2025-03）** | 强 |
| F9 | 2025-2026 功能迭代（节选）：2025-01 灰度发布托管应用、多云容灾流水线模板、OAM 跨工作区复制、服务连接；2025-03 TOS 上传正式发布、NodeJS 编译加速；2025-07 GitCode/Coding 代码源、VKE 私有资源池、应用模板正式发布；2025-09 托管应用 APM 观测；2025-10 香港地域部署集群；2025-11 Helm 应用、ECS 镜像构建流水线；2026-03 Nydus 转换、IRSA、AgentKit 部署/更新任务（Test-Time 相关 Agent 运行时）；2026-04 OAM 正式商用、**流水线支持通过 YAML 创建**；2026-06 托管应用按标签部署、OpenJDK 21 | https://www.volcengine.com/docs/6461/70635 | 2025-01 至 2026-06 | GA（YAML 流水线 / OAM 2026-04） | 强 |
| F10 | 流水线状态模型：排队中(Enqueued)→环境启动中(Dequeued)→等锁中(LockWaiting)→运行中(InProgress)→成功/失败/已取消/环境启动超时；采用异步调度机制 | https://www.volcengine.com/docs/6461/2306248 | 页面当前 | GA | 强 |
| F11 | 流水线内置步骤覆盖：编译构建（Java/NodeJS/Python/Go）、单元测试（Java Maven/Gradle、Python、Go）、SonarQube 代码扫描、镜像构建推送、制品管理（上传/下载/TOS）、Kubernetes 镜像升级/分批发布/滚动发布、Kubectl 发布、命令执行、人工卡点、选择器、函数构建/部署、ECS 镜像构建 | https://www.volcengine.com/docs/6461/117819 | 页面当前 | GA | 强 |
| F12 | 流水线支持图形化编排（阶段/任务/步骤三层拖拽）、YAML 创建、代码源分支模式、缓存目录、环境变量、Webhook/定时/代码源/镜像源触发、并发锁、失败自动重试、触发失效策略、起飞（跳过失败步骤） | https://www.volcengine.com/docs/6461/117819 + https://www.volcengine.com/docs/6461/70635 | 页面当前 | GA | 强 |
| F13 | 应用交付以 OAM（Open Application Model）与 K8s YAML 应用为中心，2026-04 OAM 正式商用；支持 Helm 应用（2025-11）、环境管理、分批/灰度/原生部署、组件/插件模板、多环境差异化部署 | https://www.volcengine.com/docs/6461/1182431 + https://www.volcengine.com/docs/6461/70635 | 2026-04 | **GA** | 强 |
| F14 | 托管应用（Java JAR/WAR/容器镜像/二进制包、ECS 技术栈）支持创建/灰度/分批发布、扩缩容、APM 观测、IRSA 授权；自动生成 Dockerfile 并推送至火山镜像仓库 CR | https://www.volcengine.com/docs/6461/1130703 + https://www.volcengine.com/docs/6461/70635 | 2024-02 正式开放 | GA | 强 |
| F15 | 私有构建资源池基于 Tekton Webhook 架构（2022-06 陶升级 Webhook 配置提高并发响应）；2025-07 新增 VKE 私有资源池封装 | https://www.volcengine.com/docs/6461/116640 + https://www.volcengine.com/docs/6461/70635 | 2022-06 / 2025-07 | GA | 强 |
| F16 | 字节 SE Lab（Trae 研究分支）2024-2026 论文覆盖：Trae Agent（SOTA on SWE-bench Verified，arXiv 2507.23370，已开源）、Repo2Run（NeurIPS 2025 Spotlight，Python 仓库 86% 成功生成无错 Dockerfile/可执行环境）、AEGIS（FSE 2025 Industry，从 issue 描述复现 bug）、MarsCode Agent（arXiv 2409.00899，SWE-bench Lite 修复 39.33%）、SGo-Oracle（ESEM 2025，Go 并发 bug 测试 oracle）、RepoMasterEval（ASE 2025 Industry）、CodeVisionary（ASE 2025）、SoRFT（ACL 2025）、ToolTrain / More with Less / LLM-based Agents for Bug Fixing（ICSE 2026）、CoReQA / ContextModule 等 | https://se-research.bytedance.com/ | 2024-2026 | 研究/开源，非生产产品 | 强（官方页面逐条核验） |
| F17 | Trae Agent 2025-06-13 开源（`bytedance/trae-agent`，11.9k★，2026-08-07 核验），定位 LLM 软件工程通用 Agent，homepage trae.ai | https://github.com/bytedance/trae-agent | 2025-06-13 | **开源** | 强 |
| F18 | Repo2Run 开源（`bytedance/Repo2Run`，2025-03-05 创建，196★），LLM Agent 自动为 Python 仓库生成无错 Dockerfile/可执行环境 | https://github.com/bytedance/Repo2Run | 2025-03-05 | **开源** | 强 |
| F19 | flowgram.ai（`bytedance/flowgram.ai`，2025-02-17 创建，8.3k★）是可组合、可视化、可扩展的工作流开发框架，内置画布/表单/变量/材料（LLM、Condition、Code Editor 等），用于构建 AI 工作流平台；README 声明被 Coze Studio 采用 | https://github.com/bytedance/flowgram.ai | 2025-02-17 | 开源/框架 | 强 |
| F20 | flow-builder（`bytedance/flow-builder`，2021-10-08 创建，696★）是可高度自定义的流式流程画布组件（React），topic 含 flow/process/builder，homepage bytedance.github.io/flow-builder | https://github.com/bytedance/flow-builder | 2021-10-08 | 开源/组件 | 强 |
| F21 | `bytedance/jenkins-plugin-lark`（2019-06-20 创建，30★）为 Jenkins 通知插件，将任务状态通知发送至飞书（Lark）工作群；2024-07-13 仍有推送 | https://github.com/bytedance/jenkins-plugin-lark | 2019-06-20 | 开源；维护状态中低 | 强 |
| F22 | GitHub `bytedance` 组织搜索 "org:bytedance deploy"、"pipeline"、"monorepo"、"ci cd" 均无结果（0 条）；未在 bytedance 组织发现 2024+ 的 CI/CD 编排/构建引擎级开源 | https://api.github.com/search/repositories?q=org:bytedance+deploy | 2026-08-07 检索 | — | 中（检索口径受限，见 G5） |
| F23 | 持续交付不支持自建代码托管：GitLab 托管服务 2022-11-17 正式下线（2022-08-11 关闭入口）；代码源仅以接入方式支持 Gitee/GitLab/GitHub/Codeup/Gerrit/BitbucketCloud/Coding/GitCode/通用 Git | https://www.volcengine.com/docs/6461/129283 + https://www.volcengine.com/docs/6461/70636 | 2022-11-17 | 已下线 | 强 |
| F24 | 持续交付服务等级协议（SLA）与专用服务条款公开存在 | https://www.volcengine.com/docs/6461/107749 + https://www.volcengine.com/docs/6461/78997 | 页面当前 | GA | 强 |
| F25 | CP 采用异步调度、资源锁（并发锁）并发控制，确保任意时刻仅一个流水线实例访问特定共享资源 | https://www.volcengine.com/docs/6461/2306248 | 页面当前 | GA | 强 |

---

## 二、火山引擎持续交付 CP：产品面与 2024+ 变化（问题 1、5）

### 产品定位（官方陈述）

持续交付 = "云原生应用交付平台 + 一站式流水线"，打通研发运维各环节，完成持续集成、持续验证、持续发布（F1）。核心能力三块：**流水线**（CI/CD 编排）、**应用交付**（以应用为中心的 K8s YAML/OAM/Helm 交付）、**资源管理**（构建资源池 + 部署资源）（F11-F12、F13）。

### 2024+ 结构性变化

1. **V1 → V2 迁移完成，V1 下线（F2-F3）**：2025-07-31 正式下线 V1，是"产品生命周期"层面的可核验变化。V2 从 2023-08 起持续迭代。
2. **构建加速服务上线后下线（F4-F5）**：2023-12-14 商业化 → 2025-09-25 下线。这是**分析推断**：其"分布式云端编译 + 共享缓存 + 精准增量"在字节内部有 Bazel/Rspack 线索（仓库既有研究），但下线原因官方仅写"产品调整"，未公开是否与内部自研构建或营收取舍有关。
3. **AI 应用部署成为产品主线之一（F8）**：从 2024-04 邀测到 2025-03 正式发布，内置 DeepSeek/Qwen/LLaMA/Stable Diffusion 一键部署模板、mGPU、分布式推理、滚动更新。这是 CI/CD 平台向"AI 应用生命周期交付"扩张的可核验证据。
4. **编排方式现代化（F9、F12）**：2026-04 支持 YAML 创建流水线（评审/审计/跨项目迁移），OAM 2026-04 正式商用，Helm 2025-11。图形化 + YAML 双通道。
5. **Agent 化初现（F9、F16）**：2026-03 新增 AgentKit 运行时部署/更新任务（流水线内编排 Agent 运行时）；SE Lab 的 Trae Agent 等研究为 Agent 化软件工程提供底层能力。但 AgentKit 是"部署 Agent 运行时"而非"Agent 编排 CI/CD"，边界须保留。

### 与仓库既有结论的衔接

仓库 `research-bytedance-alibaba-build-test-practices-2026-08-03` 的结论"Fastbot2 公开部署证据停在 2022 论文期"在本轮**未发现反证**；Hawkeye 为 ICSE-SEIP 2024 论文，2024+ 无新披露。SE Lab 2024-2026 的新论文（F16）是新增证据，但性质上是研究/开源，与生产 CI/CD 平台能力严格区分。

---

## 三、AI/LLM 重塑 CI/CD：字节可核验证据（问题 4）

### 直接覆盖 CI/CD 阶段的 SE Lab 研究

| 研究 | 覆盖的 CI/CD 阶段 | 状态 | 证据 |
|---|---|---|---|
| Trae Agent（SWE-bench Verified SOTA） | 编码后修复、issue 解决（上游） | 开源，arXiv 2507.23370 | F16-F17 |
| Repo2Run（NeurIPS 2025 Spotlight） | 构建环境搭建、可执行环境生成（CI 准备） | 开源，2025-03-05 | F16、F18 |
| MarsCode Agent（arXiv 2409.00899） | 自动 bug 修复（SWE-bench Lite 39.33%） | 论文/产品 MarCode | F16 |
| AEGIS（FSE 2025 Industry） | 从 issue 复现 bug（测试生成上游） | 论文 | F16 |
| SGo-Oracle（ESEM 2025） | Go 并发 bug 测试 oracle（测试判定） | 论文 | F16 |
| NxtUnit（既有） | Go 单测生成 | 开源 2023 | 仓库既有 |
| SoRFT / ToolTrain / CodeVisionary 等 | Agent 训练、定位、评测 | 论文 | F16 |

**结论（分析推断）**：字节在"AI 构建修复（Repo2Run）、AI 测试生成（NxtUnit/SGo-Oracle）、AI issue 解决（Trae/MarsCode/AEGIS）"三个方向有真实研究资产，且 2024-2026 显著加速。但**这些是研究/开源能力，不是已核验的字节内部生产 CI/CD 门禁**；"MarsCode 修复 39.33%"是论文观察期内部测量，不进入正式结论。

### AI 代码评审

- 火山引擎侧：CP 提供 SonarQube 代码扫描步骤（F11）与质量门禁（2022-2023 已有），属确定性工具集成，**未发现** 2024+ 的 LLM 驱动代码评审产品化证据（`unverified`，G1）。
- 内部侧：字节 AI 代码评审（如"代码评审 Agent"）在既有仓库 `00_sources` 中无一手来源；本轮 `codes.bytedance.com` 不可访问，**不得**把搜索缺失写成"字节没有 AI 代码评审"（G2）。

---

## 四、字节内部 CI/CD 平台披露（问题 2）

- **未找到 2024+ 一手披露**。`codes.bytedance.com`、`json.bytedance.com` 2026-08-07 均无法访问（连接超时/HTTP 000）；archiv.org wayback 对该二域名亦无近期快照返回（`unverified`，G2）。
- 既有 Monorepo/Bazel/Rspack/Monorepo 工具线索停留在微信公众号/博客二次转载，不进入事实表（与仓库既有结论一致）。
- 火山引擎 CP 是字节 CI/CD 能力对外产品化的唯一可核验载体；其 Tekton 私有池（F15）、Sailfish RBE（F5）等标明字节内部有自研构建执行能力，但外部不可见。

---

## 五、证据缺口清单

- **G1（重要）**：未找到火山引擎/字节 2024+ 的 LLM 驱动 AI 代码评审产品化一手证据。方舟（Ark）agent/AI 代码检查如存在，须补官方文档；本轮 `www.volcengine.com/product/flow` 与方舟文档为 JS 渲染无法提取，`docs.volcengine.com/api/docs` 返回 `UnauthorizedAccess`。不得把"未检索到"写成"业界不存在"。
- **G2（重要）**：字节内部 CI/CD 平台（Monorepo/Bazel/Rspack/内部流水线）2024+ 演进无一手来源；`codes.bytedance.com`、`json.bytedance.com` 不可访问，wayback 快照缺失。不能用模型记忆代替证据，相关结论保持阻塞。
- **G3**：持续交付"新功能发布记录"页（70635）与"历史功能发布记录"页（1767694）在 `www.volcengine.com` 上触发 JS 反爬挑战，本报告通过 `docs.volcengine.com` 域名取得全文；两域名内容一致性已抽查（F7/F9 均能回链），但未逐条比对。
- **G4**：产品公告页（107780 等）在 www 域名反爬，经 docs 域名取得；`【产品变更】构建加速服务下线`（1817282）与 Sailfish 相关公告的精确生效时间戳以页面为准，未二次核验。
- **G5**：GitHub 搜索 "org:bytedance+deploy/pipeline/monorepo" 返回 0 条，但 GitHub 搜索 API 对未认证请求限流（124.70.231.88 曾反复命中 rate limit），且按仓库 description/topics 搜索可能漏掉描述不含关键词的仓库；因此 F22 的"无 CI/CD 编排级开源"结论置信度为中，需补全量 `org:bytedance/repos` 枚举复核。
- **G6**：SE Lab 论文的数值（Repo2Run 86% 成功、MarsCode 39.33%、SoRFT 21.4% SWE-bench Verified、ToolTrain 成本降 68%）均为论文/厂商自述，无独立第三方复现，不进正式结论。
- **G7**：`Flow` 品牌名与"持续交付 CP"的名称关系：火山产品导航中"Flow"仍被用作研发效能大类常称，但官方文档正文统一用"持续交付（Code Pipeline，CP）"。两者是否同一产品、命名变更的精确时间线未获官方说明，本报告按官方文档当前命名记录。
- **G8**：`www.volcengine.com/product/flow` 产品页为 JS 渲染，无法提取产品介绍文案；本轮改用文档中心 6461 作为产品定位依据。

---

## 治理说明

本报告为业界洞察研究成果，按 AGENTS.md 归入 `00_sources/`。全部状态、日期、版本号来自 2026-08-07 实际访问的官方页面与 GitHub API；`unverified` 项（G1/G2）已显式标记，相关结论保持阻塞。SE Lab 与论文数值均为厂商自述/第一方研究，未外推为行业平均值。如需进入 `90_report/` 或 Presentation（如 `cicd-trends-2024-2026` 专题重用），须由主 Agent 完成逐主张事实审计，并补齐 G1/G2/G5 证据缺口。
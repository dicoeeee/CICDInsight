---
title: Dagger 与 Buildkite 公开采用公司核验预研（2026-08-01）
tags:
  - research/agentic-cicd
  - company/dagger
  - company/buildkite
status: pre-research
as_of: 2026-08-01
---

# Dagger 与 Buildkite 公开采用公司核验预研（2026-08-01）

> [!warning] 选题预研，非 Source Brief，不得直接进入正式 Presentation
>
> 本文只记录截至 2026-08-01 可公开核验的“采用”证据，访问日期为 2026-08-01。它不证明企业在该日仍在使用，也不能代替后续 Deep Dive 的逐主张事实审计。

## 口径与判定方法

- **A — 采用方一手证据**：公司自身工程博客、公开仓库或技术说明明确陈述自身使用。
- **B — 厂商署名客户案例**：Dagger 或 Buildkite 的客户案例。能证明案例发布时的采用范围，但仍是厂商材料。
- **C — 厂商 logo/customer 页面**：仅证明厂商将该公司列为客户或展示 logo；本轮不以 C 作为主清单证据。
- **current-confirmed**：有较新的 A 级材料直接使用现在时或描述已上线运行；这只表示“截至该证据日期”，不是对 2026-08-01 的无限外推。
- **recent-first-party-case**：2024–2025 年的 A/B 级具名案例；无更新证据，不写成当前仍在使用。
- **historical/uncertain**：2023 年及以前的材料，或没有更近证据。

不把“Dagger 可在 Buildkite 上运行”、双方文档的集成示例、合作伙伴关系或职位描述中泛列的工具当作客户采用。

## 结论先行

1. **Dagger 的公开客户证据多为厂商案例，且案例大多是局部交付流或特定平台团队的使用。** 本轮未取得可将外部客户标为 `current-confirmed` 的近期采用方 A 级证据。
2. **Buildkite 可找到更多采用方自己发布的工程材料。** Reddit（2025）是最接近当前的 A 级公开证据；Canva（2024）亦清楚描述其 CI 中的 Buildkite 环境与 pipeline。
3. **在本轮检索到的两份公司清单及其一手链接范围内，未检索到同一家公司同时拥有 Dagger 和 Buildkite 两项独立采用证明。** Dagger 团队自身的 2024 实验文章提及“在 Dagger 中运行 Buildkite Agent”的实验，但这不是外部客户采用，故不计入重叠客户。

## Dagger：具名采用证据

| 公司 | 使用对象 / 范围 | 证据日期 | 类型 | 仍在用可否判断 | 状态 | 证据 |
|---|---|---:|---|---|---|---|
| Puzzle ITC | OpenShift/Kubernetes CI 中运行 Dagger Engine，以 KubeVirt 临时 VM 提供隔离；接入 GitLab CI 与 Argo CD。 | 2025-02-24 | B | 否；仅能确认案例日期的范围。 | recent-first-party-case | [Dagger 案例](https://dagger.io/blog/puzzle-case-study/) |
| Craft CMS / Craft Cloud | Craft Cloud 的构建系统使用 Dagger 构建并部署客户网站。 | 2024-10-21 | B | 否；无更近采用方材料。 | recent-first-party-case | [Dagger 案例](https://dagger.io/blog/craft-cms-case-study/) |
| Safespring | 以 Dagger Functions 构建 Ansible 容器，执行部署、交互排障及 plan/check。 | 2024-10-01 | B | 否；无更近采用方材料。 | recent-first-party-case | [Dagger 案例](https://dagger.io/blog/safespring-case-study/) |
| OpenMeter | 用 Dagger Functions 构建生产交付 pipeline，并使用 Dagger Cloud。 | 2024-07-01 | B | 否；案例中的当时态不外推至 2026。 | recent-first-party-case | [Dagger 案例](https://dagger.io/blog/daggerizing-production-pipelines-how-openmeter-overcame-push-and-pray/) |
| Civo | 用 Dagger 将单仓库 CI/CD pipeline 以 Go 代码定义和测试。 | 2024-01-31 | B | 否；无更新证据。 | recent-first-party-case | [Dagger 案例](https://dagger.io/blog/adopting-monorepo-strategy/) |
| Airbyte | Connector CI 从 GitHub Actions/YAML/脚本迁至 Dagger；涉及远程 Engine、Kubernetes 与 Dagger Cloud 缓存。 | 2023-10-25 | B | 否；历史案例。 | historical/uncertain | [Dagger 案例](https://dagger.io/blog/airbyte-use-case/) |
| Lunar | 平台/DevEx 工具逐步迁往 Dagger；本地构建和 CI 的镜像 pipeline 共用 Go SDK。 | 2023-10-18 | B | 否；历史案例。 | historical/uncertain | [Dagger 案例](https://dagger.io/blog/enabling-platform-engineering/) |
| Discern | 每个 PR 构建隔离预览环境；以 Dagger Go SDK 和 Kubernetes，合并后复用 pipeline 部署生产。 | 2023-08-10 | B | 否；历史案例。 | historical/uncertain | [Dagger 案例](https://dagger.io/blog/discern-use-case) |
| Flipt | CI 的构建、单元测试和集成测试使用 Dagger；发布仍使用 GoReleaser。 | 2023-06-13 | B | 否；历史案例。 | historical/uncertain | [Dagger 案例](https://dagger.io/blog/flipt-use-case) |
| Grafana Labs | 用 Dagger Go SDK 为终端用户构建跨架构 Grafana 软件包，兼顾本地与远程 CI。 | 2023-05-09 | B | 否；历史案例。 | historical/uncertain | [Dagger 案例](https://dagger.io/blog/grafana-use-case) |

**Dagger 证据边界。** 以上 B 级材料证明“案例发布时采用了 Dagger”，并不证明全公司所有交付流都已迁移。未纳入 Hof 等个人维护的开源项目，也未以客户 logo 补足数量。

## Buildkite：具名采用证据

| 公司 | 使用对象 / 范围 | 证据日期 | 类型 | 仍在用可否判断 | 状态 | 证据 |
|---|---|---:|---|---|---|---|
| Reddit | iOS 与 Android 的整个 mobile CI 已迁至 Buildkite；使用 hosted XL 和自建 Kubernetes Agent Stack、动态 pipeline 与缓存。 | 2025-07-31 | A | 可以确认至文章所述已上线后三个月；不能推断此后状态。 | current-confirmed | [Reddit Engineering](https://www.reddit.com/r/RedditEng/comments/1megwf1/) |
| REA Group | 官方案例称 Buildkite 驱动组织内大多数 build，并是新项目的首选 CI 工具；案例 PDF 于 2025-07 更新。 | 2025-07 | B | 仅供应商案例的当时表述，未取得 REA 自身更新材料。 | recent-first-party-case | [Buildkite 案例](https://buildkite.com/resources/case-studies/rea/) |
| Canva | 自身工程文章说明大型 CI 的外部依赖包含 Buildkite，且将 pipeline 定义转换为 Buildkite 所需 YAML。 | 2024-07-30 | A | 仅可确认至文章日期。 | recent-first-party-case | [Canva Engineering](https://www.canva.dev/blog/engineering/faster-ci-builds-at-canva/) |
| Elastic | Kibana 是首个迁移到 Buildkite 的产品；使用动态 pipeline 切分测试。 | 2024-07（案例 PDF） | B | 否；未取得 Elastic 后续 A 级更新。 | recent-first-party-case | [Buildkite 案例](https://buildkite.com/resources/case-studies/elastic/) |
| PagerDuty | 厂商案例称其所有部署 pipeline 已迁移，99% 生产 build 经由 Buildkite；统计窗口为 2023-01 至 2023-10。 | 2023-10（统计窗口） | B | 否；统计已旧。 | historical/uncertain | [Buildkite 案例](https://buildkite.com/resources/case-studies/pagerduty/) |
| Shopify | 厂商案例称其自动扩缩 build server，运行近 10,000 个并发 agent；统计窗口为 2023-01 至 2023-10。 | 2023-10（统计窗口） | B | 否；统计已旧。 | historical/uncertain | [Buildkite 案例](https://buildkite.com/resources/case-studies/shopify/) |
| Rippling | 自身工程文章称其以 Buildkite 为控制面、AWS 为云环境，运行大量 Elastic CI Stack。 | 2023-11-15 | A | 否；无更近材料。 | historical/uncertain | [Rippling Engineering](https://www.rippling.com/blog/how-rippling-used-spot-instances-to-save-and-scale-ci-cd) |
| Retool | 自身工程文章称 CI 已从 Azure Pipelines 迁至 Buildkite，并在 Kubernetes 测试环境运行。 | 2023-01-27 | A | 否；无更近材料。 | historical/uncertain | [Retool Engineering](https://retool.com/blog/moving-retools-ci-from-azure-pipelines-to-buildkite-with-kubernetes) |
| Slack | 自身工程文章称 mobile CI 已从 Jenkins 迁出到 Buildkite；后续文章亦将每日无障碍回归运行配置在 Buildkite。 | 2022；2024 | A | 不足以判定 2026 仍在用。 | historical/uncertain | [Slack mobile CI](https://slack.engineering/mobile-developer-experience-at-slack/)；[Slack accessibility](https://slack.engineering/automated-accessibility-testing-at-slack/) |
| PlanetScale | 自身工程文章称 Rails 测试套件在 Buildkite 的 64-core machine 上并行运行。 | 2022-01-18 | A | 否；历史案例。 | historical/uncertain | [PlanetScale Engineering](https://planetscale.com/blog/how-our-rails-test-suite-runs-in-1-minute-on-buildkite) |
| Intercom | 厂商案例称其先在自有 EC2 上以 Buildkite 编排 build，之后将全部 build 迁入 Buildkite。 | 未标注；案例含 2016 客户起始信息 | B | 否；页面未给可核验的近期采用方更新。 | historical/uncertain | [Buildkite 案例](https://buildkite.com/resources/case-studies/intercom/) |

## 重叠采用核查

检索以 Dagger 的十家具名采用方为起点，组合检索 `公司名 + Dagger + Buildkite`，同时排除：

- 仅说明“可在 Buildkite Agent 上运行 Dagger”的通用文档/第三方教程；
- Dagger 自己的工程实验；
- Buildkite 或 Dagger 的竞品比较、职位要求、集成关系和 logo 页。

**结果：未检索到同一家公司同时拥有两项可独立回链的采用证据。** 这不是“市场中不存在重叠客户”的断言，而是本轮公开一手材料范围内的证据缺口。若该重叠关系会成为正式页面的关键主张，应向候选公司的公开仓库、工程博客或演讲材料补做逐家公司检索；在获得两条独立回链前，页面应保持该主张 `unverified`。

## 可用于下一页选题的谨慎判断

本轮证据支持把两者讲成**不同层的可组合能力**，但不支持拿某个公开客户作为“双栈客户”案例：

> **Buildkite 的公开证据更集中于大规模 CI 编排与自有算力；Dagger 的公开证据更集中于把构建、测试和部署的运行时写成容器化函数。二者技术上可组合，但截至 2026-08-01 未找到可以安全署名的共同客户证明。**

后续若做正式 PPT，应先为“Dagger + Buildkite 的层次关系”创建/补齐 Deep Dive，并将每个产品能力主张和客户案例单独回链；不要将本预研表的厂商案例数据或成果指标外推为行业结论。

---
title: Dagger 左半区三图来源与生成记录
as_of: 2026-08-03
status: generated-for-review
---

# Dagger 左半区三图来源与生成记录

## 图 1｜Monorepo CI Module 代码卡片

- 文件：`dagger-01-monorepo-module-code.svg` / `.png`
- 机制来源：[Dagger TypeScript SDK v0.21.7｜Concurrency](https://docs.dagger.io/0.21.7/extending/sdks/typescript/)、[Dagger API Internals](https://docs.dagger.io/reference/api/internals/)
- 生成说明：代码是用于串联三张图的高层示例，以 `@object()` main object 表达 Dagger Module，以 `@func() ci()` 暴露执行入口，并使用 Directory 和 `Promise.all` 语义；`build()` 与 `test()` 代表同一 Module 中封装的 Functions，不冒充官方原文，也不作为可直接运行的完整实现。

## 图 2｜Function 调用 DAG

- 文件：`dagger-02-function-dag.svg` / `.png`
- 映射：`moduleA → Build moduleA → Test moduleA` 与 `moduleB → Build moduleB → Test moduleB`，两条链汇聚至 `Promise.all`。
- 事实边界：模块内的顺序来自对象数据依赖；两条链的并发由代码中的 `Promise.all` 显式表达；Engine 对 Function 展开的 core API 请求进行 DAG 求值，不宣称自动推断任意 Function 并发。

## 图 3｜增量缓存路径

- 文件：`dagger-03-incremental-cache.svg` / `.png`
- 来源：[Dagger Built-In Caching](https://docs.dagger.io/features/caching/)、[API Internals](https://docs.dagger.io/reference/api/internals/)
- 映射：完全复用图 2 的节点坐标和连线；moduleA 目录变化使其 Build / Test 重跑，moduleB 目录与相关操作输入不变使其 Build / Test 命中缓存。
- 事实边界：`CACHE HIT` 是 Dagger 内容寻址语义下的条件性示例，不是本次仓库运行的实测结果；只有当节点操作及全部输入保持相同时才能复用。

## 视觉与维护

- SVG 是可维护源文件，PNG 是本轮评审资产；
- 三图 PNG 均为 2400 × 680、白底、黑/灰/红三色系统；SVG viewBox 为 1200 × 340；
- 图 2、图 3 的文字、形状和连线可从 SVG 继续修改；
- 最终进入 PPTX 前仍需在完整左/右页面中检查实际显示尺寸和左右平衡。

## Dagger 左半区整合稿

- 文件：`dagger-left-region-integrated.svg` / `.png`
- SVG 画布：900 × 850；PNG：1800 × 1700；SHA-256：`afd2ce9c0a31efa5e75b4e292daa17c6749d1bbe09636ecbbd6f6a7ba41bcb06`。
- 阅读顺序：区域主张 → `01 用 Module 封装任务` → `02 Engine 生成执行图` → `03 只重跑变化部分`。
- 组合方式：标题、代码卡和分隔线由 SVG 原生绘制；图 2、图 3 复用已批准 PNG，保持相同节点关系与状态语义。
- 适配边界：按约 760—800 px 的页面左区宽度测算，区域高度约 718—756 px；当前只用于左区视觉评审，不证明完整左右页面平衡。

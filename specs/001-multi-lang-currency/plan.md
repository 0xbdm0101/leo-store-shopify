# Implementation Plan: 多语言与多货币支持

**Branch**: `001-multi-lang-currency` | **Date**: 2026-07-25 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `specs/001-multi-lang-currency/spec.md`

## Summary

为 Shopify 主题添加完整的多语言和多货币切换功能。核心是 3 个 snippet（locale-selector、geo-recommendation、seo-locale-meta）和 2 个 section settings block（用于在 header/footer 中嵌入选择器）。基于 Shopify 原生 Localization API，通过 localStorage 持久化用户偏好，提供渐进增强（JS 启用/禁用均可用）。

## Technical Context

**Language/Version**: Liquid (Shopify theme language), vanilla JavaScript (ES2020+)

**Primary Dependencies**: Shopify Localization API (`localization` global object), Shopify Markets

**Storage**: Client-side localStorage（语言/货币偏好）

**Testing**: Shopify theme editor 预览 + 浏览器手动测试（多语言/多货币切换）

**Target Platform**: Shopify Online Store 2.0，支持最新两个版本的 Chrome/Firefox/Safari/Edge

**Project Type**: Shopify Liquid 主题（Online Store 2.0）

**Performance Goals**: 页面加载时选择器渲染不增加 >50ms；货币切换后价格更新 <1s

**Constraints**: 必须兼容 JS 禁用场景（表单降级）；必须通过 WCAG 2.1 AA 检测

**Scale/Scope**: 默认支持 3 种语言 + 4 种货币，可扩展至 Shopify 平台上限（20 语言 + 130+ 货币）

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Theme Architecture First | ✅ PASS | 组件正确放置：snippets（逻辑复用）、blocks（可编辑设置）、sections（页面模块） |
| II. Schema-Driven Configuration | ✅ PASS | 选择器位置和样式通过 `{% schema %}` 暴露，无硬编码配置 |
| III. Translation-First | ✅ PASS | 所有用户文本使用 `{{ 'key' | t }}`，翻译键已在 locales/ 中定义 |
| IV. Component-Scoped Assets | ✅ PASS | 每个 snippet 使用 `{% stylesheet %}` / `{% javascript %}` 自包含 |
| V. LiquidDoc Documentation | ✅ PASS | 所有 snippet 包含 `{% doc %}` 头部，文档化参数和用法 |

**Post-Design Re-check**: ✅ 所有设计决策符合宪法要求。无违规项。

## Project Structure

### Documentation (this feature)

```text
specs/001-multi-lang-currency/
├── plan.md              # 本文件
├── research.md          # 技术调研（语言切换、货币切换、SEO、无障碍）
├── data-model.md        # 数据模型（偏好存储、检测状态、主题设置）
├── quickstart.md        # 验证指南（7 个验证场景）
├── contracts/           # 组件接口契约
│   ├── locale-selector.md   # 语言/货币选择器 snippet
│   ├── geo-recommendation.md # 地理位置推荐横幅 snippet
│   └── seo-meta.md          # hreflang + og:locale snippet
└── tasks.md             # 实现任务（/speckit-tasks 生成）
```

### Source Code (repository root)

```text
# 新增/修改的文件
snippets/
├── locale-selector.liquid       # [NEW] 语言/货币选择器核心组件
├── geo-recommendation.liquid    # [NEW] 地理位置推荐提示横幅
├── seo-locale-meta.liquid       # [NEW] hreflang + og:locale 标签
└── css-variables.liquid         # [MODIFIED] 添加选择器相关 CSS 变量

blocks/
└── locale-selector-block.liquid # [NEW] 可嵌入 section 的选择器 block

sections/
└── [existing sections...]       # [MODIFIED] 在 header/footer section 中支持 locale-selector block

layout/
└── theme.liquid                 # [MODIFIED] 引入 seo-locale-meta snippet 到 <head>

locales/
├── en.default.json              # [MODIFIED] 添加新翻译键
├── zh-CN.json                   # [NEW] 中文翻译
└── fr.json                      # [NEW] 法语翻译

assets/
└── locale-switcher.js           # [NEW] 选择器交互 JS（偏好存储、异步切换）
```

**Structure Decision**: Shopify 主题项目。核心复用逻辑在 snippets/，主题编辑器可配置项在 blocks/ 和 sections/，布局层修改在 layout/。

## Complexity Tracking

> 无宪法违规项，本节为空。

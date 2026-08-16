# 技术方案与规划：智能实时搜索抽屉 (Predictive Search Modal/Drawer)

**功能 ID**: 005-predictive-search-drawer  
**状态**: 已规划 (PLANNED)  
**需求规范**: [spec.md](./spec.md)  
**技术选型**: [research.md](./research.md)  
**数据模型**: [data-model.md](./data-model.md)  
**验证指南**: [quickstart.md](./quickstart.md)  

---

## 技术架构与设计原则

- **Shopify 原生接口**: 使用 Shopify 官方 Predictive Search API (`/search/suggest.json?q={query}&resources[type]=product,query&resources[limit]=6`)。
- **设计宪法 (Principle VI)**: 苹果极简美学毛玻璃抽屉，`border-radius: 16px/24px`，圆角胶囊标签，严格遵循 `max-width: 100%; overflow-x: hidden` 零横向滚动条保证。
- **动态多语言 (i18n)**: Liquid `search.*` 词典无缝覆盖 `zh-CN.json`、`en.default.json` 与 `fr.json`。

---

## 宪法合规检查

- [x] **Principle VI 苹果美学**: 毛玻璃背景卡片、胶囊热门标签、骨架屏微交互动画。
- [x] **主题架构解耦**: 采用独立 Section 组件 `sections/predictive-search-drawer.liquid`。
- [x] **零横向溢出保证**: 严格使用 `width: 100%` 与 `right: 0; left: 0;`。

---

## 阶段划分与设计产物

1. **组件文件**: [sections/predictive-search-drawer.liquid](file:///Users/leo/Downloads/web/leo-store-shopify/sections/predictive-search-drawer.liquid)
2. **Header 触发关联**: [sections/header.liquid](file:///Users/leo/Downloads/web/leo-store-shopify/sections/header.liquid) 搜索图标 `data-action="open-search-drawer"`。
3. **全局挂载**: [layout/theme.liquid](file:///Users/leo/Downloads/web/leo-store-shopify/layout/theme.liquid) `{% section 'predictive-search-drawer' %}`。
4. **多语言词典**: `search.drawer_title`, `search.placeholder`, `search.trending`, `search.no_results`, `search.view_all_results`。

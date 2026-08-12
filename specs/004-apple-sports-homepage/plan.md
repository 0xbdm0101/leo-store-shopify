# 实施方案设计：现代苹果风格运动商城首页 (Apple-Style Sports Homepage)

**日期**：2026-08-12 | **需求文档**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/spec.md)

---

## 方案概要

清除原有简陋的基础 Demo 代码，全面依据项目宪法 v1.1.0 (Apple-Style Sports Aesthetics) 打造高冲击力、极致质感的现代运动商城首页。采用原生 Vanilla CSS3 打造毛玻璃 (`backdrop-filter`)、大圆角 (`border-radius: 20px`) 与微交互悬浮效果，结合模块化 Section 架构实现 100% 可视化后管配置，并无缝集成 Quick View Modal 与 Cart Drawer。

---

## 技术上下文

- **开发语言/版本**：Liquid (Shopify Theme 2.0)、ES6 JavaScript、Vanilla CSS3
- **核心依赖**：Shopify Theme Editor Schema、现有的 `CartDrawer` & `QuickViewModal` 全域控制器
- **测试验证**：主题编辑器可视化配置与首页交互验证（参阅 quickstart.md）
- **约束条件**：零第三方 JS/CSS 库，全域国际化支持，组件作用域隔离

---

## 项目宪法合规检查 (Constitution Check v1.1.0)

- [x] **Theme Architecture First**：新首页四大 Section 收拢在 `sections/` 中，布局排版收拢在 `templates/index.json`。
- [x] **Schema-Driven Configuration**：四大 Section 均暴露完整的 `{% schema %}` 配置项与 `presets`，支持 Theme Editor 拖拽。
- [x] **Translation-First**：所有文本绑定 `{{ 'home.sports_hero.*' | t }}` 等 Filter，并在 `locales/en.default.json` 与 `locales/zh-CN.json` 补齐词条。
- [x] **Component-Scoped Assets**：组件内通过 `{% stylesheet %}` 隔离，全域通用 `.theme-spinner` 复用 `critical.css`。
- [x] **Premium Apple-Style Sports Aesthetics (VI)**：大圆角 (20px)、毛玻璃 (blur(16px))、悬浮上升 (-4px/-6px)、胶囊按钮 (999px) 100% 融入每个 Section。

---

## 项目文件结构

```text
sections/
├── sports-hero.liquid                # [NEW] 苹果巨幕沉浸式 Hero 动感 Banner Section
├── sports-categories.liquid          # [NEW] 毛玻璃运动分类金刚位 Section
├── sports-featured-collection.liquid # [NEW] 热销装备展架 Section (含 Quick View/Cart Drawer 联动)
└── sports-trust-bar.liquid           # [NEW] 极简运动服务承诺条 Section

templates/
└── index.json                        # [MODIFY] 全新首页 Section 排版编排

locales/
├── en.default.json                   # [MODIFY] 添加首页多语言字典
└── zh-CN.json                        # [MODIFY] 添加首页中文多语言字典
```

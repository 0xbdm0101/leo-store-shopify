# 技术与视觉设计决策：现代苹果风格运动商城首页 (Apple-Style Sports Homepage)

**功能规范**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/spec.md)

---

## 决策 1：删除旧 Skeleton Demo 代码与结构重构

- **决策**：清理 `sections/hero.liquid` 及 `templates/index.json` 中的遗留测试模版。
- **架构计划**：
  - 新建 `sections/sports-hero.liquid`：苹果巨幕沉浸式 Hero 动感 Banner。
  - 新建 `sections/sports-categories.liquid`：毛玻璃运动分类金刚位。
  - 新建 `sections/sports-featured-collection.liquid`：热销装备展架（内置 Quick View & Cart Drawer 联动）。
  - 新建 `sections/sports-trust-bar.liquid`：极简运动服务承诺条。
  - 重构 `templates/index.json`：组织全新的 Section 排版顺序与预设值。

## 决策 2：苹果美学 (Apple Design Language) CSS3 实现

- **决策**：100% 原生 Vanilla CSS3 打造毛玻璃与大圆角体验，绝不引入外部重型 CSS 框架。
- **具体规则**：
  - **毛玻璃面板**：`background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(16px); -webkit-backdrop-filter: blur(16px); border: 1px solid rgba(255, 255, 255, 0.3);`
  - **大圆角体系**：主 Hero 块/分类卡片 `border-radius: 20px` ~ `24px`，胶囊 Action 按钮 `border-radius: 999px`。
  - **CSS3 悬浮微交互**：`transition: transform 0.35s cubic-bezier(0.16, 1, 0.3, 1), box-shadow 0.35s ease;`

## 决策 3：与现有组件零成本无缝集成

- **决策**：`sections/sports-featured-collection.liquid` 内部的商品卡片直接触发 `data-action="quick-view"` 与全局 `ThemeCartUtils.submitAddToCart`。
- **优势**：完美保持加购防重复点击、按钮 Spinner 动画及侧边 Cart Drawer 平滑滑出体验。

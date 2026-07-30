# 实施方案设计：Ajax 侧边滑出购物车抽屉（含免运费进度条与加购推荐）

**日期**：2026-07-30 | **需求文档**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/spec.md)

---

## 方案概要

在 `sections/cart-drawer.liquid` 中实现一个响应式、支持移动端的 Ajax 侧边滑出购物车抽屉。采用 Shopify Section Rendering API 与 Cart Ajax API 结合的方式，实现零刷新的商品数量增减与删除、服务端与客户端双重计算的动态免运费进度条，以及 1-Click 快速加购推荐卡片。

---

## 技术上下文

- **开发语言/版本**：Liquid (Shopify Theme 2.0)、ES6 JavaScript、Vanilla CSS
- **核心依赖**：Shopify Cart Ajax API (`/cart.js`, `/cart/change.js`, `/cart/add.js`)、Shopify Section Rendering API
- **数据存储**：Shopify Cart 会话状态
- **测试验证**：主题编辑器实时预览 + 手动浏览器验证（参阅 quickstart.md）
- **目标平台**：所有主流现代桌面端与移动端浏览器
- **项目类型**：Shopify Liquid 主题 Section 与 Snippets
- **性能目标**：抽屉滑出延迟 <150ms，Ajax 变更重新渲染 <400ms
- **约束条件**：零全局 JS/CSS 依赖，100% 支持 `locales/en.default.json` 国际化，组件级作用域隔离

---

## 项目宪法合规检查 (Constitution Check)

- [x] **Theme Architecture First**：主逻辑收拢在 `sections/cart-drawer.liquid`，片段拆分为 `snippets/cart-drawer-item.liquid` 与 `snippets/cart-drawer-upsell.liquid`。
- [x] **Schema-Driven Configuration**：免运费门槛 (`free_shipping_threshold`)、推荐集合 (`upsell_collection`) 及颜色配置全部暴露在 `{% schema %}` 中。
- [x] **Translation-First**：所有展示文本统一使用 `{{ 'cart.drawer.*' | t }}` 过滤器，字典建在 `locales/en.default.json`。
- [x] **Component-Scoped Assets**：CSS 与 JS 代码内嵌在 `sections/cart-drawer.liquid` 的 `{% stylesheet %}` 和 `{% javascript %}` 标签中。
- [x] **LiquidDoc Documentation**：所有新增 snippet 文件顶部均包含标准的 `{% doc %}` 注释说明。

---

## 项目文件结构

```text
sections/
└── cart-drawer.liquid              # 购物车抽屉主模块（含 Schema、CSS、JS）

snippets/
├── cart-drawer-item.liquid         # 购物车商品行 Liquid 片段
├── cart-drawer-shipping-bar.liquid # 免运费进度条 Liquid 片段
└── cart-drawer-upsell.liquid       # 推荐商品卡片 Liquid 片段

locales/
└── en.default.json                 # 新增 cart.drawer 国际化字典
```

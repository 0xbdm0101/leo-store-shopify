# 实施方案设计：商品快速预览弹窗 (Quick View Modal)

**日期**：2026-08-06 | **需求文档**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/spec.md)

---

## 方案概要

在 `sections/quick-view-modal.liquid` 中实现一个轻量级、响应式、无屏闪的商品快速预览 Modal 视窗。利用 Shopify Section Rendering API (`/products/<handle>?section_id=quick-view-modal`) 异步拉取预渲染好的 HTML，结合嵌入式变体 JSON 做到变体选项切换瞬间更新主图、价格与库存状态，加购成功后自动联动打开已完成的侧边购物车抽屉（`CartDrawer`）。

---

## 技术上下文

- **开发语言/版本**：Liquid (Shopify Theme 2.0)、ES6 JavaScript、Vanilla CSS
- **核心依赖**：Shopify Section Rendering API、Shopify Cart Ajax API、现有的 `CartDrawer` 全局控制器
- **数据存储**：无（依赖 Shopify 后端商品及变体模型）
- **测试验证**：主题编辑器预览与列表页交互验证（参阅 quickstart.md）
- **目标平台**：所有主流现代桌面端与移动端浏览器
- **项目类型**：Shopify Liquid 主题 Section、Snippet 及商品列表页模版修改
- **性能目标**：弹窗响应加载时间 <250ms，变体选项切换响应时间 <50ms
- **约束条件**：零第三方 JS 库依赖，100% translatable 多语言支持，组件级作用域隔离

---

## 项目宪法合规检查 (Constitution Check)

- [x] **Theme Architecture First**：主逻辑收拢在 `sections/quick-view-modal.liquid`，商品列表页卡片触发器统一加在 `sections/collection.liquid`。
- [x] **Schema-Driven Configuration**：在 Schema 中暴露基础弹窗动画与按钮配置。
- [x] **Translation-First**：所有文本使用 `{{ 'products.product.quick_view' | t }}` 等国际化过滤器，在 `locales/en.default.json` 维护词典。
- [x] **Component-Scoped Assets**：CSS 样式与 JS 逻辑使用 `{% stylesheet %}` / `{% javascript %}` 在组件内部隔离。
- [x] **LiquidDoc Documentation**：涉及的代码片段包含标准的 `{% doc %}` 注释说明。

---

## 项目文件结构

```text
sections/
├── quick-view-modal.liquid          # 快速预览弹窗主模块（含模板、CSS、JS）
└── collection.liquid                # 修改：在商品卡片上添加 Quick View 触发按钮

snippets/
└── product-card.liquid             # 修改或适配：商品卡片组件结构

locales/
└── en.default.json                 # 新增 quick_view 国际化字典词条
```

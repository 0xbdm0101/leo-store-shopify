# 技术调研与决策：商品快速预览弹窗 (Quick View Modal)

**功能规范**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/spec.md)

---

## 决策 1：弹窗视图加载机制

- **决策**：使用 Shopify **Section Rendering API** (`/products/<handle>?section_id=quick-view-modal`)。
- **原理与优势**：
  - 点击列表页的 Quick View 按钮时，异步拉取该商品在 `sections/quick-view-modal.liquid` 中渲染好的 HTML 片段。
  - 保留 100% 服务端 Liquid 逻辑（价格格式化、国际化标签 `t` 过滤、折扣信息），无需在客户端 JS 里手动拼写复杂的商品 DOM 字符串。

## 决策 2：变体选项与价格/图片联动机制

- **决策**：在 `sections/quick-view-modal.liquid` 内部将商品的 `product.variants` 序列化为轻量级 JSON 嵌入在页面中：
  `<script type="application/json" data-quick-view-variants>{{ product.variants | json }}</script>`。
- **原理与优势**：
  - 当购物者在弹窗里点击切换颜色或尺寸单选框/下拉框时，客户端 JS 瞬间匹配选中的变体对象。
  - 无需额外发网络请求，在 10ms 内直接更新变体主图 (`variant.featured_image`)、当前售价 (`variant.price`)、原价 (`variant.compare_at_price`) 及库存状态（有货 / 售罄 `available`）。

## 决策 3：与 Cart Drawer 购物车抽屉联动机制

- **决策**：在弹窗的加购表单提交时，使用全域捕获事件调用现有 `CartDrawer` 控制器或发送标准 Ajax `/cart/add.js` 并携带 `sections: 'cart-drawer'` 参数。
- **原理与优势**：
  - 加购成功后自动关闭 Quick View Modal，并瞬间平滑滑出 `CartDrawer`。
  - 实现从浏览列表 ➡️ 快速预览 ➡️ 变体选择 ➡️ 1-Click 加购 ➡️ 购物车抽屉的全局零刷新闭环。

## 决策 4：组件化封装与样式隔离

- **决策**：将弹窗 UI、CSS 动画及 JS 逻辑封装在自包含模块 `sections/quick-view-modal.liquid` 中，使用 `{% stylesheet %}` 和 `{% javascript %}`。
- **原理与优势**：
  - 符合宪法原则 I (Theme Architecture First) 与原则 IV (Component-Scoped Assets)。
  - 在 `sections/collection.liquid` 的商品卡片上增加 `data-action="quick-view"` 与 `data-handle="{{ product.handle }}"` 触发器。

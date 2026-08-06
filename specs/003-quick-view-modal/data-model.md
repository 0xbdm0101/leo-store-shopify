# 数据模型：商品快速预览弹窗 (Quick View Modal)

**功能规范**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/spec.md)

---

## 实体模型

### 1. QuickViewModalState (弹窗控制器状态)

| 属性名 | 类型 | 说明 |
| :--- | :--- | :--- |
| `isOpen` | Boolean | 弹窗是否展开 (`aria-hidden="false"`) |
| `currentHandle` | String | 当前加载商品的 Shopify Handle |
| `isLoading` | Boolean | 异步请求 Section Rendering API 的加载状态 |

### 2. QuickViewProduct (商品与变体模型)

| 属性名 | 类型 | 说明 |
| :--- | :--- | :--- |
| `id` | Number | 商品 Product ID |
| `title` | String | 商品标题 |
| `handle` | String | 商品唯一 Handle |
| `options` | Array<ProductOption> | 商品选项列表（如 ["Color", "Size"]） |
| `variants` | Array<ProductVariant> | 商品包含的所有变体数组 |
| `selectedVariant` | ProductVariant | 当前选中的变体对象 |

### 3. ProductVariant (变体数据结构)

| 属性名 | 类型 | 说明 |
| :--- | :--- | :--- |
| `id` | Number | 变体 Variant ID |
| `title` | String | 变体组合名称（如 "Red / L"） |
| `price` | Number | 变体价格 (单位：分) |
| `compareAtPrice` | Number | 变体划线原价 (单位：分，可选) |
| `available` | Boolean | 是否有货 (`true` = 有货，`false` = 售罄) |
| `featuredImage` | Object | 变体对应专属图片对象 (URL, alt) |
| `options` | Array<String> | 变体选项值组合（如 ["Red", "L"]） |

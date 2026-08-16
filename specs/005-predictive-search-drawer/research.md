# 技术决策与调研：005-predictive-search-drawer

## 决策 1：搜索 API Endpoint 选型
- **最终选择**: Shopify 原生 Predictive Search API (`/search/suggest.json`)
- **理由**: 原生内置于所有 Shopify 店铺，无需依赖任何付费第三方 App；原生返回精简 JSON 数据（含商品缩略图、标题、品牌、价格与分类词），平均响应速度在 100ms 以内。
- **备选方案**: `/search?view=ajax` 自定义模板（传输完整 HTML，开销较大）。

## 决策 2：前端性能与防抖 (Debounce) 策略
- **最终选择**: 300ms JavaScript 客户端防抖。
- **理由**: 避免用户快速连击输入时发起无效的 HTTP 网络请求，既保护店铺 API 频次，又对用户体验几乎无感知延时。

## 决策 3：热门搜索词预设机制
- **最终选择**: 在 `predictive-search-drawer.liquid` Schema 中预设可编辑选项，并结合 Liquid `t` 滤镜渲染中文/英文预设标签。
- **理由**: 商家可以在主题编辑器中自定义热门搜索词，默认展示 `🏸 羽球拍`、`👟 运动鞋`、`🎽 比赛服`、`🛡️ 护具`。

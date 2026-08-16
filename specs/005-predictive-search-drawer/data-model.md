# 数据模型与接口定义：005-predictive-search-drawer

## 预测搜索 API 返回结构 (PredictiveSearchResponse)

```json
{
  "resources": {
    "results": {
      "products": [
        {
          "id": 123456789,
          "title": "专业羽球拍 Pro Feather 9000",
          "url": "/products/pro-feather-racket-9000",
          "image": "https://cdn.shopify.com/...",
          "vendor": "YONEX",
          "price": "189.00",
          "compare_at_price": "229.00",
          "available": true
        }
      ],
      "queries": [
        {
          "text": "羽毛球拍",
          "url": "/search?q=羽毛球拍"
        }
      ]
    }
  }
}
```

## 前端组件状态定义 (SearchDrawerState)
- `isOpen`: boolean (抽屉显隐状态)
- `query`: string (当前输入的搜索关键词)
- `isLoading`: boolean (骨架屏加载状态)
- `results`: PredictiveSearchResponse (实时匹配到的商品与分类列表)
- `trendingTags`: Array<{ label: string, query: string }> (热门搜索词标签)

# Research: 多语言与多货币支持

**Feature**: 001-multi-lang-currency
**Date**: 2026-07-25

## 研究成果

### R1: 语言切换机制

**Decision**: 使用 Shopify 内置 `localization` 对象 + 语言 locale URL 路径切换

**Rationale**: Shopify Online Store 2.0 原生支持多语言发布。主题通过 `{{ localization.language }}`
获取当前语言，通过 `{{ localization.available_languages }}` 获取可用语言列表。语言切换通过
修改 URL 中的 locale 前缀实现（如 `/zh-CN/products/...`）。

**Alternatives considered**:
- 客户端 JS 翻译（i18next）：与 Shopify 翻译系统冲突，维护两套翻译文件
- 自定义查询参数 (?lang=zh)：不符合 Shopify 的 URL 约定，SEO 不友好

**Key Liquid objects**:
- `{{ localization.language }}` — 当前语言对象（含 `iso_code`、`name`、`endonym_name`）
- `{{ localization.available_languages }}` — 可用语言列表
- `{{ request.locale }}` — 当前 locale 信息

---

### R2: 货币切换机制

**Decision**: 使用 Shopify Markets 原生货币选择 + JavaScript 更新购物车货币

**Rationale**: Shopify Markets 已处理汇率转换和价格显示。主题通过
`{{ localization.country.currency }}` 获取当前货币，通过 `{{ localization.available_countries }}`
获取支持的国家/货币组合。货币切换通过表单提交或 JavaScript fetch 到 `/cart/update.js`
更新 `currency` 参数。

**Alternatives considered**:
- 纯前端货币转换：汇率不同步，无法更新结账价格
- 每个货币独立 store：维护成本过高

**Key Liquid objects**:
- `{{ localization.country.currency.iso_code }}` — 当前货币代码
- `{{ localization.country.currency.symbol }}` — 货币符号
- `{{ localization.available_countries }}` — 可选国家/货币列表

**Cart update endpoint**: `POST /cart/update.js` with `{ currency: "EUR" }`

---

### R3: 地理位置检测

**Decision**: 使用 Shopify 的 `localization.country` 自动检测 + 用户手动覆盖

**Rationale**: Shopify 自动通过 IP 检测用户国家并设置 `localization.country`。
主题读取该值作为推荐，但允许用户通过选择器覆盖。首次访问时对比检测值与偏好值，
若不同则显示推荐提示。

**Implementation note**: Shopify 的 `localization` 对象已包含自动检测的国家信息。
不需要第三方 IP 检测服务。

**Key data flow**:
1. 用户访问 → Shopify 自动设置 `localization.country`（基于 IP）
2. Theme 读取检测值 → 对比 localStorage 中保存的偏好
3. 若无偏好 → 使用检测值 + 显示推荐横幅
4. 若有偏好 → 使用偏好值，忽略检测值

---

### R4: 偏好持久化

**Decision**: JavaScript localStorage 存储语言/货币偏好，30 天过期

**Rationale**: localStorage 比 cookie 更适合客户端偏好存储（更大容量 5MB、不随 HTTP 请求发送）。
设置带时间戳的过期机制。JavaScript 负责读写；Liquid 通过 data-attributes 传递初始值。

**Alternatives considered**:
- Cookie：每次请求都发送，增加带宽；Liquid 可读取但需要服务端设置
- Session storage：关闭浏览器即丢失
- Shopify Customer Metafields：需要登录，访客无法使用

**Storage schema**:
```json
{
  "locale_preference": {
    "language": "fr",
    "currency": "EUR",
    "set_at": "2026-07-25T10:00:00Z"
  }
}
```

---

### R5: SEO 元数据（hreflang + og:locale）

**Decision**: 在 `<head>` 中通过 Liquid 动态生成 hreflang 和 og 标签

**Rationale**: Liquid 在服务端渲染时即可生成完整的 SEO 标签，搜索引擎爬虫无需执行 JS。
使用 `{{ localization.available_languages }}` 遍历所有语言版本生成 hreflang 标签。

**Implementation pattern**:
```liquid
{% for language in localization.available_languages %}
  <link rel="alternate" hreflang="{{ language.iso_code }}" href="{{ canonical_url | replace: localization.language.iso_code, language.iso_code }}">
{% endfor %}
<link rel="alternate" hreflang="x-default" href="{{ canonical_url }}">
<meta property="og:locale" content="{{ localization.language.iso_code }}">
{% for language in localization.available_languages %}
  {% if language.iso_code != localization.language.iso_code %}
    <meta property="og:locale:alternate" content="{{ language.iso_code }}">
  {% endif %}
{% endfor %}
```

---

### R6: 无障碍选择器

**Decision**: 使用原生 `<select>` 元素 + ARIA 标签，保证键盘和屏幕阅读器支持

**Rationale**: 原生 `<select>` 自带键盘导航和屏幕阅读器支持，不需要自定义 JS 实现的 ARIA 角色管理。
样式通过 CSS custom properties 控制，焦点指示器通过 `:focus-visible` 实现。

**Key ARIA attributes**:
- `<label>` 关联 `<select>` 提供无障碍名称
- `aria-label` 用于无可见 label 的场景
- 切换后使用 `aria-live="polite"` 区域公告状态变更

**WCAG 2.1 AA 检查要点**:
- 颜色对比度 ≥ 4.5:1（文字）和 ≥ 3:1（边框/图标）
- 焦点指示器可见（`:focus-visible` 轮廓）
- 200% 缩放不截断内容
- 屏幕阅读器正确播报选项和选择结果

---

### R7: JavaScript 禁用降级

**Decision**: 使用 `<form>` 提交方式作为无 JS 降级方案

**Rationale**: 当 JS 禁用时，语言/货币选择器包裹在 `<form>` 中，
选择后通过页面导航（GET/POST）实现切换。JS 启用时则拦截表单提交，
使用 fetch API 实现无刷新切换。

**Progressive enhancement**:
- 基础层（HTML）：`<form>` + `<select>` + `<noscript>` 提交按钮
- 增强层（JS）：拦截提交事件，fetch API 异步更新，平滑过渡

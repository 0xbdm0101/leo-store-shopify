# Contract: SEO Meta Snippet

**Type**: Snippet (`snippets/seo-locale-meta.liquid`)

## Purpose

在 `<head>` 中输出 hreflang 标签和 Open Graph locale 标签，确保搜索引擎和社交平台正确索引多语言页面。

## Parameters

None — 直接读取 Shopify `localization` 全局对象。

## Rendering Contract

### Output Location

必须在 `layout/theme.liquid` 的 `<head>` 标签中调用，位于其他 meta 标签之后。

### HTML Output

```html
<!-- hreflang tags for search engines -->
<link rel="alternate" hreflang="{language.iso_code}" href="{canonical_url_with_locale}">
<!-- ... 每个可用语言一个 ... -->
<link rel="alternate" hreflang="x-default" href="{default_canonical_url}">

<!-- Open Graph locale tags for social media -->
<meta property="og:locale" content="{current_locale}">
<meta property="og:locale:alternate" content="{alternate_locale}">
<!-- ... 每个其他可用语言一个 ... -->
```

### Edge Cases

- 如果只有一种语言：不输出 hreflang 标签（单语言站点不需要）
- 如果语言代码为空或无效：使用 `x-default` 回退
- `canonical_url` 替换 locale 段失败时：不修改 URL 直接使用

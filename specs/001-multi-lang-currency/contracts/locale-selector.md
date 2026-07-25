# Contract: Locale Selector Snippet

**Type**: Snippet (`snippets/locale-selector.liquid`)

## Purpose

渲染语言和货币选择器。统一处理语言/货币切换逻辑、无障碍标记、JS 交互和降级方案。

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `type` | string | Yes | - | `"language"` 或 `"currency"` |
| `position` | string | No | `"footer"` | 选择器所在位置，影响样式：`"header"` / `"footer"` |
| `style` | string | No | `"dropdown"` | 显示样式：`"dropdown"` / `"inline"` |

## Rendering Contract

### HTML Structure

```html
<div class="locale-selector locale-selector--{type} locale-selector--{style} locale-selector--{position}">
  <form method="post" action="/cart/update.js" class="locale-selector__form" data-locale-form>
    <label for="locale-select-{type}" class="visually-hidden">
      {translated_label}
    </label>
    <select
      id="locale-select-{type}"
      name="{type}_code"
      class="locale-selector__select"
      data-locale-select="{type}"
      aria-describedby="locale-select-status-{type}"
    >
      {options}
    </select>
    <noscript>
      <button type="submit">{submit_label}</button>
    </noscript>
    <div id="locale-select-status-{type}" class="visually-hidden" aria-live="polite" role="status"></div>
  </form>
</div>
```

### CSS Variables

| Variable | Description |
|----------|-------------|
| `--locale-select-font-size` | 字体大小 |
| `--locale-select-padding` | 内边距 |
| `--locale-select-border-color` | 边框颜色 |
| `--locale-select-focus-ring` | 焦点指示器颜色 |

### JavaScript Events

| Event | Target | Description |
|-------|--------|-------------|
| `change` | `[data-locale-select]` | 触发语言/货币切换 |
| `locale:switched` | `document` | 自定义事件，detail: `{ type, value }` |

## Translation Keys

| Key | Description |
|-----|-------------|
| `general.locale.language_label` | "Language" label |
| `general.locale.currency_label` | "Currency" label |
| `general.locale.switch_language` | "Switch language to {name}" |
| `general.locale.switch_currency` | "Switch currency to {name} ({code})" |
| `general.locale.language_changed` | "Language changed to {name}" |
| `general.locale.currency_changed` | "Currency changed to {name}" |

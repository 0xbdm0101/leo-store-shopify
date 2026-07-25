# Data Model: 多语言与多货币支持

**Feature**: 001-multi-lang-currency
**Date**: 2026-07-25

## Entities

### Language Preference

用户选择的界面语言，存储在客户端 localStorage。

| Field | Type | Description | Validation |
|-------|------|-------------|------------|
| `language` | string | ISO 语言代码（如 `en`、`zh-CN`、`fr`） | 必须在 `localization.available_languages` 中 |
| `set_at` | ISO 8601 datetime | 设置时间戳 | 用于过期判断（30 天） |

### Currency Preference

用户选择的显示货币，存储在客户端 localStorage。

| Field | Type | Description | Validation |
|-------|------|-------------|------------|
| `currency` | string | ISO 4217 货币代码（如 `USD`、`EUR`、`CNY`） | 必须在 `localization.available_countries` 中 |
| `set_at` | ISO 8601 datetime | 设置时间戳 | 用于过期判断（30 天） |

### Combined Preference (Storage Schema)

```json
{
  "shop_locale_prefs": {
    "language": "zh-CN",
    "currency": "CNY",
    "set_at": "2026-07-25T10:00:00Z"
  }
}
```

### Geolocation Recommendation

Shopify 平台通过 IP 自动检测的国家/语言/货币信息。

| Source | Field | Description |
|--------|-------|-------------|
| `localization.country.iso_code` | `detected_country` | 检测到的国家代码 |
| `localization.country.currency.iso_code` | `detected_currency` | 检测到的货币 |
| `localization.language.iso_code` | `detected_language` | 检测到的语言 |

**State transitions**:

```
[首次访问] → 无 localStorage 偏好
  → 读取 localization.country (IP 检测)
  → 自动设置语言/货币 = 检测值
  → 显示推荐横幅 (dismissed = false)
  → 用户关闭横幅 (dismissed = true) 或手动切换

[回访] → 有 localStorage 偏好
  → 对比偏好 vs 检测值
  → 若一致：不显示推荐
  → 若不一致且未过期：使用偏好，不显示推荐
  → 若已过期（>30 天）：使用检测值，重新显示推荐
```

### Available Locale (Shopify Platform)

店铺在 Shopify 后台已启用的语言和国家/货币配置，由平台提供。

| Source | Description |
|--------|-------------|
| `localization.available_languages` | 店铺启用的语言列表 |
| `localization.available_countries` | 店铺启用的国家/货币列表 |

### Theme Settings (Schema)

商家在主题编辑器中配置的选择器行为和样式。

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `language_selector_position` | select | `footer` | 语言选择器位置：`header` / `footer` / `both` |
| `currency_selector_position` | select | `footer` | 货币选择器位置：`header` / `footer` / `both` |
| `selector_style` | select | `dropdown` | 选择器样式：`dropdown` / `inline_buttons` |

## Relationships

```
Shopify Platform
├── localization.available_languages ──→ Language Preference.language (validation source)
├── localization.available_countries ──→ Currency Preference.currency (validation source)
└── localization.country ──→ Geolocation Recommendation (auto-detection source)

Theme Settings (Schema)
└── 控制 Language Selector UI + Currency Selector UI

Client Storage (localStorage)
└── shop_locale_prefs { language, currency, set_at }
```

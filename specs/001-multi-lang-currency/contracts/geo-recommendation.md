# Contract: Geolocation Recommendation Snippet

**Type**: Snippet (`snippets/geo-recommendation.liquid`)

## Purpose

当检测到的地理位置与用户已保存偏好不同时，显示非侵入式推荐横幅。

## Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `detected_language` | string | Yes | - | IP 检测到的语言代码 |
| `detected_currency` | string | Yes | - | IP 检测到的货币代码 |
| `current_language` | string | Yes | - | 当前生效的语言代码 |
| `current_currency` | string | Yes | - | 当前生效的货币代码 |

## Rendering Contract

### HTML Structure

```html
<aside class="geo-recommendation" role="region" aria-label="{translated_title}" data-geo-recommendation>
  <div class="geo-recommendation__content">
    <p class="geo-recommendation__message">
      {recommendation_text}
    </p>
    <div class="geo-recommendation__actions">
      <button type="button" class="geo-recommendation__accept" data-geo-accept>
        {accept_label}
      </button>
      <button type="button" class="geo-recommendation__dismiss" data-geo-dismiss aria-label="{dismiss_label}">
        {dismiss_icon}
      </button>
    </div>
  </div>
</aside>
```

### Display Logic

```
IF (no saved preference) AND (detected ≠ default)
  → SHOW banner with recommendation
ELSE IF (saved preference exists) AND (detected ≠ saved)
  → HIDE (尊重已保存偏好)
ELSE
  → HIDE
```

### JavaScript Events

| Event | Target | Description |
|-------|--------|-------------|
| `click` | `[data-geo-accept]` | 应用推荐语言/货币，保存偏好，关闭横幅 |
| `click` | `[data-geo-dismiss]` | 仅关闭横幅，不改变当前设置 |

## Translation Keys

| Key | Description |
|-----|-------------|
| `general.geo_recommendation.title` | "Location detected" |
| `general.geo_recommendation.message` | "We've detected you're in {country}. Switch to {language} / {currency}?" |
| `general.geo_recommendation.accept` | "Switch" |
| `general.geo_recommendation.dismiss` | "Dismiss" |

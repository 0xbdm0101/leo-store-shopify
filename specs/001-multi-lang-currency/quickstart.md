# Quickstart: 多语言与多货币支持

**Feature**: 001-multi-lang-currency
**Date**: 2026-07-25

## Prerequisites

1. Shopify 店铺已启用 **Shopify Markets**（Settings → Markets → 添加市场）
2. 店铺已发布至少 2 种语言（Settings → Languages → 添加语言）
3. 店铺已启用至少 2 种货币（Settings → Markets → 配置货币）
4. 本地运行 `shopify theme dev` 启动开发预览
5. `locales/en.default.json` 已包含本功能所需的所有翻译键

## Validation Scenarios

### VS-1: 语言切换（US1）

1. 访问店铺首页
2. 找到页面上的语言选择器（header 或 footer 区域）
3. 选择非默认语言（如 "Français"）
4. **验证**: 页面刷新后，所有 UI 文字变为法语，URL 包含 `/fr` 前缀
5. 再次切换回英语
6. **验证**: 页面刷新后恢复英语，URL 中 `/fr` 移除

### VS-2: 货币切换（US2）

1. 浏览任意产品列表页
2. 找到货币选择器
3. 选择非默认货币（如 "EUR (€)"）
4. **验证**: 所有价格刷新为欧元符号和对应数值
5. 将商品加入购物车
6. **验证**: 购物车中价格保持所选的 EUR

### VS-3: 地理位置推荐（US3）

1. 清除浏览器 localStorage 和 cookie
2. 使用 VPN 或 Shopify 预览工具模拟法国 IP 访问
3. **验证**: 页面顶部显示推荐横幅，语言/货币显示为检测到的法语/EUR
4. 点击 "Dismiss" 关闭横幅
5. **验证**: 横幅消失，当前语言/货币不变
6. 刷新页面
7. **验证**: 横幅不再显示（已在同一次会话中关闭）

### VS-4: 偏好持久化（US4）

1. 选择语言 "中文" 和货币 "CNY"
2. 关闭浏览器标签页
3. 重新打开店铺
4. **验证**: 语言自动显示中文，货币自动显示 CNY
5. 清除浏览器 localStorage
6. 刷新页面
7. **验证**: 恢复为店铺默认语言和货币

### VS-5: SEO 元数据（US5）

1. 查看页面源代码（右键 → 查看源代码）
2. **验证**: `<head>` 中包含 `<link rel="alternate" hreflang="...">` 标签
3. **验证**: 每个可用语言对应一个 hreflang 标签 + `x-default`
4. **验证**: 包含 `<meta property="og:locale" content="...">`
5. 使用 [hreflang testing tool](https://technicalseo.com/tools/hreflang/) 验证配置

### VS-6: 无障碍访问（US6）

1. 使用键盘 Tab 导航到语言选择器
2. **验证**: 可见焦点指示器出现在选择器上
3. 使用方向键选择不同语言
4. **验证**: 听到屏幕阅读器公告"已切换至：{语言名称}"
5. 将页面放大到 200%
6. **验证**: 选择器所有文本完整可读，无重叠

### VS-7: JS 禁用降级（Edge Case）

1. 在浏览器中禁用 JavaScript
2. 访问店铺
3. **验证**: 语言和货币选择器仍可见
4. 选择不同语言
5. **验证**: 选择器旁出现提交按钮，点击后页面刷新切换语言

## Commands

```bash
# 启动开发预览
shopify theme dev

# 检查翻译文件完整性
shopify theme check

# 使用主题检查 linter
npx @shopify/theme-check
```

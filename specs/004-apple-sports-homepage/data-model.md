# 数据模型与 Schema 规范：现代苹果风格运动商城首页

**功能规范**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/spec.md)

---

## 1. Section: sports-hero.liquid (Hero 巨幕 Banner)

| Setting ID | 类型 | 说明 | 默认值 |
| :--- | :--- | :--- | :--- |
| `badge_text` | text | 顶部毛玻璃胶囊标签文本 | "2026 PRO ATHLETIC COLLECTION" |
| `heading` | text | 动感主标题 | "超越极限 · 释放运动潜能" |
| `subheading` | textarea | 副标题说明 | "探索全新一代专业羽毛球装备与科技运动产品" |
| `button_label` | text | CTA 按钮文本 | "立即探索热销装备" |
| `button_link` | url | CTA 跳转链接 | "/collections/all" |
| `image` | image_picker | 背景运动大图 | (高清运动海报图片) |

## 2. Section: sports-categories.liquid (运动分类金刚位)

| Setting ID | 类型 | 说明 | 默认值 |
| :--- | :--- | :--- | :--- |
| `title` | text | 模块标题 | "按运动项目探索" |
| `subtitle` | text | 模块副标题 | "专业装备，精准匹配你的专属赛场" |

**Blocks (分类卡片，最多 4 个)**：
| Setting ID | 类型 | 说明 |
| :--- | :--- | :--- |
| `title` | text | 分类项目名称（如 "羽毛球 Badminton"） |
| `image` | image_picker | 分类卡片背景封面图 |
| `link` | url | 跳转的 Collection 链接 |

## 3. Section: sports-featured-collection.liquid (热销装备展架)

| Setting ID | 类型 | 说明 | 默认值 |
| :--- | :--- | :--- | :--- |
| `title` | text | 展架标题 | "极速赛场 · 热销装备" |
| `collection` | collection | 绑定的热销商品集合 | (选中的 Collection) |
| `products_to_show` | range | 展示商品数量 (4 ~ 12) | 8 |

## 4. Section: sports-trust-bar.liquid (服务承诺条)

| Setting ID | 类型 | 说明 |
| :--- | :--- | :--- |
| `show_border` | checkbox | 是否显示边框线 |

**Blocks (承诺卡片，4 项)**：
| Setting ID | 类型 | 说明 |
| :--- | :--- | :--- |
| `icon_emoji` | text | 图标 Emoji (如 "⚡", "🛡️", "🚚", "🔄") |
| `title` | text | 承诺标题 (如 "官方正品保证") |
| `description` | text | 承诺简述 (如 "100% 品牌授权 假一赔十") |

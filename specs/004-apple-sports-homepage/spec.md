# 需求规格说明书：重构现代苹果风格运动商城首页 (Apple-Style Sports Homepage)

**功能名称**：重构现代苹果风格运动商城首页  
**创建日期**：2026-08-12  
**状态**：草案  
**遵循宪法版本**：[v1.1.0 (含 VI. Premium Apple-Style Sports Aesthetics 准则)](file:///Users/leo/Downloads/web/leo-store-shopify/.specify/memory/constitution.md#L92)

---

## 业务背景与用户价值

旧版首页仅包含基础 Skeleton 架构的简单 Demo 代码，缺乏视觉吸引力与专业运动品牌质感。通过重构首页，融入**现代苹果设计风格（大圆角、毛玻璃高斯模糊、CSS3 微交互动画、极简留白）与充满张力的专业运动元素**，打造极具视觉震撼力与转化率的运动电商首页，极大提升品牌形象与购物者留存率。

---

## 核心用户故事 (User Stories)

### User Story 1 (P1 - 核心 MVP)：Apple 巨幕沉浸式 Hero 动感 Banner (Hero Section)

作为一个访问运动商城的购物者，我希望在进入首页的第一时间看到极具视觉冲击力的巨幕 Hero 区域，包含毛玻璃质感的胶囊标签与大圆角 Action 按钮，以便迅速感受品牌动感气场并开始探索热销商品。

**验收标准 (Acceptance Criteria)**：
- [x] 背景支持高清运动海报大图/视频展示，文字叠加平滑渐变蒙层。
- [x] 标签与按钮采用苹果风格毛玻璃 (`backdrop-filter: blur(16px)`) 与胶囊大圆角 (`border-radius: 999px`)。
- [x] 主按钮悬停时带有 CSS3 平滑缩放与发光反馈 (`transform: translateY(-2px)` / `box-shadow`)。
- [x] 商家可在 Shopify 后台 Theme Editor 中修改标语、副标题、图片及 CTA 按钮跳转链接。

### User Story 2 (P2 - 交互)：毛玻璃运动分类金刚位 (Category Grid Section)

作为一个购物者，我希望浏览按运动项目分类的网格（如羽毛球、跑步、篮球、健身），卡片具备悬浮升起与图片微放大效果，以便我快速找到感兴趣的运动品类。

**验收标准 (Acceptance Criteria)**：
- [x] 提供 4 宫格或多列响应式网格布局，卡片统一采用 `border-radius: 20px` 现代圆角。
- [x] 鼠标悬停卡片时，背景图片微放大 1.05 倍，卡片向上悬浮 6px 并呈现柔和阴影。
- [x] 商家可在主题编辑器中灵活更换每个分类的名称、封面图及对应的 Collection 链接。

### User Story 3 (P2 - 联动)：热销装备运动展架 (Featured Sports Collection Section)

作为一个购物者，我希望在首页直接查看热销运动装备，并且卡片上支持 **👁️ Quick View** 弹窗与 **1-Click 一键加购**，以便我零屏闪快速购物。

**验收标准 (Acceptance Criteria)**：
- [x] 渲染精选商品网格，商品卡片具备苹果风阴影与微光 hover 效果。
- [x] 完美集成已完成的 Quick View 快速预览弹窗与 Ajax Cart Drawer 购物车抽屉联动。
- [x] 加购按钮点击时触发全域按钮 Spinner Loading 动画与防连击保护。

### User Story 4 (P3 - 品牌)：极简运动服务承诺条 (Sports Trust Bar Section)

作为一个购物者，我希望能清晰看到官方正品保证、专业运动指导、极速发货与无忧退换等承诺，增加下单信任感。

**验收标准 (Acceptance Criteria)**：
- [x] 采用单排极简图标卡片网格展示 4 项服务承诺。
- [x] 响应式适配移动端，保持高对比度与清晰排版。

---

## 边界与约束

- 必须 100% 遵守项目宪法 v1.1.0 准则，所有组件写在 `sections/` 中，CSS 样式使用 `{% stylesheet %}` 隔离。
- 所有文字必须绑定 Liquid 国际化 Filter `{{ 'home.xxx' | t }}`，并在 `locales/en.default.json` 与 `locales/zh-CN.json` 补齐词条。
- 绝不引入任何庞大第三方 JS 库（如 jQuery、Swiper），使用 100% 原生 CSS3 动画与 ES6 JavaScript。

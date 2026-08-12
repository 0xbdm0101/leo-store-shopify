# 快速验证指南：现代苹果风格运动商城首页 (Apple-Style Sports Homepage)

**功能规范**：[spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/spec.md)

---

## 验证测试场景

### 场景 1：首页 Hero 巨幕与毛玻璃质感验证
1. 打开商店首页 (`/`)。
2. 观察首屏 Hero Banner。
   - **验证**：顶部胶囊标签呈半透明毛玻璃视觉 (`backdrop-filter: blur`)，主标题动感震撼，CTA 胶囊大圆角按钮排版清爽。
3. 鼠标悬停在 CTA 主按钮上。
   - **验证**：按钮向上轻微升起，带有柔和阴影与 CSS3 动画过渡。

### 场景 2：运动分类金刚位 Hover 微交互
1. 向下滚动至“按运动项目探索”区域。
2. 鼠标悬停在羽毛球或跑步等分类卡片上。
   - **验证**：
     - 卡片背景图片顺畅微放大 1.05 倍。
     - 卡片整体向上平滑悬浮 6px 并叠加微光阴影。
3. 点击任意分类卡片。
   - **验证**：顺畅跳转至对应的商品集合页面。

### 场景 3：热销装备展架加购与 Quick View 联动
1. 滚动至“热销装备”展架。
2. 悬停在任意装备卡片上，点击 **👁️ 快速预览** 按钮。
   - **验证**：顺畅弹出 Quick View Modal 视窗。
3. 在展架卡片或 Modal 视窗中点击加购。
   - **验证**：按钮出现 CSS 旋转 Spinner 动态 Loading 效果，加购成功后自动唤出侧边 Cart Drawer 购物车抽屉。

### 场景 4：Shopify 后台 Theme Editor 可视化配置验证
1. 打开主题编辑器 (`/admin/themes/<id>/editor`)。
2. 点击修改 Hero Section 的标语与图片。
   - **验证**：预览区域即时无刷新更新展示。

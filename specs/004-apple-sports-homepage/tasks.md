# 任务分解清单：现代苹果风格运动商城首页 (Apple-Style Sports Homepage)

**设计文档来源**：`specs/004-apple-sports-homepage/`

**前置依赖**：[plan.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/plan.md), [spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/spec.md), [research.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/research.md), [data-model.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/004-apple-sports-homepage/data-model.md)

## 格式说明：`[ID] [P?] [Story] 任务描述与文件路径`

- **[P]**：可并行执行（不同文件，无依赖关系）
- **[Story]**：所属用户故事 (`[US1]`, `[US2]`, `[US3]`, `[US4]`)

---

## Phase 1: 环境与多语言词条准备 (Setup)

**目标**：配置全站运动商城首页多语言字典

- [ ] T001 在 `locales/en.default.json` 中添加 `home.sports_hero.*`, `home.sports_categories.*`, `home.sports_featured.*`, `home.sports_trust.*` 字典
- [ ] T002 在 `locales/zh-CN.json` 中添加对应的中文翻译字典

---

## Phase 2: 基础准备与旧代码清理 (Foundational & Cleanup)

**目标**：清理旧 Skeleton Demo 首页结构，为新 Section 准备模板架构

- [ ] T003 [P] 整理 `templates/index.json` 清理遗留 Skeleton 测试模板结构

---

## Phase 3: 用户故事 1 - Apple 巨幕沉浸式 Hero 动感 Banner (优先级: P1) 🎯 MVP 核心

**目标**：构建具备视觉震撼力的 Hero 巨幕 Banner，融入毛玻璃质感胶囊标签、胶囊大圆角按钮与 CSS3 动感微交互。

**独立测试方法**：打开首页，观察 Hero 区域毛玻璃 `backdrop-filter: blur(16px)`、胶囊按钮 `border-radius: 999px` 与悬浮缩放效果。

### 用户故事 1 实现任务

- [ ] T004 [US1] 创建 `sections/sports-hero.liquid`，构建包含毛玻璃标签、标语、CTA 按钮及 Shopify 后台 `{% schema %}` 可视化配置项
- [ ] T005 [US1] 在 `sections/sports-hero.liquid` 的 `{% stylesheet %}` 中编写极简苹果风毛玻璃面板、胶囊大圆角与 CSS3 悬浮升起动画
- [ ] T006 [US1] 根据 `specs/004-apple-sports-homepage/quickstart.md` 的场景 1 验证 Hero 区域功能与视觉效果

**检查点**：用户故事 1 MVP 核心完成并可独立测试。

---

## Phase 4: 用户故事 2 - 毛玻璃运动分类金刚位 (优先级: P2)

**目标**：构建运动分类网格（羽毛球、跑步、篮球、健身），具备 20px 现代圆角、Hover 时图片 1.05 倍微放大与卡片向上悬浮 6px 效果。

**独立测试方法**：悬停分类卡片，观察图片顺畅放大与卡片上浮阴影，点击顺畅跳转对应 Collection。

### 用户故事 2 实现任务

- [ ] T007 [P] [US2] 创建 `sections/sports-categories.liquid`，构建分类金刚位 HTML、CSS3 微交互样式及 Block 级 `{% schema %}` 预设
- [ ] T008 [US2] 根据 `specs/004-apple-sports-homepage/quickstart.md` 的场景 2 验证分类金刚位功能

---

## Phase 5: 用户故事 3 - 热销装备运动展架 (优先级: P2)

**目标**：构建热销装备展架，完美联动已完成的 **👁️ Quick View** 弹窗与 **1-Click 一键加购 Loading** 购物车抽屉。

**独立测试方法**：在首页热销展架上悬停卡片，点击 **👁️ Quick View** 弹出视窗；点击 **Add to Cart** 按钮触发 Spinner 动画并唤出侧边抽屉。

### 用户故事 3 实现任务

- [ ] T009 [P] [US3] 创建 `sections/sports-featured-collection.liquid`，构建商品网格卡片、绑定 `data-action="quick-view"` 与 `ThemeCartUtils.submitAddToCart`
- [ ] T010 [US3] 根据 `specs/004-apple-sports-homepage/quickstart.md` 的场景 3 验证展架加购与 Quick View 联动

---

## Phase 6: 用户故事 4 - 极简运动服务承诺条 (优先级: P3)

**目标**：展示官方正品保证、专业指导、极速发货与无忧退换 4 项高质感承诺条。

**独立测试方法**：在首页底部查看 4 项图标服务承诺卡片。

### 用户故事 4 实现任务

- [ ] T011 [P] [US4] 创建 `sections/sports-trust-bar.liquid`，构建服务承诺条 HTML、CSS 样式与 Schema 配置
- [ ] T012 [US4] 更新 `templates/index.json`，编排四大 Section 的呈现顺序与默认预设数据

---

## Phase 7: 细节优化与无障碍响应式增强 (Polish)

**目标**：移动端响应式优化与端到端测试

- [ ] T013 [P] 优化移动端（手机与平板）小屏视口下的响应式排版与触摸体验
- [ ] T014 运行完整的端到端 quickstart 验证与代码清理

---

## 执行顺序与依赖关系

1. **Phase 1 & 2 (Setup & Cleanup)** -> 基础设施与清理。
2. **Phase 3 (US1 - MVP Hero Banner)** -> **Phase 4 (US2 分类)** -> **Phase 5 (US3 展架)** -> **Phase 6 (US4 承诺条)**：按优先级依次推进。
3. **Phase 7 (Polish)**：在所有 Section 完成后统一排版测试。

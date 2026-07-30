# 任务分解清单：Ajax 侧边滑出购物车抽屉（含免运费进度条与加购推荐）

**设计文档来源**：`specs/002-cart-drawer/`

**前置依赖**：[plan.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/plan.md), [spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/spec.md), [research.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/research.md), [data-model.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/data-model.md)

## 格式说明：`[ID] [P?] [Story] 任务描述与文件路径`

- **[P]**：可并行执行（不同文件，无依赖关系）
- **[Story]**：所属用户故事 (`[US1]`, `[US2]`, `[US3]`)

---

## Phase 1: 环境与基础设施准备 (Setup)

**目标**：建立静态资源结构与国际化翻译词条

- [x] T001 在 `locales/en.default.json` 中添加 `cart.drawer` 国际化翻译词典
- [x] T002 [P] 创建模块骨架文件 `sections/cart-drawer.liquid` 并初始化 `{% schema %}` 配置项
- [x] T003 [P] 创建代码片段占位文件 `snippets/cart-drawer-item.liquid`

---

## Phase 2: 基础组件与容器 (Foundational)

**目标**：构建购物车抽屉外层容器、背景蒙层 DOM 与基础 JavaScript 控制器

- [x] T004 在 `sections/cart-drawer.liquid` 中构建抽屉主体布局与背景蒙层 HTML
- [x] T005 [P] 在 `sections/cart-drawer.liquid` 的 `{% stylesheet %}` 中编写抽屉侧滑动画、固定定位蒙层与响应式样式
- [x] T006 在 `sections/cart-drawer.liquid` 的 `{% javascript %}` 中实现 `CartDrawer` 控制器，处理 `/cart.js` 获取、展开/关闭状态与无障碍键盘监听 (`Escape` 键)

**检查点**：基础容器就绪，抽屉可流畅展开与关闭。

---

## Phase 3: 用户故事 1 - 侧边滑出 Ajax 购物车抽屉 (优先级: P1) 🎯 MVP 核心

**目标**：交付完整的 Ajax 购物车抽屉，支持商品列表渲染、异步修改数量/删除，以及实时更新小计金额与页头徽章。

**独立测试方法**：点击“加入购物车”或页头购物车图标，抽屉滑出显示商品。点击 `+`/`-` 或“删除”链接，单价与总额即时刷新，无需重载页面。

### 用户故事 1 实现任务

- [x] T007 [P] [US1] 在 `snippets/cart-drawer-item.liquid` 中构建包含 `{% doc %}` 注释的商品行 Liquid 渲染片段
- [x] T008 [US1] 在 `sections/cart-drawer.liquid` 中循环渲染商品行列表、小计金额与去结算按钮
- [x] T009 [US1] 在 `sections/cart-drawer.liquid` 的 `{% javascript %}` 中实现 Ajax 数量修改 (`/cart/change.js`) 与删除逻辑
- [x] T010 [US1] 在 `sections/cart-drawer.liquid` 中添加空购物车状态及“继续购物”按钮
- [x] T011 [US1] 在 `sections/cart-drawer.liquid` 中绑定全局“加入购物车”表单提交事件，实现加购时自动滑出抽屉
- [x] T012 [US1] 根据 `specs/002-cart-drawer/quickstart.md` 的场景 1 & 2 验证用户故事 1 的功能

**检查点**：用户故事 1 (MVP 核心) 完成并可独立测试。

---

## Phase 4: 用户故事 2 - 动态免运费进度条 (优先级: P2)

**目标**：展示动态免运费进度条与剩余包邮差额提示（如“再凑 $XX.XX 即可享受免运费！”）。

**独立测试方法**：在主题编辑器中设置免运费门槛（如 $100）。加购商品时进度条按比例填充，达到 $100 时变为 100% 绿色祝贺状态。

### 用户故事 2 实现任务

- [x] T013 [P] [US2] 创建包含 `{% doc %}` 注释的免运费进度条代码片段 `snippets/cart-drawer-shipping-bar.liquid`
- [x] T014 [US2] 在 `sections/cart-drawer.liquid` 的 `{% schema %}` 中添加 `free_shipping_threshold` (免运费门槛) 设置项
- [x] T015 [US2] 在 `sections/cart-drawer.liquid` 中实现 Ajax 变更后的 JS 动态进度条百分比与文案更新逻辑
- [x] T016 [US2] 根据 `specs/002-cart-drawer/quickstart.md` 的场景 3 验证用户故事 2 的功能

**检查点**：用户故事 1 与用户故事 2 联动正常。

---

## Phase 5: 用户故事 3 - 抽屉内加购/交叉推荐 (优先级: P3)

**目标**：在抽屉底部展示推荐商品卡片，支持 1-Click 一键加购并异步刷新抽屉。

**独立测试方法**：在主题编辑器中选择推荐集合。抽屉底部展示最多 3 款推荐商品（自动过滤已在购物车中的商品），点击“加购”后商品即时加入购物车。

### 用户故事 3 实现任务

- [x] T017 [P] [US3] 创建包含 `{% doc %}` 注释的推荐商品卡片代码片段 `snippets/cart-drawer-upsell.liquid`
- [x] T018 [US3] 在 `sections/cart-drawer.liquid` 的 `{% schema %}` 中添加 `upsell_collection` 集合选择设置项，并在抽屉底部渲染推荐卡片列表
- [x] T019 [US3] 在 `sections/cart-drawer.liquid` 中实现推荐卡片的 1-Click Ajax 加购 (`/cart/add.js`) 与抽屉刷新逻辑
- [x] T020 [US3] 根据 `specs/002-cart-drawer/quickstart.md` 的场景 4 验证用户故事 3 的功能

**检查点**：所有用户故事开发完成。

---

## Phase 6: 细节优化与无障碍增强 (Polish)

**目标**：无障碍体验优化、移动端响应式微调与端到端测试

- [x] T021 [P] 在 `sections/cart-drawer.liquid` 中完善 WCAG 2.1 AA 无障碍支持 (aria-label、焦点锁定)
- [x] T022 [P] 在 `sections/cart-drawer.liquid` 中优化移动端触摸屏体验与窄屏样式
- [x] T023 根据 `specs/002-cart-drawer/quickstart.md` 运行完整的端到端测试验证
- [x] T024 进行最终代码清理与 Liquid Schema 校验

---

## 执行顺序与依赖关系

1. **Phase 1 (Setup)** -> **Phase 2 (Foundational)**：前置基础，阻塞后续故事。
2. **Phase 3 (US1 - MVP)** -> **Phase 4 (US2)** -> **Phase 5 (US3)**：按优先级依次推进。
3. **Phase 6 (Polish)**：在所有功能故事完成后统一优化。

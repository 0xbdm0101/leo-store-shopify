# 任务分解清单：商品快速预览弹窗 (Quick View Modal)

**设计文档来源**：`specs/003-quick-view-modal/`

**前置依赖**：[plan.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/plan.md), [spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/spec.md), [research.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/research.md), [data-model.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/003-quick-view-modal/data-model.md)

## 格式说明：`[ID] [P?] [Story] 任务描述与文件路径`

- **[P]**：可并行执行（不同文件，无依赖关系）
- **[Story]**：所属用户故事 (`[US1]`, `[US2]`, `[US3]`)

---

## Phase 1: 环境与基础设施准备 (Setup)

**目标**：建立国际化翻译词条与模块骨架

- [ ] T001 在 `locales/en.default.json` 中添加 `products.product.quick_view` 及快速预览相关的国际化字典
- [ ] T002 [P] 创建模块骨架文件 `sections/quick-view-modal.liquid` 并初始化基本 `{% schema %}` 配置

---

## Phase 2: 基础组件与容器 (Foundational)

**目标**：构建 Quick View 弹窗外层容器、背景蒙层 HTML 与基础 JavaScript 控制器

- [ ] T003 在 `sections/quick-view-modal.liquid` 中构建 Modal 外层容器与背景蒙层 HTML 结构
- [ ] T004 [P] 在 `sections/quick-view-modal.liquid` 的 `{% stylesheet %}` 中编写弹窗淡入淡出动画、背景蒙层高斯模糊与响应式布局
- [ ] T005 在 `sections/quick-view-modal.liquid` 的 `{% javascript %}` 中实现 `QuickViewModal` 控制器，处理 Section Rendering API 异步拉取、展开/关闭状态与 `Escape` 键监听

**检查点**：基础容器就绪，弹窗能够接收商品 Handle 并渲染开闭。

---

## Phase 3: 用户故事 1 - 列表页 Quick View 悬浮触发与 Modal 弹窗渲染 (优先级: P1) 🎯 MVP 核心

**目标**：在商品列表页卡片上增加 Quick View 触发按钮，点击后通过 Section Rendering API 动态加载并弹出 Modal。

**独立测试方法**：在列表页悬停商品卡片，点击“Quick View”按钮，弹窗平滑载入显示商品内容。

### 用户故事 1 实现任务

- [ ] T006 [US1] 在 `sections/collection.liquid` 的商品卡片上添加带有 `data-action="quick-view"` 与 `data-handle` 的触发按钮
- [ ] T007 [US1] 在 `sections/quick-view-modal.liquid` 的 `{% javascript %}` 中绑定列表页触发按钮的全局点击事件，发起 `/products/<handle>?section_id=quick-view-modal` 请求
- [ ] T008 [US1] 根据 `specs/003-quick-view-modal/quickstart.md` 的场景 1 验证用户故事 1 的功能

**检查点**：用户故事 1 MVP 核心完成并可独立测试。

---

## Phase 4: 用户故事 2 - 缩略图切换与多变体选项联动 (优先级: P2)

**目标**：实现商品多图缩略图点击切换主图，以及变体单选框/下拉框切换时价格、主图、SKU 和可售状态（有货/售罄）的瞬间更新。

**独立测试方法**：在弹窗中点击缩略图大图切换；点击变体单选框，价格、主图和可售状态实时联动更新。

### 用户故事 2 实现任务

- [ ] T009 [P] [US2] 在 `sections/quick-view-modal.liquid` 中构建多图展示区（主图 + 缩略图列表），并绑定点击切换主图逻辑
- [ ] T010 [US2] 在 `sections/quick-view-modal.liquid` 内部嵌入变体 JSON 数据 (`<script type="application/json">`)
- [ ] T011 [US2] 在 `sections/quick-view-modal.liquid` 的 `{% javascript %}` 中实现变体选择器监听事件，实时匹配 JSON 数据并更新主图、售价、划线原价与加购按钮禁用/激活状态
- [ ] T012 [US2] 根据 `specs/003-quick-view-modal/quickstart.md` 的场景 2 验证用户故事 2 的功能

**检查点**：用户故事 1 与用户故事 2 联动正常。

---

## Phase 5: 用户故事 3 - 1-Click 一键加购与购物车抽屉 (Cart Drawer) 联动 (优先级: P3)

**目标**：在 Quick View 弹窗中点击“加入购物车”后，异步加购并自动滑出上一次做好的侧边 Cart Drawer 购物车抽屉；提供“查看完整详情”跳转链接。

**独立测试方法**：点击 Quick View 弹窗中的“加入购物车”，弹窗关闭，侧边 Cart Drawer 自动滑出展示最新加购商品。

### 用户故事 3 实现任务

- [ ] T013 [US3] 在 `sections/quick-view-modal.liquid` 中连接加购表单提交事件，加购成功后关闭弹窗并触发全局 `CartDrawer` 展开
- [ ] T014 [US3] 在 `sections/quick-view-modal.liquid` 底部添加指向独立商品详情页 (`/products/{{ product.handle }}`) 的“查看完整详情”链接
- [ ] T015 [US3] 根据 `specs/003-quick-view-modal/quickstart.md` 的场景 3 验证用户故事 3 的功能

**检查点**：所有用户故事开发完成。

---

## Phase 6: 细节优化与无障碍增强 (Polish)

**目标**：无障碍体验优化、移动端适配与端到端测试

- [ ] T016 [P] 在 `sections/quick-view-modal.liquid` 中添加无障碍键盘焦点锁定（Modal 打开时焦点保留在弹窗内部）
- [ ] T017 [P] 在 `sections/quick-view-modal.liquid` 中优化移动端小屏样式与触摸体验
- [ ] T018 运行完整的端到端 quickstart 验证与代码清理

---

## 执行顺序与依赖关系

1. **Phase 1 (Setup)** -> **Phase 2 (Foundational)**：前置基础设施，阻塞后续故事。
2. **Phase 3 (US1 - MVP)** -> **Phase 4 (US2)** -> **Phase 5 (US3)**：按优先级依次推进。
3. **Phase 6 (Polish)**：在所有功能故事完成后统一优化。

# 任务清单：智能实时搜索抽屉 (Predictive Search Modal/Drawer)

**功能 ID**: 005-predictive-search-drawer  
**状态**: 待执行 (READY)  
**需求规范**: [spec.md](./spec.md)  
**技术方案**: [plan.md](./plan.md)  

---

## 阶段 1：项目基础设施与多语言词典扩展 (Setup & i18n)

- [ ] T001 [P] 扩展中文语言包 `locales/zh-CN.json`，添加 `search.*` 及 `header.search` 词条
- [ ] T002 [P] 扩展英文语言包 `locales/en.default.json`，同步添加对应英文 `search.*` 词条
- [ ] T003 [P] 扩展法文语言包 `locales/fr.json`，同步添加对应法文 `search.*` 词条

---

## 阶段 2：搜索抽屉核心 Section 组件开发 (Foundation)

- [ ] T004 创建独立搜索抽屉 Section 文件 `sections/predictive-search-drawer.liquid`
- [ ] T005 在 `sections/predictive-search-drawer.liquid` 中编写毛玻璃 Glassmorphic 抽屉结构与 CSS 样式 (`border-radius: 20px`, `z-index: 9999999`, `overflow-x: hidden`)
- [ ] T006 [P] 在 `layout/theme.liquid` 中全局挂载 `{% section 'predictive-search-drawer' %}` 组件

---

## 阶段 3：用户故事 1 - Header 搜索图标与触发器联动 (US1)

- [ ] T007 [US1] 在 `sections/header.liquid` 右侧工具栏中新增 🔍 搜索图标按钮 (`header__search-btn`)
- [ ] T008 [US1] 在 `sections/header.liquid` 中添加点击 🔍 展开 `#SearchDrawer` 抽屉并自动聚焦输入框的 JavaScript 事件监听
- [ ] T009 [US1] 在 `sections/predictive-search-drawer.liquid` 中绑定 `ESC` 键与蒙版点击平滑关闭抽屉逻辑

---

## 阶段 4：用户故事 2 - Shopify Predictive Search API 与 300ms 防抖引擎 (US2)

- [ ] T010 [US2] 在 `sections/predictive-search-drawer.liquid` JS 中实现 300ms 智能防抖定时器 (Debounce)
- [ ] T011 [US2] 对接 Shopify 官方 Predictive Search API (`/search/suggest.json?q={query}&resources[type]=product,query`)
- [ ] T012 [US2] 在 `sections/predictive-search-drawer.liquid` 中实现 Loading 骨架屏 (Skeleton Loader) 脉冲效果
- [ ] T013 [US2] 实现实时模糊匹配结果的 DOM 动态渲染 (含无匹配结果友好提示)

---

## 阶段 5：用户故事 3 - 热门搜索词标签与商品卡片预览加购 (US3)

- [ ] T014 [US3] 在 `sections/predictive-search-drawer.liquid` 中实现空输入状态下的“热门搜索词 (Trending Searches)”胶囊标签与一键填入搜索功能
- [ ] T015 [US3] 渲染实时商品卡片：展示商品缩略图、标题、品牌 Vendor、格式化价格及促销 Badge
- [ ] T016 [US3] 集成商品卡片“快捷加购”按钮并与 `ThemeCartUtils.submitAddToCart` 联动

---

## 阶段 6：最终润色、零横向溢出校验与全局测试 (Polish)

- [ ] T017 检查并确保全站 0 横向溢出 (`overflow-x: hidden`, `max-width: 100%`)，防止触摸板右滑空白
- [ ] T018 运行 `shopify theme check` 进行全站主题语法与规范校验 (要求 0 Error)

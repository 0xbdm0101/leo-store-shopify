# Tasks: 多语言与多货币支持

**Input**: Design documents from `specs/001-multi-lang-currency/`

**Prerequisites**: plan.md ✅, spec.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: 项目结构初始化和基础配置

- [ ] T001 Create project structure per plan.md (ensure `snippets/`, `blocks/`, `assets/` directories exist)
- [ ] T002 [P] Add CSS custom properties for locale selectors in `snippets/css-variables.liquid` (`--locale-select-font-size`, `--locale-select-padding`, `--locale-select-border-color`, `--locale-select-focus-ring`)
- [ ] T003 [P] Add all required translation keys to `locales/en.default.json` (general.locale.*, general.geo_recommendation.*)

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: 所有 User Story 依赖的基础翻译文件

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T004 [P] Create Chinese translations in `locales/zh-CN.json` (all keys from en.default.json translated to Chinese)
- [ ] T005 [P] Create French translations in `locales/fr.json` (all keys from en.default.json translated to French)
- [ ] T006 Verify all translation keys exist across all 3 locale files using `shopify theme check`

**Checkpoint**: 翻译基础设施就绪 — 所有 User Story 可以开始

---

## Phase 3: User Story 1+2 — 语言与货币切换 (Priority: P1) 🎯 MVP

**Goal**: 用户可以在 header/footer 切换语言和货币

**Independent Test**: 配置 3 种语言 + 4 种货币后，通过选择器切换，验证文本翻译和价格转换

### Implementation for US1 & US2

- [ ] T007 [P] [US1] Create `snippets/locale-selector.liquid` with `{% doc %}` header, Liquid logic to fetch `localization.available_languages` and `localization.available_countries`, produce `<form>` + `<select>` with native names and symbols per contract `contracts/locale-selector.md`
- [ ] T008 [P] [US1] Add component-scoped styles in `snippets/locale-selector.liquid` using `{% stylesheet %}` (mobile-first, dropdown and inline variants, focus-visible ring, 200% zoom support)
- [ ] T009 [P] [US1] Add progressive enhancement JS in `snippets/locale-selector.liquid` using `{% javascript %}` (intercept form submit for async currency update via `/cart/update.js`, emit `locale:switched` custom event)
- [ ] T010 [P] [US2] Implement JS fallback: `<noscript>` submit button inside the form for language/currency switching when JS is disabled
- [ ] T011 [US1] Create `blocks/locale-selector-block.liquid` with `{% doc %}` header and `{% schema %}` (settings: `type` [language/currency/both], `style` [dropdown/inline], `display_label` [show/hide]); render `locale-selector` snippet with block settings
- [ ] T012 [US1] Update `layout/theme.liquid` to render `seo-locale-meta` snippet in `<head>` (placeholder for Phase 6)
- [ ] T013 [US2] Add translation keys for currency switch success/failure announcements to `locales/en.default.json` (`general.locale.currency_changed`, `general.locale.currency_error`)

**Checkpoint**: MVP 完成 — 用户可通过选择器切换语言和货币，JS 禁用时可用表单降级

---

## Phase 4: User Story 3 — 地理位置推荐 (Priority: P2)

**Goal**: 首次访问用户看到基于 IP 的地理位置推荐提示

**Independent Test**: 清除 localStorage，模拟不同国家 IP 访问，验证推荐横幅的显示/关闭/应用逻辑

### Implementation for User Story 3

- [ ] T014 [US3] Create `snippets/geo-recommendation.liquid` with `{% doc %}` header, Liquid logic to compare `localization.country` detection vs saved preference/localStorage, per contract `contracts/geo-recommendation.md`
- [ ] T015 [P] [US3] Add component styles in `snippets/geo-recommendation.liquid` using `{% stylesheet %}` (non-intrusive banner: sticky top or inline, dismissible, slide-out animation)
- [ ] T016 [P] [US3] Add interaction JS in `snippets/geo-recommendation.liquid` using `{% javascript %}` (accept: apply recommendations + save prefs + close; dismiss: close only + set dismissed flag)
- [ ] T017 [US3] Integrate `geo-recommendation` snippet into `layout/theme.liquid` (render after header, before main content)

**Checkpoint**: 首次访问用户看到地理位置推荐，老用户不被打扰

---

## Phase 5: User Story 4 — 偏好持久化 (Priority: P2)

**Goal**: 用户语言和货币偏好保存 30 天，回访时自动恢复

**Independent Test**: 设置偏好 → 关闭浏览器 → 重新打开 → 验证偏好恢复

### Implementation for User Story 4

- [ ] T018 [US4] Implement localStorage read/write logic in `snippets/locale-selector.liquid` `{% javascript %}` (read `shop_locale_prefs` on load, apply saved prefs if not expired; write on change with `set_at` timestamp)
- [ ] T019 [US4] Add 30-day expiry check: if `set_at` > 30 days ago, clear preference and fall back to geolocation detection
- [ ] T020 [US4] Wire preference read into `geo-recommendation.liquid` display logic (if saved pref exists and not expired → skip recommendation; otherwise → show)
- [ ] T021 [P] [US4] Add translation keys for preference-related UI messages to `locales/en.default.json` (`general.locale.preference_saved`, `general.locale.preference_expired`)

**Checkpoint**: 回访用户体验完整 — 偏好自动恢复，过期自动重置

---

## Phase 6: User Story 5 — SEO 元数据 (Priority: P2)

**Goal**: 搜索引擎和社交媒体正确索引多语言页面

**Independent Test**: 查看页面源代码，验证 hreflang 和 og:locale 标签完整性

### Implementation for User Story 5

- [ ] T022 [US5] Create `snippets/seo-locale-meta.liquid` with `{% doc %}` header, logic to iterate `localization.available_languages` and output `<link rel="alternate" hreflang="...">` tags + `x-default`, per contract `contracts/seo-meta.md`
- [ ] T023 [US5] Add Open Graph locale tags to `snippets/seo-locale-meta.liquid` (`og:locale` for current language, `og:locale:alternate` for each other language)
- [ ] T024 [US5] Ensure `canonical_url` correctly replaces locale prefix when generating alternate URLs
- [ ] T025 [US5] Handle edge case: if only 1 language configured, output no hreflang tags (not needed for monolingual sites)

**Checkpoint**: 多语言 SEO 完整 — hreflang + OG 标签正确输出

---

## Phase 7: User Story 6 — 无障碍访问 (Priority: P3)

**Goal**: 选择器符合 WCAG 2.1 AA，键盘和屏幕阅读器可用

**Independent Test**: 使用 VoiceOver/NVDA + 键盘导航完成完整的语言/货币切换

### Implementation for User Story 6

- [ ] T026 [US6] Audit and fix `snippets/locale-selector.liquid` accessibility: `<label>` correctly associated with `<select>`, `aria-describedby` linked to status region, `aria-live="polite"` status announcements
- [ ] T027 [US6] Audit and fix `snippets/geo-recommendation.liquid` accessibility: `role="region"` with `aria-label`, dismiss button has screen-reader-only label
- [ ] T028 [P] [US6] Verify color contrast ratios (text ≥ 4.5:1, borders/icons ≥ 3:1) in both components using axe-core or browser DevTools
- [ ] T029 [P] [US6] Add `:focus-visible` styles for keyboard navigation on all interactive elements (select, buttons)
- [ ] T030 [US6] Test at 200% browser zoom — ensure no text clipping or overlap in selectors and recommendation banner

**Checkpoint**: 无障碍达标 — WCAG 2.1 AA 自动检测通过

---

## Phase 8: Polish & Cross-Cutting Concerns

**Purpose**: 跨 User Story 的优化和文档

- [ ] T031 [P] Add `{% doc %}` header audit: verify all 3 new snippets have complete LiquidDoc with @param, @example
- [ ] T032 [P] Verify schema.json validates: `blocks/locale-selector-block.liquid` schema passes `schemas/theme_block.json`
- [ ] T033 Run `shopify theme check` — fix any Liquid syntax errors, missing translation keys, or schema warnings
- [ ] T034 Run through all 7 validation scenarios in `quickstart.md` and document results
- [ ] T035 [P] Performance check: verify page load not increased by >50ms with selectors rendered, currency prices update <1s

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies — start immediately
- **Foundational (Phase 2)**: Depends on T003 (translation keys defined) — BLOCKS all user stories
- **US1+US2 (Phase 3)**: Depends on Phase 2 — P1 MVP
- **US3 (Phase 4)**: Depends on Phase 3 (uses locale-selector JS events)
- **US4 (Phase 5)**: Depends on Phase 3 (modifies locale-selector JS) + Phase 4 (geo-recommendation logic)
- **US5 (Phase 6)**: Independent of US3/US4 — can run after Phase 2
- **US6 (Phase 7)**: Depends on all prior phases (audits existing components)
- **Polish (Phase 8)**: Depends on all desired user stories complete

### User Story Dependencies

- **US1+US2 (P1)**: After Phase 2 — no dependencies on other stories
- **US3 (P2)**: After US1+US2 (uses `locale:switched` event)
- **US4 (P2)**: After US1+US2 + US3 (modifies components from both)
- **US5 (P2)**: After Phase 2 — can run in parallel with US3/US4
- **US6 (P3)**: After all stories (audits all components)

### Parallel Opportunities

- T004, T005 can run in parallel (different locale files)
- T007, T008, T009, T010 all touch the same file (`locale-selector.liquid`) — execute sequentially
- T022, T023 share `seo-locale-meta.liquid` — execute sequentially
- T015, T016 share `geo-recommendation.liquid` — execute sequentially
- T026-T030 (US6) are all in existing files — can audit in parallel
- T031, T032, T035 can run in parallel (different concerns)

---

## Implementation Strategy

### MVP First (US1+US2 Only)

1. Phase 1: Setup → T001, T002, T003
2. Phase 2: Foundational → T004, T005, T006
3. Phase 3: US1+US2 → T007-T013
4. **STOP**: Test language + currency switching independently
5. Deploy as MVP

### Incremental Delivery

1. Setup + Foundational → Foundation ready
2. US1+US2 → MVP: 用户可切换语言和货币
3. US3 → 地理位置推荐增强首次用户体验
4. US4 → 偏好持久化提升回访体验
5. US5 → SEO 元数据（可并行于 US3/US4）
6. US6 → 无障碍达标
7. Polish → 最终优化

### Parallel Team Strategy

With 2 developers:
- **Dev A**: US1+US2 (Phase 3) → US3 (Phase 4) → US4 (Phase 5)
- **Dev B**: T004 + T005 (Phase 2) → US5 (Phase 6) → US6 (Phase 7)

---

## Notes

- [P] tasks = different files, no dependencies
- [Story] label maps task to specific user story for traceability
- T007-T010 all modify `locale-selector.liquid` — sequential execution recommended
- Verify `shopify theme check` passes after each phase
- Stop at any checkpoint to validate story independently
- All snippets MUST have `{% doc %}` header (Constitution V)
- All user-facing text MUST use `{{ 'key' | t }}` (Constitution III)

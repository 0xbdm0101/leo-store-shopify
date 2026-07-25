<!--
  Sync Impact Report
  ==================
  Version change: [unversioned template] → 1.0.0
  Modified principles: N/A (first real version, template placeholders replaced)
  Added sections:
    - Core Principles (5 principles for Shopify theme development)
    - Quality Standards (accessibility, responsive design, performance)
    - Development Workflow (spec-first, theme editor testing, incremental delivery)
  Removed sections: None
  Templates requiring updates:
    - .specify/templates/plan-template.md ✅ (Constitution Check section compatible)
    - .specify/templates/spec-template.md ✅ (no changes needed)
    - .specify/templates/tasks-template.md ✅ (no changes needed)
  Follow-up TODOs: None
-->

# Leo Store Shopify Theme Constitution

## Core Principles

### I. Theme Architecture First

Every feature MUST respect the Shopify theme directory structure. Components MUST be placed
in the correct directory based on their role:

- **sections/** — full-width, merchant-customizable page modules with `{% schema %}`
- **blocks/** — nestable, reusable small components with `{% schema %}`
- **snippets/** — reusable Liquid fragments with `{% doc %}` header, rendered via `{% render %}`

A feature that spans multiple directories MUST clearly separate concerns: layout logic in
snippets, editable content in blocks/sections, and page structure in templates.

**Rationale**: The directory structure is not cosmetic — it governs what merchants can edit
in the theme editor. Placing a component in the wrong directory removes merchant control or
creates maintenance burden.

### II. Schema-Driven Configuration

Every customizable setting MUST be exposed through `{% schema %}`. No configuration values
MAY be hardcoded in templates. Theme settings fall into three categories:

- **Single-property CSS settings**: Use CSS custom properties via inline `style` attributes
  (e.g., `style="--gap: {{ block.settings.gap }}px"`)
- **Multi-property CSS settings**: Use CSS classes controlled by `select` inputs
- **Content settings**: Text, images, and product/collection references via dedicated input types

All schema JSON MUST validate against the appropriate schema (`schemas/section.json`,
`schemas/theme_block.json`, or `schemas/theme_settings.json`).

**Rationale**: Merchants configure the theme through the editor, not code. Every hardcoded
value is a missed customization opportunity and a potential support request.

### III. Translation-First (NON-NEGOTIABLE)

Every user-facing text string MUST use the `{{ 'key' | t }}` translation filter. No raw text
MAY appear in templates. This applies to:

- Headings, buttons, labels, and link text
- Placeholder text and form labels
- Error messages and status indicators
- `alt` attributes and `aria-label` values

New translation keys MUST be added to `locales/en.default.json` in a hierarchical structure
(max 3 levels). Keys MUST use snake_case. Only English text is added; translators handle
other languages.

**Rationale**: A theme without translations is single-language by default. Retrofitting
translations is exponentially more expensive than building with them from the start.
This principle directly supports the multi-language feature spec.

### IV. Component-Scoped Assets

CSS and JavaScript MUST be defined per-component using `{% stylesheet %}` and
`{% javascript %}` tags within the component file. Exceptions:

- `assets/critical.css` for above-the-fold styles shared across all pages
- Theme-wide CSS custom properties defined in a shared snippet

No component MAY inject styles or scripts globally. Each block, section, or snippet owns
its presentation and behavior.

**Rationale**: Per-component scoping prevents style conflicts, makes components portable,
and ensures unused CSS/JS is not shipped when a component is removed from a template.

### V. LiquidDoc Documentation

Every snippet and statically-rendered block MUST include a `{% doc %}` header documenting:

- The component's purpose
- All accepted parameters with types (`@param {type} name - description`)
- Optional parameters marked with brackets (`@param {type} [name]`)
- Usage examples (`@example`)

**Rationale**: Snippets are shared across the theme by multiple developers and AI agents.
Without documentation, parameters are guesswork, leading to broken renders and wasted time.

## Quality Standards

### Accessibility

All user-facing components MUST meet WCAG 2.1 AA standards:

- Color contrast ratio ≥ 4.5:1 for text, ≥ 3:1 for large text
- All interactive elements MUST be keyboard-accessible with visible focus indicators
- Forms MUST have associated `<label>` elements
- Images MUST have meaningful `alt` attributes
- Screen reader announcements for dynamic content changes

### Responsive Design

All components MUST be mobile-first. Layout changes MUST use CSS media queries.
Mobile-specific layouts (e.g., column counts) MUST be configurable via schema `select`
inputs where appropriate.

### Performance

- Above-the-fold CSS in `assets/critical.css` only — all other styles scoped to components
- No render-blocking JavaScript
- Images MUST use `image_url` filter with explicit `width` and `height` for proper sizing
- Lazy loading for below-the-fold images and iframes

## Development Workflow

### Spec-First Development

Every feature starts with a specification (`/speckit-specify`) before any code is written.
The spec defines user stories, acceptance criteria, and success metrics. Implementation
follows the plan (`/speckit-plan`) and task list (`/speckit-tasks`).

### Theme Editor Testing

Every section and block MUST be testable in the Shopify theme editor. This means:

- Valid `{% schema %}` with at least one `presets` entry for sections
- Default values for all settings (theme renders without configuration)
- `visible_if` conditions tested for at least two setting combinations

### Incremental Delivery

User stories are implemented in priority order (P1 → P2 → P3). Each story MUST be
independently testable and deliverable. P1 stories form the MVP — everything beyond
is enhancement.

## Governance

This constitution supersedes all other project practices. Amendments require:

1. A documented rationale for the change
2. Review of impact on existing specs, plans, and templates
3. Version increment following semantic versioning:
   - **MAJOR**: Principle removal or redefinition
   - **MINOR**: New principle or section added
   - **PATCH**: Clarifications, wording fixes
4. Update of all dependent templates to reflect the change

All feature specifications and implementation plans MUST include a constitution
compliance check. Violations require explicit justification in the plan's
Complexity Tracking section.

**Version**: 1.0.0 | **Ratified**: 2026-07-25 | **Last Amended**: 2026-07-25

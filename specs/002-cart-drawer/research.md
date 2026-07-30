# Research & Technical Decisions: Ajax Cart Drawer

**Feature**: [spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/spec.md)

## Decision 1: Cart Mutation & Rendering Architecture

- **Decision**: Use Shopify Cart Ajax API (`/cart/change.js`, `/cart/add.js`) combined with the **Section Rendering API** (`?sections=cart-drawer`).
- **Rationale**:
  - Fetching pre-rendered Liquid HTML from Shopify (`?sections=cart-drawer`) ensures 100% server-side Liquid logic (translations, money formatting, line item discounts) is preserved without needing to re-implement complex Liquid template filters in client JS.
  - Cart Ajax API handles item additions, quantity modifications, and removals asynchronously.
- **Alternatives Considered**:
  - *Pure Client JS Rendering (Mustache/Handlebars/JS Template)*: Rejected because re-implementing Liquid money filters, discount allocations, and internationalization strings (`{{ 'cart.drawer.title' | t }}`) in JS leads to code duplication and potential formatting inconsistencies across themes.

## Decision 2: Drawer Overlay & Component Isolation

- **Decision**: Implement `sections/cart-drawer.liquid` as a self-contained Shopify Theme 2.0 section with inline `{% stylesheet %}` and `{% javascript %}`.
- **Rationale**:
  - Aligns with Constitution Principle I (Theme Architecture First) & Principle IV (Component-Scoped Assets).
  - Uses CSS custom properties for colors (`--cart-drawer-bg`, `--cart-drawer-accent`) controlled by `{% schema %}` settings.
  - Overlays use `position: fixed`, `z-index: 9999`, and `aria-hidden="true/false"` for accessibility compliance.
- **Alternatives Considered**:
  - *Global CSS in assets/theme.css*: Rejected to prevent style pollution and keep assets scoped to the cart drawer component.

## Decision 3: Free Shipping Progress Bar Calculation

- **Decision**: Calculate threshold progress both server-side in Liquid for initial page load and client-side in JS for dynamic Ajax updates.
- **Formula**:
  - `remaining_cents = max(0, threshold_cents - cart.total_price)`
  - `progress_percent = min(100, (cart.total_price / threshold_cents) * 100)`
- **Rationale**:
  - Server-side Liquid calculation renders instantly when page loads (no layout shift).
  - Client-side JS recalculation updates the bar progress animation instantly when items are added or quantities change.

## Decision 4: Cart Upsell / Cross-Sell Filtering

- **Decision**: Render upsell items from a configurable Shopify Collection (`settings.upsell_collection`), filtering out items currently present in `cart.items`.
- **Rationale**:
  - Prevents recommending products the customer has already added to their cart.
  - Allows merchants to change recommended products at any time via the Theme Editor.

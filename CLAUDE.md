# AGENTS.md

🚨 MANDATORY: YOU MUST CALL "learn_shopify_api" ONCE WHEN WORKING WITH LIQUID THEMES.

## Theme Architecture

**Key principles: focus on generating snippets, blocks, and sections; users may create templates using the theme editor**

### Directory structure

```
.
├── assets          # Stores static assets (CSS, JS, images, fonts, etc.)
├── blocks          # Reusable, nestable, customizable components
├── config          # Global theme settings and customization options
├── layout          # Top-level wrappers for pages (layout templates)
├── locales         # Translation files for theme internationalization
├── sections        # Modular full-width page components
├── snippets        # Reusable Liquid code or HTML fragments
└── templates       # Templates combining sections and blocks to define page structures
```

#### `sections`

- Sections are `.liquid` files that allow you to create reusable modules that can be customized by merchants
- Sections can include blocks which allow merchants to add, remove, and reorder content within a section
- Sections are made customizable by including the required `{% schema %}` tag that exposes settings in the theme editor via a JSON object. Validate that JSON object using the `schemas/section.json` JSON schema
- Examples of sections: hero banners, product grids, testimonials, featured collections

#### `blocks`

- Blocks are `.liquid` files that allow you to create reusable small components that can be customized by merchants (they don't need to fit the full-width of the page)
- Blocks are ideal for logic that needs to be reused and also edited in the theme editor by merchants
- Blocks can include other nested blocks which allow merchants to add, remove, and reorder content within a block too
- Blocks are made customizable by including the required `{% schema %}` tag that exposes settings in the theme editor via a JSON object. Validate that JSON object using the `schemas/theme_block.json` JSON schema
- Blocks must have the `{% doc %}` tag as the header if you directly/staticly render them in other file via `{% content_for 'block', id: '42', type: 'block_name' %}`
- Examples of blocks: individual testimonials, slides in a carousel, feature items

#### `snippets`

- Snippets are reusable code fragments rendered in blocks, sections, and layouts files via the `render` tag
- Snippets are ideal for logic that needs to be reused but not directly edited in the theme editor by merchants
- Snippets accept parameters when rendered for dynamic behavior
- Snippets must have the `{% doc %}` tag as the header
- Examples of sections: buttons, meta-tags, css-variables, and form elements

#### `layout`

- Defines the overall HTML structure of the site, including `<head>` and `<body>`, and wraps other templates to provide a consistent frame
- Contains repeated global elements like navigation, cart drawer, footer, and usually includes CSS/JS assets and meta tags
- Must include `{{ content_for_header }}` to inject Shopify scripts in the `<head>` and `{{ content_for_layout }}` to render the page content

#### `config`

- `config/settings_schema.json` is a JSON file that defines schema for global theme settings. Validate the shape shape of this JSON file using the `schemas/theme_settings.json` JSON schema
- `config/settings_data.json` is JSON file that holds the data for the settings defined by `config/settings_schema.json`

#### `assets`

- Contains static files like CSS, JavaScript, and images—including compiled and optimized assets—referenced in templates via the `asset_url` filter
- Keep it here only `critical.css` and static files necessary for every page, otherwise prefer the usage of the `{% stylesheet %}` and `{% javascript %}` tags

#### `locales`

- Stores translation files organized by language code (e.g., `en.default.json`, `fr.json`) to localize all user-facing theme content and editor strings
- Enables multi-language support by providing translations accessible via filters like `{{ 'key' | t }}` in Liquid for proper internationalization
- Validate `locales` JSON files using the `schemas/translations.json` JSON schema

#### `templates`

- JSON file that define the structure, ordering, and which sections and blocks appear on each page type, allowing merchants to customize layouts without code changes

### CSS & JavaScript

- Write CSS and JavaScript per components using the `{% stylesheet %}` and `{% javascript %}` tags
- Note: `{% stylesheet %}` and `{% javascript %}` are only supported in `snippets/`, `blocks/`, and `sections/`

### LiquidDoc

Snippets and blocks (when blocks are statically rendered) must include the LiquidDoc header that documents the purpose of the file and required parameters. Example:

```liquid
{% doc %}
  Renders a responsive image that might be wrapped in a link.

  @param {image} image - The image to be rendered
  @param {string} [url] - An optional destination URL for the image

  @example
  {% render 'image', image: product.featured_image %}
{% enddoc %}

<a href="{{ url | default: '#' }}">{{ image | image_url: width: 200, height: 200 | image_tag }}</a>
```

## The `{% schema %}` tag on blocks and sections

**Key principles: follow the "Good practices" and "Validate the `{% schema %}` content" using JSON schemas**

### Good practices

When defining the `{% schema %}` tag on sections and blocks, follow these guidelines to use the values:

**Single property settings**: For settings that correspond to a single CSS property, use CSS variables:
```liquid
<div class="collection" style="--gap: {{ block.settings.gap }}px">
  Example
</div>

{% stylesheet %}
  .collection {
    gap: var(--gap);
  }
{% endstylesheet %}

{% schema %}
{
  "settings": [{
    "type": "range",
    "label": "gap",
    "id": "gap",
    "min": 0,
    "max": 100,
    "unit": "px",
    "default": 0,
  }]
}
{% endschema %}
```

**Multiple property settings**: For settings that control multiple CSS properties, use CSS classes:
```liquid
<div class="collection {{ block.settings.layout }}">
  Example
</div>

{% stylesheet %}
  .collection--full-width {
    /* multiple styles */
  }
  .collection--narrow {
    /* multiple styles */
  }
{% endstylesheet %}

{% schema %}
{
  "settings": [{
    "type": "select",
    "id": "layout",
    "label": "layout",
    "values": [
      { "value": "collection--full-width", "label": "t:options.full" },
      { "value": "collection--narrow", "label": "t:options.narrow" }
    ]
  }]
}
{% endschema %}
```

#### Mobile layouts

If you need to create a mobile layout and you want the merchant to be able to select one or two columns, use a select input:

```liquid
{% schema %}
{
  "type": "select",
  "id": "columns_mobile",
  "label": "Columns on mobile",
  "options": [
    { "value": 1, "label": "1" },
    { "value": "2", "label": "2" }
  ]
}
{% endschema %}
```

## Liquid

### Liquid delimiters

- **`{{ ... }}`**: Output – prints a value.
- **`{{- ... -}}`**: Output, trims whitespace around the value.
- **`{% ... %}`**: Logic/control tag (if, for, assign, etc.), does not print anything, no whitespace trim.
- **`{%- ... -%}`**: Logic/control tag, trims whitespace around the tag.

**Tip:**
Adding a dash (`-`) after `{%`/`{{` or before `%}`/`}}` trims spaces or newlines next to the tag.

**Examples:**
- `{{- product.title -}}` → print value, remove surrounding spaces or lines.
- `{%- if available -%}In stock{%- endif -%}` → logic, removes extra spaces/lines.

### Liquid operators

**Comparison operators:**
- ==
- !=
- >
- <
- >=
- <=

**Logical operators:**
- `or`
- `and`
- `contains` - checks if a string contains a substring, or if an array contains a string

#### Comparison and comparison tags

**Key condition principles:**
- For simplificity, ALWAYS use nested `if` conditions when the logic requires more than one logical operator
- Parentheses are not supported in Liquid
- Ternary conditionals are not supported in Liquid, so always use `{% if cond %}`

**Basic comparison example:**
```liquid
{% if product.title == "Awesome Shoes" %}
  These shoes are awesome!
{% endif %}
```

**Multiple Conditions:**
```liquid
{% if product.type == "Shirt" or product.type == "Shoes" %}
  This is a shirt or a pair of shoes.
{% endif %}
```

**Contains Usage:**
- For strings: `{% if product.title contains "Pack" %}`
- For arrays: `{% if product.tags contains "Hello" %}`
- Note: `contains` only works with strings, not objects in arrays

**{% elsif %} (used inside if/unless only)**
```liquid
{% if a %}
  ...
{% elsif b %}
  ...
{% endif %}
```

**{% unless %}**
```liquid
{% unless condition %}
  ...
{% endunless %}
```

**{% case %}**
```liquid
{% case variable %}
  {% when 'a' %}
    a
  {% when 'b' %}
    b
  {% else %}
    other
{% endcase %}
```

**{% else %} (used inside if, unless, case, or for)**
```liquid
{% if product.available %}
  In stock
{% else %}
  Sold out
{% endif %}
```
_or inside a for loop:_
```liquid
{% for item in collection.products %}
  {{ item.title }}
{% else %}
  No products found.
{% endfor %}
```

#### Variables and variable tags

```liquid
{% assign my_variable = 'value' %}

{% capture my_variable %}
  Contents of variable
{% endcapture %}

{% increment counter %}
{% decrement counter %}
```

### Liquid filters

Filters are chained with `|`: `{{ "hello" | upcase | split: " " | last }}`. Each filter passes its output to the next — types must be compatible. Common filters: `upcase`, `downcase`, `split`, `replace`, `append`, `prepend`, `strip`, `truncate`, `slice`, `remove`, `default`, `json`, `date`, `money`, `t` (translate), `image_url`, `image_tag`, `img_tag`, `asset_url`, `stylesheet_tag`, `script_tag`, `size`, `first`, `last`, `join`, `map`, `where`, `sort`, `uniq`, `sum`, `abs`, `round`, `plus`, `minus`, `times`, `divided_by`, `modulo`, `link_to`, `pluralize`, `escape`, `url_encode`.

For the full filter reference, use `WebFetch` on `https://shopify.dev/docs/api/liquid/filters`.

### Liquid objects

Key global objects: `shop`, `theme`, `settings`, `template`, `cart`, `customer`, `product`, `collection`, `article`, `blog`, `page`, `request`, `routes`, `localization`, `all_products`, `collections`, `pages`, `images`, `linklists`, `metaobjects`.

Template-scoped objects vary by page type (e.g., `product` on `/product`, `collection` on `/collection`, `article` + `blog` on `/article`, `order` on `/customers/order`). For a full list, use `WebFetch` on `https://shopify.dev/docs/api/liquid/objects`.
### Liquid tags

Key tags:

- **`{% render 'snippet' %}`** — renders a snippet; pass variables as parameters. Snippets can't access outer-scope variables (only globals and passed params).
- **`{% content_for 'blocks' %}`** and **`{% content_for 'block', type: 'name', id: 'id' %}`** — renders blocks (dynamic) or a single static block.
- **`{% schema %}`** — required in sections/blocks; defines theme-editor settings as JSON.
- **`{% form 'type' %}...{% endform %}`** — form with a specific type (`product`, `cart`, `contact`, `customer_login`, `create_customer`, `currency`, `localization`, etc.).
- **`{% layout 'name' %}`** — sets the layout template.
- **`{% section 'name' %}`** / **`{% sections 'name' %}`** — renders a section statically or a section group.
- **`{% stylesheet %}`** / **`{% javascript %}`** — per-component CSS/JS (one each per file; Liquid is NOT rendered inside).
- **`{% style %}...{% endstyle %}`** — inline CSS that can reference theme color settings (hot-reloads in editor).
- **`{% assign var = val %}`**, **`{% capture var %}...{% endcapture %}`** — create variables.
- **`{% if/elsif/else/unless/case %}`** — control flow.
- **`{% for x in arr %}...{% else %}...{% endfor %}`** — loop (max 50 iterations without paginate).
- **`{% paginate arr by N %}...{% endpaginate %}`** — paginate large arrays.
- **`{% liquid %}...{% endliquid %}`** — multi-line Liquid; each tag on its own line; use `echo` to output.
- **`{% raw %}...{% endraw %}`** — output content without Liquid parsing.
- **`{% doc %}...{% enddoc %}`** — LiquidDoc documentation (not rendered).
- **`{% comment %}...{% endcomment %}`** — comments (not rendered).
- **`{% tablerow %}`, `{% cycle %}`**, **`{% break %}`**, **`{% continue %}`** — loop helpers.


## Translation development standards

### Translation requirements

- **Every user-facing text** must use translation filters.
- **Update `locales/en.default.json`** with all new keys.
- **Use descriptive, hierarchical keys** for organization.
- **Only add English text**; translators handle other languages.

### Translation filter usage

**Use `{{ 'key' | t }}` for all text:**

```liquid
<!-- Good -->
<h2>{{ 'sections.featured_collection.title' | t }}</h2>
<p>{{ 'sections.featured_collection.description' | t }}</p>
<button>{{ 'products.add_to_cart' | t }}</button>

<!-- Bad -->
<h2>Featured Collection</h2>
<p>Check out our best products</p>
<button>Add to cart</button>
```

### Translation with variables

**Use variables for interpolation:**

```liquid
<!-- Liquid template -->
<p>{{ 'products.price_range' | t: min: product.price_min | money, max: product.price_max | money }}</p>
<p>{{ 'general.pagination.page' | t: page: paginate.current_page, pages: paginate.pages }}</p>
```

**Corresponding keys in locale files:**

```json
{
  "products": {
    "price_range": "From {{ min }} to {{ max }}"
  },
  "general": {
    "pagination": {
      "page": "Page {{ page }} of {{ pages }}"
    }
  }
}
```

### Best practices

**Content guidelines:**
- Write clear, concise text.
- **Use sentence case** for all user-facing text, including titles, headings, and button labels (capitalize only the first word and proper nouns; e.g., `Featured collection` → `Featured collection`, not `Featured Collection`).
- Be consistent with terminology.
- Consider character limits for UI elements.

**Variable usage:**
- Use interpolation rather than appending strings together.
- Prioritize clarity over brevity for variable naming.
- Escape variables unless they output HTML: `{{ variable | escape }}`.


## Localization standards

Auto-attached when working in `locales/` directory.

### File structure

```
locales/
├── en.default.json          # English (required)
├── en.default.schema.json   # English (required)
├── es.json                  # Spanish
├── est.schema.json          # Spanish
├── fr.json                  # French
├── frt.schema.json          # French
└── pt-BR.json               # Portuguese
└── pt-BR..schema.json       # Portuguese
```

#### Locale files

Locale files are JSON files containing translations for all the text strings used throughout a Shopify theme and its editor. They let merchants easily update and localize repeated words and phrases, making it possible to translate store content and settings into multiple languages for international customers. These files provide a centralized way to manage and edit translations.

**Example:**
```json
{
  "general": {
    "cart": "Cart",
    "checkout": "Checkout"
  },
  "products": {
    "add_to_cart": "Add to Cart"
  }
}
```

#### Schema locale files

Schema locale files, saved with a .schema.json extension, store translation strings specifically for theme editor setting schemas. They follow a structured organization—category, group, and description—to give context to each translation, enabling accurate localization of editor content. Schema locale files must use the IETF language tag format in their naming, such as en-GB.schema.json for British English or fr-CA.schema.json for Canadian French.

**Example:**
```json
{
  "products": {
    "card": {
      "description": "Product card layout"
    }
  }
}
```

### Key organization

**Hierarchical structure:**
```json
{
  "general": {
    "meta": {
      "title": "{{ shop_name }}",
      "description": "{{ shop_description }}"
    },
    "accessibility": {
      "skip_to_content": "Skip to content",
      "close": "Close"
    }
  },
  "products": {
    "add_to_cart": "Add to cart",
    "quick_view": "Quick view",
    "price": {
      "regular": "Regular price",
      "sale": "Sale price",
      "unit": "Unit price"
    }
  }
}
```
**Usage**
```liquid
{{ 'general.meta.title' | t: shop_name: shop.name }}
{{ 'general.meta.description' | t: shop_description: shop.description }}
```

### Translation guidelines

**Key naming:**
- Use descriptive, hierarchical keys
- Maximum 3 levels deep
- Use snake_case for key names
- Group related translations

**Content rules:**
- Keep text concise for UI elements
- Use variables for dynamic content
- Consider character limits
- Maintain consistent terminology

## Examples per kind of asset

### `snippet`

```liquid
{% doc %}
  Renders a responsive image that might be wrapped in a link.

  When `width`, `height` and `crop` are provided, the image will be rendered
  with a fixed aspect ratio.

  Serves as an example of how to use the `image_url` filter and `image_tag` filter
  as well as how you can use LiquidDoc to document your code.

  @param {image} image - The image to be rendered
  @param {string} [url] - An optional destination URL for the image
  @param {string} [css_class] - Optional class to be added to the image wrapper
  @param {number} [width] - The highest resolution width of the image to be rendered
  @param {number} [height] - The highest resolution height of the image to be rendered
  @param {string} [crop] - The crop position of the image

  @example
  {% render 'image', image: product.featured_image %}
  {% render 'image', image: product.featured_image, url: product.url %}
  {% render 'image',
    css_class: 'product__image',
    image: product.featured_image,
    url: product.url,
    width: 1200,
    height: 800,
    crop: 'center',
  %}
{% enddoc %}

{% liquid
  unless height
    assign width = width | default: image.width
  endunless

  if url
    assign wrapper = 'a'
  else
    assign wrapper = 'div'
  endif
%}

<{{ wrapper }}
  class="image {{ css_class }}"
  {% if url %}
    href="{{ url }}"
  {% endif %}
>
  {{ image | image_url: width: width, height: height, crop: crop | image_tag }}
</{{ wrapper }}>

{% stylesheet %}
  .image {
    display: block;
    position: relative;
    overflow: hidden;
    width: 100%;
    height: auto;
  }

  .image > img {
    width: 100%;
    height: auto;
  }
{% endstylesheet %}

{% javascript %}
  function doSomething() {
    // example
  }
  doSomething()
{% endjavascript %}

```

### `block`

#### Text

```liquid
{% doc %}
  Renders a text block.

  @example
  {% content_for 'block', type: 'text', id: 'text' %}
{% enddoc %}

<div
  class="text {{ block.settings.text_style }}"
  style="--text-align: {{ block.settings.alignment }}"
  {{ block.shopify_attributes }}
>
  {{ block.settings.text }}
</div>

{% stylesheet %}
  .text {
    text-align: var(--text-align);
  }
  .text--title {
    font-size: 2rem;
    font-weight: 700;
  }
  .text--subtitle {
    font-size: 1.5rem;
  }
{% endstylesheet %}

{% schema %}
{
  "name": "t:general.text",
  "settings": [
    {
      "type": "text",
      "id": "text",
      "label": "t:labels.text",
      "default": "Text"
    },
    {
      "type": "select",
      "id": "text_style",
      "label": "t:labels.text_style",
      "options": [
        { "value": "text--title", "label": "t:options.text_style.title" },
        { "value": "text--subtitle", "label": "t:options.text_style.subtitle" },
        { "value": "text--normal", "label": "t:options.text_style.normal" }
      ],
      "default": "text--title"
    },
    {
      "type": "text_alignment",
      "id": "alignment",
      "label": "t:labels.alignment",
      "default": "left"
    }
  ],
  "presets": [{ "name": "t:general.text" }]
}
{% endschema %}
```

#### Group

```liquid
{% doc %}
  Renders a group of blocks with configurable layout direction, gap and
  alignment.

  All settings apply to only one dimension to reduce configuration complexity.

  This component is a wrapper concerned only with rendering its children in
  the specified layout direction with appropriate padding and alignment.

  @example
  {% content_for 'block', type: 'group', id: 'group' %}
{% enddoc %}

<div
  class="group {{ block.settings.layout_direction }}"
  style="
    --padding: {{ block.settings.padding }}px;
    --alignment: {{ block.settings.alignment }};
  "
  {{ block.shopify_attributes }}
>
  {% content_for 'blocks' %}
</div>

{% stylesheet %}
  .group {
    display: flex;
    flex-wrap: nowrap;
    overflow: hidden;
    width: 100%;
  }
  .group--horizontal {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
    padding: 0 var(--padding);
  }
  .group--vertical {
    flex-direction: column;
    align-items: var(--alignment);
    padding: var(--padding) 0;
  }
{% endstylesheet %}

{% schema %}
{
  "name": "t:general.group",
  "blocks": [{ "type": "@theme" }],
  "settings": [
    {
      "type": "select",
      "id": "layout_direction",
      "label": "t:labels.layout_direction",
      "default": "group--vertical",
      "options": [
        { "value": "group--horizontal", "label": "t:options.direction.horizontal" },
        { "value": "group--vertical", "label": "t:options.direction.vertical" }
      ]
    },
    {
      "visible_if": "{{ block.settings.layout_direction == 'group--vertical' }}",
      "type": "select",
      "id": "alignment",
      "label": "t:labels.alignment",
      "default": "flex-start",
      "options": [
        { "value": "flex-start", "label": "t:options.alignment.left" },
        { "value": "center", "label": "t:options.alignment.center" },
        { "value": "flex-end", "label": "t:options.alignment.right" }
      ]
    },
    {
      "type": "range",
      "id": "padding",
      "label": "t:labels.padding",
      "default": 0,
      "min": 0,
      "max": 200,
      "step": 2,
      "unit": "px"
    }
  ],
  "presets": [
    {
      "name": "t:general.column",
      "category": "t:general.layout",
      "settings": {
        "layout_direction": "group--vertical",
        "alignment": "flex-start",
        "padding": 0
      }
    },
    {
      "name": "t:general.row",
      "category": "t:general.layout",
      "settings": {
        "layout_direction": "group--horizontal",
        "padding": 0
      }
    }
  ]
}
{% endschema %}
```

### `section`

```liquid
<div class="example-section full-width">
  {% if section.settings.background_image %}
    <div class="example-section__background">
      {{ section.settings.background_image | image_url: width: 2000 | image_tag }}
    </div>
  {% endif %}

  <div class="custom-section__content">
    {% content_for 'blocks' %}
  </div>
</div>

{% stylesheet %}
  .example-section {
    position: relative;
    overflow: hidden;
    width: 100%;
  }
  .example-section__background {
    position: absolute;
    width: 100%;
    height: 100%;
    z-index: -1;
    overflow: hidden;
  }
  .example-section__background img {
    position: absolute;
    width: 100%;
    height: auto;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
  .example-section__content {
    display: grid;
    grid-template-columns: var(--content-grid);
  }

  .example-section__content > * {
    grid-column: 2;
  }
{% endstylesheet %}

{% schema %}
{
  "name": "t:general.custom_section",
  "blocks": [{ "type": "@theme" }],
  "settings": [
    {
      "type": "image_picker",
      "id": "background_image",
      "label": "t:labels.background"
    }
  ],
  "presets": [
    {
      "name": "t:general.custom_section"
    }
  ]
}
{% endschema %}
```


<!-- SPECKIT START -->
For additional context about technologies to be used, project structure,
shell commands, and other important information, read the current plan at
specs/001-multi-lang-currency/plan.md
<!-- SPECKIT END -->

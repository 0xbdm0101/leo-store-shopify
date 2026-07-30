# Data Model: Ajax Cart Drawer

**Feature**: [spec.md](file:///Users/leo/Downloads/web/leo-store-shopify/specs/002-cart-drawer/spec.md)

## Entities

### 1. CartDrawerState
Represents the current UI and data state of the cart drawer.

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `isOpen` | Boolean | Whether the slide-out drawer is currently visible (`aria-hidden="false"`) |
| `itemCount` | Number | Total quantity of items in the cart (`cart.item_count`) |
| `totalPrice` | Number | Total cart price in cents (`cart.total_price`) |
| `items` | Array<CartLineItem> | List of current line items |
| `freeShipping` | FreeShippingState | Calculated free shipping threshold state |

### 2. CartLineItem
Represents an item in the cart drawer.

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `key` | String | Unique line item key (`item.key`) |
| `id` | Number | Variant ID (`item.id`) |
| `title` | String | Product title + variant title |
| `quantity` | Number | Current line quantity |
| `linePrice` | Number | Total price for this line item in cents |
| `image` | String | Featured image URL (`item.image | image_url: width: 150`) |
| `discounts` | Array<Discount> | Applied discounts for this line |

### 3. FreeShippingState
Represents the progress toward unlocking free shipping.

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `thresholdCents` | Number | Target free shipping amount in cents (from schema settings) |
| `currentTotalCents` | Number | Current cart total price in cents |
| `remainingCents` | Number | Cents remaining to unlock free shipping (`max(0, threshold - current)`) |
| `percentage` | Number | Progress bar fill percentage (`min(100, Math.round(current / threshold * 100))`) |
| `isUnlocked` | Boolean | `true` if `currentTotalCents >= thresholdCents` |

### 4. UpsellProduct
Represents a recommended product displayed inside the cart drawer.

| Attribute | Type | Description |
| :--- | :--- | :--- |
| `variantId` | Number | Available variant ID for 1-click addition |
| `title` | String | Product title |
| `featuredImage` | String | Product thumbnail image URL |
| `price` | Number | Variant price in cents |
| `inCart` | Boolean | `true` if this product is already in `cart.items` (hidden from list) |

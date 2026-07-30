# Contract: Shopify Cart Ajax API Endpoints

This contract documents the client-side API endpoints consumed by `cart-drawer.liquid`.

## 1. Fetch Cart State (`GET /cart.js`)

Returns full JSON object of the current cart state.

### Response `200 OK`
```json
{
  "token": "a1b2c3d4...",
  "note": null,
  "attributes": {},
  "original_total_price": 5000,
  "total_price": 5000,
  "total_discount": 0,
  "total_weight": 200,
  "item_count": 2,
  "items": [
    {
      "id": 410012345678,
      "key": "410012345678:a9b8c7...",
      "quantity": 2,
      "title": "Classic T-Shirt - Black / M",
      "price": 2500,
      "line_price": 5000,
      "image": "https://cdn.shopify.com/s/files/1/..."
    }
  ]
}
```

## 2. Update Item Quantity (`POST /cart/change.js`)

Modifies line item quantity by key or line index.

### Request Body (`application/json`)
```json
{
  "id": "410012345678:a9b8c7...",
  "quantity": 3,
  "sections": "cart-drawer"
}
```

### Response `200 OK`
Returns updated cart object + rendered HTML section snippet under `sections["cart-drawer"]`.

## 3. Add Item to Cart (`POST /cart/add.js`)

Adds a variant to the cart (used by product page Add to Cart & Upsell 1-click Add buttons).

### Request Body (`application/json`)
```json
{
  "items": [
    {
      "id": 410012345678,
      "quantity": 1
    }
  ],
  "sections": "cart-drawer"
}
```

### Response `200 OK`
```json
{
  "items": [...],
  "sections": {
    "cart-drawer": "<div id=\"shopify-section-cart-drawer\" class=\"shopify-section\">...</div>"
  }
}
```

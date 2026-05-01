# Discount-Widget Design 1
Discount Widget Liquid Code

# Preorder Discount Widget — Design 1

A Shopify Liquid snippet that shows an **Early Bird vs Launch Price** comparison widget on your product page — automatically calculated, automatically hidden when conditions aren't met.

Built for the **Essential Preorder & Back In Stock** app. Tested on **Horizon**.

---

## What It Looks Like

```
┌─────────────────────┐       ┌─────────────────────┐
│      Save 38%       │       │    Regular Price     │
│                     │  ›    │                      │
│      $49.00         │       │     ~~$79.00~~       │
│     EARLY BIRD      │       │    LAUNCH PRICE      │
└─────────────────────┘       └─────────────────────┘
```

Left card → preorder price  
Right card → compare-at price, struck through  
Badge → discount auto-calculated (percent or flat amount)

---

## Requirements

| | Requirement |
|---|---|
| ✅ | Essential Preorder & Back In Stock app installed |
| ✅ | App embed enabled in Theme Editor |
| ✅ | Compare-at price set on the variant (must be higher than Price) |

> No compare-at price = widget stays hidden. Always set a compare-at price on preorder variants.

---

## Files in This Repo

```
snippets/
└── preorder-discount-widget.liquid   ← the widget (copy this to your theme)
```

---

## Installation

### Step 1 — Add the snippet to your theme

In Shopify Admin go to **Online Store → Themes → Edit code**.

Inside the **Snippets** folder, create a new file named exactly:

```
preorder-discount-widget.liquid
```

Paste the contents of `snippets/preorder-discount-widget.liquid` from this repo and save.

---

### Step 2 — Inject into your buy buttons file

Open `blocks/buy-buttons.liquid` (Horizon) or the equivalent for your theme.

Add the render tag **above the add-to-cart button, inside the product form**:

```liquid
{%- comment -%} Preorder Discount Widget {%- endcomment -%}
{% render 'preorder-discount-widget' %}

<div class="product-form-buttons" ref="productFormButtons">
  ...add-to-cart button lives here...
</div>
```

> ⚠️ The tag must be inside `{%- form -%} ... {%- endform -%}` or variant switching won't update the widget.

---

### Step 3 — Enable the app embed

In **Theme Editor → App embeds**, toggle on **Essential Preorder & Back In Stock**. Save.

---

### Step 4 — Set compare-at price

In the product editor, for each preorder variant set a **Compare-at price** that is higher than the **Price**. This is what creates the strikethrough and discount calculation.

---

## Where to Inject — By Theme

The rule: **inside the product form, above the add-to-cart button.**

| Theme | File | Place render tag before |
|---|---|---|
| Horizon | `blocks/buy-buttons.liquid` | `product-form-buttons` div |
| Dawn | `snippets/buy-buttons.liquid` | Add to cart button block |
| Impulse | `snippets/product-form.liquid` | Add to cart button |
| Prestige | `sections/product.liquid` | buy_buttons block render |
| Debut | `sections/product-template.liquid` | form "product" open tag |
| Other | Search for `type="add-to-cart-form"` | Above add-to-cart, inside the form |

---

## Customization

### Discount format

At the top of the snippet, one variable controls the badge text:

```liquid
{% assign pw_discount_format = 'percent' %}
```

| Value | Badge shows |
|---|---|
| `'percent'` | Save 38% |
| `'amount'` | Save $30.00 |

### Colors and spacing

All styles are in the `<style>` block at the top of the snippet file. Key selectors:

```css
.preorder-widget__card--active   /* early bird card */
.preorder-widget__card--future   /* launch price card */
.preorder-widget__badge          /* the top banner label */
```

---

## How It Decides to Show

```
Selected variant
    │
    ├── Is preorder active?  (.essential-preorder-container-active in DOM)
    │       NO  → widget hidden
    │
    └── Is there a real discount?  (compare-at > price, both > 0)
            NO  → widget hidden

Both YES → widget shows, discount calculated automatically
```

The widget updates on every variant switch. No page reload needed.

---

## Troubleshooting

**Widget not showing**
- App embed is off → enable it in Theme Editor
- No compare-at price set → add one in the product editor
- Render tag is outside the form block → move it inside `{%- form -%}`

**Strikethrough price not visible**
- Compare-at price equals or is lower than the regular price → must be strictly higher

**Wrong discount shown**
- Check the compare-at and price fields in the product editor match what you expect

**Not updating on variant switch**
- Render tag is outside the form block — this is almost always the cause

**Liquid error: snippet not found**
- File must be named `preorder-discount-widget.liquid` exactly — all lowercase, hyphens

---

## Testing Checklist

- [ ] Widget appears above Add to Cart on a preorder variant
- [ ] Left card shows early-bird price (no strikethrough)
- [ ] Right card shows compare-at price with strikethrough
- [ ] Badge shows correct % or $ saving
- [ ] Widget hides when a non-preorder variant is selected
- [ ] Widget reappears when switching back to preorder variant
- [ ] On mobile — both cards remain side by side, text readable
- [ ] No empty gap when widget is hidden

---

## Notes

- This widget reads directly from the Essential Preorder app's injected DOM elements. Do not rename the `data-essential-preorder-element` attributes.
- Safe to use on stores where the app is not installed — the widget exits silently with no errors.
- Safe to render multiple times on the same page (quick-buy drawers, etc.) — the execution guard ensures JS runs only once.

---

## License

Internal use. Not for redistribution.

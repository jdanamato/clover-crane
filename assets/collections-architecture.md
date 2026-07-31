## Collections Architecture

> **⛔️ Staging is deprecated as a reference (2026-07-30).** Only look at
> `cloverandcrane.myshopify.com` (production). Notes below that cite staging (e.g.
> `store_portal` "verified on staging") are historical — re-verify against production
> before relying on them. See `site-transfer-reference.md` for the full rationale.
 
Inspired by markandgraham.com's two-axis browsing model, extended to three axes for C&C's services focus. Products can belong to collections across all three axes simultaneously.
 
### Axis 1 — Product Type (what it is)
 
| Collection Title | Handle |
| --- | --- |
| Bags & Totes | `bags-totes` |
| Accessories | `accessories` |
| Home & Décor | `home-decor` |
| Stationery & Paper | `stationery` |
| Apparel & Clothing | `apparel` |
| New Arrivals | `new-arrivals` |
 
### Axis 2 — Occasion (why they're buying it)
 
Powered `linklists['occasions']` on the home page's Inspiration section. **That section was deleted from theme source** (see Navigation Menus below) — this axis's collections are still valid for browsing/tagging purposes, just not currently wired to a menu-driven section.
 
| Collection Title | Handle |
| --- | --- |
| Wedding & Bridal | `wedding-gifts` |
| Corporate Gifting | `corporate-gifts` |
| Holiday Gifts | `holiday-gifts` |
| Graduation | `graduation-gifts` |
| New Baby | `new-baby-gifts` |
| Birthday | `birthday-gifts` |
| Thank You | `thank-you-gifts` |
 
### Axis 3 — Service (what can be done to it)
 
Powered `linklists['services']` on the home page's Inspiration section. **That section was deleted from theme source** (see Navigation Menus below) — this axis's collections are still valid for browsing/tagging purposes, just not currently wired to a menu-driven section.
 
| Collection Title | Handle |
| --- | --- |
| Monogramming | `monogramming` |
| Custom Embroidery | `embroidery` |
| Laser Engraving | `laser-engraving` |
| Corporate Branding | `corporate-branding` |
 
> The `monogramming` collection can be automated via Smart Collection using the `custom.monogramming_enabled = true` metafield condition.
>
 
### Collection Metafields
 
| Key | Type | Namespace | Purpose |
| --- | --- | --- | --- |
| ~~`promoted`~~ | ~~Boolean~~ | ~~`collection`~~ | **Superseded 2026-07-24** — Promoted section is now driven by the `promoted` linklist (see Navigation Menus below), not this metafield. No longer needs to be created on any store; safe to ignore if it already exists on production/staging from before. |
| `collection_axis` | Single-line text | `custom` | `product_type`, `occasion`, or `service` |
| `featured_image_hover` | Image | `custom` | Secondary image for card hover states |
| `short_description` | Multi-line text | `custom` | Short version for cards/sliders (full description used in Inspiration section) |
 
---
 
## Shopify Navigation Menus (Linklists)
 
| Menu Name | Handle | Used by |
| --- | --- | --- |
| ~~Services~~ | ~~`services`~~ | **Not needed (2026-07-29)** — only consumer was the Inspiration section, which has been deleted from theme source (no `_sections/Inspiration.html`, no `linklists['services']` reference anywhere in current markup). Don't create this menu; if the orphaned Inspiration section is still showing on a live store, remove it via Admin → Customize instead of feeding it a menu. |
| ~~Occasions~~ | ~~`occasions`~~ | **Not needed (2026-07-29)** — same reasoning as Services above; only consumer was the deleted Inspiration section. |
| Main Menu | `main-menu` | Header navigation |
| Footer | `footer` | Footer navigation |
| Featured | `featured` | `linklists['featured']` — Header sub-nav row, only shows if the menu has links ([Header.html](../_sections/Header.html)) |
| ~~Promoted~~ | ~~`promoted`~~ | **Superseded same day (2026-07-24)** — Promoted.html was rebuilt again, same day, into a mixed-block section (`li-content-for-blocks` with "Promoted Panel" + "Collection Slider" block types, each with its own `li-settings:custom` collection picker). The menu-based version was a same-day stepping stone; the block-based version replaced it before ever reaching production. The `promoted` menu that was created on staging is now unused — harmless to leave, safe to delete. |
 
---
 
## Product Metafields — Monogramming System
 
All in namespace: **`custom`**
 
| Definition Name | Key | Type | Pinned | Notes |
| --- | --- | --- | --- | --- |
| Monogramming Enabled | `monogramming_enabled` | True or false | ✅ | Master on/off switch |
| Monogram Styles | `monogram_styles` | List · Metaobject (`monogram_style`) | ✅ | Available styles for this product |
| Monogram Text Colors | `monogram_text_colors` | List · Metaobject (`text_color`) | ✅ | Available text colors for personalization |
| Monogram Placement | `monogram_placement` | List · Metaobject (`monogram_position`) | ✅ | Leave empty = no placement picker shown |
| Monogram Max Characters | `monogram_max_characters` | Integer | ✅ | Sets `maxlength` on the product page text input |
| Personalization Cost | `personalization_cost` | Money | ✅ | Upcharge amount (requires implementation plan — see below) |
 
### Metafield Options Settings
 
**`monogramming_enabled` only:**
 
- Filter on product list: **ON**
- Use as condition in smart collections: **ON** (auto-populates `monogramming` collection)
- Storefront API access: **ON**
- Filter or group data in Analytics: **ON**
**All other monogramming metafields:**
 
- Filter on product list: **OFF**
- Use as condition in smart collections: **OFF**
- Storefront API access: **ON**
- Analytics: **OFF**
### Liquid Access Pattern
 
```
product.metafields.custom.monogramming_enabled
product.metafields.custom.monogram_styles
product.metafields.custom.monogram_text_colors
product.metafields.custom.monogram_placement
product.metafields.custom.monogram_max_characters
product.metafields.custom.personalization_cost
```
 
### Placement — Per-Product Control
 
Leave `monogram_placement` empty on products that don't need a placement picker.
Wrap the placement UI in Webflow with:
 
```
li-if = product.metafields.custom.monogram_placement != blank
```
 
This hides the entire section (label included) when no placement options are assigned.
 
---
 
## Metaobjects
 
### `gift_guide` ✅ Already configured
 
| Field Key | Type | Notes |
| --- | --- | --- |
| `name` | Single-line text | Display name |
| `featured_image` | Image | Card thumbnail |
| `description` | Multi-line text | Optional |
| `linked_collection` | Collection reference | Optional link target |
 
### `store_portal` ✅ Already configured (as "B2B Store" on staging)

**Correction (2026-07-24):** previously documented as just `name` + `featured_image` — that was stale/wrong. Real definition on staging (verified via Admin API), display name **"B2B Store"**:

| Field Key | Type | Notes |
| --- | --- | --- |
| `name` | Single-line text | Store/organization name |
| `description` | Multi-line text | Short blurb — now shown on the Home page "Find Your Store" card |
| `logo` | File reference | Not currently rendered on Home; available if needed |
| `featured_image` | Image | Card thumbnail |
| `featured_product` | Product reference | Not currently rendered on Home; available if needed |
| `products` | List · product reference | Curated product list for this store/organization's portal — not rendered on Home (Home only shows the card), presumably used on a dedicated portal page |

Confirmed sufficient as-is for "let customers find their org's dedicated portal without paying for Shopify B2B" (2026-07-24) — no schema changes needed. Production doesn't have this metaobject type at all yet (confirmed in the Production Store Audit, `site-transfer-reference.md`) — needs creating there with this same field set before launch.
 
### `monogram_style` ✅ Already configured
 
| Field Key | Type | Notes |
| --- | --- | --- |
| `style_label` | Single-line text (required) | Display name e.g. "Classic Script". **Key is `style_label`, not `label`** — this doc said `label` until 2026-07-30 and the Product Hero markup followed the doc, so every style radio would have rendered with an empty label and submitted an empty `properties[Design]`. Verified against production's live definition; markup corrected to `style.style_label`. Metaobject field keys are immutable in Shopify, so the markup is the side that moves. |
| `style_image` | Image (required) | Sample lettering image |
| `max_characters` | Integer (optional) | Per-style character limit, drives `maxlength` on the monogram text input. Added to production 2026-07-30. Leave empty to inherit the product's `custom.monogram_max_characters` metafield (which itself falls back to 3). |

**0 entries exist on production** as of 2026-07-30 — the styles themselves still have to be created before the picker shows anything.
 
### `text_color` ✅ Already configured
 
| Field Key | Type | Notes |
| --- | --- | --- |
| `name` | Single-line text | Color name e.g. "Navy" — shown to customer and on order |
| `color_code` | Color | Hex value for CSS swatch rendering (`color` key is reserved by Shopify) |
 
### `monogram_position` ✅ Already configured
 
| Field Key | Type | Notes |
| --- | --- | --- |
| `placement_option` | Choice list (Single-line text) | Position in 3×3 grid: Top Left, Top Center, Top Right, Middle Left, Middle Center, Middle Right, Bottom Left, Bottom Center, Bottom Right |
 
> No preview images needed — the 3×3 CSS grid layout communicates position spatially. Values are ordered left-to-right, top-to-bottom, so a 3-column grid renders them correctly from the loop.
>
 
---
 
## Monogramming on the Product Page
 
### Liquiflow Structure (built directly in `_sections/Product Hero.html`, inside the `<template x-if="personalization">` block)
 
```
Monogram section wrapper
  li-if = product.metafields.custom.monogramming_enabled
 
  ── Style picker wrapper
     li-if = product.metafields.custom.monogram_styles != blank
       ── Style item
          li-for = style in product.metafields.custom.monogram_styles.value
            li-object:src = style.style_image | image_url: width: 200
            li-object     = style.label
            (hidden input) name="properties[Monogram Style]"
 
  ── Color picker wrapper
     li-if = product.metafields.custom.monogram_text_colors != blank
       ── Color swatch
          li-for = color in product.metafields.custom.monogram_text_colors.value
            li-object     = color.name
            (CSS swatch)  color.color_code via li-attribute:style
 
  ── Placement picker wrapper (3×3 CSS grid, 3 columns)
     li-if = product.metafields.custom.monogram_placement != blank
       ── Placement option
          li-for = placement in product.metafields.custom.monogram_placement.value
            li-object = placement.placement_option
            (hidden input) name="properties[Placement]"
 
  ── Text input
     <input type="text"
            name="properties[Monogram Text]"
            li-attribute:maxlength="product.metafields.custom.monogram_max_characters">
 
     Pattern (static HTML attribute, not a metafield):
     pattern = ^[a-zA-Z0-9 .\-]+$
```
 
> The regex `^[a-zA-Z0-9 .\-]+$` allows letters, numbers, spaces, periods, and hyphens — covers initials like "J.E.B" and names like "Anne-Marie". Set as a static `pattern` attribute on the input in Webflow.
>
 
### How Customer Selections Are Stored
 
All selections are submitted as **Shopify line item properties** — they travel with the cart item through to the order and appear in Shopify Admin, packing slips, and fulfillment notifications automatically.
 
```
name="properties[Monogram Style]"
name="properties[Text Color]"
name="properties[Placement]"
name="properties[Monogram Text]"
```
 
### Personalization Cost — Implementation Options
 
**Option A (Recommended): Separate "Monogramming Service" product**
 
- Create a product called "Monogramming Service" with the upcharge price
- JavaScript on the product page adds this service product to the cart alongside the main item when monogramming is selected
- Clean, visible on the order, no Shopify Plus required
**Option B: Variant-based pricing**
 
- Create "Without Monogram" and "With Monogram" variants at different prices
- Simpler but less flexible if upcharge varies by product
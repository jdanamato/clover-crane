# Clover & Crane — Site Transfer Reference
 
## Overview
 
Because C&C already has an active Shopify store, this is **not a store transfer** — it's a **theme push**. The dev store (`clover-crane-staging.myshopify.com`) stays in your Partner account; only the finished theme gets pushed to C&C's live store. Their existing products, orders, customers, and settings are untouched.
 
---
 
## Environment Config
 
`shopify.theme.toml` in the theme root:
 
```toml
[environments.staging]
store = "clover-crane-staging.myshopify.com"
 
[environments.production]
store = "clover-and-crane.myshopify.com"
```
 
---
 
## Pre-Transfer Checklist
 
- [ ] All sections built and audited (Home, Product, Cart, Collection)
- [ ] Tested on staging with real product/metafield data
- [ ] Navigation menus exist on C&C's store (`services`, `occasions`, `main-menu`, `footer`)
- [ ] Metaobjects exist on C&C's store (`gift_guide`, `store_portal`, `monogram_style`, `text_color`, `monogram_position`)
- [ ] Collection metafields defined on C&C's store (`promoted`, `collection_axis`, `featured_image_hover`, `short_description`)
- [ ] Product metafields defined on C&C's store (all `custom` namespace monogramming fields)
- [ ] `section-rendering.js` asset present in C&C's theme assets
- [ ] "Monogramming Service" product created on C&C's store, priced at the personalization upcharge; its variant ID entered into the Product Hero section's "Monogramming Service Variant ID" setting in the Theme Editor (see `project-reference.md` → Personalization Upcharge)
---
 
## Transfer Steps
 
**1. Push the theme to C&C's store as a draft**
 
```bash
shopify theme push --environment production --unpublished
```
 
This uploads the theme without activating it. Nothing goes live yet.
 
**2. Preview on C&C's store**
 
In C&C's Shopify admin → Online Store → Themes, find the newly pushed theme and click **Preview**. Review against real store data.
 
**3. Publish**
 
Once approved, C&C clicks **Publish** on the theme, or you do it via CLI:
 
```bash
shopify theme publish --environment production
```
 
---
 
## What Doesn't Transfer Automatically
 
These live in the Shopify store, not the theme files. They need to exist on C&C's store *before* the theme goes live or certain sections will render blank.
 
| Item | Where to set it |
|---|---|
| Navigation menus | C&C Admin → Content → Navigation |
| Metaobject definitions + entries | C&C Admin → Content → Metaobjects |
| Collection metafield definitions | C&C Admin → Settings → Custom data → Collections |
| Product metafield definitions | C&C Admin → Settings → Custom data → Products |
| `promoted` flag on collections | Set per-collection in C&C Admin |
| `section-rendering.js` | Upload to C&C Admin → Online Store → Themes → Assets |
 
---
 
## Notes
 
- `divmks.myshopify.com` appearing in your staging store's domain settings is normal — it's Shopify's auto-generated internal redirect domain. Ignore it; always use `clover-crane-staging` in CLI commands.
- The staging store remains in your Partner account indefinitely after the theme push. Keep it as a sandbox for future changes before pushing updates to production.
- For future theme updates: edit on staging → test → `shopify theme push --environment production`.
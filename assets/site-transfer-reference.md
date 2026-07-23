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
store = "cloverandcrane.myshopify.com"
```

**Correction (2026-07-23):** the production store's real `myshopifyDomain` is `cloverandcrane.myshopify.com` — no hyphens. `clover-and-crane.myshopify.com` (the hyphenated form used everywhere in this doc and `CLAUDE.md` previously) 404s on `shopify theme list`. `shopify.theme.toml` did not exist in the repo at all as of this date — see the "Production Store Audit" section below.

Staging storefront password (password-protected store, needed to view any page): `auyahn`
 
---
 
## Pre-Transfer Checklist
 
- [ ] All sections built and audited (Home, Product, Cart, Collection)
- [ ] Tested on staging with real product/metafield data
- [ ] Navigation menus exist on C&C's store (`services`, `occasions`, `main-menu`, `footer`, `featured`, `promoted`)
- [ ] Metaobjects exist on C&C's store (`gift_guide`, `store_portal`, `monogram_style`, `text_color`, `monogram_position`)
- [ ] Collection metafields defined on C&C's store (`collection_axis`, `featured_image_hover`, `short_description` — `promoted` retired 2026-07-24, see collections-architecture.md)
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
| Navigation menus (`services`, `occasions`, `main-menu`, `footer`, `featured`, `promoted`) | C&C Admin → Content → Navigation |
| Metaobject definitions + entries | C&C Admin → Content → Metaobjects |
| Collection metafield definitions | C&C Admin → Settings → Custom data → Collections |
| Product metafield definitions | C&C Admin → Settings → Custom data → Products |
| Which collections show in the Promoted section, and in what order | `promoted` menu links, C&C Admin → Content → Navigation (not a per-collection flag anymore) |
| `section-rendering.js` | Upload to C&C Admin → Online Store → Themes → Assets |
 
---
 
## Notes
 
- `divmks.myshopify.com` appearing in your staging store's domain settings is normal — it's Shopify's auto-generated internal redirect domain. Ignore it; always use `clover-crane-staging` in CLI commands.
- The staging store remains in your Partner account indefinitely after the theme push. Keep it as a sandbox for future changes before pushing updates to production.
- For future theme updates: edit on staging → test → `shopify theme push --environment production`.

---

## Production Store Audit (2026-07-23)

Read directly from `cloverandcrane.myshopify.com` via `shopify store auth` + `shopify store execute` (Admin GraphQL, read-only scopes: `read_products`, `read_content`, `read_online_store_navigation`, `read_metaobjects`, `read_metaobject_definitions`). Store: " Clover and Crane" (note leading space in the Shop name field), plan Basic, primary domain `cloverandcrane.org`, currently running stock **Dawn** (live) with a second unpublished Dawn copy. Nothing from this theme has been pushed yet.

### Catalog snapshot

| | |
|---|---|
| Products | 170 (mostly `DRAFT` — spot check of the first 42 showed far more draft than active) |
| Collections | 11 |
| Pages | 5 (`contact`, `fqa`, `contact-us`, `catalog`, `care-instructions`) |
| Menus | `main-menu`, `footer`, `designs` (empty), `customer-account-main-menu` — all Dawn defaults |

**The live catalog is school/team spirit-wear — this is expected, not a mismatch.** C&C is mid-pivot: spirit-wear is the current business, personalized gifting (smaller Mark and Graham) is the target the theme is being built for ahead of the catalog transition. Titles include "HMS Spring 2026 Spirit Wear," "HHS Boys Soccer," "Crewneck Raider Soccer," Carhartt jackets, team hoodies in school colors. Collections: `Corporate`, `HMS Spring 2026 Spirit Wear` (32 products), `Entertaining` (handle `cake-topper`), `Bags` (handle `wedding`), `Accessories` (handle `silicone-bento-boxes`), `Home`, `Sports` (0 products), `Baby & Kids` (37 products), `Wedding` (handle `wedding-1`), `HHS Boys Soccer` (18 products), `HHFH`. Several collection **titles and handles don't match** (e.g. title "Bags" → handle `wedding`), suggesting collections were relabeled/repurposed after creation rather than built fresh — check each one before relying on its handle in Liquid.

Product metafields present: only `customify.cstmfy_req` (a third-party personalization app, "Customify," already live and unrelated to Liquiflow's monogram system) and Shopify's own auto-generated `shopify.*` category taxonomy fields (`color-pattern`, `fabric`, `size`, `neckline`, etc. — these power the `.swatch` lookup issue already logged in `liquiflow-rules-and-gotchas.md`). There's also a hidden `DRAFT` product "Item Personalization" (`productType: PPLR_HIDDEN_PRODUCT`) — service product for a different personalization app, not this project's Monogramming Service.

### Pre-Transfer Checklist — actual status

- [x] ~~All sections built and audited~~ — out of scope for this audit, unchanged
- [ ] Tested on staging with real product/metafield data — **staging's product/metafield data doesn't resemble production's**; testing there won't catch the gaps below
- [ ] **Navigation menus** — `main-menu`/`footer` exist but are generic Dawn menus; **`services`, `occasions`, `featured`, and `promoted` linklists don't exist at all**. Every collections-browsing section that reads `linklists['services'].links` / `linklists['occasions'].links` will render empty; the Header sub-nav (`linklists['featured']`) and Home's Promoted section (`linklists['promoted']`) will just not render — both are guarded with `li-if`/an empty `li-for`, so they fail safe rather than error, but Home will show no featured collections until this menu exists.
- [ ] **Metaobjects** — none of `gift_guide`, `store_portal`, `monogram_style`, `text_color`, `monogram_position` exist. Only Shopify's built-in `shopify--*` taxonomy metaobjects (color, size, fabric, etc., auto-created by product categorization) are present.
- [ ] **Collection metafields** — zero. All 11 collections returned `metafields: []`. `collection_axis`, `featured_image_hover`, `short_description` are undefined store-wide, not just unset per-collection. (`promoted` no longer needed — retired 2026-07-24 in favor of the `promoted` linklist.)
- [ ] **Product metafields** — the `custom` namespace monogramming fields don't exist on any product. The only non-taxonomy metafield in the catalog is the unrelated `customify.cstmfy_req`.
- [ ] `section-rendering.js` asset — can't check pre-push (no theme asset list to inspect); revisit after the draft push.
- [ ] **"Monogramming Service" product** — doesn't exist. Product search for `title:*Monogram*` returned nothing.

### What this means before pushing

Confirmed with Joe (2026-07-23): spirit-wear is the current business, gifting is the deliberate target — the theme is intentionally ahead of the store's content. So this isn't "fix a mismatch," it's "the pivot's content work hasn't started yet." Recommended order, in addition to the existing checklist:

1. **The gifting content (`services`/`occasions` menus, monogram metaobjects, gift-guide content, Monogramming Service product) still has to be created on production before those sections render correctly** — building the theme ahead of the catalog doesn't remove this work, it just means it's a known, expected step rather than a surprise. No client conversation needed about "which catalog is real" — both are, sequentially.
2. **Decide what happens to Customify.** It's a live, working personalization app on real spirit-wear products right now (`customify.cstmfy_req` metafield, presumably a storefront widget). Pushing the new theme unpublished is safe, but publishing it may silently break or duplicate Customify's UI if nobody accounts for it — check whether it injects theme-app-extension blocks that need re-placing in the new theme's product template. Worth clarifying whether Customify gets retired as part of the gifting pivot or needs to coexist during a transition window.
3. Everything else on the original checklist (metaobjects, collection/product metafield definitions, `services`/`occasions` menus, Monogramming Service product) still needs to be created from scratch on production — staging having them doesn't transfer.
4. Push as `--unpublished` first regardless (per the existing Transfer Steps) and preview against this real data before anyone considers publishing — spirit-wear products will flow through gifting-shaped templates (no monogram fields, no service/occasion collections) until the pivot's content lands, so expect the preview to look incomplete by design, not broken.
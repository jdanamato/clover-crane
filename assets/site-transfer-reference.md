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
- [ ] Navigation menus exist on C&C's store (`main-menu`, `footer`, `featured`) — `services`/`occasions`/`promoted` are **not needed**, see below
- [ ] Metaobjects exist on C&C's store (`store_portal`, `monogram_style`, `text_color`, `monogram_position`) — `gift_guide` is **not needed** (dead, unreferenced in current theme source, deleted from staging 2026-07-29)
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
| Navigation menus (`main-menu`, `footer`, `featured`) | C&C Admin → Content → Navigation |
| Metaobject definitions + entries | C&C Admin → Content → Metaobjects |
| Collection metafield definitions | C&C Admin → Settings → Custom data → Collections |
| Product metafield definitions | C&C Admin → Settings → Custom data → Products |
| Which collections show in the Promoted section, and in what order | Per-block collection picker on the Promoted section itself, C&C Admin → Customize (not menu-driven — see `collections-architecture.md`) |
| `section-rendering.js` | Upload to C&C Admin → Online Store → Themes → Assets |
| Shopify Forms app (powers the Contact Form section's app block — `_sections/Contact Form.html`, used on Contact + Password pages) | Installed on staging (2026-07-24), **not installed on production yet** — install via C&C Admin → Apps before launch, then add the form block through the Theme Editor on both pages |
 
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

---

## Production Metaobjects/Metafields Audit (2026-07-29)

Re-audited via `shopify store execute` (Admin GraphQL) against `cloverandcrane.myshopify.com`, then created the missing definitions directly via CLI mutations (`metaobjectDefinitionCreate` / `metafieldDefinitionCreate`), mirroring staging's exact field schema. Also ran the same essential-vs-not audit against staging first and deleted what was unreferenced anywhere in current theme source.

**Corrections to the 2026-07-23 audit above:**
- `gift_guide` metaobject is **not needed** — its only consumer (the Inspiration section) was deleted from theme source; deleted from staging, never created on production.
- `services`/`occasions`/`promoted` navigation menus are **not needed** — see `collections-architecture.md` → Navigation Menus table for the full reasoning (Inspiration section deleted; Promoted section rebuilt as block-based, not menu-driven).
- `collection_axis`/`featured_image_hover`/`short_description` collection metafields: confirmed still unreferenced anywhere in current theme markup, same as 2026-07-23. Not created on either store. Revisit only if a future section actually starts reading them.

**Current production status (as of 2026-07-29):**
- [x] Metaobjects: `store_portal`, `monogram_style`, `text_color`, `monogram_position` — all created
- [x] Product metafields: all `custom` namespace monogramming fields — all created (`monogramming_enabled`, `personalization_cost`, `delivery_time`, `monogram_styles`, `monogram_text_colors`, `monogram_placement`, `monogram_max_characters`)
- [ ] Navigation menus: `main-menu`/`footer` exist (generic Dawn); `featured` still missing — needed for the Header sub-nav row
- [ ] "Monogramming Service" product: exists but `DRAFT` status, $0.00 price — needs real pricing + Active status before launch (variant ID `48705471414521` for the Product Hero setting)
- [ ] `section-rendering.js` asset — still unverified, check after draft push
- [ ] Shopify Forms app — still not installed on production (per table above)
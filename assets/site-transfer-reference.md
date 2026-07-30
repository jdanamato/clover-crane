# Clover & Crane — Site Transfer Reference

> ## ⛔️ Staging is deprecated as a reference (2026-07-30)
>
> **Look only at `cloverandcrane.myshopify.com` (production).** Do not inspect, query,
> browse, or cite `clover-crane-staging.myshopify.com` for anything — not to check what a
> section renders, not to confirm a schema, not to verify a fix.
>
> Staging drifted badly out of sync with both production and `origin/main`: its published
> theme got swapped to an unrelated one, its compiled sections lagged the merged source,
> and its schemas didn't match what was actually in git. Sessions that consulted it kept
> reaching confident, wrong conclusions from that mismatch and burned hours on phantom
> bugs. Historical staging references below are left intact for context but are **not
> evidence** — re-verify anything that matters against production.
>
> When the question is "what does the store actually have or render right now," the answer
> comes from production via `shopify store execute` (Admin GraphQL), or from the compiled
> theme files on `origin/main`. Nowhere else.

## Overview
 
Because C&C already has an active Shopify store, this is **not a store transfer** — it's a **theme push**. Only the finished theme gets pushed to C&C's live store. Their existing products, orders, customers, and settings are untouched.
 
---
 
## Environment Config
 
`shopify.theme.toml` in the theme root:
 
```toml
[environments.staging]
store = "clover-crane-staging.myshopify.com"
 
[environments.production]
store = "cloverandcrane.myshopify.com"
```

The `staging` environment block still exists in the file, but **don't use it** — see the
deprecation banner at the top. `--environment production` is the only one to reach for.

**Correction (2026-07-23):** the production store's real `myshopifyDomain` is `cloverandcrane.myshopify.com` — no hyphens. `clover-and-crane.myshopify.com` (the hyphenated form used everywhere in this doc and `CLAUDE.md` previously) 404s on `shopify theme list`. `shopify.theme.toml` did not exist in the repo at all as of this date — see the "Production Store Audit" section below.
 
---
 
## Pre-Transfer Checklist
 
- [ ] All sections built and audited (Home, Product, Cart, Collection)
- [ ] Tested against production's real product/metafield data — push the theme `--unpublished` and preview it there; **do not test on staging**, its data and theme state don't match production
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
 
- The staging store still exists in the Partner account, but is **no longer a reference or a test target** (see the banner at the top of this file). Don't verify against it.
- For future theme updates: edit locally → Liquiflow publish → merge the resulting PR → verify on production's own unpublished/preview theme before publishing.
- ~~`divmks.myshopify.com` in staging's domain settings is Shopify's internal redirect domain~~ — moot now that staging is out of the loop.

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
- [ ] Tested with real product/metafield data — **staging's data never resembled production's**; testing there won't catch the gaps below (and as of 2026-07-30 staging is off-limits entirely, see the banner up top). Preview against production instead.
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
---

## Production Theme Audit (2026-07-30)

Read-only audit of the store's themes via `shopify theme list` + `shopify store execute`
(Admin GraphQL `theme.files`), and a `shopify theme pull` of `sections/`, `templates/`,
`snippets/`, `layout/`, `assets/*.css` into a scratchpad (never into the repo — this repo
has no compiled theme directories, and nothing here is ever pushed by CLI).

| Theme | Role | ID |
|---|---|---|
| Dawn | **live** | `131053093113` |
| Clover | unpublished (ours) | `161621770489` |

`Clover` created 2026-07-28 20:07 UTC, 102 files. **Dawn is what customers see — every
Theme Editor question about "our" theme means the unpublished `Clover` theme**, opened at
`/admin/themes/161621770489/editor`.

Publishes from the Builder are **incremental** ("changes only"): section file timestamps
are spread across 07-28, 07-29 and 07-30 rather than sharing one publish time. So a stale
compiled section can sit on the theme indefinitely while its source moves on — check
`theme.files(filenames: [...]) { updatedAt }` before concluding a section is current.

Findings from comparing every `_sections/*.html` against its compiled `sections/*.liquid`
schema (source `li-settings` → generated setting ids, plus block names):

- **Promoted → Panel block: all three settings missing on the theme.** Root cause and fix
  in `project-reference.md` + the gotchas doc. Not yet re-verified — needs a publish.
- **Every other section's settings match source.** Two apparent mismatches were false
  alarms: setting ids are generated from the label with any Liquid filters stripped
  (`li-settings:image="Image | image_url: width: 1024 | placeholder: '…'"` → `image_image`),
  and `li-settings:collection` auto-adds a companion `<id>_limit` range setting.
- **`_sections/Team.html` source has drifted past the published theme** (edited after the
  07-30 21:04 publish, still uncommitted): source now emits `url_button_url` +
  `text_button_label`, the theme has `href_routescontact_url` + `text_secondary_label`, and
  `templates/index.json` stores values under the old ids. After the next publish those
  stored values are orphaned — the Team button's URL/label will read empty in the editor
  and need re-entering once.
- **Stale compiled sections on the theme with no source:** `sections/hooper.liquid` and
  `sections/inspiration.liquid` — leftovers from the `Hooper`→`Stores` rename and the
  deleted Inspiration section. Harmless at render time (nothing references them) but they
  still appear in the editor's "add section" picker. Safe to delete from the theme.
- **Password page assets never published:** no `layout/password.liquid` and no
  `sections/password.liquid` on the theme, although `_layouts/password.html` and
  `_sections/Password.html` exist in source. Publish them before the store is put behind a
  password page.

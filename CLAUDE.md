# Clover & Crane — Shopify theme

Shopify storefront for Clover & Crane. This repo **is** the theme: hand-written Liquid,
pushed to the store with the Shopify CLI.

## Pipeline

Liquid written here → `shopify theme push` → Shopify. Design reference comes from Figma or
whatever mockup is at hand; there is no design tool in the build path.

**Webflow and Liquiflow are out of the pipeline** (migrated September 2026). Nothing is
exported, converted, or generated. If you find `li-object`, `li-if`, `li-for`,
`li-settings:*` or similar `li-*` attributes in the markup, they are inert leftovers from
the old converter — dead attributes that ship to the browser and do nothing. Delete them
when you are already editing that markup. Do not run a project-wide sweep for them.

The pre-migration Webflow/Liquiflow tree is preserved on the **`source`** branch. It is
history only. Never edit it, and never treat `_sections/*.html` as the source of anything.

## Branches and checkouts

| Branch | What it is |
|---|---|
| `liquid-migration` | Current working branch, checked out at `../clover-crane-liquid` |
| `main` | The theme, plus "Update from Shopify" pulls |
| `source` | Archived Webflow/Liquiflow tree, checked out at `../clover-crane` — historical |

## Layout

Standard Shopify theme structure: `sections/`, `snippets/`, `blocks/`, `templates/`,
`layout/`, `assets/`, `config/`, `locales/`.

Section files are snake_case (`product_hero.liquid`, `collection_products.liquid`).
Templates are JSON and carry merchant configuration — treat their contents as store data,
not code, and expect the Theme Editor to rewrite them.

## Store facts

- Production: `cloverandcrane.myshopify.com`, primary domain `cloverandcrane.org`. There is
  no staging store.
- **Dawn** (`131053093113`) is the live theme. **Clover** (`161621770489`) is ours,
  unpublished. A CLI development theme also exists.
- Plan is Basic. No Shopify Plus, so no B2B, no Functions-based cart transforms, no
  per-customer catalogs.
- The catalog is still school and team spirit-wear. The theme is built for the gifting
  pivot that hasn't happened yet, so empty gifting sections are expected, not broken.

## Workflow

```bash
shopify theme dev --store cloverandcrane.myshopify.com     # local preview
shopify theme push --store cloverandcrane.myshopify.com --theme 161621770489
shopify theme pull --store cloverandcrane.myshopify.com --theme 161621770489
```

Read live store state with `shopify store execute` (Admin GraphQL) rather than guessing.

Merchant edits in the Theme Editor land on the Clover theme and reach this repo only
through a pull, so pull before editing templates. Editing theme code in the Shopify admin
works but competes with pushes from here — prefer editing locally.

## Personalization data model

Per product, in the `custom` metafield namespace: `monogramming_enabled` (the on/off
switch), `monogram_styles`, `monogram_text_colors`, `monogram_placement`,
`monogram_max_characters`, `delivery_time`. Each picker hides itself when its field is
empty. `personalization_cost` is retired and read nowhere.

Metaobjects: `monogram_style` (`style_label`, `style_image`, optional `max_characters`),
`text_color` (`name`, `color_code` — `color` is a reserved key), `monogram_position`
(`placement_option`, a nine-value choice list driving a 3×3 grid).

The upcharge is a separate cart line, because changing a price at add-to-cart needs Plus.
`product_hero.liquid` has a `monogramming_service_product` product-picker setting; the
customer's quantity is mirrored onto the fee line, and the fee is only added once the
native add-to-cart dispatches `cartupdated`, so a failed add can't leave an orphan fee. When the setting is empty the whole personalization panel is hidden,
which is deliberate: no configured fee means no way to order personalization free.

Order line item properties are `Design`, `Color`, `Placement`, `Text`,
`Add Personalization`, plus a hidden `_Add-Personalization`.

Character limit precedence: the chosen style's `max_characters`, then the product's
`monogram_max_characters`, then 3. Client-side only — nothing validates length server-side.

## Cart and variant runtime

`assets/li_custom.js` (`handleVariant`, `activateVariants`, add-to-cart),
`assets/li_minicart.js`, `assets/section-rendering.js`, `assets/li_helper.js.liquid`. These
came from the old converter but are now just ordinary theme assets that we own. Nothing is
fetched at runtime. If variants or the cart look broken, the fault is almost always in the
markup, not in these files — do not reimplement them.

`addToCart` serializes the whole product form, so line item properties and quantity flow
through automatically, and it dispatches `cartupdated`, `toggleminicart` and
`showcartmessage` when it succeeds.

## Schema rules that still bite

These are Shopify's, not the old converter's:

- Each resource-picker type (`collection`, `product`, `blog`, `article`, `page`,
  `link_list`) may appear only once per settings array. Blocks own their own array.
- A `range` default must sit on the step grid.
- Never `"default": ""` — omit the key.
- Shopify aborts a section at its first schema error, so fix and re-push until the upload
  log is empty. "Section type does not refer to an existing section file" is a follow-up
  error, not the cause.

## Known cruft

Clean these up when convenient; none of it is urgent.

- `layout/project-reference.liquid` and `assets/project-reference.json` — documentation
  that got published to the theme. Delete.
- `assets/shopify.theme.toml` — CLI config inside `assets/`, publicly served. Move or delete.
- `sections/inspiration.liquid` — dead section, still shows in the Add Section picker.
- Leftover `li-*` attributes throughout the markup.
- `templates/index.json` stores `"text_heading": "{{ block.settings.collection_products.title }}"`
  on a Product Slider block. Setting values are not evaluated as Liquid, so that renders
  literally. Either type a real heading or default to the collection title in the section.

## Open items

- `sections/stores.liquid` reads `store.portal_collection.url`, but the `store_portal`
  metaobject has no such field on production, so those cards link nowhere. The likely
  resolution is retiring the section in favour of a Collection Slider block.
- A metaobject reference field probably needs `.value` before a property access
  (`store.portal_collection.value.url`). Unverified — test on the dev theme before relying
  on either form.
- Portal collections are public and listed on the All Collections page. Decide whether to
  filter them out of `collections_list.liquid`.

## Documentation

Merchant and handoff docs live in the sibling repo `../clover-crane-docs`: merchant guide,
launch checklist, collections and personalization architecture, and dated audits under
`archive/`. Update them when behaviour changes; the merchant guide is client-facing.

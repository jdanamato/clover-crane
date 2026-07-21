## Key Liquiflow Rules & Gotchas
 
- **Two liquid tags cannot be on the same element** — e.g. `li-for` and `li-if` cannot coexist on one element. Use the `:inside` modifier (`li-for:inside`, `li-if:inside`) or nest elements.
- **`li-object:text` and `li-object` are equivalent** — both set inner text content.
- **Bracket notation required for hyphenated handles** — `pages['about-us'].url` not `pages.about-us.url`.
- **`li-for:inside`** — renders the loop inside the element rather than wrapping it. Used on Swiper wrapper elements.
- **`li-settings` compatible modifiers**: `:text`, `:image`, `:url`, `:richtext`, `:number`, `:color`.
- **`li-form:product=""`** — empty value is intentional; tells Liquiflow to use the current page's product context.
- **`li-form:cart=""`** — same pattern for cart form.
- **`li-content-for-theme-blocks=""`** — empty value is correct; marks the block container for the section.
- **`cart.total_discounts`** — plural, not `cart.total_discount`.
- **`image_url` not `img_url`** — `img_url` is deprecated in Shopify. Use `image_url: width: [n]`.
- **`li-block` names must be unique within a section** — duplicate names cause schema conflicts.
- **`forloop.index` only works inside `li-for`** — never use it in a `li-block` context.
- **Properties starting with `_`** are hidden from customers by convention — filter them with `li-unless="property.first contains '_'"`.
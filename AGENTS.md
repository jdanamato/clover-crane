# Clover & Crane — Shopify theme

See [CLAUDE.md](CLAUDE.md). It is the single set of project rules and applies to every
agent working in this repo — pipeline, branches, store facts, workflow, the personalization
data model, schema rules, and known cruft.

Two things worth knowing before you read anything else:

- This repo is hand-written Liquid pushed with the Shopify CLI. Webflow and Liquiflow were
  removed from the pipeline in September 2026; leftover `li-*` attributes in the markup are
  inert and can be deleted as you touch them.
- The old Webflow/Liquiflow tree lives on the `source` branch as history. Never edit it.

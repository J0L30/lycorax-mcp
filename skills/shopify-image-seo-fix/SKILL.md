---
name: shopify-image-seo-fix
description: Use when the user wants to find and fix missing, empty or weak product image alt text on a Shopify store they own or manage, in English, French or Arabic, with a dry run, explicit human approval before any write, and a rollback plan, using the Lycorax Shopify tool on Apify.
version: 1.0.0
license: MIT
metadata:
  author: Youssef Ferri (Lycorax Technologies)
  requires: Lycorax MCP tools (https://github.com/J0L30/lycorax-mcp)
  tags: [shopify, alt-text, image-seo, ecommerce, accessibility]
---

# Shopify image SEO fix

Audit, review, apply only what the user approves, verify, and keep a rollback path.

## When to use
- "Fix the alt text on my Shopify store."
- A Shopify merchant or agency wants product images to be accessible and to show up in image search.

## When not to use
- WooCommerce or WordPress: use `j0l30--woocommerce-seo-alt-text-fixer` with the same approval flow.
- Any other platform: use `j0l30--website-image-seo-audit` (audit only).
- Stores the user cannot prove they manage. Never ask for, accept or use someone else's Admin API token.

## Tool
`j0l30--shopify-seo-alt-text-fixer`, then `get-dataset-items` to read results.

Key inputs: `storeUrl` (required), `maxProducts` (default 250), `altLanguage` (`auto`, `en`, `fr`, `ar`), `onlyImagesWithIssues` (default true), `autoFix` (default false), `dryRun` (default true), `authorizationConfirmed` (default false), `shopDomain`, `adminAccessToken`, `maxFixes` (default 500), `rollback`.

## Procedure
1. **Audit (no credentials).** Run with `storeUrl`, a small `maxProducts` (e.g. 50) for the first pass, `autoFix: false`. Tell the user the run is billed to their Apify account.
2. **Review.** Read the dataset. Show the user a sample table: product, image, current alt, suggested alt, issue type. Summarise totals by issue type from the data.
3. **Ask for approval in plain words**: how many images would change, in which language, and that a dry run comes first. Do not continue without a clear yes.
4. **Dry run.** With the user's own `shopDomain` and an Admin API token the user created (scopes as described on the Actor page), run `autoFix: true`, `dryRun: true`, `authorizationConfirmed: true` only after the user confirms they own or manage the store, and a `maxFixes` cap the user chose. Show exactly what would change.
5. **Second approval.** Ask: "Apply these N changes?" Only on an explicit yes, run again with `dryRun: false`.
6. **Report** what changed, and save the run's change log so a rollback is possible.

## Pitfalls
- The token is a secret. Ask the user to paste it into the MCP client's tool input or a secret store, never into a file in the repo, and suggest they revoke the custom app when finished.
- `authorizationConfirmed` is the user's statement, not yours. Never set it to true on your own.
- Start small. Cap `maxProducts` and `maxFixes` until the user trusts the output.
- Alt text describes the image for people. If a suggestion is wrong for a product (variant colour, bundle), let the user edit or exclude it.
- `altLanguage: auto` follows the store's `<html lang>`; set it explicitly for multilingual stores.

## Verification
- Re-run the audit (no credentials) on the same products and confirm the fixed images no longer appear with issues.
- Spot-check three product pages in a browser and read the `alt` attribute.

## Rollback
If the user is unhappy, run the tool with the `rollback` input from the fix run's log. It restores the original alt text and leaves images edited after the fix alone.

## Human approval
Two explicit approvals are required: one before the dry run with credentials, one before `dryRun: false`. No write-back happens without both.

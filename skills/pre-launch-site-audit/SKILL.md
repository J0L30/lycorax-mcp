---
name: pre-launch-site-audit
description: Use when the user is about to launch, relaunch or migrate a website they own and wants a go/no-go check of image SEO, page SEO basics, domain registration, DNS, TLS, HTTPS redirect, security headers and email records, using the Lycorax MCP tools on Apify.
version: 1.0.0
license: MIT
metadata:
  author: Youssef Ferri (Lycorax Technologies)
  requires: Lycorax MCP tools (https://github.com/J0L30/lycorax-mcp)
  tags: [seo, launch, audit, dns, tls, image-seo]
---

# Pre-launch site audit

Produce one prioritised go/no-go list for a site before it goes live, from real tool output only.

## When to use
- "We launch next week, is the site ready?"
- A redesign, CMS migration or domain move on a site the user owns or manages.
- A staging site that is publicly reachable and about to replace production.

## When not to use
- Sites the user does not own or manage. Ask once; if they cannot confirm, stop.
- Deep performance tuning (Core Web Vitals field data), accessibility certification, or legal compliance reviews. Say these are out of scope and suggest a specialist tool.
- Staging sites behind a login: the tools only see public pages.

## Tools
- `j0l30--website-image-seo-audit` (input `siteUrl`, `maxPages`, `checkImageSpeed`, `maxImageChecks`, `altLanguage`)
- `j0l30--domain-rdap-dns-reputation-checker` (input `mode: "check"`, `domains`, `checkDns`, `checkTls`, `checkHttp`)
- `get-dataset-items` to read results from the `datasetId` each run returns.

## Procedure
1. Confirm the site URL, that the user owns or manages it, and a page budget (default `maxPages: 30` for a first pass). Tell the user runs are billed to their Apify account.
2. Run the image SEO audit on the launch URL with `onlyImagesWithIssues: true`. Read the dataset with `get-dataset-items` (page through with `limit`/`offset` if large).
3. Run the domain checker in `check` mode on the registrable domain (and the old domain, if this is a migration).
4. Classify every finding:
   - **Blocker**: no HTTPS redirect, invalid or soon-expiring TLS certificate, domain expiring within 30 days, no MX when the site sends mail, pages that cannot be fetched.
   - **Fix before launch**: missing alt text on key templates (home, product, category), missing title/meta/canonical, images far over 200 KB, missing width/height.
   - **Fix after launch**: duplicate or generic alt text on deep pages, missing optional security headers, DMARC at `p=none`.
5. Give the user a table: finding, where, why it matters in one line, the fix, and owner (dev, content, DNS).
6. End with a single verdict: **Go**, **Go with fixes** (list them) or **No-go** (list blockers).

## Pitfalls
- Do not invent numbers. Every count, score and URL must come from the dataset.
- Template issues repeat on every page; report them once as "template" with an example URL, not 300 times.
- A staging host (e.g. `staging.example.com`) is reduced to the registrable domain by the domain checker, so its DNS results describe the main domain.
- Some TLDs publish no RDAP; the checker marks them unknown. Say so instead of guessing an expiry date.
- Robots or noindex on staging is expected; flag it only if it is still present on the production URL.

## Verification
- Re-run the image audit on the same pages after fixes (or use `baselineDataset` with the first run's dataset for a before/after proof) and confirm the blocker list is empty.
- Re-run the domain check and confirm the HTTPS redirect and certificate dates.

## Human approval
This skill is read-only. If the user wants alt text written back, hand off to `shopify-image-seo-fix` (Shopify) or use the WooCommerce tool's dry run first, and get explicit approval before any write.

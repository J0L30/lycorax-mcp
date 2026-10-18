---
name: client-audit-report
description: Use when an agency, freelancer or consultant wants a clear, plain-language website audit report for a client site they are authorised to audit, covering image SEO, page SEO basics and domain, DNS, TLS and email records, with an optional white-label before/after proof, using the Lycorax MCP tools on Apify.
version: 1.0.0
license: MIT
metadata:
  author: Youssef Ferri (Lycorax Technologies)
  requires: Lycorax MCP tools (https://github.com/J0L30/lycorax-mcp)
  tags: [agency, seo, report, image-seo, dns, white-label]
---

# Client audit report

Turn tool output into a report a non-technical client can act on.

## When to use
- Pitching or onboarding an SEO or web maintenance client.
- Monthly or quarterly reporting with before/after proof.

## When not to use
- Sites you are not engaged or authorised to audit.
- Promising rankings or traffic. The report shows what is wrong and what was fixed, not future results.

## Tools
- `j0l30--website-image-seo-audit`: `siteUrl`, `maxPages`, `onlyImagesWithIssues`, optional `brandName`, `brandLogoUrl`, `brandColor`, `brandWebsite` for a white-label report, `baselineDataset` for before/after, optional `gscBeforeCsv`/`gscAfterCsv` (the client's own Search Console exports), `monitoring`.
- `j0l30--domain-rdap-dns-reputation-checker`: `mode: "check"` on the client's domain.
- `get-dataset-items` and `get-key-value-store-record` to read results and report files.

## Procedure
1. Confirm the engagement, the URL, the page budget, and who pays for the Apify runs (they bill to the account connected to the MCP client).
2. Run the image SEO audit. Add the agency's branding inputs if the client should see only the agency brand.
3. Run the domain check on the client's domain.
4. Write the report:
   - **Summary** in three sentences: overall state, the biggest risk, the quickest win.
   - **Findings** table: issue, pages affected (from the data), business impact in plain words, fix, effort (S/M/L).
   - **Domain and email health**: expiry, certificate, HTTPS, SPF/DMARC, each as OK / needs attention.
   - **Next steps** with an owner for each.
5. For a follow-up report, run the audit again with `baselineDataset` set to the first run's dataset to produce the before/after proof, and add Search Console exports only if the client provided them.

## Pitfalls
- Every number must come from the run. Do not estimate traffic gains or add industry statistics.
- Explain jargon once ("alt text: the description screen readers and image search use").
- Report template issues once, not per page.
- Keep client data private: do not paste their Search Console data or report into public places.

## Verification
- Check that each figure in the report matches the dataset, and that every URL in the report opens.
- If a white-label PDF or HTML report was generated, open it and confirm the branding and that no other brand appears.

## Human approval
Read-only. Any write-back (alt text fixes on Shopify or WooCommerce, a Cloudflare Worker) needs the client's explicit approval, a dry run first, and the authorisation flag set only on the client's confirmation.

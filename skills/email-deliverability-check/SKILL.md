---
name: email-deliverability-check
description: Use when the user's emails land in spam or bounce, or they are setting up email on a domain they own, and want SPF, DMARC and MX records checked and the exact DNS changes explained, using the Lycorax domain checker on Apify.
version: 1.0.0
license: MIT
metadata:
  author: Youssef Ferri (Lycorax Technologies)
  requires: Lycorax MCP tools (https://github.com/J0L30/lycorax-mcp)
  tags: [email, spf, dmarc, dns, deliverability]
---

# Email deliverability check

Read the domain's public mail records and turn them into a short, safe list of DNS changes.

## When to use
- "Why do my emails go to spam?"
- New domain or new email provider (Google Workspace, Microsoft 365, a newsletter tool).
- Checking a client's domain before a campaign, with the client's permission.

## When not to use
- Content or reputation problems inside an email platform (bounce lists, complaint rates). Say the DNS check is only part of the picture.
- DKIM selectors: the tool reads SPF, DMARC and MX; it does not guess DKIM selectors. Ask the user for the selector from their provider if DKIM must be checked.

## Tool
`j0l30--domain-rdap-dns-reputation-checker` with `mode: "check"`, `domains: ["example.com"]`, `checkDns: true`. Read the output with `get-dataset-items`.

## Procedure
1. Confirm the sending domain(s) and which services send mail for them (the user's provider, website forms, invoicing, newsletters).
2. Run the check. Read MX, SPF (TXT `v=spf1`) and DMARC (`_dmarc` TXT) from the result.
3. Explain each record in one line, then flag:
   - No SPF, or more than one SPF record (only one is allowed).
   - SPF that does not include every service the user named, or that ends in `+all`.
   - SPF that needs more than 10 DNS lookups (if the tool reports it).
   - No DMARC, or DMARC with no `rua` reporting address.
   - No MX on a domain that should receive mail.
4. Propose the corrected records as text the user can paste into their DNS host. Mark each as add, replace or remove.
5. Recommend a DMARC rollout: start at `p=none` with reports, move to `quarantine`, then `reject` once reports show all legitimate senders pass.

## Pitfalls
- Never tell the user to jump straight to `p=reject`; it can block their own legitimate mail.
- Use only the provider include values the user's provider documents. If you are not sure of an include, say so and link the provider's help page instead of guessing.
- DNS changes take time to propagate; a re-check minutes later may still show old values.
- Subdomains are reduced to the registrable domain by the tool; check a sending subdomain's records separately if the provider uses one.

## Verification
- After the user changes DNS, run the check again and confirm one SPF record, a DMARC record with `rua`, and MX present.
- Suggest sending a test email to a mailbox the user controls and reading the authentication results in the message headers.

## Human approval
This skill is read-only. The user makes DNS changes themselves; never change DNS on their behalf without explicit approval and their own access.

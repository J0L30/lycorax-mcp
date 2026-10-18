---
name: attack-surface-review
description: Use when a site owner wants a defensive review of what their OWN domains expose publicly (TLS, HTTPS redirect, security headers, DNS, expiry, lookalike domains of their brand) and how to fix leaks such as indexed .env, .git or backup files, using the Lycorax domain checker and exposure defense knowledge on Apify. Never use on domains the user does not own or is not authorised in writing to test.
version: 1.0.0
license: MIT
metadata:
  author: Youssef Ferri (Lycorax Technologies)
  requires: Lycorax MCP tools (https://github.com/J0L30/lycorax-mcp)
  tags: [security, defensive, osint-defense, dns, tls, incident-response]
---

# Attack surface review (defensive, own sites only)

Help a site owner see their public footprint the way an outsider would, then close the gaps.

## Rule zero
- Only review domains the user owns or has **written** authorisation to test. Ask once at the start. If they cannot confirm, stop and explain why.
- Never open, download or use credentials, personal data or files found on any site. If a secret is exposed, the fix is to rotate it, not to test it.
- If the user finds someone else's leak by accident, recommend responsible disclosure to that owner and stop.
- Unauthorised access is a crime in Morocco and in most other countries.

## When to use
- "What does my site expose?" for the user's own domains.
- After an incident (an indexed `.env`, an exposed `.git` folder, a public backup) to plan the clean-up.
- A monthly self-check for a business's own domain portfolio.

## When not to use
- Penetration testing, vulnerability scanning, exploitation or credential testing. These tools do not do that, and this skill must not be used to plan it.
- Any third-party, competitor or "just curious" target.

## Tools
- `j0l30--domain-rdap-dns-reputation-checker`: `mode: "check"` (or `"monitor"` for repeat runs), `domains`, `checkDns`, `checkTls`, `checkHttp`, optional `brandKeywords` and `ownedDomains` to flag lookalike domains of the user's brand.
- `j0l30--website-exposure-defense-knowledge`: `mode: "catalog"` (free table of contents), `mode: "ask"` with a `query` for targeted guidance.
- `get-dataset-items` to read results.

## Procedure
1. Confirm ownership (rule zero) and list the domains in scope. Tell the user runs are billed to their Apify account.
2. Run the domain checker on the in-scope domains. Put every domain the user owns in `ownedDomains` so they are never flagged as lookalikes.
3. Summarise per domain: registry expiry, TLS issuer and days left, HTTPS redirect, HSTS and other security headers, MX/SPF/DMARC, and any risk notes the tool returns.
4. For each concern, or for a leak the user already knows about, call the exposure knowledge tool in `ask` mode with a specific question, for example "an old backup.zip is in search results, what do I do first?". Quote the steps it returns and cite the section.
5. Build a fix plan in this order: rotate exposed secrets, remove or block the file at the origin, request removal from search engines and caches, then add guards (deploy rules, `robots.txt` is not a security control, monitoring).
6. Offer `monitor` mode on a schedule the user chooses so expiry, certificate and DNS changes raise alerts.

## Pitfalls
- Do not run the knowledge tool's paid modes (`unit`, `chapter`, `pack`) without telling the user; `catalog` is free and `ask` is the cheapest way to answer one question. Check the Actor page for current pricing.
- Lookalike domains are signals, not proof of abuse. Report them as "to review", never as accusations.
- `robots.txt` hides nothing from attackers; do not recommend it as a fix for exposed files.
- Keep the report private: it is a map of weaknesses.

## Verification
- After fixes, re-run the domain check and confirm the HTTPS redirect, headers and certificate.
- For removed files, confirm the URL now returns 404 or 410 from the origin, then track the search engine removal request until it clears.

## Human approval
Read-only. Any change (DNS, server config, secret rotation, removal requests) is done by the user or with their explicit approval for each action.

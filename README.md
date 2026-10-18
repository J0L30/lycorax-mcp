# Lycorax MCP: agent-ready website audits for Claude, Cursor, Codex, ChatGPT and VS Code

**Lycorax MCP gives your AI agent five website audit tools over one MCP URL: Shopify and WooCommerce alt text fixes, image SEO audits, domain RDAP/DNS/SPF/DMARC checks, and defensive exposure guidance. They run on Apify's hosted MCP server, so there is nothing to self-host.**

Paste the bundle URL into any MCP client that supports remote (Streamable HTTP) servers, sign in to Apify, and ask your agent to audit a site.

```
https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge
```

This repository contains client configs, free Agent Skills that use the tools, and docs. It does not contain server code: the tools are [Apify Actors](https://apify.com/j0l30) exposed by the official [Apify MCP server](https://github.com/apify/apify-mcp-server) at `mcp.apify.com`.

## Contents

- [Tools](#tools)
- [Connect your client](#connect-your-client)
- [Authentication and billing](#authentication-and-billing)
- [Example prompts](#example-prompts)
- [Free Agent Skills](#free-agent-skills)
- [REST alternatives (OpenAPI)](#rest-alternatives-openapi)
- [Responsible use](#responsible-use)
- [FAQ](#faq)

## Tools

| MCP tool | Apify Actor | What it does |
|---|---|---|
| `j0l30--shopify-seo-alt-text-fixer` | [Shopify Alt Text Generator & Image SEO Auto-Fix](https://apify.com/j0l30/shopify-seo-alt-text-fixer) | Audits any public Shopify store for missing or weak product image alt text and page SEO basics (titles, meta, H1, canonical, schema, image size and format). It suggests alt text in English, French or Arabic, built by rules from your product data. Optional write-back through the Shopify Admin API, with a dry run by default and a rollback. |
| `j0l30--woocommerce-seo-alt-text-fixer` | [WooCommerce Alt Text Generator & Image SEO Fixer](https://apify.com/j0l30/woocommerce-seo-alt-text-fixer) | Same idea for WooCommerce and WordPress: product and media library images, page SEO basics and page-speed image checks. Optional write-back through the WordPress REST API with an Application Password, dry run by default, rollback available. |
| `j0l30--website-image-seo-audit` | [Image SEO Checker & Alt Text Checker for Any Website](https://apify.com/j0l30/website-image-seo-audit) | Crawls any website (found from robots.txt, sitemap.xml and home page links) and flags missing, empty, filename-like, generic and duplicate alt text, heavy or non-WebP/AVIF images, missing width/height and lazy loading. Optional before/after proof report, white-label branding, monitoring, and a generated Cloudflare Worker for sites behind Cloudflare. |
| `j0l30--domain-rdap-dns-reputation-checker` | [Domain Reputation Checker: Bulk RDAP, DMARC & DNS Lookup](https://apify.com/j0l30/domain-rdap-dns-reputation-checker) | Checks up to 1,000 domains: RDAP registration data from the registry, DNS (A, AAAA, MX, NS, TXT, CAA, DS), SPF and DMARC parsing, TLS certificate, HTTPS redirect and security headers, with explained scores. Modes: `check`, `monitor` (snapshots and alerts) and `availability`. Registrant names, emails and phones are never returned. |
| `j0l30--website-exposure-defense-knowledge` | [Website Exposure Defense Knowledge for AI Agents](https://apify.com/j0l30/website-exposure-defense-knowledge) | A knowledge tool, not a scanner. It answers questions such as "my .env file was indexed, what do I do first?" with the best sections of the guide *Find and Fix What Search Engines Expose About Your Site*. Modes: `ask`, `catalog` (free table of contents), `unit`, `chapter`, `pack`. For site owners fixing their own exposure. |

Tool names follow the Apify MCP convention of replacing `/` with `--`. When you load Actor tools, the server also adds `get-actor-run`, `get-dataset-items`, `get-key-value-store-record` and `abort-actor-run`. A tool call returns the run status and a `datasetId`; your agent then reads the results with `get-dataset-items`. Each Actor's input fields, defaults and current pricing are listed on its Apify page.

Want only one or two tools? Remove the others from the `tools=` list. Want Apify's discovery and docs tools too? Add `actors,docs` to the list. See [Apify's tool selection docs](https://docs.apify.com/platform/integrations/mcp#tool-selection).

## Connect your client

The bundle URL above is a standard remote MCP endpoint. Syntax below follows each client's own documentation. Ready-to-copy files are in [`configs/`](configs/).

### Claude (claude.ai and Claude Desktop)

Add it as a custom connector, as described in [Apify's Claude Desktop guide](https://docs.apify.com/platform/integrations/claude-desktop):

1. Open **Settings → Connectors → Add → Add custom connector**.
2. Name: `Lycorax`. Remote MCP server URL: the bundle URL.
3. Select **Continue**, keep the detected OAuth settings, select **Add**, then sign in to Apify in the browser.

If your Claude Desktop build only supports local servers, [`configs/claude_desktop_config.json`](configs/claude_desktop_config.json) runs Apify's own published package (`@apify/actors-mcp-server`) with the same five tools. It needs Node.js 18+ and your Apify token in place of `YOUR_APIFY_TOKEN`. Never commit that file with a real token.

### Claude Code

```bash
claude mcp add --transport http lycorax "https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge"
```

Then run `/mcp`, select `lycorax` and choose **Authenticate**.

### Cursor

Put [`configs/cursor-mcp.json`](configs/cursor-mcp.json) in `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "lycorax": {
      "url": "https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge"
    }
  }
}
```

Open **Cursor Settings → Tools & MCPs** and select **Connect** to sign in to Apify.

### VS Code (GitHub Copilot)

Put [`configs/vscode-mcp.json`](configs/vscode-mcp.json) in `.vscode/mcp.json`, or run **MCP: Add Server → HTTP** and paste the URL:

```json
{
  "servers": {
    "lycorax": {
      "type": "http",
      "url": "https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge"
    }
  }
}
```

For the portable workspace file `.mcp.json`, use the same entry under a top-level `mcpServers` key instead of `servers`.

### Codex (CLI, IDE extension, ChatGPT desktop app)

```bash
codex mcp add lycorax --url "https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge"
codex mcp login lycorax
```

Or add [`configs/codex-config.toml`](configs/codex-config.toml) to `~/.codex/config.toml`:

```toml
[mcp_servers.lycorax]
url = "https://mcp.apify.com/?tools=j0l30/shopify-seo-alt-text-fixer,j0l30/woocommerce-seo-alt-text-fixer,j0l30/website-image-seo-audit,j0l30/domain-rdap-dns-reputation-checker,j0l30/website-exposure-defense-knowledge"
```

### ChatGPT

Per [Apify's ChatGPT guide](https://docs.apify.com/platform/integrations/chatgpt): enable Developer mode, open **Settings → Apps & Connectors → Create**, paste the bundle URL as **MCP Server URL**, choose **OAuth**, and authorize Apify. ChatGPT does not let you change a connector's tools after it is created, so make a new connector if you change the list.

### Other clients

Any client that speaks Streamable HTTP MCP works with the same URL. The [Apify MCP configurator](https://mcp.apify.com) generates client-specific snippets, and `apify mcp install <client>` in the [Apify CLI](https://docs.apify.com/cli) writes the config for you.

## Authentication and billing

- **Apify OAuth (recommended):** connect with the URL alone; on first use your browser opens to sign in to Apify and approve access. No token goes into a config file.
- **Apify API token:** for headless setups, send `Authorization: Bearer <APIFY_TOKEN>` as a header. Get the token from [Apify Console → Settings → API & Integrations](https://console.apify.com/settings/integrations) and keep it out of version control.
- **Billing:** the tools run in **your** Apify account. Apify bills each run to you, according to the pricing shown on each Actor's page and your Apify plan. Your AI client's own usage is billed separately. Set limits in your Apify account and ask your agent to keep runs small (for example `maxProducts`, `maxPages`, `maxDomains`) while you test.
- Lycorax never sees your OAuth session or API token. Shopify and WordPress credentials are only needed for optional write-back and are sent only to your own store.

## Example prompts

- "Audit the image SEO of https://example.com, 30 pages max. Group the problems by type and show the 10 worst pages."
- "Check alt text on my Shopify store https://my-store.com. Dry run only. Show me the suggested alt text before anything is changed."
- "Scan my WooCommerce shop https://shop.example.com in French and list products whose images have no alt text."
- "Check SPF, DMARC and MX for example.com and example.ma, and explain what to fix so my emails stop going to spam."
- "Monitor my three domains and tell me which one expires first and whether any TLS certificate ends within 14 days."
- "My .env file was indexed by a search engine. Using the exposure defense tool, what do I do first, in order?"
- "Run a pre-launch audit of my staging site and give me a prioritised fix list."

## Free Agent Skills

[`skills/`](skills/) holds free [Agent Skills](https://agentskills.io) (a `SKILL.md` per folder) that tell your agent how to chain the tools into a full job, with guardrails. Each skill has when to use and when not to, a procedure, pitfalls, a verification step, and a rule that nothing is written back to a store without your explicit approval.

| Skill | Uses | Job |
|---|---|---|
| [`pre-launch-site-audit`](skills/pre-launch-site-audit/SKILL.md) | image SEO audit, domain checker | Go/no-go checklist before a site goes live |
| [`shopify-image-seo-fix`](skills/shopify-image-seo-fix/SKILL.md) | Shopify alt text fixer | Audit → review → approved fix → verify → rollback plan |
| [`email-deliverability-check`](skills/email-deliverability-check/SKILL.md) | domain checker | SPF, DMARC and MX review with DNS records to add |
| [`attack-surface-review`](skills/attack-surface-review/SKILL.md) | domain checker, exposure defense knowledge | Defensive review of your own domains only |
| [`client-audit-report`](skills/client-audit-report/SKILL.md) | image SEO audit, domain checker | Plain-language audit report for an agency client |

### Install the skills

Copy the folders you want into your agent's skills directory:

| Agent | User-level skills directory |
|---|---|
| Claude Code | `~/.claude/skills/` (or `.claude/skills/` in a project) |
| Codex | `~/.agents/skills/` (or `.agents/skills/` in a repo), per the [Codex skills docs](https://developers.openai.com/codex/skills) |
| Hermes Agent | `~/.hermes/skills/` |
| OpenClaw | `~/.openclaw/skills/` (or `<workspace>/skills/`) |

```bash
git clone https://github.com/J0L30/lycorax-mcp.git
mkdir -p ~/.claude/skills && cp -r lycorax-mcp/skills/* ~/.claude/skills/
```

The skills expect the Lycorax MCP tools to be connected. Start a new session after installing.

## REST alternatives (OpenAPI)

If your agent framework uses OpenAPI tools instead of MCP, two read-only HTTP APIs cover the domain and single-page image checks:

- Domain Reputation API (RDAP, DNS, SPF, DMARC): https://lycorax-domain-api.j0l30.workers.dev/openapi-simple.json
- Image SEO Checker API (one page, 0-100 score): https://lycorax-image-seo-api.j0l30.workers.dev/openapi-simple.json

Read each spec for endpoints, limits and access terms.

## Responsible use

- Only write to stores and sites you own or are authorised to manage. Write-back is off by default, needs an explicit authorisation flag, and defaults to a dry run.
- The exposure defense tool is for **defensive** work on your own sites: finding and removing what search engines, archives and caches hold about you. Do not use it, or any skill here, to probe systems you are not authorised to test. Unauthorised access is a crime in Morocco and in most countries.
- The domain checker reads public registry, DNS and HTTP data only, and never returns registrant personal data.

## FAQ

**Do I need to host anything?** No. The tools run on Apify and the MCP server is Apify's hosted `mcp.apify.com`.

**Do I need an Apify account?** Yes, to run the tools. Apify offers a free plan; check [Apify pricing](https://apify.com/pricing) for current limits.

**Is the alt text written by an AI model?** No. Suggestions are built by deterministic rules from your own product and page data (titles, captions, words already on the page), in English, French or Arabic.

**Can the agent change my store without asking?** The Actors default to `dryRun: true`, and the skills in this repo require your explicit approval before any write-back. Keep your MCP client's tool approval prompts on for write runs.

## Links

- Publisher: [apify.com/j0l30](https://apify.com/j0l30)
- Apify MCP server docs: https://docs.apify.com/platform/integrations/mcp
- Machine-readable summary: [`llms.txt`](llms.txt)

## License

MIT, see [LICENSE](LICENSE). The license covers this repository (docs, configs and skills). The Apify Actors and the Lycorax APIs are separate services with their own terms.

Made by Youssef Ferri, Lycorax Technologies, Casablanca.

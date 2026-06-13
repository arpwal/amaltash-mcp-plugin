# Amaltash plugin for Claude

Trade on [Amaltash](https://amaltash.com) directly from Claude. This plugin
ships a skill that teaches Claude how to drive the **hosted Amaltash MCP server**
at `https://mcp.amaltash.com` — nothing runs locally.

## What you can do

- **Strategies** — create one from a plain-English idea, fine-tune it
  (revise → draft → backtest → auto-activate), backtest it (metrics + an
  embedded equity curve), deploy with a capital allocation (respecting the
  platform minimum), and undeploy.
- **Paper trading** — provision a sandbox account funded with **$25,000**.
- **Onboarding** — check `connection_status`; get deep-links to finish **KYC**
  (kyc.amaltash.com), connect a **US bank via Plaid**, or connect a **live
  exchange** (OAuth).
- **Funding** — list connected banks, deposit (ACH / wire), withdraw (ACH),
  and read your balance plus *why* part of your cash isn't withdrawable yet.

## Install

> A step-by-step walkthrough lives in [`install-guide.html`](install-guide.html)
> — open it in a browser.

1. **Create a connector token** in your Amaltash dashboard:
   **Settings → Security → Claude connector → Create token** (copy it — it's
   shown once; it looks like `amat_…`).
2. **Expose it to Claude** as an environment variable:
   ```bash
   export AMALTASH_AGENT_TOKEN="amat_…"
   ```
3. **Add the plugin** in Claude Code:
   ```
   /plugin marketplace add arpwal/amaltash-mcp-plugin
   /plugin install amaltash@amaltash
   ```
   The bundled `.mcp.json` connects to `https://mcp.amaltash.com` and sends your
   token as an `Authorization: Bearer` header.

### Configuration

| Var | Purpose |
| --- | --- |
| `AMALTASH_AGENT_TOKEN` | Your `amat_…` connector token (sent as the Bearer header). Revoke/rotate from the dashboard any time. |

## Try it

> "Show my Amaltash account, then create a momentum strategy on SPY and
> backtest it."

Claude will read your account, `create_strategy`, and `backtest_strategy`
(returning metrics + an equity curve).

## How it works

```
Claude ──(MCP over HTTPS, Authorization: Bearer amat_…)──▶ mcp.amaltash.com  ──▶  Amaltash API
```

The hosted server authenticates every request with your token — no passwords or
local secrets ever reach the plugin. Manage and revoke tokens from
**Settings → Security → Claude connector** in your dashboard.

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

1. **Add the plugin** in Claude Code:
   ```
   /plugin marketplace add arpwal/amaltash-mcp-plugin
   /plugin install amaltash@amaltash
   ```
2. **Connect** — run `/mcp`, pick **amaltash**, choose **Authenticate**, and
   approve in the browser that opens. No token to copy.

The bundled `.mcp.json` points at `https://mcp.amaltash.com/mcp` with no
credentials, so the first request triggers Claude's OAuth flow: it discovers the
authorization server, signs you in, and stores an encrypted, auto-refreshed
token for you.

### Headless / CI

Where no browser is available, connect with a personal token instead. Mint one
at **Settings → Security → Claude connector → Create token** (`amat_…`, shown
once), then:

```bash
claude mcp add --transport http amaltash \
  https://mcp.amaltash.com/mcp \
  --header "Authorization: Bearer amat_…"
```

## Try it

> "Show my Amaltash account, then create a momentum strategy on SPY and
> backtest it."

Claude will read your account, `create_strategy`, and `backtest_strategy`
(returning metrics + an equity curve).

## How it works

```
Claude ──(MCP over HTTPS, OAuth 2.1)──▶ mcp.amaltash.com  ──▶  Amaltash API
        first call → 401 + discovery → sign in once → encrypted token, auto-refreshed
```

`mcp.amaltash.com` is an OAuth resource server: a tokenless request returns a
discovery challenge that Claude uses to run the sign-in (auth-code + PKCE) flow.
The minted token rides the same security model as everything else — revoke it
from **Settings → Security → Claude connector**, or `/mcp` → Clear
authentication. (Headless setups send a personal `amat_…` token as a Bearer
header instead.)

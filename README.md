# Amaltash for Claude

Run your [Amaltash](https://amaltash.com) investing account straight from a
conversation with Claude. Describe a strategy in plain English, have Claude
backtest it, paper-trade it with $25,000 of simulated cash, fund a live account,
and check balances — all by just asking. **No coding, no tokens to copy — just
install it and approve once in your browser.**

---

## Get started in 2 steps

You'll need [Claude Code](https://claude.com/claude-code) (the desktop app or
CLI) and an Amaltash account.

### 1. Add the plugin in Claude Code
Type these two commands into Claude Code:

```
/plugin marketplace add arpwal/amaltash-mcp-plugin
/plugin install amaltash@amaltash
```

### 2. Connect your account
Run `/mcp`, pick **amaltash**, and choose **Authenticate**. Your browser opens
to Amaltash — sign in if you aren't already, then click **Approve access**.
That's it: Claude is connected, and it stays connected (the sign-in is
remembered and refreshed for you — no token to paste, nothing to add to your
shell).

> Prefer a click-through guide with screenshots? Open
> [`amaltash-plugin/install-guide.html`](amaltash-plugin/install-guide.html) in
> your browser. Running in a headless or CI environment with no browser? See
> [Headless / automated setup](#headless--automated-setup) below.

---

## Try it — sample prompts

Just talk to Claude normally. A few to get you going:

**Build & test a strategy**
> "Create a momentum strategy on SPY that buys when the 50-day average crosses
> above the 200-day, then backtest it over the last 5 years."

> "Backtest a strategy that rotates monthly into the top 3 performing sectors."

> "My strategy's returns look weak — suggest a few ways to improve them and
> re-test the best one."

**Practice with a paper account (simulated money)**
> "Set me up with a paper trading account so I can try this risk-free."

> "Deploy that strategy to my paper account with $10,000."

**Go live & fund your account**
> "What do I need to do to start trading for real?"  *(Claude gives you the
> links to finish KYC and connect your bank.)*

> "Connect my bank account."   ·   "Deposit $5,000 from my linked bank."

**Check on things**
> "What's my account balance?"   ·   "How much of my cash can I withdraw right
> now, and why?"

> "Show me my connected banks and my brokerage status."

---

## What you can ask for

| You want to… | Just ask |
|---|---|
| **Invent a strategy** from an idea in plain English | "Create a strategy that…" |
| **Improve / fix a strategy** | "Fine-tune this to…" / "optimize the returns" |
| **Backtest** over historical data | "Backtest it over the last 5 years" |
| **Paper trade** with $25K simulated cash | "Set up a paper account" |
| **Deploy** a strategy with real or simulated capital | "Deploy it with $X" |
| **Stop** a running strategy | "Undeploy my strategy" |
| **Finish onboarding** (KYC, bank, exchange) | "What's left to go live?" |
| **Fund / withdraw** | "Deposit $X" / "Withdraw $X" |
| **Check balances & status** | "What's my balance?" |

> 🔒 **You're always in control of your money.** Claude will ask you to confirm
> before any withdrawal or live deployment, and anything sensitive (identity
> verification, connecting a bank, going live) is finished by you in your own
> browser — never automatically.

---

## Good to know

- **Nothing runs on your computer.** The plugin connects to Amaltash's secure
  hosted service over HTTPS; there's no software to install or update.
- **One-click, secure sign-in.** Connecting uses OAuth — the same standard
  "Sign in with…" flow you know from the web. Claude never sees your password,
  and the access it's granted is stored encrypted on your machine and refreshed
  automatically. Revoke it any time from **Settings → Security → Claude
  connector** in your dashboard (or `/mcp` → amaltash → Clear authentication).
- **Questions or issues?** See the
  [install & troubleshooting guide](amaltash-plugin/install-guide.html) or visit
  [amaltash.com](https://amaltash.com).

---

## Headless / automated setup

Most people should use the one-click browser flow above. But if you're running
Claude where **no browser is available** (a server, CI, a container), connect
with a personal token instead:

1. In your dashboard, open **Settings → Security → Claude connector** and click
   **Create token** (copy it — it's shown once and looks like `amat_…`).
2. Add the connector with that token as a Bearer header:

   ```bash
   claude mcp add --transport http amaltash \
     https://mcp.amaltash.com/mcp \
     --header "Authorization: Bearer amat_…"
   ```

The token can be revoked or rotated any time from the same dashboard screen.

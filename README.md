# Amaltash for Claude

Run your [Amaltash](https://amaltash.com) investing account straight from a
conversation with Claude. Describe a strategy in plain English, have Claude
backtest it, paper-trade it with $25,000 of simulated cash, fund a live account,
and check balances — all by just asking. **No coding, no setup beyond three
steps below.**

---

## Get started in 3 steps

You'll need [Claude Code](https://claude.com/claude-code) (the desktop app or
CLI) and an Amaltash account.

### 1. Create your connector token
In your Amaltash dashboard, open **Settings → Security → Claude connector** and
click **Create token**. Copy it right away — it's shown only once and looks like
`amat_xxxx…`. (Think of it like a password for Claude; you can revoke it any
time from the same screen.)

### 2. Give the token to Claude
Paste this into your terminal, replacing `amat_…` with the token you copied:

```bash
export AMALTASH_AGENT_TOKEN="amat_…"
```

> 💡 To avoid doing this every time, add that same line to the end of your
> `~/.zshrc` (Mac) or `~/.bashrc` (Linux) file, then open a new terminal.

### 3. Add the plugin in Claude Code
Type these two commands into Claude Code:

```
/plugin marketplace add arpwal/amaltash-mcp-plugin
/plugin install amaltash@amaltash
```

Restart Claude Code. That's it — Amaltash is now connected. (Prefer a
click-through guide with screenshots? Open
[`amaltash-plugin/install-guide.html`](amaltash-plugin/install-guide.html) in
your browser.)

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
- **Your token is yours.** It's sent privately with each request and can be
  revoked instantly from **Settings → Security → Claude connector**. If it ever
  stops working, just create a new one and update the `AMALTASH_AGENT_TOKEN`
  line from step 2.
- **Questions or issues?** See the
  [install & troubleshooting guide](amaltash-plugin/install-guide.html) or visit
  [amaltash.com](https://amaltash.com).

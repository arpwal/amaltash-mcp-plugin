---
name: amaltash-trading
description: Use when the user wants to do anything on their Amaltash trading account from Claude — sign in / authenticate, create or fine-tune a trading strategy from an idea, run a backtest, deploy or undeploy a strategy, provision a paper account, finish KYC, connect a bank, or deposit / withdraw / check balance. Triggers on "Amaltash", "my trading account", "backtest my strategy", "deploy this strategy", "fund my account", "connect my bank", "paper trading account".
---

# Amaltash trading via the MCP server

This plugin exposes the Amaltash account through the hosted `amaltash` MCP
server (`mcp.amaltash.com`). Authentication is OAuth, handled by the MCP client:
the user runs `/mcp` → **amaltash** → **Authenticate** and approves once in the
browser; Claude stores and refreshes the access token. (Headless/CI setups
instead send a personal `amat_…` token as a Bearer header — see the README.)

## Always start here
1. Call `connection_status` to see what's set up (live/paper account, KYC, banks).
2. If a tool reports it isn't authenticated, the user hasn't connected (or their
   access was revoked/expired). Tell them to run `/mcp`, pick **amaltash**, and
   choose **Authenticate**, then approve in the browser. Do NOT attempt a
   headless login yourself.

## Tool map (call these, don't hand-roll HTTP)

| Goal | Tool |
| --- | --- |
| Confirm the connection works | `authenticate` (validates the current OAuth/Bearer credential) |
| Who am I / profile | `get_account_info` |
| Find strategies | `search_marketplace` |
| Create a strategy from an idea | `create_strategy` (streams token usage → strategy_id) |
| Improve a strategy | `fine_tune_strategy` (revise → draft → backtest → activate) |
| Backtest | `backtest_strategy` (streams; returns metrics + equity_curve) |
| Deploy with capital | `deploy_strategy` (respects the platform minimum) |
| Stop a deployment | `undeploy_strategy` |
| Get a $25K paper account | `provision_paper_account` (streams provision → fund) |
| Where am I in onboarding | `connection_status` |
| Links to finish KYC / connect bank / connect exchange | `connect_links` |
| Brokerage status | `get_brokerage_status` |
| Balance | `get_account_balance` |
| Balance + why cash isn't all withdrawable | `balance_detail` |
| Connected banks | `list_banks` |
| Add money | `deposit_funds` (ACH if a bank is linked, else a Plaid connect link, or `method="wire"`) |
| Take money out | `withdraw_funds` (needs `confirm=true`) |

## Important behaviours
- **Deep-links, not headless actions.** KYC, bank connection (Plaid), and live
  exchange OAuth can't be completed headlessly. For those, call `connect_links`
  and give the user the relevant URL to finish in their signed-in browser.
- **Money moves need confirmation.** Never call `withdraw_funds` with
  `confirm=true` (or `deploy_strategy` live) until the user has explicitly
  approved the amount. Default to paper (`paper_trade=true`) when unsure.
- **Streamed tools** (`create_strategy`, `fine_tune_strategy`,
  `backtest_strategy`, `provision_paper_account`) emit progress as they run —
  relay it. `backtest_strategy` returns a downsampled `equity_curve`
  (`[{t, v}]`) you can render or summarize.
- **No connected account?** `get_account_balance` / `deposit_funds` will say so;
  guide the user to `provision_paper_account` (sandbox) or `connect_links` (live).

## Fine-tuning & error recovery
`fine_tune_strategy` revises a strategy's code from a plain-English instruction,
then drafts → backtests → activates it. Use it two ways:

- **Auto-fix code errors.** If `create_strategy`, `backtest_strategy`, or
  `deploy_strategy` fails because the **strategy code itself errored** (a
  compile/runtime/execution bug in the generated code), don't make the user
  debug it — immediately call `fine_tune_strategy` with the error text as the
  instruction (e.g. "Fix this execution error: <message>"), then re-backtest.
  Tell the user what broke and what you changed. Retry at most **twice**; if it
  still errors, stop and show the error rather than looping.
  - This applies ONLY to code errors. For **environment** failures — not
    authenticated, below the capital minimum, KYC/bank not set up — do NOT
    fine-tune; guide the user to fix the account instead.
- **Offer a next iteration after a clean backtest.** Once a backtest succeeds,
  proactively offer the user two choices:
  1. **Tune it your way** — ask what they want to change (entry/exit rules,
     asset, timeframe, risk limits) and pass that to `fine_tune_strategy`.
  2. **Optimize the returns** — suggest 2–3 concrete improvements (e.g. add a
     stop-loss / take-profit, tune the lookback window, add volatility-based
     position sizing, filter trades by trend/regime, reduce overtrading) and, if
     they pick one, run `fine_tune_strategy` with it. Always re-backtest and
     compare metrics (CAGR, Sharpe, max drawdown) against the prior version so
     the user can see whether it actually improved.

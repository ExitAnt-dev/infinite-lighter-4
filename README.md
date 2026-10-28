# ExitAnt Infinite-Buy — Lighter

Deploy this automated trading bot to **your own Render account** as a background
worker. The bot runs from a prebuilt Docker image; this repository contains **only
the Render Blueprint (`render.yaml`)** — no source code.

## Deploy to Render

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/ExitAnt-dev/infinite-lighter-4)

Or open this link in your browser:

```
https://render.com/deploy?repo=https://github.com/ExitAnt-dev/infinite-lighter-4
```

1. Click the button (or open the link) and sign in to Render.
2. Render reads `render.yaml` and proposes **one background worker in Frankfurt**.
3. Enter the environment variables listed below. They are marked `sync: false`,
   so Render asks you for them on the deploy screen and stores them **only in your
   own Render account** — they are never written to this repository.
4. Click **Apply** and wait for the first deploy to finish.
5. Open Telegram and send **`/start`** to your bot to begin setup.

> Tip: you can also deploy from the Render dashboard — **New → Blueprint**, then
> select this repository. Because `render.yaml` is on the default branch, both
> methods work.

## Before you deploy

- **Region: keep Frankfurt.** Lighter refuses requests from US IP addresses, so the Blueprint places the worker
  in Frankfurt. Do not move it to a US region.
- **A Lighter account with USDC.** Connect your wallet at [app.lighter.xyz](https://app.lighter.xyz) and deposit
  USDC — the first deposit creates the account. On a Simple Trading Account the bot moves USDC from spot to perps
  by itself when a buy needs more collateral; on a Unified Trading Account spot already counts as collateral.
  The bot does not change your account type.
- **Account tier: Plus.** The ExitAnt fee is only accepted on Plus accounts. If your account is on Standard, the bot
  asks for your consent in Telegram and switches it to Plus (Plus adds Lighter's own small trading fee).
- **ExitAnt fee approval.** Right after the welcome message the bot approves the ExitAnt integrator fee for your
  account (0.05% per trade) with your wallet signature. An existing higher approval is never lowered. This approval
  is the membership check: the bot does not trade until it is confirmed, and checks it again before every daily buy.
- **Trading key.** At every start the bot creates its own Lighter API key (slot 252) and registers it with one
  wallet signature. Other ExitAnt bots use other slots, so they can share the account — but do not run two bots on
  the **same symbol** of one account: Lighter keeps one position per market.

## Environment variables

You fill these in during deployment (nothing is pre-filled or stored here):

| Variable | What to enter |
|---|---|
| `LIGHTER_WALLET_KEY` | The **private key** of a **dedicated, bot-only** wallet you use on Lighter (`0x` + 64 hex characters) — not the address, not a recovery phrase, not a Lighter API key |
| `TELEGRAM_BOT_TOKEN` | Bot token issued by [@BotFather](https://t.me/BotFather) |
| `TELEGRAM_CHAT_ID` | Your own Telegram chat ID — the bot ignores every other sender and every group |

## How it works

- **Prebuilt image.** The worker pulls `docker.io/exitant/exitant-infinite-lighter:4` from Docker Hub. The image ships
  a compiled binary only — no Python source is included or exposed.
- **Background worker.** There is no web page; the bot runs continuously and talks
  to you through Telegram. Menus and settings are the same as the ExitAnt Infinite-Buy bot on Hyperliquid; amounts are in USDC.
- **Your key stays yours.** The wallet key is read from your own Render environment, is used only to sign two
  transactions (the trading-key registration and the fee approval) and is never logged, stored, backed up or sent
  anywhere. The bot never asks for a key in Telegram — if you paste one into the chat, it deletes the message and warns you.
- **No disk needed.** Settings are kept in a pinned backup message in your Telegram chat and restored after a
  restart. The backup never contains your wallet key or bot token.

## Important

- **Keep the instance count at 1.** Running two or more instances causes a Telegram
  `getUpdates` conflict and the bot stops responding. `render.yaml` sets
  `numInstances: 1`; do not raise it.
- Render **background workers have no free plan** — choose Starter or higher.
- **Use a dedicated bot-only wallet.** Create a fresh wallet for this bot's Lighter account and fund only what you
  intend to trade. Never enter the private key of your main wallet.
- After the deploy succeeds, send **`/start`** in Telegram to configure the bot.

---

Blueprint spec: <https://render.com/docs/blueprint-spec> ·
Prebuilt images: <https://render.com/docs/deploying-an-image>

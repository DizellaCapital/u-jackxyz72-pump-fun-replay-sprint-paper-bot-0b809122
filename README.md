# Pump.fun Replay Sprint — Paper Bot

[![Make.fun](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![Rank](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/DizellaCapital/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![Reward](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/DizellaCapital/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)

A paper trading bot for the [Pump.fun Replay Sprint](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122) on make.fun. It trades 10 SOL of paper money on a 24-hour replay of recorded pump.fun bonding-curve activity. Nothing is traded for real.

## Strategy: broad momentum, tight stops, let winners run

Most pump.fun coins die, but a fat tail of them run 2–10x. This bot buys **many coins in small stakes**, only when the tape shows genuine buying interest, and cuts every loser fast:

- **Entry** — a coin qualifies when it is old enough to have survived the initial dump (≥240 slots), its real SOL reserves are in a liquid-but-not-pumped range (1–80 SOL), and over the last 40 slots it shows net SOL inflow (≥0.5 SOL), ≥4 buy trades from ≥4 distinct buyers, price up 8–200% and sitting within 5% of its window high, with inflow accelerating into the newer half of the window and no single buyer dominating (>60% of buy SOL).
- **Sizing** — first buy 0.08 SOL, add 0.08 SOL into strength only (position in profit, price above the last buy), up to 0.4 SOL per position, max 5 open positions, max ~2 SOL total exposure. Small stakes keep every loser cheap and spread the bets across the coins that run.
- **Exits** — sell the whole position on a stop loss (-15% vs cost), a trailing stop (-20% from peak, armed at +15%), a fade (net outflow over 20 slots), a big single sell (≥2 SOL), or a time limit (1800 slots, unless up ≥10%). The harness closes positions when a curve completes or the replay ends.
- **Guardrail pacing** — the run must end with a ≥0.01 SOL round trip on 20+ coins. When fills or coins lag the pace, the bot opens tiny 0.01 SOL probe round trips until it is back on pace.

Fully deterministic: every decision is a pure function of the batch stream (no clock, no environment, no randomness), so the two grading runs produce byte-identical action logs.

## Layout

- `main.ts` — entrypoint (run by the grader as `node /bot/main.ts`).
- `strategy.ts` — the trading logic.
- `vendor/makefun-bot.ts` — the house SDK shim for the agent-fun/replay/v2 protocol (vendored, byte-identical to the kit's `sdk/ts/makefun-bot.ts`).

## Local grading

```sh
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process --bundle transcripts.tar.gz
node <kit>/cli/makefun.mjs grade-local --kit <kit> --bot . --driver process --stress 20
```

## License

MIT — see [LICENSE](LICENSE).

# The Seven Experts — Confluence Panel

A multi-indicator confluence system for TradingView that consolidates the verdicts of six technical "experts" into a single directional score, plus named trade setups and forward-looking predictions. Shown as three stacked tables on the right side of your chart.

![Pine Script](https://img.shields.io/badge/Pine%20Script-v6-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## What it does

Most traders stare at a dozen indicators at once and try to mentally reconcile them. This indicator **does the reconciliation for you**. It watches six independent signals, scores each one, and sums them into a single number called **confluence**. That number then drives a plain-English trade recommendation for the **next bar**.

At a glance you get:

- **A current-regime read** (are we in an uptrend, downtrend, top, base?)
- **A named setup** if one is firing (breakout, pullback, bounce, rollover, overextension)
- **A directional bet** for the next bar, with stop and target
- **A hit/miss scorecard** for the previous prediction so you can audit its accuracy in real time

---

## Installation

1. Open TradingView, click **Pine Editor** at the bottom of the chart.
2. Copy the contents of [`panel_seven_experts.pine`](./panel_seven_experts.pine) and paste into the editor.
3. Click **Save**, give it a name, then click **Add to chart**.
4. Three tables appear on the right side of your chart.

---

## The six "experts"

Each one contributes a vote between −2 and +2. Total range: **−9 to +9**.

| Expert | What it measures | Vote range |
|---|---|---|
| **Ribbon strength** | Smoothed Heikin Ashi trend filter — is the market bullish/bearish and how strongly | −2 to +2 |
| **Price vs. ribbon** | Is price above, below, or inside the trend band | −1, 0, +1 |
| **Momentum sign** | B-Xtrender oscillator — above or below zero | −1, 0, +1 |
| **Momentum slope** | Is momentum accelerating or decelerating | −1, 0, +1 |
| **Structure** | Higher-highs/higher-lows vs. lower-highs/lower-lows | −1, 0, +1 |
| **Volume** | Expansion + candle direction (conviction behind the move) | −1, 0, +1 |

The system takes a trade only when the sum crosses **±3**. Below that threshold it says "stand aside."

---

## The four stages

Borrowed from Stan Weinstein's cycle model:

- **Stage 1 — Base:** sideways accumulation after a downtrend
- **Stage 2 — Uptrend:** the trend you want to be long in
- **Stage 3 — Top:** distribution, uptrend losing steam
- **Stage 4 — Downtrend:** the trend you want to be short or out of

Derived from ribbon color + slope + price position.

---

## The five archetypes

Named setups the indicator looks for. If none are firing, it shows "—".

| Code | Name | When it fires |
|---|---|---|
| **A** | Stage 2 pullback | Pullback to ribbon in an uptrend with strong confluence |
| **B** | Stage 1→2 breakout | Ribbon just turned green with strong bullish confluence |
| **C** | Stage 4 bounce | Counter-trend mean reversion in a downtrend |
| **D** | Distribution / rollover | Top formation with strong bearish confluence |
| **E** | Overextension | Price stretched too far from the ribbon — possible reversal |

---

## The three tables

### 1. Previous Candle (top-right)
**Backtest of the last prediction.** Shows what the model predicted one bar ago versus what actually happened. This is your real-time accuracy check — if the previous row consistently shows "✗ MISS," trust the current signal less.

Key rows:
- **Direction result:** ✓ HIT / ✗ MISS — was the directional bet right?
- **Range result:** ✓ In-range / ✗ Broke out — did price stay inside the predicted ATR box?
- **Invalidation:** ✓ Held / ✗ Broken — did price respect the stop-loss line?

### 2. Current Candle (middle-right)
**Live grading of the in-flight prediction.** Same structure as Previous, but the "Actual" column is the bar that's still forming. Watch this to see if the current bet is on track or breaking down.

### 3. Next Candle (bottom-right)
**The forecast.** Pure prediction, no "actual" column yet. The right column ("Basis") explains *why* each number is what it is. The **One-liner** row is the actionable summary.

---

## How to read a signal

A good trade setup lines up like this:

1. **Stage** = 2 (uptrend) or 4 (downtrend) — you're with the bigger regime, not fighting it.
2. **Archetype** = a named setup (not "—") — the model recognizes *what kind* of trade this is.
3. **Confluence** ≥ +3 for longs or ≤ −3 for shorts — enough experts agree.
4. **Momentum** direction matches the trade — not fighting the move.
5. **Invalidation** holding — the thesis is still alive.
6. **One-liner** tells you the entry, stop, and target.

If even one of these is wrong (especially confluence), the system is telling you **don't trade**.

---

## Worked example

Imagine you see this in the Next Candle table:
- Direction: **▲ UP**
- Confidence: **Medium**
- Expected High: **27.45**
- Invalidation: **26.14**
- Stage: **Stage 2 · Uptrend**
- Setup: **A · Stage 2 pullback**
- One-liner: *"Long above 27.10, stop 26.14, first target 27.45"*

**Interpretation:** The market is in a confirmed uptrend. Price has pulled back to the trend band and is bouncing. At least four experts agree the next bar leans up. You can buy if price takes out 27.10, put your stop at 26.14 (a loss of ~$1), and your first profit target is 27.45 (a gain of ~$0.35). Risk/reward roughly 1:0.35, but confidence is Medium — so size accordingly, or wait for High confidence.

---

## How to read a "no-signal"

When the indicator says stand aside, it's often *more* valuable than a trade signal. Examples:

- **Confluence +2, Momentum Bearish Falling, Ribbon Green Strong** → uptrend is intact but short-term is weakening. Don't short into the uptrend; don't buy into the weakness. **Wait.**
- **Ribbon Flat, Archetype "—", Confluence 0** → pure chop. Any trade here is a coin flip.

The indicator is designed to be **quiet 70% of the time and confident 30% of the time**. That's the feature, not a bug.

---

## Key settings

| Input | What it controls | When to change |
|---|---|---|
| **HA smoothing length (100)** | How slow the ribbon reacts | Lower for faster signals on intraday, higher for position trading |
| **Ribbon smoothing length (100)** | Band thickness | Same logic as above |
| **Swing lookback (20)** | How far back to measure structure highs/lows | Larger for higher timeframes |
| **ATR length (14)** | Volatility window | Standard; rarely change |
| **Expected-range ATR× (1.0)** | Width of next-bar forecast box | Raise to 1.5–2.0 on volatile instruments |
| **Overextension ATR× (2.0)** | Trigger for "stretched too far" archetype | Raise on volatile names |

---

## Alerts

Five built-in alert conditions:

- **Bullish confluence turn** — confluence crossed above +3
- **Bearish confluence turn** — confluence crossed below −3
- **Stage 1→2 breakout** — fresh breakout fired
- **Stage 3 rollover** — distribution pattern detected
- **Overextension** — possible exhaustion

Set these in TradingView's alert dialog to get pinged without having to watch the chart.

---

## History export

Enable **"Log CSV rows to Pine Logs"** in the settings to emit one row per closed bar containing the prediction, actual outcome, hit/miss flags, and all sub-indicator values. Open the Pine Logs panel, copy the rows, and paste into Excel or Python to compute your own hit-rate statistics over any time period.

Columns exported:
`time, pred_dir, act_dir, dir_hit, pred_high, act_high, pred_low, act_low, range_hit, pred_invalid, invalid_broken, confluence, stage, archetype, bxraw, ribbon_score, struct_score, vol_score`

---

## The mental model

- **Ribbon** tells you *which way* to lean.
- **Stage** tells you *where you are* in the cycle.
- **Momentum** tells you if the move has *juice* left.
- **Volume** tells you if anyone *believes* in the move.
- **Structure (HH/HL or LH/LL)** tells you if the *pattern* confirms it.
- **Confluence** is the **sum of all votes** — the single number that decides whether the system takes a trade.
- **Archetype** names the setup so you know *what kind of trade* this is.
- **Invalidation + Expected range** give you the risk/reward frame.

If confluence isn't ≥ ±3, everything else is just context — the system won't trade.

---

## Limitations

- **Works only on regular candle charts** (Bars, Candles, Hollow Candles). Heikin Ashi, Renko, Kagi, PnF, and Range bars feed synthetic OHLC and will distort the readings.
- **Closed-bar signals.** Current-bar values update live, but the *verdict* is based on the previous bar's close — so predictions don't repaint.
- **Range side only.** The forecast box is ±ATR around close; it doesn't model skewed distributions.
- **No fundamentals.** Pure technical — earnings, news, and macro are not inputs.

---

## Bottom line

The Seven Experts panel is a **discipline tool**. It replaces gut-feel confluence with a scored, documented, auditable version of the same logic — and forces you to wait for real agreement before pulling the trigger. The hit-rate column lets you verify in real time that the model's predictions are actually landing on *your* instrument, *your* timeframe, before you put size on.

If confluence < ±3, **do nothing**. If confluence ≥ ±3 and the one-liner makes sense, the setup has already been pre-checked for you.

---

## License

[MIT](./LICENSE) — use it, modify it, ship it. No warranty. Not financial advice.

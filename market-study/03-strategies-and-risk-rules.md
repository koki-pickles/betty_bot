# Market Study — 03. Strategies & Risk Rules

> **Status:** draft · **Date:** 2026-10-21 · **Updated:** 2026-11-02 (rules made precise enough to code) · **Related:** [02-use-cases.md](02-use-cases.md)
>
> These are the starting settings. Every number can be changed in the settings form (UC-01, UC-06). None of these strategies is assumed to be profitable: each one has to pass the validation gate (UC-03) and the demo gate (UC-04) like any other. If a strategy fails, it fails. I don't tweak it until the backtest looks good, because that is exactly the curve fitting described in pain point #3.
>
> Every rule below has to be exact. In a backtest, a vague word ("pulls back to", "too close", "very wide") becomes a guess in the code, and two different guesses can give two very different results.

## Risk rules (defaults)

| Rule | Default | Limit | Why |
|---|---|---|---|
| Risk per trade | **1%** of balance | never above **2%** | Five losses in a row at 1% is −5%, not −14% |
| Stop loss | **required** on every trade | — | No trade without a defined risk |
| Minimum reward : risk | **2 : 1** for S1 and S2, **1 : 1** for S3 | set per strategy | 2R is profitable even with ~40% winning trades. Mean reversion (S3) wins more often with smaller targets, so 2R would block almost all its trades |
| Daily loss limit | **3%** (open losses included) | — | Pain point #2: most blown accounts die on one bad day |
| Losses in a row | stop for the day after **3** | — | Common advice from traders who overtrade after losses |
| News pause | **15 min before → 15 min after** high-impact news (USD, EUR) | — | Gold and NAS100 spike on NFP, CPI, FOMC |
| Max drawdown | **10%** from peak → strategy paused | — | Last safety net before the emergency stop |
| Max open positions | **1 per strategy per market** | — | Keeps risk easy to read |
| Max spread | trade skipped if spread > **10% of the stop distance** | — | Works the same on every market and timeframe. A wide spread can turn a good setup into a losing trade |
| Weekend | all positions closed **1 hour before the market closes on Friday** | — | Gold and NAS100 can gap past the stop over the weekend |

**Position sizing:** the bot calculates the lot size from the stop loss distance so that a stopped-out trade loses exactly the configured risk %. The trader never picks the lot size by hand.

**Changing rules:** tightening a rule applies immediately; loosening it applies from the next trading day (UC-06).

---

## General trading rules (all strategies)

- **Signals are checked on candle close only.** The bot never acts on a candle that is still forming.
- **Entry:** when a signal appears on a closed candle, the bot sends a **market order at the open of the next candle**. This works the same in backtest, demo and live, and the backtest can't "see the future" (lookahead bias, pain point #3).
- **Overnight:** allowed. **Weekend:** not allowed (see risk rules).
- **No trade management:** once a trade is open, only its stop loss and take profit close it (plus the time exits written in each strategy, the weekend close, and the risk guard). No breakeven, no trailing stop for now, because these are the hardest parts to backtest reliably (pain point #3).
- **Hours** are in each city's local time (London, New York), so daylight saving changes are handled automatically.
- **Every rule is checked before each order:** risk rules, spread, news pause, trading hours. If one fails, the trade is skipped and the reason is logged (UC-07).

---

## The 3 built-in strategies

Each strategy targets a different kind of market, so together they cover trending, breakout and ranging conditions.

### S1 — Trend Pullback

| | |
|---|---|
| **Markets** | XAUUSD, NAS100 |
| **Timeframe** | H1 |
| **Trading hours** | New trades only between **08:00 and 20:00 London time** (London + New York sessions) |
| **Idea** | Trade in the direction of the trend, entering on a pullback instead of chasing the move |
| **Trend filter** | Long only if EMA 50 > EMA 200, short only if EMA 50 < EMA 200 |
| **No trend** | No trade if the distance between EMA 50 and EMA 200 is less than **0.5 × ATR(14)** |
| **Pullback** | Uptrend: a candle's **low touches or crosses the EMA 20**. Downtrend: a candle's **high touches or crosses the EMA 20** |
| **Entry signal** | Within the **3 candles** after the pullback (the pullback candle included), a candle **closes back on the trend side** of the EMA 20 (above it in an uptrend, below it in a downtrend). Entry at the next candle's open |
| **Stop loss** | 1.5 × ATR(14) from entry |
| **Take profit** | 2 × the stop distance (2R) |
| **Trade management** | None: SL and TP only |

**Adjustable in the form:** EMA periods, no-trend threshold, ATR multiplier, reward : risk.

### S2 — Opening Range Breakout

| | |
|---|---|
| **Markets** | NAS100 (New York open), XAUUSD (London open) |
| **Timeframe** | M15 |
| **Idea** | The first 30 minutes of a session set a range; a clean break of that range often starts the day's move |
| **Session open** | NAS100: **09:30 New York time**. XAUUSD: **08:00 London time** |
| **Range** | High and low of the first 30 minutes after the session opens (the first 2 M15 candles) |
| **Wide-range filter** | No trade that day if the range is larger than **0.5 × daily ATR(14)** (a chaotic open, often a news day) |
| **Entry signal** | A M15 candle closes above the range high (long) or below the range low (short). Entry at the next candle's open |
| **Stop loss** | Other side of the range |
| **Take profit** | 2R |
| **Limits** | Max 1 trade per session; no new entry later than 2 hours after the open |
| **Time exit** | Any open trade is closed at the session close: NAS100 at **16:00 New York time**, XAUUSD at **16:30 London time** |

**Adjustable in the form:** range length (15/30/60 min), session, wide-range threshold.

### S3 — Mean Reversion

| | |
|---|---|
| **Markets** | EURUSD |
| **Timeframe** | H1 |
| **Trading hours** | New trades only between **08:00 and 20:00 London time** (when EUR/USD is liquid and spreads are tight) |
| **Idea** | EUR/USD often moves sideways; in a flat market, price tends to come back to its average after stretching too far |
| **Market filter** | Only trade when ADX(14) < 20 (no strong trend) |
| **Entry signal** | Long: a candle **closes at or below** the lower Bollinger Band (20, 2) **and** RSI(14) < 30. Short: a candle **closes at or above** the upper band **and** RSI(14) > 70. A wick touching the band is not enough. Entry at the next candle's open |
| **Stop loss** | 1 × ATR(14) beyond the band |
| **Take profit** | Middle Bollinger Band, measured at entry. Trade skipped if this gives less than **1R** |
| **Exit early** | ADX rises above 25 (a trend is starting): close at the next candle's open |

**Adjustable in the form:** Bollinger settings, RSI levels, ADX thresholds, minimum reward : risk.

---

## Summary

| Strategy | Market type | Markets | Timeframe | Hours (new trades) | Min R:R | Indicators |
|---|---|---|---|---|---|---|
| S1 Trend Pullback | Trending | XAUUSD, NAS100 | H1 | 08:00–20:00 London | 2 : 1 | EMA 20/50/200, ATR |
| S2 Opening Range Breakout | Breakout | NAS100, XAUUSD | M15 | First 2 h after the open | 2 : 1 | Session range, daily ATR |
| S3 Mean Reversion | Ranging | EURUSD | H1 | 08:00–20:00 London | 1 : 1 | Bollinger Bands, RSI, ADX, ATR |

MVP order: **S1 first** (simplest to build and test), then S2, then S3.

## To check with the first backtests

- Does each strategy reach **100 trades in 2 years** (validation gate, UC-03)? S2 should (about 500 sessions in 2 years, max 1 trade each). S1 and S3 are less certain because of their filters. If one doesn't, the backtest period gets longer. I don't loosen the filters just to get more trades.
- How often does the spread filter skip trades on each market? If it skips a lot, the broker's costs are too high for that strategy.

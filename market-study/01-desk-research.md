# Market Study — 01. Desk Research

> **Status:** draft · **Date:** 2026-10-21 · **Method:** online desk research (forums, reviews, vendor blogs)
>
> **Limits:** Main sources are Forex Factory, BabyPips, MQL5, Myfxbook and Trustpilot. I then read r/Forex, r/Daytrading and r/algotrading separately and found the same pain points (see #6 for an extra one). Several sources are vendors selling competing tools and may be biased. This research shows where the problems are; it doesn't prove anyone wants Betty Bot. That needs the user interviews.

## Goal

Find the complaints that keep coming up among retail traders (forex, gold, indices) about:
- their own trading process (discipline, risk, backtesting)
- the tools they use: TradingView, FX Replay, Edgewonk, MT5 / MQL5 bots

## Recurring pain points

### 1. "I know my rules but I don't follow them" (most frequent)
- On BabyPips, traders describe breaking their own plan "over and over again", even after promising to stop for the day at a set win or loss amount.
- The fixes people suggest all depend on willpower: asking a friend or family member to check on them, hiding open trades from the screen, stopping after 3 losses in a row, taking a break after a big loss.
- BabyPips lists boredom (which leads to forced trades), overconfidence after wins and lost confidence after losing streaks as reasons traders drop their plan.
- **Note:** the forum advice is all willpower, but tools that enforce the rules do exist. Free and paid MT5 risk guard EAs (PropGuard, Prop Firm Equity Guard, KT Equity Protector…) close trades at a loss limit and block new ones. They're single-purpose and built for prop firm accounts, not for learning (see competitor overview).

### 2. Daily losses and revenge trading blow up accounts
- One prop firm (OneFunded) reports that **78.7% of failed challenges ended on the daily loss limit**, 15.0% on the overall loss limit and 6.3% on other rules. The strategy was rarely the cause.
- Traders don't check how much of their daily loss limit is left before entering a trade, forget that open losses count right away, and lose a whole day's budget on one candle during news (NFP, CPI).
- "One more trade" when close to the target, oversizing, and revenge trading after a loss come up again and again.
- Suggested fixes: check the remaining daily loss before every entry, set a personal stop at about 50–60% of the firm's limit, risk at most 1% per trade.

### 3. "My backtest looked great, then I lost money live"
- MT4/MT5 backtests assume perfect fills, use a fixed spread taken at test time, and estimate price movement inside each candle instead of using real ticks. Scalping and trailing-stop bots are the least reliable to backtest.
- Strategies tuned too closely to past data and lookahead bias inflate results. A Forex Factory user's bot went from 1000 to 90000 in a 3-month backtest, then performed nowhere near that live.
- Data feeds differ between brokers, so testing on a demo account with your own broker's live feed is recommended.
- MQL5 Market buyers report bots that "only win up until the last upload date", faked backtests and fake reviews (Trustpilot rating about 1/5).

### 4. Manual backtesting is slow, and its results are scattered
- TradingView Bar Replay takes **10–40 hours to test one year** of data. It calculates no statistics, replays one market at a time, can briefly show future candles when switching timeframes, and puts intraday replay behind a paid plan.
- FX Replay (Trustpilot about 4.2/5) gets complaints about lag, freezing and lost sessions, take profits not closing, a limited free tier, billing and cancellation trouble, and no automation or custom strategies.

### 5. Journals are expensive, and you fill them in by hand
- Edgewonk costs about $197/year with no real free trial. It has no backtesting engine, no trade replay, no mobile app and limited broker imports.
- Its psychology tracking only works if the trader logs every trade honestly by hand.
- Traders end up with their journal, backtests and live trading spread across separate tools.

### 6. Too much strategy content, and most of it is fake (found by hand on Reddit)
- Traders are flooded with strategies from YouTube, social media, Discords and courses, often with faked or cherry-picked results.
- They can't tell which ones actually work, so they jump from one strategy to the next without testing any of them properly.
- This is the same pattern as the faked MQL5 Market backtests in #3, but for strategies sold as content instead of bots.
- **Gap:** traders have no quick, neutral way to check a strategy against real data before believing it.

## Competitor overview

| Tool | Backtesting | Journal | Automation | Enforces risk rules | Main complaints |
|---|---|---|---|---|---|
| TradingView Bar Replay | Manual, slow | No | Alerts only | No | Paywall, no stats, one market at a time |
| FX Replay | Manual replay | Basic | No | No | Lag/bugs, billing, no automation |
| Edgewonk | Basic simulator | Yes (strong analytics) | No | No | Price, no trial, manual entry |
| MT5 / MQL5 bots | Strategy Tester | No | Yes | Depends on the bot | Unrealistic backtests, scams, no refunds |
| MT5 risk guard EAs (PropGuard, Equity Guard, KT Equity Protector) | No | No | Closes/blocks trades only | **Yes** | Single-purpose, prop-firm focused, teach nothing |
| StrategyQuant X / Build Alpha | Yes, advanced robustness tests (walk-forward, Monte Carlo) | No | Generates EAs | No | Expert tools, steep learning curve |
| **Betty Bot (target)** | Automated + demo phase | Automatic | Yes (MT5) | **Yes** | — |

## Takeaways for Betty Bot

1. **Backtesting on its own is a crowded market.** It's not where Betty Bot stands out.
2. **Each feature already exists on its own; the gap is the connected flow for learners.** Risk guard EAs enforce rules, StrategyQuant and Build Alpha validate strategies, Edgewonk journals. No single tool takes a learner through *test a strategy → prove it on demo → go live with the guard on → journal everything automatically*. The expert tools are too complex for learners, and the risk guards do one job only.
3. **Possible pitch:**
   > Betty Bot won't let you trade a strategy with real money until it has proven itself in backtesting and on a demo account. Once it's live, it enforces your risk rules (daily loss limit, pause around news, stop after X losses in a row) so emotions can't override them.
4. **The risk guard is necessary but not a differentiator on its own** (pain points 1 and 2). Free EAs already do it, so its value comes from being part of the flow.
5. **The backtest-to-demo gate also filters fake strategies (#6).** Any strategy someone hears about has to prove itself on real data before it can touch real money. Betty Bot doesn't promise a winning strategy; it tells you which ones don't work.

## Next steps

- [x] Read r/Forex, r/Daytrading and r/algotrading by hand to check these findings (confirmed, plus pain point #6)
- [ ] Self-study: log personal rule breaks and losses for 2 weeks
- [ ] Interview 5 traders (questions about past behaviour, not "would you use…")
- [ ] Write 2–3 user profiles and rank the pain points

## Sources

- [BabyPips – Mistake in trading that keeps on repeating](https://forums.babypips.com/t/mistake-in-trading-that-keeps-on-repeating/281083)
- [BabyPips – Overtrading Wiped Me](https://forums.babypips.com/t/overtrading-wiped-me/152395?page=3)
- [BabyPips – 4 Reasons Traders Can't Stick to Trade Plans](https://www.babypips.com/trading/reasons-traders-cant-stick-to-trade-plans-2022-07-18)
- [OneFunded – Why traders fail prop firm challenges](https://onefunded.com/blog/trading-psychology/why-traders-fail-prop-firm-challenges/)
- [MQL5 – Why prop firm challenges really fail](https://www.mql5.com/en/forum/517925)
- [Forex Factory – Why does backtesting differ so much?](https://www.forexfactory.com/thread/post/3284834)
- [Forex Factory – Mismatch between forward and back testing](https://www.forexfactory.com/thread/1113547-mismatch-between-live-forward-testing-and-back-testing)
- [Forex Factory – Does backtesting work?](https://www.forexfactory.com/thread/535226-does-backtesting-work)
- [Myfxbook – Why your backtest and live account never agree](https://www.myfxbook.com/community/general/why-your-backtest-your-live/3364621,1)
- [Forex Factory – MQL5 Market: a scammer's paradise](https://www.forexfactory.com/thread/1080525-mql5-market-a-scammers-paradise-and-activations-scam)
- [MQL5 forum – EA scams](https://www.mql5.com/en/forum/457568)
- [Trustpilot – MQL5](https://www.trustpilot.com/review/www.mql5.com?page=3)
- [Trustpilot – FX Replay](https://www.trustpilot.com/review/fxreplay.com)
- [QuantVPS – FX Replay review](https://www.quantvps.com/blog/fx-replay-review)
- [TradingJournal.com – Edgewonk review](https://tradingjournal.com/review/edgewonk)
- [Trustpilot – Edgewonk](https://www.trustpilot.com/review/edgewonk.com)
- [Market Maker Method – TradingView replay issues](https://marketmakermethod.com/tradingview-issues)
- [fxbacktest – TradingView Bar Replay free limits](https://fxbacktest.app/guide/tradingview-bar-replay-free-limits/)
- [MQL5 – PropGuard](https://www.mql5.com/en/code/77710)
- [MQL5 – Prop Firm Equity Guard](https://www.mql5.com/en/code/78243)
- [MQL5 – PropFirm Risk Guardian MT5](https://www.mql5.com/en/code/77591)
- [KT Equity Protector EA](https://www.keenbase-trading.com/products/equity-protector-ea/)
- [StrategyQuant](https://strategyquant.com/)
- [Build Alpha – Robustness Testing Guide](https://www.buildalpha.com/robustness-testing-guide/)
- [Backtrex – TradingView Bar Replay tutorial](https://backtrex.com/en/blog/tradingview-bar-replay-backtesting-tutorial)

# Futures & Options: Learning From Scratch

A from-the-ground-up reference for understanding derivatives — what they are, how futures and options actually work mechanically, how they're priced, and how they're used in practice (hedging, speculation, income strategies).

## Table of Contents

1. [What Is a Derivative?](#what-is-a-derivative)
2. [Forwards vs. Futures vs. Options — The Big Picture](#forwards-vs-futures-vs-options--the-big-picture)
3. [Futures Contracts](#futures-contracts)
4. [Margin, Leverage & Mark-to-Market](#margin-leverage--mark-to-market)
5. [Options Contracts](#options-contracts)
6. [Options Terminology Glossary](#options-terminology-glossary)
7. [Payoff Diagrams: The Four Basic Positions](#payoff-diagrams-the-four-basic-positions)
8. [Moneyness: ITM, ATM, OTM](#moneyness-itm-atm-otm)
9. [What Determines an Option's Price](#what-determines-an-options-price)
10. [The Greeks](#the-greeks)
11. [Why Use Futures & Options?](#why-use-futures--options)
12. [Common Strategies](#common-strategies)
13. [Key Risks](#key-risks)
14. [How to Actually Start Learning/Practicing](#how-to-actually-start-learningpracticing)
15. [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## What Is a Derivative?

A **derivative** is a financial contract whose value is *derived* from the price of something else — the "underlying" (a stock, an index, a commodity, a currency, a bond, even an interest rate).

You're not buying the underlying asset itself; you're buying a contract that pays off based on what the underlying asset does.

- **Why derivatives exist:**
  - **Hedging** — protect against adverse price moves you're already exposed to (a farmer locking in a crop price, an exporter locking in an exchange rate).
  - **Speculation** — take a view on price direction without owning the underlying, often with leverage.
  - **Arbitrage** — exploit price discrepancies between the derivative and the underlying (or between related derivatives).

Futures and options are the two most common exchange-traded derivatives. Both let you take a position on the *future* price of something today.

---

## Forwards vs. Futures vs. Options — The Big Picture

| | Forward | Future | Option |
|---|---|---|---|
| **What it is** | Private agreement to buy/sell at a set price on a set future date | Standardized, exchange-traded version of a forward | Right (not obligation) to buy/sell at a set price by/on a set date |
| **Obligation?** | Both parties *must* transact | Both parties *must* transact | Buyer *may* transact; seller *must* if buyer exercises |
| **Where traded** | Over-the-counter (OTC), private | Exchange (standardized contract) | Exchange (standardized contract) |
| **Counterparty risk** | Yes (no clearinghouse) | No (clearinghouse guarantees it) | No (clearinghouse guarantees it) |
| **Upfront cost** | None | None (margin posted, not a price) | Premium paid upfront by buyer |
| **Max loss (buyer)** | Unlimited (symmetric) | Unlimited (symmetric) | Limited to premium paid |

The core distinction to internalize early: **a future is an obligation; an option is a choice.** That one difference drives almost everything else about how they're priced, used, and risk-managed.

---

## Futures Contracts

A **futures contract** is a standardized agreement to buy (go "long") or sell (go "short") a specific quantity of an underlying asset at a specific price (the **futures price**), on a specific future date (**expiry**), traded on an exchange.

### Key Mechanics

- **Contract size / lot size** — futures are standardized (e.g., one crude oil futures contract = 1,000 barrels; one index futures contract = a fixed multiplier × index value). You can't trade an arbitrary quantity.
- **Expiry date** — contracts expire monthly/quarterly; you can hold to expiry (settle) or close your position earlier by taking an offsetting trade.
- **Settlement:**
  - **Cash-settled** — the difference between contract price and final settlement price is paid/received in cash (common for index futures — you never actually receive "the index").
  - **Physically settled** — the actual underlying is delivered (common for commodities, though most speculative traders close out before this happens).
- **Long position** — you profit if the price *rises* above your entry price.
- **Short position** — you profit if the price *falls* below your entry price. Critically, going short a future is just as natural/symmetric as going long — there's no need to "borrow" anything first (unlike shorting a stock).

### Example

You buy 1 futures contract on Stock XYZ at ₹1,000, lot size 100 (so contract value = ₹100,000).

- If XYZ rises to ₹1,050 at expiry → you gain ₹50 × 100 = ₹5,000.
- If XYZ falls to ₹950 at expiry → you lose ₹50 × 100 = ₹5,000.

Notice the symmetry: unlike an option, your gain or loss is linear and uncapped in both directions.

---

## Margin, Leverage & Mark-to-Market

This is the part of futures that trips up most beginners, so it's worth slowing down on.

- **You don't pay the full contract value to enter a futures position.** You post **margin** — a fraction of the contract's notional value — as a good-faith deposit with the exchange/broker.
  - **Initial margin** — the amount required to open the position.
  - **Maintenance margin** — the minimum balance you must maintain; if losses push your account below it, you get a **margin call** and must deposit more or your position gets forcibly closed (liquidated).
- **This margin is what creates leverage.** If a contract is worth ₹100,000 and you only post ₹10,000 margin, you're controlling 10x your capital — a 10% move in the underlying is a 100% move on your margin, in either direction. Leverage magnifies gains *and* losses equally.
- **Mark-to-market (MTM)** — unlike most contracts, futures profits/losses are settled in cash *daily*, not just at expiry. Every day, the exchange recalculates your position's value against the day's closing price and credits/debits your account accordingly. This is what keeps counterparty risk low (losses can't silently accumulate to expiry) but it also means you need enough cash on hand to cover adverse moves *along the way*, not just at the end.

**This is the single most important risk fact about futures:** leverage + daily mark-to-market means you can be forced out of a position (or lose far more than your initial margin) well before your original thesis has time to play out, purely due to short-term volatility.

---

## Options Contracts

An **option** gives the buyer the **right, but not the obligation**, to buy or sell the underlying at a fixed price (the **strike price**) on or before a fixed date (**expiry**), in exchange for paying a **premium** upfront to the seller (**writer**).

### The Two Types

- **Call option** — the right to **buy** the underlying at the strike price. Buyers want the price to go **up**.
- **Put option** — the right to **sell** the underlying at the strike price. Buyers want the price to go **down**.

### The Two Sides of Every Option

| Role | Call | Put |
|---|---|---|
| **Buyer (long / "holder")** | Pays premium; right to buy at strike; profits if price rises well above strike | Pays premium; right to sell at strike; profits if price falls well below strike |
| **Seller (short / "writer")** | Receives premium; obligated to sell at strike if exercised; profits if price stays below strike | Receives premium; obligated to buy at strike if exercised; profits if price stays above strike |

**Critical asymmetry to internalize:** the **buyer's** maximum loss is capped at the premium paid, but their potential gain is large (calls: theoretically unlimited; puts: capped at strike value). The **seller's** situation is the mirror image: capped maximum gain (the premium received), but potentially large/unlimited loss. This asymmetry is *the* defining feature of options and the reason buying options and selling (writing) options are completely different risk profiles, not just opposite sides of the same trade.

### American vs. European Style

- **American-style** — can be exercised any time up to expiry.
- **European-style** — can only be exercised *at* expiry (most index options, e.g., Nifty/Bank Nifty in India, are European-style; many US equity options are American-style).

This affects strategy and pricing (American options are worth at least as much as equivalent European ones, since optionality to exercise early has some value, even if rarely used).

---

## Options Terminology Glossary

- **Strike price (exercise price)** — the fixed price at which the option holder can buy (call) or sell (put) the underlying.
- **Premium** — the price paid by the buyer to the seller for the option; this is what you risk as a buyer and what you receive as a seller.
- **Expiry / expiration date** — the date the option contract ceases to exist.
- **Exercise** — the act of the buyer invoking their right under the contract.
- **Assignment** — what happens to the seller when a buyer exercises — the seller is now obligated to fulfill the contract.
- **Lot size** — the standardized quantity of the underlying one contract represents (same concept as in futures).
- **Open interest (OI)** — the total number of outstanding (not yet closed or expired) contracts for a given strike/expiry — a proxy for how much "action"/liquidity is at that strike.
- **Intrinsic value** — the amount an option would be worth if exercised right now (zero if it wouldn't be profitable to exercise).
- **Time value (extrinsic value)** — the portion of an option's premium above its intrinsic value, reflecting the chance it becomes more valuable before expiry. Premium = Intrinsic Value + Time Value.
- **Implied volatility (IV)** — the market's expectation of future volatility, *implied* by the option's current market price (backed out of an option pricing model) — see [What Determines an Option's Price](#what-determines-an-options-price).
- **Option chain** — the table listing all available strikes and expiries for an underlying, with their prices, OI, IV, and volume — the standard view traders use to look at options.

---

## Payoff Diagrams: The Four Basic Positions

Every options strategy, no matter how complex, is built from combinations of these four basic positions. Visualizing payoff at expiry (profit/loss on the Y-axis, underlying price on the X-axis) is the single most useful habit to build early.

```
Long Call (bought a call)              Short Call (sold/wrote a call)
Profit                                  Profit
  │           ╱                           │
  │         ╱                             │─────────╲
──┼───────╱──── Price                   ──┼───────────╲──── Price
  │     ╱  (loss capped                   │             ╲ (loss
Loss  ╱    at premium)                  Loss              unlimited)

Long Put (bought a put)                 Short Put (sold/wrote a put)
Profit                                  Profit
  │╲                                      │
  │  ╲                                    │        ╱──────
──┼────╲──── Price                      ──┼──────╱──────── Price
  │      ╲ (loss capped                   │    ╱ (loss capped
Loss       at premium)                  Loss ╱  at strike value)
```

- **Long call** — unlimited upside, loss capped at premium paid. Used when bullish.
- **Long put** — large upside (capped at strike, since price can't go below zero), loss capped at premium paid. Used when bearish.
- **Short call** — income capped at premium received, unlimited downside risk. Used when neutral-to-bearish (often with a hedge — see Strategies).
- **Short put** — income capped at premium received, large downside risk (capped at strike). Used when neutral-to-bullish, or as a way to "get paid to wait" for a lower entry price.

Every multi-leg strategy (spreads, straddles, condors, etc.) is just several of these four added together.

---

## Moneyness: ITM, ATM, OTM

Describes the relationship between the strike price and the current underlying price:

| | Call | Put |
|---|---|---|
| **In-the-money (ITM)** | Strike < current price (has intrinsic value) | Strike > current price (has intrinsic value) |
| **At-the-money (ATM)** | Strike ≈ current price | Strike ≈ current price |
| **Out-of-the-money (OTM)** | Strike > current price (no intrinsic value, pure time value) | Strike < current price (no intrinsic value, pure time value) |

- OTM options are cheaper (pure time value, no intrinsic value) and riskier to hold to expiry — they expire worthless unless the underlying moves enough to make them ITM.
- ITM options are more expensive but behave more like owning the underlying itself (higher "delta" — see Greeks).

---

## What Determines an Option's Price

An option's premium is driven by six inputs (formalized in pricing models like **Black-Scholes** for European options, or binomial trees for American options):

1. **Underlying price** — how far the current price is from the strike.
2. **Strike price** — relative to the underlying price (moneyness).
3. **Time to expiry** — more time = more chance of a favorable move = more time value.
4. **Volatility (implied)** — higher expected volatility = higher chance of a large favorable move = higher premium. This is usually the single biggest driver of day-to-day option price changes, more than the underlying's actual move.
5. **Interest rates** — affects the cost of carrying a hedged position; a smaller effect for most retail use cases.
6. **Dividends** (for equities) — expected dividends reduce call premiums and increase put premiums slightly, since the stock price is expected to drop by the dividend amount on the ex-date.

You don't need to compute Black-Scholes by hand to trade options, but understanding *which* of these six inputs is moving is what separates informed trading from guessing — e.g., losing money on a long call even though you were right about direction, because implied volatility collapsed (this is extremely common around earnings announcements).

---

## The Greeks

The **Greeks** measure an option's sensitivity to each of the pricing inputs above — essentially the partial derivatives of the option price with respect to each variable. You don't need calculus to use them; you need the intuition.

| Greek | Measures sensitivity to... | Intuition |
|---|---|---|
| **Delta (Δ)** | Underlying price | How much the option's price moves per ₹1 move in the underlying. Also roughly the probability the option expires ITM. Calls: 0 to 1. Puts: 0 to -1. |
| **Gamma (Γ)** | Delta itself | How fast Delta changes as the underlying moves. High gamma = your directional exposure can change very quickly (common near expiry, near the strike). |
| **Theta (Θ)** | Time | How much value the option loses per day, all else equal ("time decay"). Always negative for option buyers — options are a decaying asset by nature. |
| **Vega (ν)** | Implied volatility | How much the option's price changes per 1% change in implied volatility. Long options = long vega (benefit from rising IV); short options = short vega. |
| **Rho (ρ)** | Interest rates | How much the option's price changes per 1% change in interest rates. Usually the least impactful Greek for short-dated retail trading. |

**The single most important beginner intuition:** buying an option means time (Theta) is working against you every single day, and you additionally need the underlying to move far enough, fast enough, to overcome both time decay and any drop in implied volatility. This is why "I was right about direction but still lost money" is one of the most common new-options-trader experiences — it usually means Theta and/or Vega moved against the position even as Delta worked in their favor.

---

## Why Use Futures & Options?

1. **Hedging** — e.g., an investor holding a stock portfolio buys index put options or sells index futures to protect against a market downturn without selling the actual holdings.
2. **Speculation with leverage** — take a larger position than your capital would otherwise allow, amplifying both gains and losses.
3. **Income generation** — selling (writing) options against existing holdings (e.g., covered calls) to collect premium as regular income.
4. **Expressing a non-linear view** — e.g., "I think volatility will spike but I don't know the direction" is a view that's *impossible* to express with just stock, but straightforward with options (see Straddle below).
5. **Price discovery** — futures markets often reflect the market's forward-looking expectation of a price before it's visible in the spot market.

---

## Common Strategies

Built from combining the basic positions above. Ordered roughly from simplest to more advanced.

- **Covered Call** — own the underlying + sell a call against it. Caps your upside in exchange for premium income; a way to generate yield on a stock you're willing to sell at the strike.
- **Protective Put** — own the underlying + buy a put. Acts like insurance: caps your downside in exchange for paying a premium, while keeping unlimited upside.
- **Cash-Secured Put** — sell a put while holding enough cash to buy the underlying if assigned. A way to either collect premium or "get paid" to buy a stock at a lower price than today's.
- **Bull Call Spread** — buy a call at a lower strike + sell a call at a higher strike (same expiry). Reduces cost (and caps profit) versus a plain long call — a defined-risk bullish bet.
- **Bear Put Spread** — buy a put at a higher strike + sell a put at a lower strike. The bearish mirror of the above.
- **Straddle** — buy a call and a put at the *same* strike and expiry. Profits from a large move in *either* direction; loses if the underlying stays flat. A pure bet on volatility/magnitude, not direction.
- **Strangle** — like a straddle, but using OTM calls and puts (different strikes). Cheaper than a straddle, but needs an even bigger move to profit.
- **Iron Condor** — sell an OTM call spread + sell an OTM put spread simultaneously. Profits if the underlying stays within a range until expiry; a popular defined-risk income strategy in low-volatility conditions.

**The general pattern:** buying options = paying for the chance of a big move (defined, limited risk); selling options = collecting income for providing that chance to someone else (often undefined or large risk unless paired with a hedge, as in a spread or iron condor).

---

## Key Risks

- **Leverage risk (futures & short options)** — small adverse moves can produce losses far exceeding your initial margin/capital.
- **Time decay (long options)** — even being directionally correct isn't enough; you must be correct *within the option's remaining lifetime*.
- **Volatility risk (Vega)** — implied volatility can collapse even when you're right on direction (classic post-earnings move), eroding or eliminating gains.
- **Unlimited risk (short calls, naked short positions)** — selling uncovered/naked options or holding a short futures position without a hedge exposes you to theoretically unlimited loss.
- **Liquidity risk** — illiquid strikes/expiries (low open interest, wide bid-ask spreads) make it expensive or difficult to enter/exit at a fair price.
- **Assignment risk (option sellers)** — American-style short options can be assigned at any time before expiry, sometimes unexpectedly (e.g., around dividend dates for calls).
- **Margin call / forced liquidation risk (futures)** — a string of adverse daily mark-to-market moves can force you out of a position at the worst possible time, even if your original thesis was eventually correct.

**The one-sentence version:** in futures and options, *how much you can lose and how fast* is usually far more important to understand upfront than *how much you could gain.*

---

## How to Actually Start Learning/Practicing

A sensible progression, in order:

1. **Master the vocabulary and payoff diagrams first** (sections above) — before touching real money, be able to draw the payoff diagram for any basic position from memory.
2. **Paper trade** — most brokers offer a simulated/paper trading mode; use it to place trades, watch Greeks move in real time, and see what happens as expiry approaches, with zero financial risk.
3. **Start with buying, not selling** — buying a call or put has strictly limited, known-upfront risk (the premium), which is a much safer way to learn the mechanics than writing (selling) options, where risk can be large/unlimited.
4. **Use an option chain and a payoff calculator** — most brokers/exchanges provide a live option chain and a strategy payoff visualizer; use it to see theoretical P&L across prices *before* placing a trade, not after.
5. **Learn index options before single-stock options** — index options (e.g., on a broad market index) tend to have more liquidity, more predictable volatility behavior, and (in many markets) simpler European-style, cash-settled mechanics, making them a gentler starting point than individual stock options.
6. **Only move to selling (writing) options, spreads, and futures once comfortable with #1–#5** — and start small, sized so that a full loss of margin/premium is a cost you can absorb without affecting your finances.
7. **Study real market data, not just theory** — track an actual option chain for a stock/index you know well for a few weeks; watch how premium, IV, and Greeks move with the underlying before ever placing a trade.

---

## Quick Reference Cheat Sheet

| Concept | One-line definition |
|---|---|
| Future | Obligation to transact at a set price on a set date |
| Option | Right (not obligation) to transact at a set price by/on a set date |
| Call | Right to buy |
| Put | Right to sell |
| Premium | Price paid for an option |
| Strike | The fixed transaction price in the contract |
| ITM / ATM / OTM | Has intrinsic value / at the strike / no intrinsic value |
| Margin | Good-faith deposit to hold a futures/short-option position |
| Mark-to-market | Daily cash settlement of futures gains/losses |
| Delta | Sensitivity to underlying price |
| Theta | Daily time decay (works against option buyers) |
| Vega | Sensitivity to implied volatility |
| Long option max loss | Premium paid |
| Short (naked) option max loss | Potentially unlimited (calls) / large (puts) |

---

*This document covers conceptual fundamentals only — it is not financial advice. Futures and options involve substantial risk of loss and are not suitable for all investors; practice on a simulator and understand a strategy's full risk profile before trading with real capital.*

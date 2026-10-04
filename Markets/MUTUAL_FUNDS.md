# Mutual Funds: Learning From Scratch

A from-the-ground-up reference for understanding mutual funds — how they're structured, the different types, how returns and costs actually work, and how to evaluate one.

## Table of Contents

1. [What Is a Mutual Fund?](#what-is-a-mutual-fund)
2. [How a Mutual Fund Actually Works](#how-a-mutual-fund-actually-works)
3. [NAV: Net Asset Value](#nav-net-asset-value)
4. [Open-Ended vs. Closed-Ended Funds](#open-ended-vs-closed-ended-funds)
5. [Active vs. Passive Management](#active-vs-passive-management)
6. [Types of Mutual Funds](#types-of-mutual-funds)
7. [Direct vs. Regular Plans, and Costs](#direct-vs-regular-plans-and-costs)
8. [How Returns Are Measured](#how-returns-are-measured)
9. [SIP vs. Lump Sum](#sip-vs-lump-sum)
10. [Risk: Riskometer & Fund Categorization](#risk-riskometer--fund-categorization)
11. [Taxation (India, General Overview)](#taxation-india-general-overview)
12. [Evaluating a Fund: What Actually Matters](#evaluating-a-fund-what-actually-matters)
13. [Mutual Funds vs. Direct Stock Picking](#mutual-funds-vs-direct-stock-picking)
14. [Common Pitfalls](#common-pitfalls)
15. [How to Actually Start Learning/Practicing](#how-to-actually-start-learningpracticing)
16. [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## What Is a Mutual Fund?

A **mutual fund** pools money from many investors and invests it, as a single portfolio, in stocks, bonds, or other securities, managed by a professional **fund manager** on behalf of all the pooled investors. Each investor owns **units** of the fund proportional to how much they put in, and shares proportionally in the fund's gains, losses, income, and costs.

- **Why it exists** — it lets an individual investor get diversified, professionally managed exposure to markets with a small amount of money, without having to pick individual securities or have the capital to build a diversified portfolio alone.
- **Who regulates it** — in India, mutual funds are regulated by **SEBI** (Securities and Exchange Board of India), which sets rules on fund categorization, disclosure, expense limits, and investor protection; in the US, it's the SEC.
- **Who runs it** — an **Asset Management Company (AMC)** (e.g., the fund house) creates and manages the fund; a fund manager (or team) makes the day-to-day buy/sell decisions within the fund's stated mandate.

---

## How a Mutual Fund Actually Works

```
Many investors → pool money → Mutual Fund (a trust) → invests in → Stocks/Bonds/Cash
     ↑                                                                    │
     └──────────────── own units proportional to investment ◄────────────┘
```

1. Investors buy **units** of the fund at the current **NAV** (Net Asset Value per unit).
2. The fund manager invests the pooled money according to the fund's stated **mandate** (e.g., "large-cap Indian equity," "short-duration debt") described in its **Scheme Information Document (SID)**.
3. As the underlying holdings gain or lose value, the fund's total value (and therefore its NAV) rises or falls.
4. When an investor **redeems** (sells) units, they receive the current NAV per unit (minus any applicable exit load/tax), regardless of what other investors in the fund do.

Every investor in the same fund and plan gets the exact same NAV-based return — a mutual fund doesn't "run out" of good returns for later investors the way a fixed-price asset might.

---

## NAV: Net Asset Value

**NAV per unit** is the price of one unit of the fund, calculated once per business day (for most open-ended funds):

```
NAV per unit = (Total value of fund's holdings − Fund's liabilities) / Total units outstanding
```

- NAV moves with the market value of the fund's underlying holdings — it isn't set by supply and demand for the fund's units the way a stock's price is (mutual fund units aren't traded on an exchange the way stocks are; you transact directly with the AMC at the day's NAV, with a few exceptions like exchange-traded funds).
- A **lower NAV is not "cheaper"** in any meaningful sense — unlike a stock price, NAV says nothing about valuation. A fund with NAV ₹10 and a fund with NAV ₹500 can deliver identical percentage returns; only the number of units you're allotted for a given investment amount differs.

---

## Open-Ended vs. Closed-Ended Funds

| | Open-Ended | Closed-Ended |
|---|---|---|
| Can you buy/sell anytime? | Yes, on any business day, at that day's NAV | No — fixed tenure; units bought only during a launch window (NFO) |
| Liquidity | High — redeem directly with the AMC anytime | Lower — must sell on a stock exchange (if listed) to another investor before maturity |
| Unit count | Variable — grows/shrinks as investors enter/exit | Fixed at launch |
| Most common type | Yes — the vast majority of mutual funds | No — a small minority of the market |

The overwhelming majority of mutual funds investors encounter day to day are open-ended; closed-ended funds are a niche structure, often used for less liquid underlying strategies.

---

## Active vs. Passive Management

- **Actively managed funds** — a fund manager/team actively researches and picks securities, aiming to **outperform** a benchmark index (e.g., a large-cap fund trying to beat the Nifty 50). Higher expense ratio, reflecting the research/management effort.
- **Passively managed funds (Index Funds / ETFs)** — simply replicate a benchmark index (e.g., a Nifty 50 index fund holds the same 50 stocks in the same proportion as the index), with no attempt to beat it. Much lower expense ratio, since there's no active research/decision-making involved.

A large and long-running body of research shows that **most actively managed funds underperform their benchmark index over long horizons, after costs** — which is the core argument for passive/index investing, especially in highly efficient, well-researched markets (e.g., US large-cap). This case is somewhat weaker in less efficient market segments (e.g., certain emerging-market small/mid-caps), where skilled active managers have historically had more room to add value — but costs and manager skill both still matter either way.

---

## Types of Mutual Funds

### By Asset Class

| Type | Invests mainly in | Typical risk/return profile |
|---|---|---|
| Equity funds | Stocks | Higher risk, higher long-term return potential |
| Debt funds | Bonds, money-market instruments | Lower risk, lower, more stable returns |
| Hybrid funds | A mix of equity and debt | Moderate risk, moderate return — risk level depends on the equity/debt split |
| Money market / Liquid funds | Very short-term debt instruments | Lowest risk, modest returns; used as a cash-parking alternative |

### Equity Fund Sub-Categories (by market cap / style)

- **Large-cap funds** — invest in the largest, most established companies; relatively more stable.
- **Mid-cap / Small-cap funds** — invest in smaller, faster-growing (but more volatile) companies.
- **Multi-cap / Flexi-cap funds** — invest across market-cap segments at the manager's discretion.
- **Sectoral/Thematic funds** — concentrated in a specific sector (e.g., banking, IT) or theme (e.g., ESG); higher risk due to lack of diversification across sectors.
- **ELSS (Equity-Linked Savings Scheme)** — equity funds with a tax deduction benefit (in India, under Section 80C) and a mandatory 3-year lock-in.

### Debt Fund Sub-Categories (by duration/credit)

- **Overnight / Liquid funds** — invest in instruments maturing in days; near-cash safety.
- **Short/Medium/Long duration funds** — categorized by the average maturity of holdings; longer duration = more sensitive to interest rate changes.
- **Corporate bond / Credit risk funds** — categorized by the credit quality of issuers held; higher yield generally means higher credit risk (risk of default).

### Other Structures

- **Index funds / ETFs** — passive funds tracking an index (see above).
- **Fund of Funds (FoF)** — a fund that invests in other mutual funds rather than directly in securities (e.g., for international diversification).
- **Solution-oriented funds** — built around a goal with a mandatory lock-in, e.g., retirement funds, children's education funds.

---

## Direct vs. Regular Plans, and Costs

Every mutual fund scheme in India is offered in two plan variants:

| | Direct Plan | Regular Plan |
|---|---|---|
| Bought via | Directly from the AMC (or via a direct platform) | Through a distributor/advisor/broker |
| Commission to distributor? | No | Yes, embedded in the expense ratio |
| Expense ratio | Lower | Higher (by the commission amount) |
| Same underlying portfolio? | Yes — identical holdings to the regular plan of the same scheme | Yes |

Because the underlying portfolio is identical, a direct plan will **always** deliver a slightly higher return than its regular-plan twin, by exactly the difference in expense ratio, compounded over time — the only thing you give up with a direct plan is the distributor's advice/service.

### Key Costs to Know

- **Expense Ratio (TER — Total Expense Ratio)** — the annual fee, expressed as a % of assets, covering fund management, administration, and (for regular plans) distributor commission. Charged daily, embedded in the NAV (you never see a separate bill).
- **Exit Load** — a fee charged if you redeem before a specified holding period (e.g., 1% if redeemed within 1 year), designed to discourage short-term churn.
- **No "entry load"** — entry loads were banned by SEBI in 2009; you don't pay a fee to invest, only (potentially) to exit early.

---

## How Returns Are Measured

- **Absolute return** — simple % change in NAV over a period: `(End NAV − Start NAV) / Start NAV`. Fine for periods under a year; misleading for longer periods since it doesn't annualize.
- **CAGR (Compound Annual Growth Rate)** — the annualized return for a lump-sum investment held over multiple years: `(End Value / Start Value)^(1/years) − 1`. The standard way to compare lump-sum fund performance across different time periods.
- **XIRR (Extended Internal Rate of Return)** — the annualized return accounting for multiple cash flows at different dates — essential for SIPs (regular periodic investments), since CAGR alone can't handle money going in at different times.
- **Rolling returns** — CAGR calculated repeatedly over overlapping windows (e.g., every 3-year period within the last 10 years), giving a much more honest picture of consistency than a single point-to-point return, which can be flattered or hurt by the specific start/end dates chosen.
- **Benchmark comparison** — a fund's return only means something relative to its benchmark index and category peers over the same period; a 12% return looks very different if the benchmark did 8% versus 16%.

---

## SIP vs. Lump Sum

- **SIP (Systematic Investment Plan)** — investing a fixed amount at regular intervals (usually monthly), automatically buying more units when NAV is low and fewer when NAV is high (**rupee-cost averaging**), removing the need to time the market and building investing discipline.
- **Lump Sum** — investing a large amount all at once.

| | SIP | Lump Sum |
|---|---|---|
| Best suited for | Regular income earners, building a habit, volatile/equity markets | Investors with a large sum available now, more conviction on market timing |
| Market timing risk | Averaged out over time | Full exposure to whatever the market does right after investing |
| Behavioral benefit | Automates discipline, reduces emotional decision-making | None inherently — requires separate discipline |

Neither is strictly superior in all conditions — SIP tends to reduce timing risk and smooth the emotional experience of investing in volatile markets, but in a sustained rising market a lump sum invested early will generally outperform the same amount drip-fed in via SIP, simply because more money is exposed to the market for longer.

---

## Risk: Riskometer & Fund Categorization

SEBI mandates every scheme display a **Riskometer** — a simple visual gauge of the fund's risk level, ranging from "Low" to "Very High," based on the fund's category and underlying holdings (e.g., a liquid fund shows "Low to Moderate"; a small-cap equity fund shows "Very High").

- Risk here means **volatility and potential for loss**, not just "chance of losing all your money" — even a "Very High" risk equity fund isn't expected to go to zero, but its NAV can swing significantly in the short-to-medium term.
- SEBI's fund categorization rules (introduced 2017) standardized what a fund can call itself (e.g., a "large-cap fund" must hold at least a specified minimum % in the largest companies by market cap), making it easier to compare like-for-like funds across different AMCs.
- **Risk and the investor's time horizon are linked** — a fund appropriate for a goal 15 years away (can ride out short-term volatility) may be entirely inappropriate for a goal 1 year away (where capital preservation matters more than growth).

---

## Taxation (India, General Overview)

*(General awareness only — tax rules change; verify current rates before making decisions.)*

- **Equity-oriented funds** (≥65% in domestic equities) — gains held **over 1 year** are taxed as **Long-Term Capital Gains (LTCG)**; gains held **under 1 year** as **Short-Term Capital Gains (STCG)**, generally at a higher rate.
- **Debt-oriented and other funds** — historically had a separate long-term rate with indexation benefit; rules here have changed in recent years, so this is an area to specifically verify against the current Finance Act rather than relying on older assumptions.
- **ELSS** — gets an upfront Section 80C deduction (within the overall 80C limit) in addition to equity-fund capital gains treatment on exit, but comes with a mandatory 3-year lock-in.
- **Dividends/IDCW (Income Distribution cum Capital Withdrawal)** — taxed in the investor's hands at their income tax slab rate.

---

## Evaluating a Fund: What Actually Matters

A practical checklist, roughly in order of importance:

1. **Does the category match your goal and time horizon?** — the single biggest driver of a good outcome; a great small-cap fund is still a bad choice for a goal 1 year away.
2. **Consistency of performance vs. benchmark/peers over multiple market cycles** — look at rolling returns across at least one full bull-and-bear cycle, not just trailing 1-year or 3-year returns, which can be dominated by a single good or bad year.
3. **Expense ratio** — lower, all else equal, is better — and compounds meaningfully over long holding periods; always prefer the direct plan unless you specifically value an advisor's ongoing service.
4. **Fund manager tenure and consistency of process** — has the manager (or team/process) been stable, or has there been recent turnover that makes past performance less predictive?
5. **Portfolio concentration and quality** — how diversified is the fund across stocks/sectors, and does the portfolio composition match what the fund claims to be (**"style drift"** — e.g., a "large-cap fund" quietly holding a lot of mid-caps — is a red flag)?
6. **AUM (Assets Under Management) trends** — rapidly shrinking AUM (large redemptions) can be a warning sign; for small/mid-cap funds, *very* large AUM can also hurt agility, since it becomes harder to trade meaningful positions in less liquid stocks.
7. **Downside capture ratio** — how much of the benchmark's decline the fund captured during past down periods; a fund that falls less than its benchmark in bad markets (while still capturing most of the upside) indicates good risk management, not just a lucky good year.

Past returns, while the most commonly cited number, should be the *last* thing weighted heavily — they're the least predictive of future performance of everything on this list.

---

## Mutual Funds vs. Direct Stock Picking

| | Mutual Funds | Direct Stock Picking |
|---|---|---|
| Diversification | Built-in, even with small amounts | Requires meaningfully more capital to diversify properly |
| Time/skill required | Minimal — delegated to a professional manager | Significant — requires [fundamental analysis](FUNDAMENTAL_ANALYSIS.md) skill and ongoing monitoring |
| Costs | Expense ratio (ongoing, embedded) | Brokerage/transaction costs (per trade), no ongoing management fee |
| Control | Limited — manager decides individual holdings | Full — you choose every position |
| Tax events | Fewer — switching stocks within the fund doesn't trigger your personal capital gains tax | More — every personal trade can be a taxable event |

Mutual funds and direct stock picking aren't mutually exclusive — many investors use mutual funds as their diversified "core" and allocate a smaller "satellite" portion to direct stock picks they have high conviction in after doing their own [fundamental analysis](FUNDAMENTAL_ANALYSIS.md).

---

## Common Pitfalls

- **Chasing last year's top performer** — the best-performing fund in one category one year is rarely the best the next; fund performance mean-reverts more often than it persists.
- **Ignoring the expense ratio difference between direct and regular plans** — over 15–20 years, even a 1% annual difference compounds into a very large gap in final corpus.
- **Over-diversifying across too many funds** — holding 15+ funds, especially within the same category, mostly just replicates an index at a higher combined cost, without the simplicity of an actual index fund.
- **Panic-selling during a downturn** — redeeming equity fund units during a market crash locks in the loss; the long-term return assumptions behind equity investing rely on staying invested through volatility.
- **Ignoring the fund's actual category when assessing "risk"** — comparing a liquid fund's return against a small-cap fund's return as if they're substitutes misunderstands what each is for.
- **Confusing a fund's NAV level with cheapness** — as noted above, NAV level says nothing about valuation (see [NAV](#nav-net-asset-value)).
- **Not aligning fund choice with actual time horizon** — putting short-term emergency funds into equity funds, or multi-decade retirement savings into ultra-conservative debt/liquid funds, both undermine the goal they're meant to serve.

---

## How to Actually Start Learning/Practicing

A sensible progression, in order:

1. **Start with a single, broad, low-cost index fund** (e.g., a Nifty 50 or Nifty 500 index fund, direct plan) to understand the mechanics of buying, holding, and tracking a fund before adding complexity.
2. **Set up a small SIP** to experience rupee-cost averaging firsthand — watch how the number of units allotted changes month to month as NAV moves.
3. **Read one fund's actual Scheme Information Document (SID) and factsheet** — understand what a real mandate, expense ratio, exit load, and portfolio composition actually look like in the primary source, not just a summary on an app.
4. **Track rolling returns, not just trailing returns**, for a few funds you're considering, using a free screener — this alone will reframe how you read "past performance."
5. **Compare a direct plan vs. regular plan of the same scheme** side by side to see the real-money impact of the expense ratio difference over a hypothetical 10–20 year holding period.
6. **Build a simple goal-based allocation** (e.g., equity fund for a 10-year goal, debt/liquid fund for a 1-year goal) rather than picking funds in isolation from what they're actually for.
7. **Revisit your funds annually, not daily** — check category consistency, manager/process stability, and whether the fund still fits your goal; avoid reacting to short-term NAV noise.

---

## Quick Reference Cheat Sheet

| Concept | One-line definition |
|---|---|
| NAV | Price per unit of a fund, calculated once per day from the value of its holdings |
| Unit | A share of ownership in a mutual fund |
| AMC | The company that manages the fund |
| Expense ratio (TER) | Annual fee charged as a % of assets, embedded in NAV |
| Exit load | Fee for redeeming before a specified holding period |
| Direct plan | Bought directly from the AMC — lower expense ratio, no distributor commission |
| Regular plan | Bought via a distributor — higher expense ratio, includes commission |
| Active fund | Manager tries to beat the benchmark |
| Passive fund (Index fund/ETF) | Simply replicates a benchmark index |
| SIP | Fixed-amount, regular interval investing — averages purchase cost over time |
| XIRR | Annualized return accounting for multiple cash flows at different dates |
| CAGR | Annualized return for a single lump-sum investment |
| Riskometer | SEBI-mandated visual gauge of a fund's risk level |
| ELSS | Tax-saving equity fund with a 3-year lock-in |

---

*This document covers conceptual fundamentals only — it is not financial or tax advice. Mutual fund investments are subject to market risk; tax rules change over time and should be verified against current regulations (or a qualified advisor) before making decisions.*

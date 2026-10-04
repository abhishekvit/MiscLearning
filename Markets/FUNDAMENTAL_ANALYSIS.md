# Fundamental Analysis: Learning From Scratch

A from-the-ground-up reference for understanding fundamental analysis — how to read a company's financial statements, value a business, and judge whether a stock is worth owning at its current price.

## Table of Contents

1. [What Is Fundamental Analysis?](#what-is-fundamental-analysis)
2. [Top-Down vs. Bottom-Up Analysis](#top-down-vs-bottom-up-analysis)
3. [The Three Financial Statements](#the-three-financial-statements)
4. [Key Financial Ratios](#key-financial-ratios)
5. [Valuation Methods](#valuation-methods)
6. [Qualitative Analysis: Moat, Management, Governance](#qualitative-analysis-moat-management-governance)
7. [Industry & Macro Context](#industry--macro-context)
8. [Growth vs. Value Investing](#growth-vs-value-investing)
9. [Reading an Annual Report](#reading-an-annual-report)
10. [Fundamental vs. Technical Analysis](#fundamental-vs-technical-analysis)
11. [Common Pitfalls](#common-pitfalls)
12. [How to Actually Start Learning/Practicing](#how-to-actually-start-learningpracticing)
13. [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

---

## What Is Fundamental Analysis?

**Fundamental analysis** is the practice of estimating a security's **intrinsic value** — what a business is actually worth — by examining the real economic factors behind it: revenue, profit, debt, cash flow, competitive position, and management quality. The goal is to answer one question: *is the current market price above, at, or below what this business is really worth?*

This sits in contrast to **technical analysis**, which ignores the business entirely and studies price/volume charts to predict short-term price movement (see [Fundamental vs. Technical Analysis](#fundamental-vs-technical-analysis)).

- **Core premise** — over the long run, a stock's price tends to track the underlying business's earnings and cash generation, even though in the short run price can swing far from intrinsic value due to sentiment, liquidity, and macro noise.
- **Who uses it** — long-term investors (value investors, growth investors, most mutual funds — see [MUTUAL_FUNDS.md](MUTUAL_FUNDS.md)) rather than short-term traders.
- **The output** — an estimate of **fair value per share**, compared against the current **market price**, to decide whether a stock is undervalued, fairly valued, or overvalued.

---

## Top-Down vs. Bottom-Up Analysis

Two starting points for the same research process:

| | Top-Down | Bottom-Up |
|---|---|---|
| Starting point | Macro economy → sector → company | Individual company first, macro second |
| Question asked first | "Which economies/sectors look attractive right now?" | "Is this specific business good, regardless of the macro backdrop?" |
| Typical user | Asset allocators, macro-driven fund managers | Stock-pickers, value investors |
| Risk | Can miss a great company in an out-of-favor sector | Can miss macro headwinds that hurt an otherwise good company |

Most practical analysis blends both: use top-down thinking to understand the environment a company operates in ([Industry & Macro Context](#industry--macro-context)), then do bottom-up, company-specific work to decide if *this* business, at *this* price, is worth owning.

---

## The Three Financial Statements

Every public company publishes three core statements (quarterly and annually); fundamental analysis starts here.

### 1. Income Statement (Profit & Loss)

Shows profitability over a period (a quarter or a year).

```
Revenue (Sales)
  − Cost of Goods Sold (COGS)
  = Gross Profit
  − Operating Expenses (SG&A, R&D)
  = Operating Profit (EBIT)
  − Interest & Taxes
  = Net Profit (Net Income)
```

- **Revenue growth** — is the top line growing, and how fast relative to the industry?
- **Margins** — gross margin, operating margin, net margin (each as a % of revenue) show how much of each sales rupee actually turns into profit, and whether that's improving or eroding.
- **EPS (Earnings Per Share)** — Net Profit ÷ number of shares outstanding; the per-share profitability figure most valuation multiples are built on.

### 2. Balance Sheet

A snapshot of what the company **owns** and **owes** at a single point in time.

```
Assets = Liabilities + Shareholders' Equity
```

- **Assets** — cash, inventory, receivables (current assets); property, plant, equipment, goodwill (non-current/fixed assets).
- **Liabilities** — what the company owes: payables, short-term borrowings (current); long-term debt (non-current).
- **Shareholders' Equity** — the residual: what's left for owners after all liabilities are paid off. Grows via retained profits, shrinks via losses or buybacks/dividends beyond profit.
- Read together with the income statement, the balance sheet tells you *how* a company's growth was funded — organically (retained earnings) or with debt (leverage).

### 3. Cash Flow Statement

Tracks actual cash moving in and out — arguably the hardest statement to manipulate, and the one many experienced analysts check first.

- **Operating Cash Flow (OCF)** — cash generated from core business operations. Should track net profit reasonably closely over time; a large, persistent gap (profit rising, operating cash flow not) is a red flag worth investigating (e.g., revenue booked but not yet collected).
- **Investing Cash Flow** — cash spent on or received from capital expenditure (capex), acquisitions, asset sales.
- **Financing Cash Flow** — cash from/to debt issuance or repayment, equity issuance, dividends, buybacks.
- **Free Cash Flow (FCF)** = Operating Cash Flow − Capital Expenditure. This is the cash genuinely available to a company after it has reinvested enough to sustain itself — the basis for [DCF valuation](#valuation-methods).

---

## Key Financial Ratios

Ratios turn raw statement numbers into comparable, scale-independent signals.

### Profitability

| Ratio | Formula | What it tells you |
|---|---|---|
| Gross Margin | Gross Profit ÷ Revenue | Pricing power / production efficiency |
| Operating Margin | Operating Profit ÷ Revenue | Core operating efficiency, before financing/tax effects |
| Net Margin | Net Profit ÷ Revenue | Bottom-line profitability after everything |
| ROE (Return on Equity) | Net Profit ÷ Shareholders' Equity | How efficiently shareholder capital generates profit |
| ROCE (Return on Capital Employed) | EBIT ÷ (Total Assets − Current Liabilities) | Return generated on *all* capital (debt + equity) deployed in the business |

### Liquidity & Solvency

| Ratio | Formula | What it tells you |
|---|---|---|
| Current Ratio | Current Assets ÷ Current Liabilities | Can the company cover near-term obligations? (>1 generally healthy) |
| Quick Ratio | (Current Assets − Inventory) ÷ Current Liabilities | Same, but stricter — excludes hard-to-liquidate inventory |
| Debt-to-Equity | Total Debt ÷ Shareholders' Equity | How leveraged the company is; higher = more financial risk |
| Interest Coverage | EBIT ÷ Interest Expense | How comfortably the company can service its debt from operating profit |

### Efficiency

| Ratio | Formula | What it tells you |
|---|---|---|
| Asset Turnover | Revenue ÷ Total Assets | How efficiently assets are used to generate sales |
| Inventory Turnover | COGS ÷ Average Inventory | How quickly inventory is sold and replaced |
| Receivables Days | (Receivables ÷ Revenue) × 365 | How long it takes to collect cash from customers |

### Valuation

See [Valuation Methods](#valuation-methods) below — P/E, P/B, EV/EBITDA and dividend yield are valuation ratios specifically, kept separate because they compare financials *against the market price*, not just against each other.

---

## Valuation Methods

### Relative Valuation (Multiples)

Compare a company's price to a financial metric, then compare that multiple against peers, the sector average, or its own history.

| Multiple | Formula | Best used for |
|---|---|---|
| P/E (Price-to-Earnings) | Share Price ÷ EPS | Mature, profitable companies; the most widely used multiple |
| P/B (Price-to-Book) | Share Price ÷ Book Value per Share | Asset-heavy businesses (banks, financials) |
| EV/EBITDA | Enterprise Value ÷ EBITDA | Comparing companies with different debt loads/tax structures |
| Dividend Yield | Annual Dividend per Share ÷ Share Price | Income-focused investing, mature low-growth companies |
| PEG Ratio | P/E ÷ Expected Earnings Growth Rate | Adjusting P/E for growth — a "cheap-looking" high P/E can be justified by high growth |

A low P/E isn't automatically "cheap" and a high P/E isn't automatically "expensive" — multiples only mean something **relative to** growth prospects, margins, risk, and the peer set being compared against.

### Absolute Valuation: Discounted Cash Flow (DCF)

Estimates intrinsic value directly from a company's expected future cash flows, discounted back to today's value using a required rate of return (the discount rate).

```
Intrinsic Value = Σ [ FCF_year_n / (1 + r)^n ]  +  Terminal Value / (1 + r)^N
```

- **FCF_year_n** — projected free cash flow in each future year of an explicit forecast period (commonly 5–10 years).
- **r** — the discount rate, typically the **Weighted Average Cost of Capital (WACC)**, reflecting the riskiness of those future cash flows.
- **Terminal Value** — the value of all cash flows *beyond* the explicit forecast period, usually estimated assuming a stable long-term growth rate thereafter.
- DCF is powerful because it's grounded in cash generation rather than market sentiment, but it's also highly sensitive to its assumptions — small changes in the growth rate or discount rate can swing the output dramatically, so it's best used to establish a *range* of fair value, not a single precise number.

### Dividend Discount Model (DDM)

A DCF variant for dividend-paying, stable companies: values a stock as the present value of all future expected dividends rather than free cash flow. Most useful for mature utilities, banks, and similarly stable dividend payers; less useful for growth companies that reinvest everything and pay no dividend.

---

## Qualitative Analysis: Moat, Management, Governance

Numbers alone don't capture everything that determines whether a business stays good for the next decade.

- **Economic moat** — a durable competitive advantage that protects profits from competitors: brand power, network effects, switching costs, cost advantages (scale), regulatory/licensing barriers, or intellectual property. A business with a wide moat can sustain high ROE/margins for longer; one without will see competitors erode its returns over time.
- **Management quality** — track record of capital allocation (do they reinvest wisely, or destroy value with overpriced acquisitions/buybacks?), communication honesty (do guidance and results generally line up?), and insider alignment (do promoters/executives hold meaningful equity, and are they buying or selling?).
- **Corporate governance** — board independence, related-party transactions, audit quality, history of regulatory issues, promoter pledging of shares (a red flag in Indian markets specifically, since pledged shares can trigger forced selling if the stock falls and the promoter is margin-called).
- **Business model clarity** — can you explain, in one or two sentences, how this company actually makes money and why customers choose it over alternatives? If not, that's a signal to dig deeper (or walk away) before relying on the numbers.

---

## Industry & Macro Context

### Porter's Five Forces (Industry Attractiveness)

A framework for judging how structurally profitable an industry is likely to be, regardless of any single company within it:

1. **Threat of new entrants** — how easy is it for new competitors to enter?
2. **Bargaining power of suppliers** — can suppliers squeeze the industry's margins?
3. **Bargaining power of buyers** — can customers demand lower prices?
4. **Threat of substitutes** — can customers switch to a fundamentally different solution?
5. **Competitive rivalry** — how intense is competition among existing players?

An industry with high barriers to entry, weak supplier/buyer power, few substitutes, and rational competition tends to support healthier margins for everyone in it — a company's moat is partly a function of which industry it sits in, not just company-specific factors.

### Macro Factors Worth Tracking

- **Interest rates** — higher rates raise the discount rate used in DCF (lowering valuations generally) and raise borrowing costs for leveraged companies.
- **Inflation & input costs** — affects margins differently depending on a company's pricing power.
- **Currency** — matters disproportionately for exporters/importers and companies with foreign-currency debt.
- **Regulatory environment** — policy changes, tariffs, and sector-specific regulation (e.g., banking capital requirements, pharma pricing controls) can materially change an industry's economics overnight.

---

## Growth vs. Value Investing

Two broad philosophies that both sit inside fundamental analysis:

| | Value Investing | Growth Investing |
|---|---|---|
| Looks for | Companies trading below estimated intrinsic value | Companies with high expected future growth, even at a higher current price |
| Typical metrics favored | Low P/E, low P/B, high dividend yield | Revenue/earnings growth rate, large addressable market |
| Risk | "Value trap" — cheap for a structural reason that doesn't resolve | Overpaying for growth that slows down or doesn't materialize |
| Classic proponents | Benjamin Graham, Warren Buffett (earlier career) | Philip Fisher, most modern tech-focused investors |

In practice, many successful investors (Buffett included, in his later career) blend both: look for a high-quality, moated, growing business, but still pay attention to not overpaying for it — sometimes summarized as "a wonderful company at a fair price" rather than "a fair company at a wonderful price."

---

## Reading an Annual Report

A practical checklist for working through a company's annual report (or 10-K/annual filing):

1. **Chairman's/MD's letter** — read for tone and substance; compare this year's stated priorities against whether *last* year's letter's promises were actually delivered.
2. **Management Discussion & Analysis (MD&A)** — management's own narrative on performance, risks, and outlook; useful, but remember it's written by the company, not an independent party.
3. **Financial statements + notes** — the notes to accounts often contain the details that matter most: contingent liabilities, related-party transactions, changes in accounting policy, segment-wise breakdowns.
4. **Auditor's report** — check for a qualified opinion, emphasis-of-matter paragraphs, or auditor resignation — all are red flags worth investigating further.
5. **Related-party transactions** — are there significant transactions with promoter-owned entities, and are they on fair market terms?
6. **Segment/geography breakdown** — understand where revenue and profit actually come from, especially for diversified or multi-geography businesses.
7. **Year-over-year trends, not single snapshots** — read at least 3–5 years of statements side by side; a single year tells you much less than a trend.

---

## Fundamental vs. Technical Analysis

| | Fundamental Analysis | Technical Analysis |
|---|---|---|
| What it studies | The business: financials, competitive position, management | Price and volume charts/patterns |
| Time horizon | Typically long-term (months to years) | Typically short-term (minutes to weeks) |
| Core question | "What is this business actually worth?" | "What is the price likely to do next?" |
| Tools | Financial statements, ratios, DCF, industry research | Chart patterns, indicators (moving averages, RSI, MACD), volume |
| Best suited for | Investors | Traders |

The two aren't mutually exclusive — many practitioners use fundamental analysis to decide *what* to own and technical analysis to help decide *when* to enter or exit a position, though this document focuses only on the fundamental side. (For the mechanics of derivatives often used alongside either approach, see [FUTURES_AND_OPTIONS.md](FUTURES_AND_OPTIONS.md).)

---

## Common Pitfalls

- **Anchoring on a single ratio** — a low P/E alone says nothing without context on growth, quality, and why it's low.
- **Ignoring cash flow in favor of reported profit** — profit can be manipulated by accounting choices (revenue recognition, depreciation policy) more easily than cash flow can.
- **Extrapolating recent growth indefinitely** — most high-growth phases slow down eventually ("mean reversion"); DCF and multiple-based valuations are highly sensitive to assuming growth continues forever.
- **Ignoring dilution** — rising net profit can still mean falling EPS if the share count is growing faster (frequent new equity issuance).
- **Confusing a cheap stock with a good investment** — a statistically cheap stock can stay cheap indefinitely (or get cheaper) if the underlying business is genuinely deteriorating — the "value trap."
- **Over-trusting management guidance** — treat forward guidance as one data point, not a certainty; track management's historical accuracy in hitting its own guidance.
- **Skipping the footnotes** — the most important risk disclosures are often in the notes to the financial statements, not the headline numbers.

---

## How to Actually Start Learning/Practicing

A sensible progression, in order:

1. **Learn to read one full annual report start to finish** — pick a company you already understand as a consumer (a bank you use, a retailer you shop at) and work through its most recent annual report using the [checklist above](#reading-an-annual-report).
2. **Build a simple ratio spreadsheet** — pull 5 years of financials for that company and calculate the [key ratios](#key-financial-ratios) yourself, by hand, before relying on a screener's pre-calculated numbers — this builds intuition for what each ratio is actually doing.
3. **Compare against 2–3 direct peers** — ratios mean little in isolation; the same exercise against competitors in the same industry is what makes them meaningful.
4. **Attempt a basic DCF** — even a rough, simplified DCF (a few years of projected FCF plus a terminal value) teaches you far more about valuation sensitivity than reading about DCF in the abstract.
5. **Track your own thesis over time** — write down why you think a stock is under/overvalued, then revisit that thesis every quarter against actual results — this is how analytical judgment actually improves.
6. **Read a few annual reports of companies that later failed or scandalized** — spotting red flags in hindsight (inflated receivables, aggressive related-party deals, auditor changes) trains you to spot them prospectively.
7. **Use screeners to scale up, not to replace the above** — stock screeners (filtering by P/E, ROE, debt levels, etc.) are useful for generating a shortlist, but the deep-dive work above is what actually tells you whether a shortlisted company is worth owning.

---

## Quick Reference Cheat Sheet

| Concept | One-line definition |
|---|---|
| Intrinsic value | What a business is actually worth, independent of its market price |
| Income statement | Profitability over a period (revenue → net profit) |
| Balance sheet | Snapshot of assets, liabilities, and equity at a point in time |
| Cash flow statement | Actual cash moving in/out; hardest statement to manipulate |
| Free cash flow (FCF) | Operating cash flow minus capital expenditure |
| ROE | Net profit ÷ shareholders' equity — efficiency of shareholder capital |
| P/E ratio | Share price ÷ EPS — most common relative valuation multiple |
| DCF | Values a business as the present value of its future free cash flows |
| Economic moat | A durable competitive advantage protecting long-term profits |
| Value investing | Buying businesses trading below estimated intrinsic value |
| Growth investing | Paying up for businesses with high expected future growth |
| Value trap | A statistically cheap stock that stays cheap because the business is deteriorating |

---

*This document covers conceptual fundamentals only — it is not financial advice. Fundamental analysis involves judgment and assumptions that can be wrong; always do independent research (or consult a qualified advisor) before making investment decisions.*

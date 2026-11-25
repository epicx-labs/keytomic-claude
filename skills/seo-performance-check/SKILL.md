---
name: seo-performance-check
description: Run a daily, weekly, or monthly SEO performance check for any Keytomic site — GA4 traffic, GSC search data, top pages, conversions, and keyword rankings with period-over-period comparison. Use when asked for a daily check, weekly snapshot, monthly review, or 'how is my site doing'.
---

# SEO Performance Check

Run a daily, weekly, or monthly SEO performance check for any Keytomic site. Compares GA4 + GSC data period-over-period and surfaces keyword movements.

## Step 0 — Identify site and period

- Call `list_sites` to identify the site (ask if more than one)
- Call `get_project` to confirm GA4 and GSC integrations are connected
- Determine cadence from the user's message:
  - **Daily:** yesterday vs the day before
  - **Weekly:** last 7 days vs prior 7 days
  - **Monthly:** last 30 days vs prior 30 days
- Always end date ranges on yesterday to avoid incomplete data (GA4/GSC have a 1–2 day lag)

## Step 1 — Pull data in parallel

Run all queries simultaneously:

**A — GA4 overall, this period:**
`get_analytics_performance` — this period, segment: "all"

**B — GA4 overall, prior period:**
`get_analytics_performance` — prior period, segment: "all"

**C — GSC search performance, this period:**
`get_search_performance` — this period, topLimit: 50

**D — GSC search performance, prior period:**
`get_search_performance` — prior period, topLimit: 50

**E — GA4 events, this period:**
`list_analytics_events` — this period

**F — Keyword rankings, this period (weekly/monthly only):**
`search_analytics_query` — this period, dimensions: ["query"], filter for target keywords

**G — Keyword rankings, prior period (weekly/monthly only):**
`search_analytics_query` — prior period, same filter

## Step 2 — Build the report

### Section 1: Sessions & Users

| Metric | This period | Prior period | Change |
|---|---|---|---|
| Active Users | | | |
| Sessions | | | |
| New Users | | | |
| Bounce Rate | | | |

Show % change with ▲ (improvement) or ▼ (decline). For bounce rate, a decrease = improvement.

### Section 2: Traffic Sources

Top 8 sources by session count. Flag bot/spam sources with ⚠️. Call out AI assistant traffic (ChatGPT, Claude.ai, Gemini) separately — it's a positive signal.

### Section 3: Search (GSC)

| Metric | This period | Prior period | Change |
|---|---|---|---|
| Total clicks | | | |
| Non-branded clicks | | | |
| Impressions | | | |
| CTR | | | |
| Avg. position | | | |

For non-branded clicks: subtract clicks from queries containing the brand name from total clicks. List top 5 queries by click this period.

### Section 4: Events & Conversions

Table of key events this period with count and period comparison. Focus on conversion-relevant events: sign_up, onboarding steps, checkout started/canceled, purchase, trial conversions, churn events. Note checkout-to-purchase rate if available.

### Section 5: Top Pages

Top 5 pages by views this period. Flag any unusual spikes.

### Section 6: Keyword Rankings (weekly/monthly only)

| Keyword | Goal | Prior position | This position | Change | Gap to goal |
|---|---|---|---|---|---|

- In search rankings, **lower position = better**. Moving from 44 → 30 = improvement of +14.
- Show change as ▲ +N (improved) or ▼ −N (dropped) or → flat
- Add 🔥 for moves of 8+ positions in the right direction
- Show ✅ if at or better than goal

### Closing observations

3–5 bullets: biggest win, anything to watch, top keyword movers, one actionable suggestion. Keep it direct and data-driven. Skip generic SEO advice.

## Output style

- Markdown tables throughout
- Direct, analytical tone — this is an internal report
- If comparing a partial period (fewer than 7 days), say so at the top in one line and show daily averages alongside totals

---
name: blog-content-review
description: Review any Keytomic blog draft before publishing — SERP intent, information gain, brand section handling, competitor links, disclaimers, and SEO scoring. Use when a user wants to check a draft, review today's blogs, or run a pre-publish quality check on any site managed in Keytomic.
---

# Blog Content Review (Pre-publish)

Two-phase pre-publish review for any Keytomic site. Claude stops after each phase and waits for the user's decision before editing or publishing.

## Phase 0 — Get the drafts

Accepts:
- "today's blogs" or "the ones about to publish" → fetch with `list_calendar_topics` then `get_draft`
- A topic name or keyword → search and fetch
- A URL → `WebFetch` the page
- Pasted text → use directly

For each draft, note: target keyword, post type (guide / listicle / comparison / refresh), content cluster, and the money page it supports.

---

## Phase 1 — Content review

Always run this first, on every draft.

### 1.1 Disclaimers
Find every disclaimer, hedge or caveat: pricing/accuracy disclaimers, "not affiliated", "verify before buying", "results may vary", "at the time of writing", AI/drafting leftovers ("confirm with the team", "should not claim"). Quote each one with its section. Do not remove yet — just list.

### 1.2 SERP intent and coverage
- Pull the live SERP for the target keyword. Use web search and read the top 5 organic results (skip forums/videos unless they dominate). Note People Also Ask.
- Compare the draft:
  - **Intent:** Does the format match (listicle, guide, comparison, how-to)?
  - **Angle:** What angle do top results take? Does ours match?
  - **Coverage:** Subtopics most top results cover that the draft misses; PAA questions not answered; depth vs median
- Verdict: matches / partially matches / misses, with specific gaps

### 1.3 Information gain
Judge against Google's helpful content criteria:
- Original information, analysis, frameworks, data or opinions not in competing posts?
- Goes beyond the obvious, adds substantial value?
- Shows first-hand experience or expertise?
- Written for people, not to rank (no filler, no keyword stuffing)?

List what's unique, what's generic, and 2–4 concrete additions that would raise information gain.

### 1.4 Brand/product section
The content generator may add a generic "Why [Brand] fits" section to every post. Rules:
- **Regular blogs (guides, how-tos, industry posts, refreshes):** Remove the generic section. Draft a unique closing paragraph tied to the post's topic — one or two sentences, not a sales pitch.
- **Listicles and comparisons:** Remove any promotional closing entirely. If the brand appears as one of the listed entries, leave it and flag it for review.
- Flag any off-sounding brand mentions (hedged, self-undermining, or overselling).

### 1.5 Competitor links
No blog should link to competitors or third-party tools in the same category. List every outbound link to a competitor, with anchor text and location. Recommend keeping brand names as plain unlinked text. Non-competitor outbound links (data sources, studies) are fine — just list them.

### Phase 1 output

For each draft:
1. **Disclaimers to review** (quoted, with section)
2. **SERP intent and coverage:** verdict + gaps
3. **Information gain:** verdict + unique points, generic points, suggested additions
4. **Brand section:** what's there, what to do, proposed closing (regular blogs only)
5. **Competitor links:** list to remove
6. **Proposed changes:** numbered list

For batches, start with a summary table: Post | Type | Intent match | Info gain | Brand section | Competitor links | Disclaimers

**Stop.** Ask whether to update the draft. Apply only the changes the user approves using `update_draft`. Confirm what changed.

---

## Phase 2 — SEO score check

Only run after Phase 1 is settled.

Score each area 0–10:

| Area | What to check |
|---|---|
| Intent match and depth | Format, coverage vs top results, FAQs, decision help |
| Information gain | Original frameworks, data, opinions not in competing posts |
| On-page / technical | Title tag (≤60 chars, keyword-led), meta description, H1, H2/H3 hierarchy, slug, schema |
| Internal linking | Link to hub/money page with keyword anchor; 5–8+ contextual internal links total |
| E-E-A-T | First-hand use, screenshots, author byline, dates, sources |
| LLM SEO / AEO | Answer-first blocks under H2s, quotable definitions, tables, 40–60 word FAQ answers |
| Brand and commercial fit | Brand positioned confidently, factually; clear CTA; no hedging |

### Phase 2 output

1. **Overall: X/10 now; ~Y/10 after fixes**
2. Score table (Area | Score | Why)
3. **Critical fixes** — max 5–7, each with the exact replacement (title, link, sentence)
4. **Quick wins**

**Stop.** Ask whether to make the suggested changes. Apply only what the user approves. Never publish without explicit go-ahead.

Publishing: `update_draft` → `publish_draft`

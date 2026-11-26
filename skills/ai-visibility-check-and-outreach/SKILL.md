---
name: ai-visibility-check-and-outreach
description: Run an AI visibility gap and outreach campaign using Keytomic — find the prompts where AI answers cite competitors but not your brand, then qualify the cited sources, find contacts, and draft and send pitches. Use when a user wants to find brand gaps in AI answers and do AI-era SEO outreach to the sources LLMs cite. To only set up AI Visibility, edit prompts, run a check or read the report, use the ai-visibility skill.
---

# AI Visibility Check and Outreach

Two-phase workflow: audit brand presence across LLMs, then reach out to sources citing competitors but not your brand.

## Phase 1 — AI visibility audit

### 1.1 Setup

- Call `list_sites` to identify the site
- Call `get_project` to read the brand profile
- Call `get_visibility` to check if a tracker exists
  - If no tracker exists: run `start_visibility_setup`, which researches the site and saves Suggested prompts. Pick 5–10 buyer-intent prompts from them (or write new ones), ask the user to confirm or adjust, then add them with `add_visibility_prompts`.
  - If a tracker exists: use the existing prompts. Ask if the user wants to add or change any.

### 1.2 Run the check

- Call `start_visibility_check` without `confirm` first. It returns a plan and starts nothing. Show the user the observations the check needs and the observations left this month, and call again with `confirm` set to `true` only after they agree
- Wait for results
- Call `get_visibility_report` to fetch the results

### 1.3 Build the visibility report

**Brand mention overview:**

| LLM | Prompts checked | Brand mentioned | Share of voice |
|---|---|---|---|
| ChatGPT | | | |
| Claude | | | |
| Gemini | | | |
| Perplexity | | | |
| **Total** | | | |

**Gap table — prompts where brand is absent but competitors appear:**

| Prompt | Competitors mentioned | Brand present? | Sources cited |
|---|---|---|---|

**Key observations:**
- Strongest prompts (brand mentioned consistently)
- Biggest gaps (brand absent, competitors dominate)
- LLMs where brand is least visible
- Priority outreach sources (pages cited in gaps)

**Stop.** Present the report and ask the user to confirm before moving to outreach.

---

## Phase 2 — Outreach to gap sources

### 2.1 Qualify gap sources

For each source cited in the gap table:
- `WebFetch` the page to understand its type and content
- Score by type: listicle/roundup > directory > editorial > forum
- Note what competitor is mentioned and how (listed, quoted, recommended)
- Drop: paywalled pages, forums, government/academic pages unlikely to update, low-quality pages

Qualify list: source URL, page type, how competitor is mentioned, why the brand belongs, priority (High/Medium/Low).

### 2.2 Find contact details

For each qualified source:
- Check the byline for author name
- `WebSearch` for "[author name] [publication] email"
- Try common email patterns: firstname@domain.com, firstname.lastname@domain.com
- Check the publication's editorial contact or "write for us" page
- Check the author's LinkedIn
- Max 2 search attempts per contact. If not found, mark "contact not found" and move on.

### 2.3 Draft pitches

One email per source:
- Subject line: reference the article by name
- Opening: reference a specific point from the article
- 2–3 sentences making the case for inclusion — one specific differentiator
- CTA: offer something useful — a trial link, a data point, a quote
- Max 6–8 sentences total
- Tone: peer-to-peer, not "please add me"
- No "I hope this email finds you well"

**Stop.** Show the user each draft before sending. Send only what they approve.

### 2.4 Send and log

- Send approved emails via `mcp__Gmail__send_message`
- Log each outreach:

| Source | Author | Email | Date sent | Status |
|---|---|---|---|---|

One follow-up after 7 days max. 2–3 sentences only. Never follow up more than once.

## Re-run cadence

- **Weekly:** track visibility score over time
- **After publishing new content:** check if LLMs pick it up
- **After a competitor launch:** check if they appear in prompts where the brand was alone
- **After outreach converts:** confirm brand now appears in that source

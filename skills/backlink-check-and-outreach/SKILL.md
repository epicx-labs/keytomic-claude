---
name: backlink-check-and-outreach
description: Run a backlink outreach campaign from Keytomic's backlink gaps — qualify the pages that link to competitors but not to your site, find contact details, and draft and send personalised pitches. Use when a user wants to run link-building outreach, pitch the sites in their backlink gap, or close a competitor link gap. To only read backlinks or change the competitor list, use the backlinks skill.
---

# Backlink Check and Outreach

Two-phase workflow: find domains linking to competitors but not to your site, then reach out with personalised pitches.

## Phase 1 — Backlink gap analysis

### 1.1 Setup

- Call `list_sites` to identify the site
- Call `get_project` to read the brand profile
- Call `get_backlinks` and read `competitorDomains`
  - If the list is empty or wrong: confirm 3–5 competitor domains with the user (or infer from the brand profile). Show the full new list and get a yes, then save it with `set_backlink_competitors` (it replaces the list, max 10 domains). The gaps of a new competitor show after the next view of the Backlinks page, so give the user the `backlinksUrl` and stop until they have opened it.

### 1.2 Pull backlink data

- Call `get_backlinks`. It reads the saved report only and spends no credits. Say when the data was read (`fetchedAt`).
- If no report is saved, give the user the `backlinksUrl` and stop. The Backlinks page reads the data when the user opens it.

### 1.3 Find the gap

Keytomic already computes the gap: the backlink gaps in `get_backlinks` are the pages that link to a competitor but not to the site, one page per linking site, each with an outreach draft. Use that list. Drop any domain that also appears in the site's own backlinks.

### 1.4 Qualify gap domains

For each gap domain:
- `WebFetch` the linking page
- Score by type: listicle/roundup > directory/resource page > editorial > forum
- Score by relevance: does the page cover the site's topic?
- Score by quality: real publication? (Spam domains, link farms → drop)
- Note what the competitor is listed for and whether the site could fit

Priority scoring:
- **High:** listicle/roundup, relevant topic, real publication
- **Medium:** directory or resource page, relevant topic
- **Low:** editorial, loosely relevant, contact hard to find

### Phase 1 output

Gap table sorted by priority:

| Domain | Linking page | Page type | Competitor(s) listed | Why we belong | Priority |
|---|---|---|---|---|---|

**Stop.** Present the gap table and ask for confirmation before starting outreach.

---

## Phase 2 — Outreach

### 2.1 Find contact details

For each approved gap domain:
- Check the linking page for an author byline
- `WebSearch` for "[author name] [publication] email"
- Try email patterns: firstname@domain.com, firstname.lastname@domain.com, hello@domain.com, editor@domain.com
- Check the publication's "contact", "write for us", or "editorial" page
- Check LinkedIn for the author
- Max 2 search attempts. If not found, mark "contact not found" and move on.

### 2.2 Research the page before writing

Re-read the linking page. Note: the article's angle, what the competitor is praised for, what the site does better or differently, and whether there's a specific gap the site could fill.

### 2.3 Draft the pitch

One email per domain:
- Subject: reference the article title by name
- Opening: reference a specific point from the article
- 2–3 sentences making the case for inclusion: one concrete differentiator, one reason it helps their readers
- CTA: offer something useful — a trial, a data point, a quote, a comparison
- Max 6–8 sentences
- Tone: peer-to-peer
- No "I hope this email finds you well", no "quick question"

**Stop.** Show the user each draft before sending. Send only what they approve.

### 2.4 Send and log

- Send approved emails via `mcp__Gmail__send_message`
- Log each outreach:

| Domain | Page | Author | Email | Date sent | Status |
|---|---|---|---|---|---|

**Follow-up:** one follow-up after 7 days. 2–3 sentences only. Never follow up more than once.

## Re-run cadence

- **Monthly:** refresh the gap list as competitors earn new links
- **After publishing a linkable asset:** target pages covering that specific topic immediately
- **After competitor press coverage:** add the new publication to the outreach queue
- **After a link goes live:** update the log, re-check the domain for other opportunities

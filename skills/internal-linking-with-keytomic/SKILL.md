---
name: internal-linking-with-keytomic
description: Fix a site's internal link structure using Keytomic — money pages, hub→child downward links, child→hub upward links with correct anchors, and sibling cross-links. Use when a Keytomic user wants to audit and repair their internal linking in one go.
---

# Internal Linking with Keytomic

Fix a site's entire internal link structure in one workflow. Any Keytomic user can run this skill to audit and repair money page coverage, hub→child links, child→hub anchor links, and sibling cross-links.

## The 4 rules this skill enforces

1. Every money page must have ≥3 in-body inbound links from other pages
2. Every hub page must link downward to each of its live child pages in the body
3. Every child page must link upward to its hub using the target keyword (or a close variation) as anchor text
4. Siblings within a cluster cross-link each other where the audience overlaps — minimum 3 cross-links per cluster

## Phase 0 — Setup

- Call `list_sites` to identify the site
- Call `get_project` to read the brand profile and understand the site structure
- Ask the user to confirm or share their cluster plan (hub pages + child pages per cluster) if not already known. If they have a doc or sheet, read it. If not, infer from the site's navigation and published pages.

## Phase 1 — Money page health check

**Goal:** Protect the pages that drive revenue before touching anything else.

1. Identify all money pages (high-intent, commercial pages: pricing, feature pages, landing pages, comparison pages, solution pages). Ask the user to confirm the list if unclear.
2. For each money page, count in-body inbound links from other published pages, using the latest finished Website Audit:
   - Call `list_audits` and take the newest completed audit. If there is none, or it is older than the latest content changes, ask the user before starting one with `start_audit`, then follow it with `watch_audit`.
   - Page through `list_audit_links` (internal links only) and count the distinct source pages that link to each money page.
   - Do not count nav or footer links: a link with the same target and anchor that appears on most of the pages is site chrome, not a body link.
3. Classify each money page:
   - **Orphan (critical):** 0–2 inbound body links → must fix
   - **Underlinked:** 3–9 inbound body links → improve
   - **Healthy:** 10+ inbound body links → protect, don't remove

Present a money page health table:

| Page | URL | Inbound body links | Status |
|---|---|---|---|

**Stop here.** Ask the user to confirm before proceeding to Phase 2.

## Phase 2 — Hub and child structure audit

**Goal:** Map every cluster and find every gap in the hub↔child link network.

For each cluster:

### 2a — Downward links (hub → child)
- Fetch the hub page draft with `get_draft`
- Confirm the hub body-links to every live child page
- Flag any child the hub does not link to

### 2b — Upward links (child → hub)
- Fetch each child page draft with `get_draft`
- Confirm each child links back to its hub in the body
- Confirm the anchor text is the target keyword or a close variation (not "click here", not bare URLs, not "read more")
- Flag weak or missing anchors

### 2c — Sibling cross-links
- Within each cluster, identify children whose audiences overlap
- Confirm at least 3 sibling cross-links exist per cluster
- Flag missing sibling links

Present the full gap table:

| Page | URL | Expected link to | Direction | Anchor needed | Status |
|---|---|---|---|---|---|

**Stop here.** Ask the user to confirm the gap table before fixing anything.

## Phase 3 — Fix gaps

For each gap, in order (money pages first, then hubs, then children, then siblings):

1. `get_draft` — fetch the current draft
2. Find the best paragraph to insert the link naturally (near a relevant mention, or add a contextual sentence)
3. Add one sentence with the keyword as anchor text — minimum change, no rewrites
4. `update_draft` — save the change
5. `publish_draft` — publish

After fixing, re-fetch each page and confirm the link is live. If a page cannot be edited via Keytomic, flag it as a manual item for the user.

## Phase 4 — Final report

| Metric | Count |
|---|---|
| Money page gaps fixed | |
| Hub → child gaps fixed | |
| Child → hub gaps fixed | |
| Sibling gaps fixed | |
| Manual items remaining | |

List any manual items with what needs to be done and where.

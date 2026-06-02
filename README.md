# aurafy-blog — source repo for aurafy.dev/blog/

This repo is the **source of truth** for blog posts destined for `aurafy.dev/blog/<slug>/`.

- **Author:** `w1337-cyber` (Claude growth operator, running on VPS)
- **Deploys to:** `aurafy.dev/blog/` — via a separate "deploy-side Claude session" on the operator's machine
- **Workflow:** Path B refined (operator-approved 2026-06-02). I write posts here → deploy-side Claude session reads this repo → opens a PR to the aurafy site repo (`w1337-cyber/aurafy`) → operator merges → live.

I never push to the aurafy site repo directly. This repo is my sandbox. The deploy-side Claude session is the bridge.

---

## Section 1 — Post index

| Slug | Title | Status | Published | Updated |
|---|---|---|---|---|
| `apex-vs-topstep-2026` | Apex vs Topstep (2026): Honest Side-by-Side Comparison for Futures Traders | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `best-prop-firms-2026` | Best Prop Firms for Futures Traders (2026): Honest Ranking | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `aurafy-vs-tradezella-2026` | Aurafy vs TradeZella (2026): Honest Side-by-Side for Futures Traders | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `topstep-vs-tpt-2026` | Topstep vs TPT (2026): Same Lock Family, Opposite Trail Trajectory | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `topstep-vs-mffu-2026` | Topstep vs MyFundedFutures (2026): Honest Side-by-Side Comparison | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `apex-vs-mffu-2026` | Apex vs MyFundedFutures (2026): Honest Side-by-Side Comparison | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `earn2trade-vs-topstep-2026` | Earn2Trade vs Topstep (2026): Same Lock, Different Lifecycle | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `bulenox-vs-tradeify-2026` | Bulenox vs Tradeify (2026): Same Lock, Opposite Everything Else | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `aurafy-vs-tradersync-2026` | Aurafy vs TraderSync (2026): Honest Side-by-Side for Futures Traders | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `aurafy-vs-tradervue-2026` | Aurafy vs Tradervue (2026): Honest Side-by-Side for Futures Traders | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `tpt-vs-tradeify-2026` | TPT vs Tradeify (2026): Both Shift the Rules on You — In Opposite Directions | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `trade-replay-vs-screen-recording` | Trade Replay vs Screen Recording (2026): What's the Difference and Which One Actually Helps You Improve? | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |
| `apex-4-0-eod-vs-intraday-2026` | Apex 4.0 EOD vs Intraday Drawdown (2026): The Day-One Choice You Can't Undo | `ready-to-deploy` | 2026-06-02 | 2026-06-02 |

**Status values:**
- `draft` — work-in-progress, do NOT deploy
- `ready-to-deploy` — finished, awaiting deploy-side Claude pickup
- `deployed` — live at `aurafy.dev/blog/<slug>/`; deploy-side Claude flips this after merge

Deploy-side Claude: process every `ready-to-deploy` row, flip to `deployed` after the aurafy-repo PR is merged.

---

## Section 2 — File shape spec

Each post is a self-contained folder under `posts/`:

```
posts/<slug>/
  index.html      ← standalone HTML page (full <html><head><body>), inline <style>, no external deps
  meta.json       ← structured metadata (title, description, canonical, og:*, dates, tags)
```

**`index.html` requirements:**
- Standalone, self-styled, no external CSS/JS/font CDN — single file
- `<link rel="canonical" href="https://aurafy.dev/blog/<slug>/" />` — already correct
- Mobile-first; works on a 360px viewport
- No analytics SDK calls, no tracking pixels (aurafy site layer will add those at deploy time if needed)

**`meta.json` shape:**

```json
{
  "slug": "<slug>",
  "title": "<title>",
  "description": "<meta description>",
  "canonical": "https://aurafy.dev/blog/<slug>/",
  "published": "YYYY-MM-DD",
  "updated": "YYYY-MM-DD",
  "og": { "title": "...", "description": "...", "type": "article" },
  "tags": ["..."],
  "source_path": "posts/<slug>/index.html"
}
```

The deploy-side Claude session uses `meta.json` for structured data instead of HTML-parsing.

---

## Section 3 — Deploy-side Claude prompt (copy-paste)

Operator: when you have ~30 seconds, paste this into a fresh Claude Code session on the machine that has access to both `w1337-cyber/aurafy-blog` (this repo) and `w1337-cyber/aurafy` (the site repo).

```
You are the deploy-side bridge between w1337-cyber/aurafy-blog (source) and
w1337-cyber/aurafy (site). Both are accessible via the gh CLI on this machine.

For each post in w1337-cyber/aurafy-blog with status == "ready-to-deploy" (see
the table in its README.md):

  1. Clone or pull the latest w1337-cyber/aurafy-blog and w1337-cyber/aurafy.
  2. If Section 4 below ("Discovered path") of aurafy-blog's README is blank,
     inspect the aurafy site repo to find where the ONE existing blog post
     (slug: charts-by-tradingview) lives. Match that convention. Write the
     discovered relative path back into Section 4 in a commit to aurafy-blog
     so future runs skip discovery.
  3. Copy posts/<slug>/index.html from aurafy-blog into the discovered location
     inside the aurafy site repo. Use the post's meta.json for any frontmatter
     or template hooks the aurafy site format requires.
  4. Open a PR against w1337-cyber/aurafy main branch titled
     "blog: add <slug>" with the meta description in the PR body. DO NOT push
     to main directly — always a PR (operator merges).
  5. After the PR is merged (poll, or hand off back to operator), edit
     aurafy-blog's README.md to flip the row's status from "ready-to-deploy"
     to "deployed". Commit + push.

HARD RULES:
  - PR-only (no direct push to main of either repo).
  - Do NOT modify post HTML during the copy — byte-for-byte.
  - Honest-content rules from Section 5 of this README are author-side; you
    just deploy. Do NOT add disclaimers or edits the source didn't have.
  - If a post's HTML or meta.json fails sanity check (broken HTML, missing
    canonical, missing meta.json), skip it and leave status as
    "ready-to-deploy" — do not flip status, do not open a PR. Note the skip
    in a comment on the source repo.
```

---

## Section 4 — Discovered path (filled in by the deploy-side Claude on first run)

> _Empty. The deploy-side Claude writes the discovered path here on first run so subsequent runs skip the discovery step._

Example of what should appear here once filled:
```
- Aurafy blog posts live at: <relative/path/inside/aurafy-repo>/<slug>/<filename>
- Existing example: charts-by-tradingview at <path>
- Format: <one-file-per-post | folder-per-post | mdx | etc.>
- Discovered: YYYY-MM-DD by deploy-side Claude
```

---

## Section 5 — Honest-content rules (author-side)

Every post here MUST satisfy these before status flips to `ready-to-deploy`. The deploy-side Claude does not enforce these — the source-side author (`w1337-cyber` growth operator) does.

1. **No fake claims.** Every numeric / rule claim must be verifiable from primary sources (firm docs, broker docs, exchange specs) at time of writing. Prop-firm rules change — cite the version (e.g. "Apex 4.0" not just "Apex").
2. **No affiliate disguising.** If a link is affiliate, it must be visibly disclosed at link-side. Aurafy currently runs no affiliate links to prop firms — links are factual references.
3. **No copy-paste competitor content.** Comparison posts must rephrase from primary sources, not from competitor blogs.
4. **No anonymous user-quote fabrication.** If a post quotes a trader, the quote is either (a) sourced from a public post with attribution, (b) operator's own words, or (c) flagged as illustrative (not a real person).
5. **No fake urgency / scarcity.** Founder-seat counters, deadlines, etc. are only valid if they map to real backend state.
6. **Verify-with-your-firm disclaimer** on any rule-mechanic claim (drawdown lock, consistency, payout). Rules drift; we are a planning reference, not advice.
7. **Soft CTA only.** Each post may include ONE soft CTA to aurafy.dev with `?utm_source=blog&utm_medium=<slug>` for attribution. Not above-the-fold, not nag-style.

---

## Repo conventions

- `master` is the default working branch on the growth-VPS side; this repo uses `main` (GitHub default) for cleanliness.
- Commits squash-clean; no force-push to `main`.
- Issues: not currently tracked here — growth-side feedback flows through Discord ("Trading Journal" channel) per CLAUDE.md.

---

_Built by the growth operator running on the VPS. See `/home/growth/growth/CLAUDE.md` (private) for the broader autonomous-operator context._

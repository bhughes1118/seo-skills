---
name: content-coverage
description: Plan and write pages that cover the buyer questions where AI answers name competitors and not you, using the free ai-answer-check log. Use when the user wants content for AI-answer gaps, cited listicles, competitor sources, or asks "what should we publish" for ChatGPT, Perplexity or AI Overviews.
metadata:
  version: 1.1.0
  based_on: aiseotracker content-coverage (paid MCP removed)
---

# AI search content coverage

Cover **approved question clusters** with evidence-backed pages in the user's content repository. Then re-check the questions after those pages are live. Do not promise full coverage or rankings.

## Inputs

- `seo/ai-questions.md` and `seo/ai-answers.csv` from the `ai-answer-check` skill (run it first if they are missing or older than 7 days)
- A writable content repository
- Optional: a project image skill or tool the user already has

## Phase 1: Snapshot

Fill this block from the log only:

```
CONTENT PLAN:
Brand: <name>   Domain: <domain>
Log window: <first date> - <last date>  (Claude with web search; proxy for Bing/Google-fed engines)
Questions: <N> | Brand named: <n> | Missing: <m>
Missing questions:
- <qid> <question>
Top cited sources (independent editorial first):
#1 <url> · <type> · cited on <k> questions
Competitors named most:
- <name> (<count>)
```

Separate **independent editorial listicles** (third-party roundups) from **competitor-owned pages**. Pitch only independent lists later.

## Phase 2: Cluster and canonicalize

Group missing and opportunity prompts that share one search intent. One cluster = one page unless the user asked otherwise.

For every target, decide **one** of:

- `new`: no existing page covers this intent
- `rewrite`: same slug, update in place (`updateDate` if the repo uses it)
- `consolidate`: merge into an existing URL; do not spawn a competing slug

Audit the target repo first: existing blog filenames, titles, descriptions, and redirects. Prefer rewrite or consolidate over near-duplicates.

Cap a batch at **5 posts**. If more clusters remain, queue them for the next approved batch.

## Phase 3: Research (bounded)

For each approved or proposed slug:

1. Start from the `cited_urls` logged for that question cluster.
2. Fetch 3–5 verified URLs. Prefer the cited listicles, then current web results for the primary keyword.
3. Record titles, intent, and whether the user's brand is named.
4. If a URL does not resolve, write `unverified` and skip it. Never invent a listicle URL.
5. Do not scrape live ChatGPT, Perplexity, Gemini, Copilot, or AI Overview answers.

Title and slug:

- Put the primary keyword near the start of the title. Keep most titles under 60 characters.
- Slug: lowercase, hyphens, 2–5 words, no year unless the query requires freshness.
- Match listicle vs how-to vs definition intent from the live SERP, not from memory.

## Phase 4: Stop for approval

Present the campaign and **wait**. Do not write files yet.

Include:

- The CONTENT PLAN snapshot
- A table: `prompt cluster | intent | slug | action (new/rewrite/consolidate) | title | sources`
- Estimated post count (max 5 this batch)
- Cannibalization risks
- Independent listicles to pitch after publish (drafts only)

Write files only after the user approves the table.

## CONTENT BATCH MANIFEST

Keep this manifest in the chat (or a file the user named). Update it as you go. Resume from it. Do not repeat finished research. Do not overwrite another worker's slug.

```
CONTENT BATCH MANIFEST v1
Planned at: <ISO date>
Log snapshot: <last date in ai-answers.csv>
Phase: <snapshot|plan|research|draft|validate|report>

| slug | action | prompt cluster | phase | notes |
| --- | --- | --- | --- | --- |
| example-slug | new | missing prompt text | planned | |

phase values: planned | research | draft | validate | done | blocked
```

On resume: re-run Phase 1 if the log has new rows or the user says data changed. Skip rows at `done`. Read existing files before merging a `blocked` or partial slug.

## Phase 5: Write (after approval)

Use available parallel subagents if the host supports them. Grok CLI is optional. Never required.

If you split work:

- One slug per worker. No overlapping files.
- Give each worker the approved row, the 3–5 source URLs, product facts the user confirmed, and local MDX conventions.
- Parent agent owns the manifest.

Write native repository markdown/MDX. Follow files already in that repo (frontmatter, CTA components, internal link style). Short sentences. No invented stats, prices, rankings, or first-hand tests. If a competitor price is unverified, omit it or say to check their site.

Images:

1. Write image alt (under 125 characters) and a scene prompt.
2. Generate an image only if the user has a project image skill/tool **and** asks to run it.
3. Never print API keys. Never tell the user to paste keys. Never call a private operator script from this skill.

Do not deploy, commit, push, email, post publicly, or purchase unless the user explicitly asks.

## Phase 6: Validate

Before you hand off a batch:

- Frontmatter complete and consistent with sibling posts
- Slug unique (or rewrite of the intended file)
- Internal links point at real paths
- Image paths exist, or alt+prompt is listed as pending
- No unresolved placeholders
- Repo format/lint on touched files when a formatter is available

## Phase 7: After publication

When the user says the pages are live:

1. Re-run `ai-answer-check` for the questions in this batch.
2. Report which questions changed (brand now named or not), and which independent listicles still omit the brand.
3. Leave outreach as copy-paste drafts. Do not send.

## Do not

- Invent ranks, URLs, competitor names, listicle domains, DR, traffic, or trend deltas
- Write files before the approval checkpoint
- Promise rankings or "full coverage"
- Send email, post publicly, pay for placements, or charge a card


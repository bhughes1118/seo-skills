---
name: ai-answer-check
description: Free, automated AI-answer tracking. Keeps a fixed list of about 25 questions buyers ask AI assistants, has Claude agents with web search ask them on a daily rotation, and logs whether the brand is named, which competitors are named, and which pages are cited. Use for AI search visibility, GEO tracking, "are we named in ChatGPT", or a daily or weekly AI-answer check, without paid trackers.
metadata:
  version: 1.0.0
  replaces: aiseotracker, ai-seo-report (paid)
---

# AI answer check (free)

Paid trackers sell one thing: asking AI assistants the same questions on a schedule and recording the answers. A Claude agent with web search can do the asking for free. This skill keeps the question list, runs it on a rotation, and keeps a log you can diff week to week.

## What it measures, honestly

- **Claude with web search** is asked directly. That is a real AI answer.
- **ChatGPT and Copilot** lean on Bing results; **Google AI Overviews** lean on Google results. The agent's web search shows which pages those engines are likely to cite, but it is a proxy, not their answer. Label it that way.
- **ChatGPT, Perplexity, Gemini directly**: only by hand (or a browser the owner controls). Do it monthly for the 5 most important questions and log it with `engine: chatgpt-manual`.

Never write "ChatGPT recommends us" from a proxy check.

## Files

```
seo/ai-questions.md     the question list (id, stage, question, market)
seo/ai-answers.csv      one row per question per check
```

`ai-answers.csv` columns: `date,qid,engine,brand_named,brand_position,competitors_named,cited_urls,notes`

## Step 1: Write the question list (once, then monthly)

About 25 questions, written the way a real buyer types them into a chat box, not keyword strings. Spread them across the buying journey (see `customer-journey`):

| Stage | How many | Example shape |
| --- | --- | --- |
| Problem-aware | 5 | "How long does it take to build a custom home in [city]?" |
| Solution-aware | 6 | "What should I ask a custom home builder before hiring one?" |
| Shortlist | 8 | "Who are the best custom home builders in [area]?" |
| Brand | 3 | "What do people say about [brand]?" |
| Local / niche | 3 | "Can I rebuild after a flood in [neighborhood]?" |

Keep ids stable (`q01`-`q25`). Replace a question only when it stops matching how buyers ask; note the change.

## Step 2: Daily rotation

Ask 5 questions a day so all 25 run every 5 days. Keep a pointer in your rotation file.

For each question, spawn one subagent (parallel is fine) with this brief:

> You are an AI assistant answering a homeowner. Use web search. Answer the question below in 120 words the way you would for a real person, naming specific companies only if your sources support it. Then, separately, list: (1) every company you named, in order; (2) every URL you relied on. Question: "<question>"

Then, for each answer, record:

1. `brand_named`: yes/no, and position in the list of named companies.
2. `competitors_named`: names in order.
3. `cited_urls`: every URL used. Mark the type: builder site, directory, roundup/listicle, news, forum, government.
4. Anything the answer gets **wrong** about the brand.

## Step 3: Act on it

```
brand missing, a roundup or directory is cited
└── that page is a target: get listed or pitch its editor (free listings and earned mentions only)
brand missing, a competitor's own page is cited
└── compare that page with ours: what question does it answer that we don't?
brand named but a fact is wrong
└── the page that owns that fact states it unclearly: fix the wording there
brand named
└── note which of our pages was cited and protect it (keep facts current)
```

## Step 4: Report

In the daily or weekly report, one short block:

- Named in X of N answers this week (vs last week)
- New: questions where the brand appeared or dropped out
- Top 3 cited sources we are not on, with the action for each

Say "Claude with web search" in the report, so nobody mistakes it for a ChatGPT result.

## Do not

- Pay for trackers, placements, or links
- Invent an answer, a rank, or a citation; if search fails, log `error`
- Count the same answer twice, or ask a question twice in one day to fish for a better result
- Post, submit, or email anything; outreach is drafted for the owner

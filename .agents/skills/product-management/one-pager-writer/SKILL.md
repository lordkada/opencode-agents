---
name: one-pager-writer
description: Helps Product Managers create a synthetic, highly effective and engaging one-pager that presents a new product or initiative to strategic decision-makers and stakeholders. Use this skill whenever the user wants to draft, structure, or pressure-test a one-pager, executive brief, executive summary, pitch on a single page, or "one page for the board/leadership" — the short, persuasive artifact meant to win a decision, not the detailed build spec. Trigger it even when the user doesn't say "one-pager" explicitly but describes the task — e.g. "I need to convince leadership on X in a page", "give me a punchy pitch for the steering committee", "help me sell this initiative to execs", or "review my one-pager and tell me where it's weak". Works in both Italian and English. Do NOT use for detailed PRDs / product specs (use prd-writer), full rewriting or ghostwriting of an existing draft, marketing/go-to-market copy, or technical design docs.
---

# One-Pager Writer

This skill helps a Product Manager turn an idea into a synthetic, persuasive one-pager that wins a decision from strategic decision-makers and stakeholders.

## Core philosophy: persuasion for deciders, not completeness for builders

A one-pager is the opposite cognitive job from a PRD. A PRD optimizes **completeness** so builders can execute without guessing. A one-pager optimizes **persuasion and clarity for a single decision** — the reader has seconds, not minutes, and needs to grasp the ask and the "so what" immediately. If the user actually needs a detailed spec engineering can build from, redirect to `prd-writer`; don't try to make this skill do both jobs.

So the guiding principles are:

1. **One decision, one page.** Every one-pager exists to win a specific decision from a specific audience (funding, a green light, a resource ask, a strategic bet). If that decision isn't clear, find it before writing anything.

2. **Bottom line up front.** Decision-makers skim. Lead with the ask and the payoff, not with background and context that delays the point.

3. **Never invent facts.** Do not fabricate metrics, market sizes, research findings, or quotes. When something needs a real number or evidence the user hasn't given, either ask, or write it as an explicit, visible marker (see "Handling unknowns"). A convincing one-pager full of fake numbers is dangerous, not helpful — it can talk a real decision-maker into a bad decision.

4. **Bilingual.** Write in the language the user is using for the request. If they switch languages or ask explicitly, follow that. When unsure which language the *document* should be in (as opposed to the conversation), ask once.

## Handling unknowns

Use these markers, visibly, whenever a real value or piece of evidence is missing — never present a guess as fact:

- **Open question:** `> ❓ Open question: ...`
- **Assumption:** `> ⚠️ Assumption: ...`
- **Placeholder:** `Placeholder: ... from [X%] to [Y%] — _fill in from analytics_`

## Workflow

First, detect the **mode**: is the user asking to *create* a one-pager from scratch, or to *critique/pressure-test* an existing draft? If ambiguous (e.g. they paste a draft with no clear ask), ask which they want before proceeding.

### Creation branch

1. **Understand** — ask a small, focused batch of questions (2–4 max, not an interrogation): who's the audience and what decision are we asking them to make, what type of initiative is this, and what evidence is already available. If the user clearly wants a fast first draft ("just draft it, I'll fix it"), honor that — write the draft and mark every guess as an assumption rather than blocking on questions.

2. **Diagnose + recommend framework** — from the signals gathered, recommend ONE framework with an explicit rationale (why this framework fits this audience/initiative). The user can override the recommendation. Read `references/framework-library.md` for the signal-to-framework mapping and each framework's structure before recommending.

3. **Write** using the chosen framework's structure, filling it with the user's real evidence (or markers for what's missing).

4. **Apply the impact bar** — cross-cutting quality gates that apply regardless of framework: BLUF, a single clear decision/call-to-action, explicit non-goals, measurable success metrics, evidence over opinion, single-page discipline, visual hierarchy. Read `references/impact-craft.md` for the full bar and the craft behind it.

5. **Output** — Markdown, with visual/layout guidance (hierarchy, callouts, tables, whitespace) and notes on how to render the same content onto a slide/deck.

### Critique branch

Read the existing one-pager and score it against the impact bar — hook, clarity, "so-what", evidence, single decision/CTA, length, framework fit — as defined in `references/impact-craft.md`. Report specific, actionable weaknesses, tied to what's actually on the page.

**Do not rewrite the document.** This is an explicit non-goal: the skill critiques, the user applies the feedback.

## Reference files

- `references/framework-library.md` — the four frameworks (PR/FAQ, NABC, Product One-Pager, Heilmeier Catechism): scope, diagnostic signal, structure, skeleton, and pitfalls, plus the signal-to-framework decision guide. Read before diagnosing or drafting (step 2).
- `references/impact-craft.md` — persuasive, synthesis, and visual craft, plus the shared impact bar. Read when writing (step 4) and whenever critiquing an existing draft.

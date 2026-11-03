# Framework Library

Four proven one-pager frameworks, each optimized for a different decision context. Read this file before recommending a framework (creation branch, step 2) and before drafting (step 3).

## Decision guide

| Framework | Choose when the signal is... |
|---|---|
| PR/FAQ (Working Backwards) | New customer-facing product whose value must be validated |
| NABC (Need–Approach–Benefit–Competition) | Needs buy-in / budget approval from internal decision-makers |
| Product One-Pager (SVPG/Cagan) | Discovery / product-strategy alignment |
| Heilmeier Catechism | Innovative / R&D initiative with high uncertainty and risk |

Recommend exactly one framework based on the strongest matching signal from the "Understand" step (audience, initiative type, decision being asked). State the rationale explicitly — which signal drove the pick — so the user can judge it, not just accept it. If two signals conflict (e.g. a customer-facing product that also needs budget approval from a skeptical internal committee), weigh the audience: whoever holds the decision is the signal that wins. Always honor an explicit user override — if they name a framework, use it, note briefly if it seems like a mismatch for the signals gathered, and move on without relitigating the choice.

---

## PR/FAQ (Working Backwards)

### Purpose

Forces the writer to start from the customer's experience of a finished product, not from the internal plan to build it. Popularized by Amazon: you write the press release and FAQ *before* writing a line of code, so the value proposition has to survive being said in plain, customer-facing language.

### Diagnostic signal

A new customer-facing product or feature whose value proposition hasn't been pressure-tested yet — especially when the real risk is "will anyone care," not "can we build it."

### Structure

1. **Headline** — the product name and its one-sentence promise, as a customer would read it.
2. **Subheadline** — who it's for and what they get, one line.
3. **Summary paragraph** — the product, the problem it solves, and why now, in 3-4 sentences.
4. **Customer problem** — the pain today, described from the customer's point of view.
5. **Solution** — how the product solves it, still in customer language, not architecture.
6. **Executive quote** — a hypothetical, illustrative quote from a leader explaining why this matters strategically. Mark it as illustrative — never present it as something anyone actually said.
7. **How to get started** — the concrete first step a customer takes.
8. **Customer quote** — a hypothetical, illustrative quote from a customer describing the benefit they experienced. Mark it as illustrative — never present it as something anyone actually said.
9. **FAQ** — split into internal questions (economics, risks, dependencies, what could kill this) and external questions (what a real customer would actually ask: pricing, availability, how it works, what it doesn't do).

### Skeleton

```markdown
# [Product/Feature Name]: [One-sentence promise]

*[One-line subheadline: who it's for and what they get]*

[Summary paragraph: 3-4 sentences on the product, the problem, and why now.]

## The problem

[Customer problem in the customer's own words/context — what's broken or missing today.]

## The solution

[How the product solves it, described the way a customer would experience it.]

> "[Illustrative executive quote on why this matters strategically — hypothetical, not a real quote.]"
> — [Name, Title] *(illustrative)*

## Getting started

[The first concrete step a customer takes to use this.]

> "[Illustrative customer quote describing the benefit in their own words — hypothetical, not a real quote.]"
> — [Name, Role/Company] *(illustrative)*

## FAQ

**Internal**
- What does this cost to build and run? [Placeholder: from analytics/finance]
- What's the biggest risk to shipping this? ...
- What does this depend on? ...

**External (what a customer would ask)**
- How much does it cost? ...
- When is it available? ...
- How is this different from [alternative]? ...
- What does it *not* do? ...
```

### Common pitfalls

- Writing the PR/FAQ **after** the product is mostly built, as a retroactive marketing exercise — the whole point is to write it first and let it reveal weak value propositions early.
- Solution-first framing: describing the feature before establishing the customer's problem is real and worth solving.
- An FAQ that dodges the hard questions (real cost, real risk, real dependencies) instead of surfacing them — a safe FAQ is a useless FAQ.
- Presenting the executive/customer quote as a real, sourced testimonial rather than a clearly marked hypothetical — a fabricated quote dressed up as real is a credibility risk, not a persuasive device.

---

## NABC (Need–Approach–Benefit–Competition)

### Purpose

A compact framework (SRI International) built for winning internal buy-in and budget. It forces a direct line from a real need to a quantified benefit and an honest look at alternatives — the exact things a budget-holder will probe.

### Diagnostic signal

The core decision is buy-in or budget approval from internal decision-makers — a steering committee, a VP, a resource-allocation review — not a customer-facing pitch.

### Structure

1. **Need** — the real, evidenced need this addresses, for whom.
2. **Approach** — the proposed solution, described at the value level, not the build-detail level.
3. **Benefit** — the payoff, quantified wherever possible.
4. **Competition/alternatives** — other ways this need could be met, including doing nothing, and why this approach wins.

### Skeleton

```markdown
# [Initiative name]

## Need

[The need, who has it, and the evidence it's real — data, requests, cost of the status quo.]

## Approach

[The proposed solution, described in terms of what it enables, not how it's engineered.]

## Benefit

[The quantified payoff: from [X%/units] to [Y%/units], or [$Z] saved/gained, by [timeframe].]
[If a number isn't known yet: > ⚠️ Assumption: ... or Placeholder: [X%] — fill in from analytics.]

## Competition / alternatives

| Option | Why not |
|---|---|
| Do nothing | ... |
| [Alternative approach] | ... |
| [Buy/partner instead of build] | ... |

Why this approach wins: ...
```

### Common pitfalls

- Stating a Benefit qualitatively ("this will improve things") instead of quantifying it — a budget-holder needs a number to compare against other asks.
- Skipping the "do nothing" alternative — it's the default competitor to every internal ask and ignoring it looks like you haven't considered the cost of inaction.
- Describing the Approach in build-detail (architecture, tech stack, sprint plan) instead of the value it delivers — that detail belongs in a PRD, not here.

---

## Product One-Pager (SVPG/Cagan)

### Purpose

A discovery-stage tool (Marty Cagan / SVPG) for aligning a product team and stakeholders on a problem worth exploring *before* committing to a solution. It's deliberately evidence-heavy and solution-light.

### Diagnostic signal

The decision is about product strategy or discovery alignment — should we invest time exploring this problem/opportunity at all — rather than approving a specific build or winning external customers.

### Structure

1. **Problem/opportunity** — stated with evidence (data, research, qualitative signal), not assertion.
2. **Target customer & context** — who has this problem and when/where it shows up.
3. **Goals/success metrics** — how we'd know this was worth pursuing, stated as measurable outcomes.
4. **Non-goals** — what this deliberately excludes, to prevent scope creep from day one.
5. **Solution hypothesis** — a tentative direction, explicitly framed as unproven.
6. **Risks & open questions** — what could invalidate the hypothesis, and what's still unknown.

### Skeleton

```markdown
# [Problem/Opportunity name]

## Problem / opportunity

[The problem, with evidence: data point, research finding, or qualitative signal — cite the source.]

## Target customer & context

[Who experiences this, and in what situation/context it shows up.]

## Goals & success metrics

- Goal: [measurable outcome]
- Metric: [name] — baseline [X], target [Y], measured by [method]

## Non-goals

- [What this explicitly does not attempt to solve, at least for now.]

## Solution hypothesis

[A tentative direction — explicitly a hypothesis, not a commitment.]
> ⚠️ Assumption: this is unvalidated; discovery should test it before build.

## Risks & open questions

- ❓ Open question: ...
- Risk: ... — could invalidate the hypothesis if ...
```

### Common pitfalls

- Missing non-goals entirely — without them, the doc invites scope creep the moment stakeholders start adding "while we're at it" requests.
- Leading with opinion instead of evidence in the Problem/opportunity section — this framework's whole value is forcing evidence-first thinking.
- Success metrics that sound measurable but aren't (e.g. "improve engagement") instead of a named metric with baseline and target.

---

## Heilmeier Catechism

### Purpose

A set of nine questions (George Heilmeier, DARPA) designed to stress-test high-uncertainty, high-risk initiatives — R&D, novel technical bets, or anything where "will this even work" is the central question, not "will customers want it" or "will the org fund it."

### Diagnostic signal

An innovative or R&D-flavored initiative carrying real technical or scientific uncertainty, where the decision-maker needs to judge feasibility and risk as much as desirability.

### Structure

The nine questions, answered in order:

1. What are you trying to do? (plain language, no jargon)
2. How is it done today, and what are the limits of current practice?
3. What is new in your approach, and why do you think it will succeed?
4. Who cares?
5. If you succeed, what difference will it make?
6. What are the risks?
7. How much will it cost?
8. How long will it take?
9. What are the mid-term and final "exams" to check for success?

### Skeleton

```markdown
# [Initiative name]

## 1. What are you trying to do?

[Plain-language statement — no jargon, as if explaining to a smart non-specialist.]

## 2. How is it done today, and what are the limits?

[Current practice / status quo, and specifically where it falls short.]

## 3. What's new, and why will it succeed?

[The novel element of the approach, and the reasoning for why it overcomes the limits above.]

## 4. Who cares?

[Who specifically benefits from this succeeding.]

## 5. If you succeed, what difference will it make?

[The concrete change in the world/business if this works — not just "it'll be better."]

## 6. What are the risks?

- [Technical risk] — ...
- [Execution/organizational risk] — ...

## 7. What will it cost?

[Placeholder: $X / [X FTE-months] — fill in from planning]

## 8. How long will it take?

[Timeline, with major milestones.]

## 9. What are the mid-term and final "exams"?

- Mid-term checkpoint: [measurable signal] by [date]
- Final exam: [measurable success criterion] by [date]
```

### Common pitfalls

- Answering the "what are you trying to do" question in jargon — if a smart outsider can't follow it, the rest of the document loses its foundation.
- No measurable checkpoints for the mid-term/final "exams" question — "we'll know it's working" isn't an exam; a checkpoint needs a date and a criterion.
- Hand-waving the risks question — vague or omitted risks are the fastest way to lose credibility with a reviewer trained to look for exactly this gap.

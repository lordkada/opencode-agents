---
name: prd-writer
description: Helps Product Managers write Product Requirement Documents (PRDs) for digital products — apps, web platforms, SaaS, APIs, internal tools, and new features. Use this skill whenever the user wants to draft, structure, refine, critique, or expand a PRD, product spec, product brief, feature spec, or requirements document. Trigger it even when the user doesn't say "PRD" explicitly but describes the underlying task — e.g. "I need to spec out this new feature", "write up the requirements for X", "turn this idea into something engineering can build", "help me define what we're building and why", or "review my product doc". Works in both Italian and English. Do NOT use for go-to-market plans, marketing copy, pure technical design docs (RFCs/architecture), or project timelines unless they're a section inside a PRD. For a short, persuasive one-pager aimed at decision-makers, use one-pager-writer instead.
---

# PRD Writer

This skill helps a Product Manager turn an idea into a clear, decision-ready Product Requirements Document for a digital product.

## Core philosophy: the document is the easy part

A PRD is not a form to fill in. It is a tool for **alignment and decision-making**. The value is in the thinking it forces, not in the sections. The single biggest failure mode when an AI writes a PRD is confidently inventing the things that should come from the PM and the team: the real user problem, the metrics, the constraints, the evidence. A beautifully formatted PRD full of plausible-sounding fiction is worse than no PRD, because people trust it.

So the guiding principles are:

1. **Problem before solution.** Always anchor the document in a real user/business problem and the evidence for it, before describing what to build. If the user jumps straight to a solution ("we need a chatbot"), gently work backwards to the problem it solves. This is the Cagan / "working backwards" instinct: a feature is only as good as the problem it solves.

2. **Outcomes over outputs.** What changes for the user or business if this ships? Push toward measurable success criteria, not just a list of features. "Reduce time-to-first-value from 5 days to 1" is a goal; "build an onboarding wizard" is a tactic.

3. **Never invent facts.** Do not fabricate metrics, user research findings, market sizes, competitor data, or quotes. When something needs a real number or evidence the user hasn't given, either ask, or write it as an explicit placeholder/assumption that flags it needs filling in (see "Handling unknowns").

4. **Match depth to the decision.** A small feature tweak needs a one-pager; a new product line needs a full PRD. Don't bury a simple decision under fifteen headings. Adapt.

## Workflow

### Step 1 — Understand before writing

Read what the user gave you. Then decide whether you have enough to write something genuinely useful, or whether you'd be guessing. If key things are missing, ask a **small, focused** batch of questions first (3-6 max, not an interrogation). The highest-value questions are usually:

- **Problem & evidence:** What problem are we solving, for whom, and how do we know it's real? (data, support tickets, user interviews, intuition?)
- **Users:** Who specifically is this for? (a persona, a segment, an internal team)
- **Goal & success metric:** What outcome are we trying to move, and how will we measure success?
- **Scope & constraints:** Timeline, team size, must-not-break, platforms, dependencies, regulatory?
- **Current state:** Is this net-new, or changing something that exists?

If the user clearly wants a fast first draft and says "just draft it, I'll fix it", honor that — write the draft and mark every guess as an assumption rather than blocking on questions. Use judgment about how much to ask: a vague one-line request needs questions; a detailed brief needs few or none.

### Step 2 — Pick the right format

Read `references/prd-template.md` for the full structure and section-by-section guidance. Then choose the depth:

- **One-pager / brief** — for a small feature, an experiment, or an early idea that needs alignment before investing more. Use the lightweight section set.
- **Full PRD** — for a substantial feature or product where engineering, design, and stakeholders need a shared, detailed reference.

The template file describes both. Don't include sections that add no value for the case at hand — an empty "Internationalization" heading is noise. It's better to have six sharp sections than fifteen padded ones.

### Step 3 — Write in Markdown

Output the PRD as a Markdown file in `/mnt/user-data/outputs/` (or inline if the user is clearly just iterating in chat on a short piece). Markdown is the requested default. Use headings, tables for requirements and metrics, and checklists where they earn their place. Keep prose tight — engineers and execs skim PRDs.

Write in the language the user is using for the request. If they switch languages or ask explicitly, follow that. When unsure which language they want the *document* in (as opposed to the conversation), ask once.

### Step 4 — Pressure-test it

Before presenting, read it back as a skeptical reader and check it against the quality bar in `references/quality-bar.md`. The quick version:

- Could an engineer start building from this without a dozen clarifying questions?
- Is every requirement testable and unambiguous? ("fast" is not a requirement; "p95 < 200ms" is)
- Are success metrics measurable and tied to the goal?
- Is scope explicit, including what's **out** of scope?
- Are assumptions, risks, and open questions surfaced rather than hidden?
- Did you invent any number or fact? If so, convert it to an assumption or a question.

## Handling unknowns

This is what separates a trustworthy PRD from a dangerous one. When you don't have a real value, never make one up and present it as fact. Instead use one of these, visibly:

- **Open question:** `> ❓ Open question: what's the current weekly active user count? (needed to size impact)`
- **Assumption:** `> ⚠️ Assumption: we're targeting iOS first; Android in a fast-follow. Confirm with eng.`
- **Placeholder:** `Success metric: increase trial-to-paid conversion from [X%] to [Y%] — _fill in baseline from analytics_`

Collect these so the PM can see at a glance exactly what still needs their input. A PRD that honestly says "we don't know X yet" is more useful than one that pretends to.

## What good looks like

- **Requirements** are specific, prioritized (P0/P1/P2 or Must/Should/Could), and ideally carry acceptance criteria. Prefer user-story framing where it fits ("As a [user], I want [capability], so that [outcome]") with clear, testable acceptance criteria.
- **Metrics** name the metric, the current baseline, the target, and how it's measured.
- **Scope** has an explicit "out of scope" / "non-goals" section — saying no is half a PM's job, and writing it down prevents scope creep.
- **Tone** is concise and neutral. A PRD persuades through clarity and evidence, not adjectives.

See `references/quality-bar.md` for concrete good-vs-weak examples of requirements, metrics, and acceptance criteria.

## Reference files

- `references/prd-template.md` — full PRD structure, the one-pager variant, and what each section is for. Read this before drafting.
- `references/quality-bar.md` — examples of strong vs weak requirements, metrics, acceptance criteria, and clarifying questions. Read this when refining or critiquing.

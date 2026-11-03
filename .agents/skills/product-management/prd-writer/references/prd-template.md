# PRD Template & Structure

This file describes the structure of a PRD. It is a *menu*, not a mandatory checklist — include only the sections that help the reader decide and build. Section names are given in English with an Italian equivalent in parentheses; write the actual document in whichever language the user wants.

---

## Variant A — One-pager / Brief (Brief)

Use for small features, experiments, or early ideas that need alignment before deeper investment. Aim for one screen.

```
# [Feature/Product name]
**Status:** Draft · **Author:** [name] · **Date:** [date] · **Stakeholders:** [eng/design/etc.]

## TL;DR
2-3 sentences: what we're building and why it matters now.

## Problem (Problema)
The user/business problem and the evidence it's real.

## Goal & success metric (Obiettivo e metrica)
The outcome we want + how we'll measure it (metric, baseline, target).

## Proposed solution (Soluzione proposta)
High-level approach. Not a full spec — enough to align.

## Scope & non-goals (Ambito e non-obiettivi)
What's in. What's explicitly out.

## Open questions & risks (Domande aperte e rischi)
What we still need to resolve.
```

---

## Variant B — Full PRD (PRD completo)

Use for substantial features or products. Below, each section includes *why it exists* so you can judge whether it's needed.

### 1. Header (Intestazione)
Name, status (Draft/In review/Approved), author, last updated, contributors/stakeholders, and links (designs, tickets, related docs). Lets readers know how much to trust it and where to go next.

### 2. TL;DR / Summary (Sintesi)
3-5 sentences a busy exec can read and understand the gist. Write this *last*, even though it appears first.

### 3. Problem & context (Problema e contesto)
The heart of the document. What problem, for whom, and the evidence (data, research, support volume, strategic rationale). Include "why now?" if timing matters. A PRD that can't articulate the problem shouldn't proceed to a solution.

### 4. Goals & non-goals (Obiettivi e non-obiettivi)
Goals: the outcomes this should achieve, ideally as measurable statements. Non-goals: what this deliberately does *not* try to do. Non-goals are as important as goals — they prevent scope creep and set expectations.

### 5. Success metrics (Metriche di successo)
How we'll know it worked. For each: metric name, current baseline, target, and measurement method. Distinguish the primary metric (the one number that defines success) from secondary/guardrail metrics (things that must *not* get worse). A table works well.

### 6. Target users (Utenti target)
Personas, segments, or Jobs-To-Be-Done. Who experiences the problem and will use the solution. Keep it grounded in real segments, not invented demographics.

### 7. User stories / use cases / scenarios (Storie utente / casi d'uso)
The key journeys. User-story format is great here: "As a [user], I want [capability] so that [benefit]." Order by importance. These bridge the problem and the requirements.

### 8. Requirements (Requisiti)
The functional requirements, prioritized (P0/P1/P2 or Must/Should/Could/Won't). Each requirement should be specific, testable, and ideally carry acceptance criteria. A table with columns *ID · Requirement · Priority · Acceptance criteria · Notes* works well. Separate **functional** (what it does) from **non-functional** (performance, security, accessibility, scalability, compliance) where the latter matter.

### 9. UX / flows / design (UX e flussi)
Link to mockups/prototypes rather than reproducing them. Describe key states, edge cases, empty/error/loading states, and the happy path. If designs don't exist yet, say so and describe intent.

### 10. Scope (Ambito)
In-scope vs out-of-scope, ideally split by release/phase if it ships incrementally (MVP → fast-follow → later).

### 11. Dependencies & risks (Dipendenze e rischi)
Technical, organizational, legal, or third-party dependencies. Risks with likelihood/impact and mitigation. Be honest — hidden risks surface at the worst time.

### 12. Rollout / launch plan (Piano di rilascio)
Phasing, feature flags, beta/GA, success gates, rollback plan. Include only if the rollout is non-trivial.

### 13. Analytics & instrumentation (Analytics e strumentazione)
What events/properties must be tracked to measure the success metrics above. PMs forget this constantly and then can't tell if the feature worked. Tie it back to section 5.

### 14. Open questions (Domande aperte)
A living list of unresolved decisions, with owners where possible. This is where honesty about unknowns lives.

### 15. Appendix (Appendice)
Research links, competitive notes, prior discussions, glossary — anything supporting but not essential to the main read.

---

## Ordering principle

Lead with **why** (problem, goals, metrics, users), then **what** (requirements, UX), then **how/when** (scope, dependencies, rollout). A reader should be able to stop after the "why" sections and already understand whether this is worth doing.

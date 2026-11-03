# Quality Bar: strong vs weak

Concrete examples of the difference between a PRD that engineering can build from and one that generates a dozen follow-up questions. Use these when drafting, refining, or critiquing.

## Requirements

A good requirement is **specific, testable, and unambiguous**. If two engineers could read it and build different things, it's not done.

**Weak:** The search should be fast and return relevant results.
**Strong:** P0 — Search returns results in < 300ms (p95) for the top 10k queries. Results are ranked by relevance score; exact title matches rank first. Acceptance: a query with a known exact match shows it as result #1.

**Weak:** Users can manage their notifications.
**Strong:** P1 — Users can toggle each notification category (mentions, comments, weekly digest) independently from Settings → Notifications. Changes take effect immediately. Acceptance: toggling "comments" off stops comment emails within 1 minute; other categories unaffected.

The pattern: replace vague adjectives ("fast", "relevant", "easy", "robust") with observable, measurable behavior, and add an acceptance criterion that someone could literally test.

## Success metrics

A good metric has a **name, baseline, target, and measurement method**, and is tied to the goal.

**Weak:** Increase user engagement.
**Strong:** Primary — Increase 7-day retention of new signups from 32% (current, last 90 days) to 40%, measured via the cohort retention dashboard. Guardrail — support ticket volume must not increase by more than 5%.

If you don't have the baseline, write it as a placeholder rather than inventing one:
`from [baseline — pull from analytics] to [target TBD with team]`.

## Acceptance criteria

Acceptance criteria make a story testable. Prefer a clear given/when/then or a short checklist.

**Story:** As a returning user, I want to resume an unfinished form, so that I don't lose my progress.
**Acceptance:**
- Given a form with at least one field filled, when the user leaves and returns within 7 days, then the previously entered values are restored.
- Given the user explicitly clears the form, when they return, then no data is restored.
- Saved data is encrypted at rest and purged after 7 days.

## Clarifying questions worth asking

The best clarifying questions unblock the document, not satisfy curiosity. Examples of high-leverage ones:

- "What's the one metric that, if it moved, would make this a success?" (forces a single primary metric)
- "Who specifically is this for — and who is it *not* for?" (sharpens users + non-goals)
- "What's the evidence this problem is worth solving now?" (guards against solution-first thinking)
- "What must not break / what's explicitly out of scope for v1?" (controls scope)
- "Is there a hard deadline or dependency driving timing?" (surfaces constraints)

Low-value questions to avoid: anything you can reasonably infer from context, or cosmetic preferences that don't change the substance ("what font?").

## Red flags to catch when critiquing a PRD

- Solution described before the problem is established.
- Metrics that can't actually be measured, or "engagement" with no definition.
- Requirements full of adjectives instead of testable behavior.
- No out-of-scope section (scope creep waiting to happen).
- Invented numbers presented as fact with no source.
- Every section filled in for completeness even where there's nothing real to say.
- Risks and open questions absent — a sign of optimism, not readiness.

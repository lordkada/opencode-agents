# Impact Craft

Persuasive writing craft and visual/layout guidance for a one-pager, plus the impact bar — the shared quality gate used both when drafting (creation branch, step 4) and when scoring an existing draft (critique branch).

## Persuasive craft

**Headline/hook.** The opening line is the one most readers will act on — if it doesn't survive alone, nothing after it gets read. Lead with the "so what": the payoff or the ask, not the setup. "We propose a new onboarding flow" is a topic; "Cutting onboarding drop-off from [X%] to [Y%] requires a [N]-week redesign — approve now to hit [quarter]" is a hook.

**SCQA opening.** Use Situation → Complication → Question → Answer to build and resolve tension in the first few lines: state the stable situation, introduce the complication that breaks it, pose the question that complication raises, then answer it with the recommendation. This gives the reader the whole argument in miniature before they've read the details, and it's the fastest way to make an opening feel inevitable rather than assembled.

**The "so what" test.** Run every claim, stat, and bullet through "why does this matter to the reader's decision?" A fact that doesn't pass — "we shipped [N] tickets last sprint" — either needs a "so what" clause attached ("...which cleared the backlog blocking the launch") or should be cut. Facts without consequence are noise in a document that only has seconds of attention.

**Cut jargon.** Write as if explaining to a smart outsider who doesn't share your team's acronyms or internal shorthand. If a decision-maker outside the immediate team can't follow a sentence, the sentence is doing the writer's thinking for them, not the reader's — rewrite it in plain language.

**Active voice.** "We will reduce churn by [X%]" carries more force and more accountability than "churn will be reduced by [X%]." Passive voice tends to hide who's doing what, which is exactly the ambiguity a decision-maker is trying to resolve.

**Quantified claims over adjectives.** "Significant," "substantial," and "dramatic" ask the reader to trust an adjective; a number lets them judge for themselves. Prefer "reduces latency from [X]ms to [Y]ms" over "significantly improves latency." When the real number isn't known yet, use a visible marker (an assumption note or a bracketed placeholder) rather than a vague qualifier — a marked unknown is more credible than an unmarked guess.

**BLUF (bottom line up front).** State the ask and the recommendation before the supporting argument, not after it. Decision-makers who stop reading after the first paragraph should still walk away with the point; everything that follows is justification, not the payload.

## Visual & layout

**Visual hierarchy.** One dominant element — usually the headline/ask or a single key metric — should be unmissable at a glance, with everything else subordinate to it. If three things compete for the reader's eye with equal weight, none of them wins.

**Scannability.** A reader should grasp the ask within seconds of opening the document, before reading a single full paragraph. Front-load headers and bolded key phrases so skimming down the left margin alone reveals the argument's shape.

**Callouts for the key decision.** Make the single decision/CTA visually distinct — a bolded line, a boxed/blockquoted statement, or a clearly labeled "Ask" section — so it can't be missed even by a reader who only skims.

**Tables for comparisons and metrics.** Any time the content is a comparison (alternatives, before/after, options) or a set of metrics with baselines and targets, a table communicates it faster and more credibly than prose. Prose forces the reader to reconstruct the comparison themselves; a table hands it to them.

**Whitespace and one-page discipline.** A one-pager that's crammed edge-to-edge reads as harder to trust, not more thorough — density signals "unedited," not "rigorous." Cut ruthlessly to protect margins and line breaks between sections; if it doesn't fit on one page after cutting, the scope of the ask is probably still too broad.

**Translating to a slide/deck.** When the same content needs to become a slide: the one-pager's headline becomes the slide title verbatim; the single most decision-relevant number or comparison becomes the one chart (never more than one chart per slide); the callout/ask becomes the slide's closing CTA line. Everything else — supporting detail, FAQ, secondary evidence — moves to speaker notes or an appendix rather than fighting for space on the slide.

## The impact bar

Cross-cutting quality gates that apply regardless of which framework was used to structure the document. Use this checklist both to self-check a draft before finishing it (creation branch, step 4) and to score an existing document (critique branch).

- [ ] **Hook** — does the opening lead with the "so what"?
- [ ] **Single decision / CTA** — is exactly one decision being asked for, and is it explicit?
- [ ] **Clarity** — can a decision-maker grasp the ask in seconds?
- [ ] **So-what** — does every claim answer "why does this matter"?
- [ ] **Evidence over opinion** — are claims backed by data/evidence, with unknowns visibly marked?
- [ ] **Non-goals** — is scope bounded (what we are NOT doing)?
- [ ] **Success metrics** — measurable, with baseline and target?
- [ ] **Length** — does it fit on one page?
- [ ] **Framework fit** — does it follow the chosen framework's structure?
- [ ] **Visual hierarchy** — is it scannable, with the key point visually dominant?

In the critique branch, report each unchecked item as a specific, actionable weakness tied to what's actually on the page — do not rewrite the document.

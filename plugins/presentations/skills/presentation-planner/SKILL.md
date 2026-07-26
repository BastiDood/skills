---
name: presentation-planner
description: Turn a talk script, transcript, outline, or source document into an implementation-ready Markdown presentation plan. Use when Codex must segment spoken material into paced slides, structure presenter notes semantically, derive faithful on-slide cues, choose content-led slide forms, specify required visuals and transitions, and plan a deck without creating it.
---

# Basti Presentation Planner

## Objective

Produce a tactical Markdown production script that a separate creator can execute without rereading or reinterpreting the source. Treat each slide as a visual aid for one spoken beat, using only the text, evidence, imagery, whitespace, diagram, or motion that beat requires.

## Boundaries

- Create or revise only the requested Markdown plan; do not create, edit, render, or inspect a slide deck.
- Plan from the source script and this skill; do not request or inspect reference decks.
- Specify required assets without sourcing or generating them, choosing exact coordinates, or implementing template internals.
- Do not invent facts, quotations, examples, or claims. Mark verification needs and consequential gaps.

## Inputs

Require the complete source or equivalent material. Accept audience, duration, venue, format, objective, live-demo requirements, mandatory content, and exclusions. Treat existing slide markers as hints.

Infer harmless details from the source and record the assumptions. Ask only when ambiguity changes the thesis, audience outcome, duration, required content, or delivery format.

## House Principles

### Deck-Level Scope

- Let the content determine the mix of slide forms; set no quotas for visuals, builds, or transitions.
- Make the sequence cumulative. Let each slide answer a question raised by the previous slide or create the question answered by the next.
- Open with context, stakes, tension, or a question. Close by resolving it with a useful takeaway or action, not a generic thank-you.

### Slide-Level Scope

- Give each slide one narrative job and one primary claim.
- Choose the simplest legible form. Text-only, bullet-only, evidence-only, diagram-only, and nearly empty slides are complete when they serve the beat.
- Prefer one coherent composition over card grids, panels, badges, or decorative UI.
- Split competing claims across slides. Prefer another slide over smaller type or denser copy.
- Let code, a diagram, screenshot, photograph, quotation, or other direct evidence dominate when it carries the beat; add only an essential title or labels.

### Diagram-Level Scope

- Use a diagram only when an audience-critical relationship, sequence, structure, flow, cause, comparison, or change is clearer visually than through direct text or evidence.
- Do not turn concepts into boxes and arrows merely because possible. Avoid filler mini-diagrams and repeated prose-left, framed-diagram-right compositions.

### Copy-Level Scope

- For a bullet-driven slide, default to a title or claim, one verbatim main bullet, and zero to four verbatim supporting sub-bullets in source order. Use no bullets when another element carries the beat.
- Derive titles and bullets from short contiguous source spans that are understandable at a glance. Avoid slogans, marketing taglines, and summaries the speaker cannot locate in the notes.
- Use natural sentence case for body copy and title case for titles. Preserve source casing for acronyms, names, filenames, commands, paths, identifiers, and quotations; never normalize a literal such as `tokio::spawn`.
- Mark one to three load-bearing spans per sentence-sized bullet with `**...**` or `*...*` for bold cyan emphasis.
- Write titles as speakable claims or questions. Use **Title Case without Prepositions and Articles**, while preserving product, acronym, and identifier casing.

## Semantically Structured Presenter Notes

Use the assigned source passage for every substantive slide. Preserve its meaning, words, order, casing, punctuation at split boundaries, terminology, humor, candor, and technical precision. Do not summarize, polish, correct, embellish, or synthesize spoken content.

Format the passage as a semantic Markdown hierarchy:

- Use level 0 for each independent claim, transition, question, setup, conclusion, or observation. Do not make the first sentence a parent merely because it comes first.
- Nest only supporting, explanatory, qualifying, exemplary, enumerative, completing, or answering spans. Keep independent sentences as siblings, even within one paragraph.
- Use one sentence per bullet unless tightly coupled sentences lose meaning when separated.
- Split long sentences at a colon, semicolon, dash, or strong comma. Make dependent continuations children and independent continuations siblings; preserve the words, order, and boundary punctuation.
- Preserve source-authored list depth. Indent introduced lists once and their nested items again; use levels 0–2 unless the source requires more.
- Allow several level-0 groups on one slide when they form one beat and fit the pacing limits. Treat list markers and indentation as non-spoken structure.

Prefer slide boundaries between complete level-0 groups. When pacing or visual continuity requires a split inside a group, retain the continuing bullets' indentation on the next slide, even when that slide begins at level 1 or 2. Do not repeat or invent a parent solely to make the slide-local list self-contained.

After allocating the spoken notes, add a next-thought preview to each applicable non-final slide:

- Copy the first spoken bullet of the next slide, or its first natural clause when the bullet is long.
- Preserve its words, casing, and punctuation; append an ellipsis, wrap it in parentheses, and place it as the final level-0 bullet: `- (The next thought begins here…)`. Add a leading ellipsis when copying a mid-sentence continuation: `- (… then the consequence follows…)`.
- Treat the preview as non-spoken. Exclude it from the outgoing slide's counts and coverage; assign the source only to the incoming slide.
- Omit it when no spoken thought follows.

Preserve source-authored parentheses around spoken asides, pauses, and questions; these remain spoken content and are not next-thought previews.

Use square brackets only for terse, non-spoken delivery cues such as `[Read slide.]`, `[Pause for questions.]`, or `[Advance.]`. Add a cue only when the slide interaction requires it.

## Presenter Note Pacing

Apply the stricter category reached by word or character count:

- **Normal:** at most 90 words and 525 characters.
- **Review:** 91–122 words or 526–750 characters.
- **Split:** above 122 words or 750 characters.
- **Explicit exception:** retain above 155 words or 900 characters only with a rationale in `Production Notes`.

Count only spoken source text. Exclude list syntax, previews, delivery cues, and other structure. When a split retains one visual context, preserve that visual across the successive slides.

## Transition Grammar

Use motion only to communicate meaning:

- **Cut:** quiet default.
- **Build:** dependency order.
- **Morph:** evolution of the same idea, code, or diagram.
- **Push:** sustained catalogue, gallery, or lateral sequence.
- **Fade:** section boundary, reflection, reset, or comedic release.
- **Directional transition:** deliberate directional handoff.

Use near-duplicate slides when controlled emphasis explains code or a diagram better than internal animation. Keep advance manual unless the source requires timing.

## Workflow

### 1. Define the Job and Arc

Read the complete source. Express the communication job in one sentence:

> By the end, **[audience]** should **[outcome]** because **[central takeaway]**.

Identify the essential claims, examples, demonstrations, evidence, and emotional beats. Choose an appropriate arc and describe its opening, development, pivot, resolution, and final audience state. Treat an agenda as navigation, not the narrative.

### 2. Segment the Beats

Create a new beat when the audience task, claim, evidence, emotional register, demonstration, or need for a pause changes. Apply the pacing limits even when successive slides retain the same visual.

Assign the simplest fitting form:

- Text-led: title, section marker, question, assertion, concise bullets, quotation, recap, call to action, or credits.
- Evidence-led: code, screenshot, chart, table, photograph, document excerpt, or demonstration.
- Relationship-led: a diagram justified by the Diagram-Level Scope.
- Deliberate pause: one phrase, one cue, or no additional content.

### 3. Write Each Slide Entry

Follow the plan template. Format presenter notes first, then derive on-slide copy through the Copy-Level Scope. Use direct alignment unless the script deliberately previews the next visible idea; record bridge alignment in `Slide Treatment`.

Specify only required production elements:

- For text-led slides, write `None — text-led slide` for required visual or evidence.
- For code, include the exact code or source range and highlighted lines.
- For diagrams, state the relationship clarified, why text is insufficient, and every node, relation, label, reveal stage, and continuity requirement.
- For imagery, state the subject, composition, aspect ratio, placement, and narrative purpose.
- For unresolved required elements, add an actionable `Placeholder:` blockquote. Add no placeholder when no visual is required.

### 4. Audit the Plan

Map all material source content to slides. Record deferred or omitted material with reasons; never condense it silently in presenter notes. Keep optional detail in an appendix only when the audience or format benefits. Check pacing, speaking time by slide and section when duration is known, source fidelity, next-thought previews, visual necessity, transition meaning, and runs of identical silhouettes. Treat slide count as an outcome of narrative and pacing, not a target.

### 5. Deliver

Use the linked template. Mark the plan `Draft for Review` until the user approves it, then `Approved for Creation`. Do not generate slides during planning.

## Required References

Before writing the final plan, [read the complete presentation-plan template](references/presentation-plan-template.md). Follow its required headings and field guidance. Use ordinary Markdown headings, paragraphs, lists, blockquotes, and fenced code, and keep the plan readable by a human.

Before planning, read every example below. Treat the XML tags as prompt delimiters, not plan output. Apply the transformations without changing the words inside each selected span.

- [Review how semantic dependency determines whether sentences become siblings or children](references/main-idea-with-supporting-sentences-example.md).
- When prose introduces a list or the source already contains nested items, [preserve the demonstrated list depth](references/colon-ended-setup-example.md).
- When a candidate slide contains several source paragraphs or independent ideas, [choose between one cumulative beat and a slide split as demonstrated](references/multiple-source-paragraphs-example.md).
- When an on-slide phrase begins with a lowercase technical identifier, [preserve its casing as demonstrated](references/lowercase-technical-identifier-example.md).
- When pacing divides a semantic group across slides, [retain the continuation's original indentation](references/cross-slide-continuation-example.md).
- When a long sentence contains a deliverable dependent clause, [split it without losing its punctuation or lowercase continuation](references/dependent-clause-split-example.md).
- When one slide hands off to the next, [copy the next spoken thought into the outgoing notes as a parenthesized preview](references/next-slide-preview-example.md).

## Acceptance Checklist

Before delivery, verify:

- One coherent audience outcome, cumulative arc, and necessary narrative job per slide.
- Complete source coverage, with every omission, deferral, verification need, assumption, and high-impact decision visible.
- Faithful semantic presenter-note structure, including list depth, clause splits, cross-slide indentation, delivery cues, and next-thought previews.
- Pacing status and counts for every substantive note body, with exceptions justified.
- Source-derived, naturally cased on-slide copy with correct hierarchy and emphasis markers.
- The simplest fitting slide form, no filler visuals or repeated diagram motif, and actionable directions for every required asset.
- Meaningful transitions and manual timing unless the source requires otherwise.
- A complete template contract that another creator can execute without the original source.

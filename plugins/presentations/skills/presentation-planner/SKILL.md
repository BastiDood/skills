---
name: presentation-planner
description: Turn source material into an implementation-ready Markdown presentation plan. Use when planning slide narrative, notes, visuals, and transitions without creating the deck.
---

# Basti Presentation Planner

Produce a tactical Markdown production script that a separate creator can execute without rereading or reinterpreting the source. Keep presenter notes complete and scripted while treating each slide as selective visual support for one audience task. A slide may support several chained spoken ideas when they share a visual state or delivery moment.

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
- Cluster adjacent source ideas before splitting them. Create a new slide when the audience task, evidence, emotional register, interaction, or visual state changes—not because a paragraph or sentence ends.
- Track every material source idea, but do not give every paragraph visible copy or its own slide. Details may live only in presenter notes when they support the same visual beat.
- Open with context, stakes, tension, or a question. Close by resolving it with a useful takeaway or action, not a generic thank-you.

### Slide-Level Scope

- Give each slide one narrative job or coherent idea cluster.
- Do not equate one narrative job with one source paragraph, one presenter-note group, or one visible parent bullet. Group several related claims, examples, consequences, or steps when the audience can hold them under one visual frame.
- Choose the simplest legible form. Text-only, bullet-only, evidence-only, diagram-only, and nearly empty slides are complete when they serve the beat.
- Prefer one coherent composition over card grids, panels, badges, or decorative UI.
- Split competing audience tasks or incompatible visual states across slides. Prefer another slide over smaller type or genuinely dense copy, but do not split merely to make each source paragraph visible.
- Let code, a diagram, screenshot, photograph, quotation, or other direct evidence dominate when it carries the beat; add only essential labels and follow the stricter quotation rule below.

### Diagram-Level Scope

- Use a diagram only when an audience-critical relationship, sequence, structure, flow, cause, comparison, or change is clearer visually than through direct text or evidence.
- Do not turn concepts into boxes and arrows merely because possible. Avoid filler mini-diagrams and repeated prose-left, framed-diagram-right compositions.

### Copy-Level Scope

- Treat visible copy as editorial compression, not a transcript. Preserve the source's meaning and terminology, but shorten, combine, and faithfully paraphrase prose into audience-facing cues. Keep quotation wording and technical literals exact.
- Prefer phrases and short clauses over complete sentences. Target 2–8 words per cue and treat 12 words as a review ceiling. Exceed it only when exact wording is the beat, as with a quotation, code, formal definition, or deliberate standalone assertion.
- Make every cue a grammatically complete label, noun phrase, verb phrase, short clause, question, or assertion that carries a whole audience-facing concept. Meaning outranks the word target; never use the first _n_ words of a sentence as a cue.
- Do not fake compression by clipping a source sentence and appending `...`. Rewrite it as a complete phrase or short clause. Reserve ellipses for exact quotations, source-authored punctuation, and presenter-note previews.
- Let the source logic determine the hierarchy. Use sibling top-level cues for parallel ideas and children only for genuine support, dependency, or list depth. Do not default to one main bullet with every other idea nested beneath it.
- Use one dominant line, several sibling cues, a compact hierarchy, or no bullets at all according to the beat. Omit explanatory detail that the presenter notes already carry unless the audience needs it to follow the visual.
- Use `### Slide N — <Title>` as the slide title. Do not repeat it under `On-Slide Content` or add a `Title:` label. Treat the heading as visible by default; quotation-only and deliberately titleless treatments use it only as the plan identifier.
- Treat a standalone source quotation or Markdown blockquote as a quotation beat unless the user explicitly asks to integrate it into another visual.
- For a quotation beat, make `On-Slide Content` the exact quotation alone as plain or italic text. Do not add `Title:`, a heading, bullet marker, preface, interpretation, or decorative copy. Put the setup, interpretation, and spoken attribution in presenter notes; add visible attribution only when the user or source requires it.
- Avoid slogans, marketing taglines, and summaries the speaker cannot locate in the notes.
- Use natural sentence case for body copy and title case for titles. Preserve source casing for acronyms, names, filenames, commands, paths, identifiers, and quotations; never normalize a literal such as `tokio::spawn`.
- Use plain ASCII punctuation in Markdown prose and transcribed source text: `'` for apostrophes, `"` for double quotes, and `...` for ellipses. Do not use typographic quotation marks or the single-character ellipsis.
- Mark one to three load-bearing spans across a compact text group with `**...**` or `*...*` for bold cyan emphasis. Do not emphasize every line.
- Write titles as speakable claims or questions. Use **Title Case without Prepositions and Articles**, while preserving product, acronym, and identifier casing.

## Semantically Structured Presenter Notes

Use the assigned source passage for every substantive slide. Preserve its meaning, words, order, casing, punctuation at split boundaries, terminology, humor, candor, and technical precision, except for the required ASCII punctuation normalization. Do not summarize, polish, correct, embellish, or synthesize spoken content in presenter notes. This verbatim requirement does not apply to compressed on-slide copy.

Format the passage as a semantic Markdown hierarchy:

- Use level 0 for each independent claim, transition, question, setup, conclusion, or observation. Do not make the first sentence a parent merely because it comes first.
- Nest only supporting, explanatory, qualifying, exemplary, enumerative, completing, or answering spans. Keep independent sentences as siblings, even within one paragraph.
- Use one sentence per bullet unless tightly coupled sentences lose meaning when separated.
- Split long sentences at a colon, semicolon, dash, or strong comma. Make dependent continuations children and independent continuations siblings; preserve the words, order, and boundary punctuation.
- Preserve source-authored list depth. Indent introduced lists once and their nested items again; use levels 0–2 unless the source requires more.
- Allow several level-0 groups on one slide when they form one deliverable beat. Treat list markers and indentation as non-spoken structure, and use the pacing diagnostics below without forcing a split.

Presenter notes may contain several paragraphs, level-0 groups, and supporting ideas that have no separate visible counterpart. Use `[Read slide.]` in either of two ways:

- As a substitution cue when an exact quotation, code excerpt, or equivalent source text appears as the dominant content on the same slide. Omit that exact span from the notes and map its coverage to the visible content.
- As a delivery cue after the complete spoken notes when the audience should scan relevant compressed cues or evidence already visible on that slide. This does not replace presenter-note coverage.

Never use `[Read slide.]` when the slide lacks relevant audience-readable content.

Prefer slide boundaries between complete level-0 groups. When pacing or visual continuity requires a split inside a group, retain the continuing bullets' indentation on the next slide, even when that slide begins at level 1 or 2. Do not repeat or invent a parent solely to make the slide-local list self-contained.

After allocating the spoken notes, add a next-thought preview to each applicable non-final slide:

- Copy the first spoken bullet of the next slide, or its first natural clause when the bullet is long.
- Stop after that clause or sentence; never copy several sentences or an entire next-slide note group into the preview.
- Preserve its words, casing, and punctuation; append an ellipsis, wrap it in parentheses, and place it as the final level-0 bullet: `- (The next thought begins here...)`. Add a leading ellipsis when copying a mid-sentence continuation: `- (... then the consequence follows...)`.
- Treat the preview as non-spoken. Exclude it from the outgoing slide's counts and coverage; assign the source only to the incoming slide.
- Omit it when no spoken thought follows.

Preserve source-authored parentheses around spoken asides, pauses, and questions; these remain spoken content and are not next-thought previews.

Use square brackets only for terse, non-spoken delivery cues such as `[Read slide.]`, `[Pause for questions.]`, or `[Advance.]`. Add a cue only when the slide interaction requires it.

## Presenter Note Pacing

Use counts as pacing diagnostics, not automatic slide boundaries. Apply the stricter category reached by word or character count:

- **Normal:** at most 90 words and 525 characters.
- **Dense:** 91–155 words or 526–900 characters; review the delivery, but retain the cluster when it has one audience task and visual state.
- **Very dense:** above 155 words or 900 characters; look for a meaningful narrative or visual break, but do not split solely to satisfy the count.
- **Explicit exception:** above 200 words or 1,200 characters requires a rationale in `Production Notes`.

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

### 2. Cluster and Segment the Beats

Group successive semantic note groups that share an audience task and visual state. Then create a new beat when the audience task, governing frame, evidence, emotional register, demonstration, interaction, or need for a pause changes. Treat paragraphs and headings as source navigation, not default slide markers. Use the pacing diagnostics only after clustering.

Before segmenting the source, read every example below in full; do not skip examples based on apparent relevance. Treat the XML tags as prompt delimiters, not plan output. Preserve presenter-note wording while applying the demonstrated editorial compression to visible copy.

- [Separate complete semantic notes from compact, sibling visual cues](./references/main-idea-with-supporting-sentences-example.md).
- When prose introduces a list or the source already contains nested items, [preserve the demonstrated list depth](./references/colon-ended-setup-example.md).
- When a candidate slide contains several source paragraphs or independent ideas, [cluster them by audience task before considering a split](./references/multiple-source-paragraphs-example.md).
- When an on-slide phrase begins with a lowercase technical identifier, [preserve its casing as demonstrated](./references/lowercase-technical-identifier-example.md).
- When pacing divides a semantic group across slides, [retain the continuation's original indentation](./references/cross-slide-continuation-example.md).
- When a long sentence contains a deliverable dependent clause, [split it without losing its punctuation or lowercase continuation](./references/dependent-clause-split-example.md).
- When one slide hands off to the next, [copy the next spoken thought into the outgoing notes as a parenthesized preview](./references/next-slide-preview-example.md).
- To see how a verbose script becomes a compact slide without losing coverage, [compare the clustered visual cues against the full presenter notes](./references/speaker-led-compression-example.md).
- For direct quotations, [keep the canvas blank except for the quotation and move its setup into presenter notes](./references/quotation-slide-example.md).
- For ordinary text-led slides, [omit inapplicable production fields instead of narrating their absence](./references/sparse-plan-entry-example.md).

Assign the simplest fitting form:

- Text-led: title, section marker, question, assertion, concise bullets, quotation, recap, call to action, or credits.
- Evidence-led: code, screenshot, chart, table, photograph, document excerpt, or demonstration.
- Relationship-led: a diagram justified by the Diagram-Level Scope.
- Deliberate pause: one phrase, one cue, or no additional content.

### 3. Write Each Slide Entry

Follow the plan template. Format presenter notes first, then derive a selective visual abstraction through the Copy-Level Scope. Do not mirror the note hierarchy or force every note group onto the slide. Use direct alignment unless the script deliberately previews the next visible idea; record bridge alignment in `Slide Treatment`.

Before writing the plan, [read the complete presentation-plan template](./references/presentation-plan-template.md). Follow its required headings and field guidance. Use ordinary Markdown headings, paragraphs, lists, blockquotes, and fenced code, and keep the plan readable by a human.

Include only applicable production elements. Omit fields and subsections that would otherwise say `None`, `N/A`, or restate why an element is unnecessary.

- For code, include the exact code or source range and highlighted lines.
- For diagrams, state the relationship clarified, why text is insufficient, and every node, relation, label, reveal stage, and continuity requirement.
- For imagery, state the subject, composition, aspect ratio, placement, and narrative purpose.
- For unresolved required elements, add an actionable `Placeholder:` blockquote. Add no placeholder when no visual is required.

Before accepting each `On-Slide Content` block:

1. Confirm every cue expresses a complete audience-facing concept. Reject prefix fragments created by taking the first _n_ words of a note sentence.
2. Confirm the block does not repeat the slide heading or contain a `Title:` label.
3. Compare every cue with the presenter notes. Rewrite sentence mirrors as shorter phrases or clauses.
4. Remove any detail the presenter can supply aloud without impairing the audience's understanding.
5. Review every non-quotation, non-code cue above 12 words and every cue ending in a period. Keep it only when the exact sentence is intentionally the visual beat.
6. Reject clipped sentences ending in added ellipses.
7. Confirm parallel ideas are siblings instead of children under an invented parent.
8. For every `[Read slide.]` cue, verify that relevant readable content appears on that same slide. If the cue substitutes for omitted source wording, verify that the exact source text appears.

### 4. Audit the Plan

Map every spoken source span to presenter notes, except exact source spans intentionally replaced by `[Read slide.]`, such as quotations, code, or document excerpts. Compressed on-slide copy and visual evidence never substitute for scripted notes. Map non-spoken source material to visible evidence, the appendix, a production instruction, or an explicit omission. Record deferred or omitted material with reasons; never condense it silently in presenter notes. Keep optional detail in an appendix only when the audience or format benefits. Check pacing, speaking time by slide and section when duration is known, source fidelity, next-thought previews, visual necessity, transition meaning, and runs of identical silhouettes. Treat slide count as an outcome of narrative and pacing, not a target.

Reject mechanical symptoms before delivery: one slide per paragraph, sentence-heavy visible bullets, incomplete sentence-prefix cues, clipped sentences with added ellipses, most bullet slides using the same one-parent hierarchy, presenter notes mirrored onto the canvas, every source detail receiving visible copy, quotation slides with visible setup text, `[Read slide.]` without relevant same-slide content, source wording omitted behind a read cue without exact visible text, repeated boilerplate production rationales, or repeated `None` and `N/A` commentary.

### 5. Deliver

Use the linked template. Mark the plan `Draft for Review` until the user approves it, then `Approved for Creation`. Do not generate slides during planning.

## Acceptance Checklist

Before delivery, verify:

- One coherent audience outcome, cumulative arc, and necessary narrative job per slide.
- Complete scripted coverage in presenter notes, except exact source spans assigned to `[Read slide.]`; visible compression never replaces spoken notes, and no paragraph requires its own visible copy.
- Faithful semantic presenter-note structure, including list depth, clause splits, cross-slide indentation, delivery cues, and next-thought previews.
- Pacing status and counts for every substantive note body, with only true exceptions justified.
- Compact, source-grounded on-slide copy that expresses complete concepts without repeating the slide heading, adding a `Title:` label, mirroring notes, slicing sentence prefixes, overusing full sentences, clipping with added ellipses, or forcing a one-parent hierarchy; every non-exempt cue above 12 words has been rewritten or deliberately justified.
- The simplest fitting slide form, no filler visuals or repeated diagram motif, and actionable directions for every required asset.
- Quote-only treatment for quotation beats unless the user or source requires additional visible context; the `On-Slide Content` block contains no title line, bullet marker, or setup copy.
- Every `[Read slide.]` cue has relevant content to read on that same slide; any source wording omitted from notes because of the cue appears there exactly.
- No empty optional fields, `None`, `N/A`, or absence justifications.
- Meaningful transitions and manual timing unless the source requires otherwise.
- A complete template contract that another creator can execute without the original source.

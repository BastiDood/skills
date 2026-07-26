---
name: presentation-planner
description: Turn a long talk script, transcript, outline, or source document into a reviewable, implementation-ready Markdown presentation plan. Use when Codex needs to divide a speaker's material into consistently paced slides, format source passages as semantically nested presenter-note bullets, extract naturally cased slide cues from those passages, mark emphasized keywords, choose an appropriate slide form for each spoken beat, specify only the visuals and builds the content requires, plan transitions, and produce a standalone plan without creating a slide deck.
---

# Basti Presentation Planner

## Objective

Produce a tactical Markdown production script for a presentation. Make the plan concrete enough that a separate creator can build the deck without rereading or reinterpreting the source script.

Treat the slide as a visual aid for a spoken beat, not as a document. Let the audience look at the slide to understand what the speaker is discussing and listen to the speaker to understand what it means.

Treat text, code, direct evidence, imagery, whitespace, and diagrams as different presentation materials. A diagram is optional, not a completion criterion.

## Boundaries

- Create or revise only the Markdown presentation plan requested by the user.
- Do not create, edit, render, or inspect a PowerPoint or Google Slides deck.
- Do not request or inspect reference decks. Derive the plan from the source script and the house rules in this skill.
- Do not source or generate images. Write actionable production directions or placeholders instead.
- Do not choose exact coordinates or implement template internals. Describe visual hierarchy, composition, continuity, and motion at the level needed by a creator.
- Do not silently invent facts, quotations, examples, or claims. Preserve the source, mark verification needs, and surface consequential gaps.

## Inputs

Require the full source script or equivalent source material. Accept optional audience, duration, venue, format, objective, live-demo requirements, mandatory content, and exclusions.

Treat existing slide markers in the script as hints rather than immutable boundaries. Re-segment them when the narrative or audience-processing load demands it.

Infer missing details when the source makes them clear. Ask only about ambiguity that materially changes the thesis, audience outcome, duration, required content, or delivery format. State harmless assumptions in the plan.

## House Principles

### Deck-Level Scope

- Let the content determine the mix of slide forms. Do not set or infer a quota for diagrams, images, charts, screenshots, builds, or transitions.
- Make the sequence cumulative. Let each slide answer a question raised by the previous slide or create the question answered by the next.
- Open with context, stakes, tension, or a question. Close by resolving the opening and stating the useful takeaway or action; do not rely on a generic thank-you as the conclusion.

### Slide-Level Scope

- Give each slide one narrative job and one primary claim.
- Choose the simplest slide form that makes the beat legible. A text-led assertion, question, concise bullet hierarchy, quotation, or nearly empty beat is complete without a visual asset.
- Prefer one coherent composition over grids of cards, panels, badges, or decorative UI.
- Keep visible copy concise enough to understand at a glance.
- Split the slide when the assigned passage contains more than one independent key idea instead of creating several competing main bullets.
- Let complete code, a full diagram, a screenshot, a photograph, or a quotation dominate the slide when it is the evidence under discussion. Add no competing prose beyond an essential title or labels.
- Allow text-only, bullet-only, image-only, code-only, diagram-only, and nearly empty slides when they create a deliberate beat.
- Prefer another slide over smaller text or denser copy.

### Diagram-Level Scope

- Use a diagram only when relationships, sequence, structure, flow, causality, comparison, or change are central and the diagram improves understanding over direct text or evidence. Do not diagram a concept merely because it can be converted into boxes and arrows.
- Never default to a repeated prose-left, framed-diagram-right composition. Do not invent decorative mini-diagrams to fill whitespace or satisfy the plan contract.

### Copy-Level Scope

- For bullet-driven slides, use a title or claim plus one main bullet and zero to four supporting sub-bullets as the default, not a quota. Use no bullets when code, a diagram, an image, or another primary visual carries the beat.
- Derive titles and bullets from the exact vocabulary of the assigned source passage. Avoid synthesized slogans, marketing-style taglines, or abstract summaries that the speaker cannot locate in the notes at a glance.
- For every bullet-driven slide, use one main bullet containing the verbatim key idea. Place verbatim supporting phrases beneath it as indented sub-bullets.
- Keep the main bullet and its sub-bullets in source order. Let the hierarchy cue the corresponding progression through the presenter notes.
- Use natural sentence case for audience-facing prose bullets and sub-bullets. Start them with a capitalized source word whenever the script provides one. Reserve title case for titles.
- Never mechanically lowercase or title-case on-slide body copy. Preserve the source capitalization of acronyms, proper nouns, product names, file names, commands, paths, handles, code identifiers, and quoted wording.
- Prefer a self-contained source phrase that begins with the intended capitalization. If the only faithful opening token is a lowercase literal such as `tokio::spawn`, preserve it rather than changing the source.
- Wrap load-bearing keywords in `**bold markers**` or `*italic markers*`. Use one to three emphasized spans per sentence-sized bullet. Treat these markers as instructions for bold cyan emphasis during slide creation.
- Write audience-facing titles as natural claims or questions a presenter could plausibly say aloud.
- Use **Title Case without Prepositions and Articles** for every presentation title, section title, and slide title. Keep articles such as `a`, `an`, and `the` and prepositions such as `of`, `in`, `for`, `with`, and `to` lowercase unless they are the first or last word. Preserve the intended capitalization of acronyms, product names, and code identifiers.

## Semantically Structured Presenter Notes

Use the assigned source passage as the presenter notes for every substantive slide. Preserve its meaning, vocabulary, source order, capitalization, terminology, humor, candor, and technical precision. Reuse source sentences directly by default.

Do not summarize, polish, correct, embellish, or synthesize new spoken content. Structural editing is permitted: split prose into bullets, divide a long sentence at a natural clause boundary, retain source list depth, and mark a source-authored handoff. Preserve all words and their order within each resulting span. Keep the source punctuation at a split boundary.

Format the passage as a semantic Markdown bullet hierarchy rather than a prose block:

- Use a level-0 bullet for each new spoken move: a claim, transition, question, setup, conclusion, or independent observation.
- Nest a span only when it supports, explains, qualifies, exemplifies, enumerates, completes, or answers its parent.
- Keep independent sentences as level-0 siblings even when they came from the same source paragraph. Do not make the first sentence a parent merely because it came first.
- Allow several level-0 groups on one slide when they perform one cumulative beat and fit the pacing limits. Split them when they create separate audience tasks or exceed those limits.
- Use one sentence per bullet by default. Keep tightly coupled sentences together when separating them would obscure their relationship.
- Divide a long sentence at a colon, semicolon, dash, or strong comma boundary when the parts are easier to deliver separately. Make a dependent continuation a child; make an independent continuation a sibling.
- Preserve the structure of source-authored lists. Put a list introduced by prose one level below its setup and preserve nested list items one further level down. Use levels 0–2 as the normal maximum; go deeper only when the source itself requires it.
- Treat Markdown list markers and indentation as structure only; they are not spoken additions.

Prefer slide boundaries between complete level-0 groups. When pacing or visual continuity requires a split inside a group, retain the continuing bullets' indentation on the next slide, even when that slide begins at level 1 or 2. Do not repeat or invent a parent solely to make the slide-local list self-contained.

After allocating the spoken notes, add a next-thought preview to each applicable non-final slide:

- Copy the first spoken bullet of the next slide, or its first natural clause when the bullet is long.
- Append an ellipsis, wrap the copied span in parentheses, and place it as the final level-0 bullet of the outgoing slide: `- (The next thought begins here…)`.
- Preserve the copied words, capitalization, and source punctuation. When the copied span begins mid-sentence, use a leading ellipsis as well: `- (… then the consequence follows…)`.
- Treat the preview as a non-spoken presenter cue. Speak the source span only when it appears normally on the next slide.
- Exclude the preview copy from the outgoing slide's word count, character count, and source coverage. Map the source span only to the incoming slide.
- Omit the preview when there is no next spoken thought, including the final slide.

Preserve source-authored parentheses around spoken asides, pauses, and questions; these remain spoken content and are not next-thought previews.

Use square brackets only for terse, non-spoken delivery cues such as `[Read slide.]`, `[Pause for questions.]`, or `[Advance.]`. Add a cue only when the slide interaction requires it.

## Presenter Note Pacing

Use the following distribution of substantive presenter-note bodies as the pacing baseline:

| Percentile |  Words | Characters |
| ---------- | -----: | ---------: |
| p50        |     48 |        288 |
| p75        |   87.5 |      513.5 |
| p90        |    122 |        748 |
| p95        | 154.45 |     878.15 |

Apply the stricter category reached by either word count or normalized character count:

- Target at most 90 words and 525 characters per slide. Treat this as the normal soft limit.
- Review notes containing 91–122 words or 526–750 characters for an additional slide boundary.
- Split notes above 122 words or 750 characters by default.
- Allow notes above 155 words or 900 characters only as an explicit exception with a rationale in `Production Notes`.
- Preserve a single visual across successive slides when the script must be split but the visual context should remain stable.

Use these counts as a practical proxy for avoiding a presenter-note scrollbar. The application viewport can vary, but the limits preserve the observed pacing distribution.

Count only the spoken source text assigned to the slide. Exclude Markdown list markers, indentation, parenthesized next-thought previews copied from the next slide, square-bracket delivery cues, and other structural notation.

## Transition Grammar

Choose motion only when it communicates the relationship between slides:

- Use a cut as the quiet default.
- Use a build when the audience must understand elements in dependency order.
- Use Morph when the same idea, code sample, or diagram evolves.
- Use Push for a sustained catalogue, gallery, or lateral sequence.
- Use Fade for a section boundary, reflective beat, emotional reset, or comedic release.
- Use a wipe or another directional transition only when the direction itself communicates a deliberate handoff.
- Use successive near-duplicate slides when controlled emphasis explains code or a diagram more clearly than an internal animation.
- Keep advance manual unless the source explicitly requires timed playback.

Never prescribe motion merely for variety.

## Workflow

### 1. Define the Communication Job

Read the complete source before allocating slides. Express the job in one sentence:

> By the end, **[audience]** should **[outcome]** because **[central takeaway]**.

Identify the essential claims, examples, demonstrations, evidence, and emotional or comedic beats required to achieve that outcome.

### 2. Build the Narrative Arc

Choose an arc appropriate to the material, such as question to analysis to answer, problem to mechanism to application, context to stakes to evidence to implications, learning progression, chronology, or story to lesson.

Describe the opening, development, pivot, resolution, and final audience state. Treat an agenda as navigation, not as the narrative itself.

### 3. Segment the Spoken Beats

Divide the script by changes in audience task, not by paragraph length. Create a new beat when the speaker asks a new question, introduces a new claim, changes evidence, shifts emotional register, begins a demonstration, or needs a pause.

Assign each beat the simplest fitting slide form:

- Text-led: title, section marker, question, assertion, concise bullets, quotation, recap, call to action, or credits.
- Evidence-led: code, screenshot, chart, table, photograph, document excerpt, or demonstration.
- Relationship-led: a diagram, only when the audience must understand relationships, sequence, structure, flow, causality, comparison, or change.
- Deliberate pause: a nearly empty slide that holds one phrase, one cue, or no additional content.

Do not use `diagram` as a generic synonym for `visual`. Do not repeat one diagram template across unrelated beats.

Measure the spoken passage assigned to each slide. Add a slide boundary when the passage crosses the pacing limits, even when the narrative job or visual remains the same. Preserve the visual through a build, Morph, or near-duplicate slide when needed.

### 4. Write the Slide Production Script

Format the assigned presenter-note passage as semantically nested note bullets. Then extract the on-slide title and bullets from that same passage. Preserve the script's vocabulary, capitalization, and sequence; do not replace it with a synthesized takeaway or tagline.

Use direct alignment by default: each visible bullet cues the corresponding current portion of the presenter notes. Use bridge alignment only when the script itself deliberately introduces the next visible idea; identify that choice explicitly in `Slide Treatment`.

For a bullet-driven slide, select one short contiguous source phrase as the main bullet's key idea. Select short contiguous source phrases that support, explain, qualify, or enumerate that idea as sub-bullets. Keep every level in source order and preserve the words within each selected span.

Select naturally cased source spans for on-slide bullets. Start prose bullets and sub-bullets with a capitalized source word, but preserve lowercase technical identifiers and other literals exactly. Never convert the body hierarchy into all-lowercase placeholders or title case.

Mark one to three load-bearing terms per sentence-sized bullet with `**...**` or `*...*`. Do not emphasize decorative fragments or rewrite the source to create emphasis.

Describe only the elements required by the selected slide form, including what appears first, what changes, what remains, and what disappears. Do not manufacture elements merely to populate the plan contract.

For a text-led slide, write `None — text-led slide` for required visual or evidence and omit visual placeholders. Let typography, placement, emphasis, and whitespace carry the beat.

For code slides, include the exact code or identify the exact source range and the intended highlighted lines. For diagrams, state the audience relationship the diagram clarifies and why direct text or evidence is insufficient; then name every node, relationship, label, reveal stage, and continuity requirement. For imagery, write a concrete image brief with subject, composition, aspect ratio, placement, and narrative purpose.

If a required visual cannot yet be specified completely, add a blockquote beginning with `Placeholder:` and describe the ideal graphic, animation, or transition in actionable language. Do not add a placeholder to a slide that does not need a visual.

### 5. Preserve Source Coverage

Map every material source section to planned slides. Consolidate repetition, but do not silently drop unique reasoning, evidence, caveats, examples, demonstrations, or conclusions.

Do not condense source passages inside presenter notes. Allocate them faithfully, defer them, or omit them explicitly. Record deferred and intentionally omitted material with the reason. Keep optional detail in an appendix only when the audience or delivery format benefits from it.

### 6. Review Pacing

Record word count, character count, and pacing status for every substantive note body. Split above the default threshold and justify every retained exception. Estimate speaking time by slide and by section when duration is known. Use more slides for progressive technical explanations and fewer for simple statements. Treat slide count as a consequence of narrative pacing, not a target.

Check for long runs of identical silhouettes. Vary the rhythm through content form, not decoration or interchangeable diagram motifs.

### 7. Deliver the Markdown Plan

Write the plan using the contract below. Mark it `Draft for Review` until the user explicitly approves it, then revise the status to `Approved for Creation`. Do not generate slides during planning.

## Markdown Plan Contract

Before writing the final plan, [read the complete presentation-plan template](references/presentation-plan-template.md). Follow its required headings and field guidance. Use ordinary Markdown headings, paragraphs, lists, blockquotes, and fenced code, and keep the plan readable by a human.

## Few-Shot Examples

Before producing any plan, read every example below. Treat the XML tags as prompt delimiters, not as presentation-plan output. Apply the demonstrated transformations to the user's actual script without changing the words inside each selected span.

- [Review how semantic dependency determines whether sentences become siblings or children](references/main-idea-with-supporting-sentences-example.md).
- When prose introduces a list or the source already contains nested items, [preserve the demonstrated list depth](references/colon-ended-setup-example.md).
- When a candidate slide contains several source paragraphs or independent ideas, [choose between one cumulative beat and a slide split as demonstrated](references/multiple-source-paragraphs-example.md).
- When an on-slide phrase begins with a lowercase technical identifier, [preserve its casing as demonstrated](references/lowercase-technical-identifier-example.md).
- When pacing divides a semantic group across slides, [retain the continuation's original indentation](references/cross-slide-continuation-example.md).
- When a long sentence contains a deliverable dependent clause, [split it without losing its punctuation or lowercase continuation](references/dependent-clause-split-example.md).
- When one slide hands off to the next, [copy the next spoken thought into the outgoing notes as a parenthesized preview](references/next-slide-preview-example.md).

## Acceptance Checklist

Before delivery, verify all of the following:

- The plan communicates one coherent audience outcome.
- Every slide has one necessary narrative job.
- Every spoken presenter-note span comes from a documented source span.
- No presenter-note text is summarized, polished, reordered, corrected, embellished, or synthesized.
- Presenter notes use Markdown list hierarchy, not prose paragraphs.
- Presenter-note nesting expresses semantic dependency rather than paragraph position.
- Independent spoken moves are level-0 siblings; supporting, explanatory, qualifying, exemplary, enumerative, completing, or answering spans are children.
- Source-authored list depth is preserved, normally within levels 0–2.
- Clause splits preserve the source words, punctuation at the boundary, capitalization, and order.
- A cross-slide continuation retains its prior indentation without an invented or repeated parent.
- Every applicable non-final slide ends with one parenthesized, ellipsis-marked preview copied from the next slide's first spoken bullet or clause.
- Parenthesized next-thought previews are non-spoken and excluded from outgoing pacing and coverage; source-authored parenthetical text remains spoken.
- Square-bracket delivery cues remain terse and non-spoken.
- On-slide titles and bullets reuse the assigned script's exact vocabulary.
- On-slide prose bullets use natural sentence case and are never mechanically lowercased or title-cased.
- Lowercase technical identifiers and other literal tokens preserve their source casing.
- Every bullet-driven slide has one source-derived main bullet containing the key idea.
- Supporting sub-bullets remain subordinate to the main point and keep source order.
- Bullet hierarchy follows the corresponding presenter-note passage and acts as a glanceable speaking cue.
- Load-bearing bullet keywords use `**...**` or `*...*` emphasis markers.
- Notes stay within 90 words and 525 characters by default; longer notes follow the review, split, and exception rules.
- Presentation, section, and slide titles follow Title Case without Prepositions and Articles.
- Every slide uses the simplest fitting lead format, and no slide includes a diagram by default.
- Every diagram clarifies an audience-critical relationship, sequence, structure, flow, causality, comparison, or change that direct text or evidence would communicate less clearly.
- Text-led and nearly empty slides contain no filler asset, decorative mini-diagram, or unnecessary visual placeholder.
- The deck does not repeat one diagram silhouette across unrelated beats.
- Code, direct evidence, and diagrams receive the full emphasis they require when they carry the beat.
- Every transition preserves the intended spoken sequence and identifies the next thought without inventing new language.
- Motion describes meaning rather than decoration.
- Every visual placeholder is required and actionable; slides that need no visual contain no placeholder.
- All material source content is accounted for.
- A separate slide-production workflow can execute the plan without reading the original script.
- All high-impact open decisions are visible.

---
name: presentation-planner
description: Turn a long talk script, transcript, outline, or source document into a reviewable, implementation-ready Markdown presentation plan. Use when Codex needs to divide a speaker's material into consistently paced slides, format verbatim source passages as structured presenter-note bullets, extract naturally cased slide cues from those passages, mark emphasized keywords, choose an appropriate slide form for each spoken beat, specify only the visuals and builds the content requires, plan transitions, and produce a standalone plan without creating a slide deck.
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

## Structured Verbatim Presenter Notes

Copy the assigned source passage verbatim into the presenter notes for every substantive slide. Preserve the original wording, sentence order, capitalization, grammar, punctuation, terminology, humor, candor, repetition, and level of technical precision.

Do not paraphrase, summarize, polish, correct, reorder, or synthesize presenter-note text. Do not add transitions, jokes, examples, caveats, delivery language, or new ideas. Preserve audience prompts, live-demo cues, pauses, and delivery cues only when they already exist in the source.

Format the verbatim passage as a Markdown bullet hierarchy rather than a prose block:

- Treat each ordinary source paragraph as one note group.
- Use the paragraph's key idea, topic sentence, transition, question, or colon-ended setup as the level-0 main bullet.
- Put its supporting sentences beneath it as level-1 sub-bullets in source order.
- Use one sentence per bullet by default.
- Keep two tightly coupled sentences in one bullet when separating them would remove necessary context, especially when the second sentence introduces a list.
- A sentence may be divided at a natural clause ending in a colon. Keep both resulting spans verbatim and place the introduced material beneath the colon-ended setup.
- Preserve paragraph boundaries as separate level-0 note groups. Unlike an on-slide bullet hierarchy, one slide's presenter notes may contain multiple main-bullet groups.
- If one source paragraph contains independent key ideas that would compete as main bullets, prefer an additional slide boundary instead of flattening the paragraph into a long note block.
- Treat Markdown list markers and indentation as structure only; they are not additions to the spoken script.

Split the source across slides only at natural sentence, paragraph, or section boundaries. Preserve source order across slides and avoid duplicating passages. Use a source-authored closing sentence or question as the spoken bridge when available; otherwise record that the source contains no spoken bridge.

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

Count only the verbatim speaker text. Exclude Markdown list markers, indentation, and other structural notation.

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

Measure the verbatim passage assigned to each slide. Add a slide boundary when the passage crosses the pacing limits, even when the narrative job or visual remains the same. Preserve the visual through a build, Morph, or near-duplicate slide when needed.

### 4. Write the Slide Production Script

Copy the assigned presenter-note passage verbatim and format it as structured note bullets. Then extract the on-slide title and bullets from that same passage. Preserve the script's vocabulary, capitalization, and sequence; do not replace it with a synthesized takeaway or tagline.

Use direct alignment by default: each visible bullet cues the corresponding current portion of the presenter notes. Use bridge alignment only when the script itself deliberately introduces the next visible idea; identify that choice explicitly in `Slide Treatment`.

For a bullet-driven slide, select one short contiguous source phrase as the main bullet's key idea. Select short contiguous source phrases that support, explain, qualify, or enumerate that idea as sub-bullets. Keep every level verbatim and in source order.

Select naturally cased source spans for on-slide bullets. Start prose bullets and sub-bullets with a capitalized source word, but preserve lowercase technical identifiers and other literals exactly. Never convert the body hierarchy into all-lowercase placeholders or title case.

Mark one to three load-bearing terms per sentence-sized bullet with `**...**` or `*...*`. Do not emphasize decorative fragments or rewrite the source to create emphasis.

Describe only the elements required by the selected slide form, including what appears first, what changes, what remains, and what disappears. Do not manufacture elements merely to populate the plan contract.

For a text-led slide, write `None — text-led slide` for required visual or evidence and omit visual placeholders. Let typography, placement, emphasis, and whitespace carry the beat.

For code slides, include the exact code or identify the exact source range and the intended highlighted lines. For diagrams, state the audience relationship the diagram clarifies and why direct text or evidence is insufficient; then name every node, relationship, label, reveal stage, and continuity requirement. For imagery, write a concrete image brief with subject, composition, aspect ratio, placement, and narrative purpose.

If a required visual cannot yet be specified completely, add a blockquote beginning with `Placeholder:` and describe the ideal graphic, animation, or transition in actionable language. Do not add a placeholder to a slide that does not need a visual.

### 5. Preserve Source Coverage

Map every material source section to planned slides. Consolidate repetition, but do not silently drop unique reasoning, evidence, caveats, examples, demonstrations, or conclusions.

Do not condense source passages inside presenter notes. Allocate them verbatim, defer them, or omit them explicitly. Record deferred and intentionally omitted material with the reason. Keep optional detail in an appendix only when the audience or delivery format benefits from it.

### 6. Review Pacing

Record word count, character count, and pacing status for every substantive note body. Split above the default threshold and justify every retained exception. Estimate speaking time by slide and by section when duration is known. Use more slides for progressive technical explanations and fewer for simple statements. Treat slide count as a consequence of narrative pacing, not a target.

Check for long runs of identical silhouettes. Vary the rhythm through content form, not decoration or interchangeable diagram motifs.

### 7. Deliver the Markdown Plan

Write the plan using the contract below. Mark it `Draft for review` until the user explicitly approves it, then revise the status to `Approved for creation`. Do not generate slides during planning.

## Markdown Plan Contract

Use ordinary Markdown headings, paragraphs, lists, blockquotes, and fenced code. Keep the structure readable by a human while preserving the headings defined by this contract.

````markdown
# Presentation Plan: <Title>

## Status

Draft for review

## Presentation Brief

- Audience:
- Intended duration:
- Communication job:
- Central thesis:
- Audience takeaway:
- Tone and delivery mode:
- Planned slide count:
- Assumptions and constraints:

## Narrative Arc

### Opening

Describe the starting audience state, tension, and promise.

### Development

Describe the cumulative argument, lesson, demonstration, or story.

### Pivot

Describe the decisive conceptual or emotional turn.

### Resolution

Describe how the opening is resolved and what the audience retains or does.

## Slide-by-Slide Implementation Plan

### Slide 1 — <Working Title>

#### Narrative Purpose

State the one job this slide performs and why it exists at this point.

- Estimated delivery time:

#### Source Allocation

- Source section or location:
- Starts with:
- Ends with:
- Presenter-note word count:
- Presenter-note character count:
- Pacing status: Normal, Review, Split Required, or Explicit Exception

#### On-Slide Content

Extract the title and bullets from the verbatim presenter-note passage. Keep bullets in script order and use short contiguous phrases that cue what the speaker says next. Do not paraphrase or synthesize taglines.

For bullet-driven slides, use this hierarchy:

```markdown
- The main idea starts in **natural sentence case**.
  - Supporting phrases preserve source capitalization.
  - Literal identifiers such as `tokio::spawn` keep their exact casing.
```
````

Use one main bullet. Add only the sub-bullets needed to support that point. Keep every phrase verbatim.

Wrap load-bearing keywords in `**...**` or `*...*` so the slide-production workflow can render them in bold cyan. Include fenced code when code is the slide.

#### Slide Treatment

- Lead format: Text-led, Evidence-led, Relationship-led, or Deliberate pause
- Composition and hierarchy:
- Required visual or evidence: Write `None — text-led slide` when no asset is needed.
- Diagram justification: Complete only for a relationship-led diagram; otherwise write `None`.
- Required elements and labels: Write `None` when not applicable.
- Build or emphasis sequence: Write `None` unless staged disclosure improves understanding.
- Continuity from the previous slide:
- Alignment mode: Direct or Bridge

> Placeholder: Describe an unresolved required graphic, animation, or transition. Omit this block when no placeholder is needed.

#### Presenter Notes (Structured Verbatim Script)

Format the assigned source passage as level-0 and level-1 Markdown bullets. Preserve every spoken word, its capitalization, punctuation, and source order. Do not paraphrase, summarize, reorder, correct, or add language.

```markdown
- First sentence of a source paragraph.
  - Supporting sentence from the same paragraph.
  - Another supporting sentence from the same paragraph.

- First sentence of the next source paragraph.
  - Supporting sentence from that paragraph.
```

#### Transition to the Next Slide

- Spoken bridge: Quote the source-authored line or question that motivates the next slide, or write `None in source`.
- Slide action: Cut, Build, Morph, Push, Fade, Wipe, or another explicit action.
- Continuity: State what remains, changes, appears, or disappears.
- Reason: Explain what the transition communicates.

For the final slide, replace the bridge with `End` and describe the intended closing beat.

#### Production Notes

- Required assets or sources: Write `None` when the slide needs no external asset or source.
- Special production dependencies: List required code, diagrams, or animation; otherwise write `None`.
- Accessibility or legibility considerations:
- Verification needs:
- Pacing-exception rationale:
- Unresolved implementation questions:

### Slide 2 — <Working Title>

Repeat the same headings for every slide.

## Cross-Deck Production Notes

### Asset Checklist

List only the photographs, screenshots, logos, code samples, diagrams, quotations, and generated visuals that the plan actually requires. Do not create assets for text-led slides merely to populate this checklist.

### Animation and Transition Map

Collect every non-trivial build, Morph sequence, gallery transition, or timed event in slide order.

### Demonstration and Interaction Cues

Collect live demos, audience exercises, handoffs, and fallback instructions.

### Script Coverage Ledger

Map exact source ranges to slides. Identify deferred and omitted material with reasons. Do not label paraphrased or condensed notes as source coverage.

### Open Decisions

List unresolved choices that require presenter approval before creation.

```

## Few-Shot Examples

These XML-delimited examples demonstrate structure and casing. Treat the tags as prompt delimiters, not as part of the presentation-plan output. The sample prose is intentionally generic; apply the same transformation to the user's actual script without changing its words.

### Main Idea with Supporting Sentences

<example>

<original_verbatim_text>

Concurrency lets a server wait on many connections. The work is mostly I/O-bound. Tasks sleep until data arrives.

</original_verbatim_text>

<expected_on_slide_content>

- **Concurrency** lets a server wait on many connections.
  - The work is mostly **I/O-bound**.
  - Tasks sleep until data arrives.

</expected_on_slide_content>

<expected_presenter_notes>

- Concurrency lets a server wait on many connections.
  - The work is mostly I/O-bound.
  - Tasks sleep until data arrives.

</expected_presenter_notes>

<rule_demonstrated>

The prose begins in sentence case. Both outputs preserve the original wording, capitalization, punctuation, and order. Only the on-slide emphasis markers are added.

</rule_demonstrated>

</example>

### Colon-Ended Setup

<example>

<original_verbatim_text>

The runtime has two strengths: It handles I/O-bound operations concurrently. It keeps CPU-bound work fast.

</original_verbatim_text>

<expected_on_slide_content>

- The runtime has **two strengths**:
  - It handles **I/O-bound operations** concurrently.
  - It keeps **CPU-bound work** fast.

</expected_on_slide_content>

<expected_presenter_notes>

- The runtime has two strengths:
  - It handles I/O-bound operations concurrently.
  - It keeps CPU-bound work fast.

</expected_presenter_notes>

<rule_demonstrated>

The colon creates a natural level-0 setup. The introduced sentences become level-1 siblings without rewriting.

</rule_demonstrated>

</example>

### Multiple Source Paragraphs

<example>

<original_verbatim_text>

Classes group behavior with state. Objects are instances of those classes.

Constructors parameterize object creation. They can validate inputs before returning an object.

</original_verbatim_text>

<expected_on_slide_content>

Slide 1:

- **Classes** group behavior with state.
  - Objects are instances of those classes.

Slide 2:

- **Constructors** parameterize object creation.
  - They can validate inputs before returning an object.

</expected_on_slide_content>

<expected_presenter_notes>

Slide 1:

- Classes group behavior with state.
  - Objects are instances of those classes.

Slide 2:

- Constructors parameterize object creation.
  - They can validate inputs before returning an object.

</expected_presenter_notes>

<rule_demonstrated>

Each source paragraph becomes a separate note group. Because the groups contain independent slide-level ideas, they are split across slides without changing the text.

</rule_demonstrated>

</example>

### Lowercase Technical Identifier

<example>

<original_verbatim_text>

tokio::spawn creates a task. The task can run concurrently.

</original_verbatim_text>

<expected_on_slide_content>

- **tokio::spawn** creates a task.
  - The task can run concurrently.

</expected_on_slide_content>

<expected_presenter_notes>

- tokio::spawn creates a task.
  - The task can run concurrently.

</expected_presenter_notes>

<rule_demonstrated>

The lowercase opening is retained because it is a literal identifier, not an all-lowercase prose style.

</rule_demonstrated>

</example>

## Acceptance Checklist

Before delivery, verify all of the following:

- The plan communicates one coherent audience outcome.
- Every slide has one necessary narrative job.
- Every presenter-note body is copied verbatim from a documented source span.
- No presenter-note text is paraphrased, summarized, polished, reordered, corrected, or synthesized.
- Presenter notes use Markdown list hierarchy, not prose paragraphs.
- Each ordinary source paragraph maps to a level-0 main bullet with its supporting sentences as level-1 sub-bullets.
- Presenter-note structure preserves the exact capitalization, punctuation, and order of the source text.
- On-slide titles and bullets reuse the assigned script's exact vocabulary.
- On-slide prose bullets use natural sentence case and are never mechanically lowercased or title-cased.
- Lowercase technical identifiers and other literal tokens preserve their source casing.
- Every bullet-driven slide has one verbatim main bullet containing the key idea.
- Supporting sub-bullets are verbatim, subordinate to the main point, and kept in source order.
- Bullet hierarchy follows the corresponding presenter-note passage and acts as a glanceable speaking cue.
- Load-bearing bullet keywords use `**...**` or `*...*` emphasis markers.
- Notes stay within 90 words and 525 characters by default; longer notes follow the review, split, and exception rules.
- Presentation, section, and slide titles follow Title Case without Prepositions and Articles.
- Every slide uses the simplest fitting lead format, and no slide includes a diagram by default.
- Every diagram clarifies an audience-critical relationship, sequence, structure, flow, causality, comparison, or change that direct text or evidence would communicate less clearly.
- Text-led and nearly empty slides contain no filler asset, decorative mini-diagram, or unnecessary visual placeholder.
- The deck does not repeat one diagram silhouette across unrelated beats.
- Code, direct evidence, and diagrams receive the full emphasis they require when they carry the beat.
- Every logical jump uses a source-authored spoken bridge when available or an explicit visual transition.
- Motion describes meaning rather than decoration.
- Every visual placeholder is required and actionable; slides that need no visual contain no placeholder.
- All material source content is accounted for.
- A separate slide-production workflow can execute the plan without reading the original script.
- All high-impact open decisions are visible.
```

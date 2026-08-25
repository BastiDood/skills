---
name: presentation-creator
description: Create and visually verify a Basti-styled PowerPoint deck from an approved Markdown plan. Use when executing an approved slide-by-slide plan, not when planning or rewriting the narrative.
---

# Basti Presentation Creator

Turn an approved Markdown presentation plan into a finished, visually verified PowerPoint deck. Treat the plan as an execution contract: implement it faithfully, resolve production details, and avoid reopening narrative decisions.

## Required Companion Workflow

Load and follow the installed Presentations skill before taking any deck action. Read every instruction or reference that the Presentations skill marks as required, including its content-quality rules and implementation documentation. Treat those requirements as authoritative for file creation, artifact-tool usage, workspace placement, rendering, QA, and delivery.

Use `@oai/artifact-tool` from plain JavaScript ES modules as required by the Presentations skill. Do not use `python-pptx` or an alternate slide-construction path.

## Preconditions

Require:

- An approved Markdown presentation plan with a complete slide sequence.
- A requested output format and destination, or enough context to apply the Presentations skill defaults.
- Resolved high-impact decisions that would change the narrative, slide count, mandatory content, or audience outcome.

Do not require or inspect reference decks. Treat the house style in this skill as explicit custom formatting and create the deck from scratch. Do not use Codex Grid or imitate a prior deck unless the user explicitly changes the scope of this skill.

Require the plan status to be `Approved for Creation`. If the plan remains marked `Draft for Review`, contains unresolved narrative decisions, or omits a required slide's audience-facing content or presenter script, stop and request an approved revision. Resolve ordinary production details without escalating them.

## Authority and Boundaries

- Treat the approved plan as the sole narrative source of truth.
- Do not reread the original long-form script unless the user explicitly asks for reconciliation.
- Do not add, remove, combine, reorder, or substantially rewrite slides without approval.
- Preserve on-slide copy and presenter notes exactly. Do not paraphrase, summarize, polish, correct, reorder, or synthesize their wording. Resolve fit through layout or an approved plan revision.
- Do not expose planning commentary, production notes, or implementation scaffolding to the audience unless the plan explicitly requests an audience-visible placeholder.
- Do not silently omit notes, assets, builds, transitions, citations, or placeholders that the plan requires.
- Record every unavoidable deviation and explain its effect.

## Basti Style Guide

Treat simplicity and readability as the visual identity.

- Use a 16:9 canvas.
- Use a charcoal `#23272E` background.
- Use Fira Sans for audience-facing text.
- Use JetBrains Mono for code.
- Use pale gray `#ABB2B1` for primary body text.
- Use coral `#EF4E4D` for primary title emphasis.
- Use cyan `#00B0F0`, green `#83C271`, amber `#D19A66`, and purple `#D55FDE` sparingly as semantic accents.
- Interpret `**...**` in `On-Slide Content` as bold cyan emphasis. Interpret `*...*` as bold italic cyan emphasis. Remove the Markdown delimiters from rendered text.
- Keep emphasis selective. Preserve one to three marked spans per sentence-sized bullet and do not add unplanned highlights.
- Preserve the planned bullet hierarchy. Render the single main bullet as the dominant point and indent supporting sub-bullets clearly without reducing them below the readable body-text range.
- Preserve the plan's natural sentence casing for audience-facing bullets and sub-bullets. Never force body copy to lowercase or title case.
- Preserve the exact casing of acronyms, proper nouns, product names, file names, commands, paths, handles, code identifiers, and quoted wording.
- Keep title slides minimal.
- Use **Title Case without Prepositions and Articles** for every presentation title, section title, and slide title. Keep articles such as `a`, `an`, and `the` and prepositions such as `of`, `in`, `for`, `with`, and `to` lowercase unless they are the first or last word. Preserve the intended capitalization of acronyms, product names, and code identifiers.
- Prefer one flat composition over cards, dashboards, pills, badges, or ornamental UI chrome.
- Keep margins balanced and alignment obvious.
- Use approximately 50–60 pt for deck titles, 35–48 pt for slide titles, 24–32 pt for ordinary audience text, and 16–26 pt for code. Change composition or request an approved plan revision before shrinking type.
- Prevent one-line titles from wrapping.
- Use large text as the visual when a short statement is the whole beat.
- Let code, a complete diagram, a screenshot, a photograph, or a quotation occupy the canvas when it is the primary evidence. Add no competing prose beyond essential titles and labels.
- Vary slide silhouettes through the planned content form rather than decorative styling.

## Execution Workflow

### 1. Read the Complete Plan

Read the plan from beginning to end before creating slides. Extract the presentation brief, slide sequence, presenter scripts, visual requirements, asset checklist, transition map, demonstrations, coverage ledger, and open decisions.

Confirm that slide numbering is consecutive and that every slide contains `Narrative Purpose`, `Source Allocation`, `On-Slide Content`, `Visual Implementation`, `Presenter Notes (Structured Verbatim Script)`, `Transition to the Next Slide`, and `Production Notes`.

### 2. Run a Preflight

Create an internal implementation ledger in the scratch workspace required by the Presentations skill. For every slide, track audience copy, notes, layout, assets, build stages, transition, verification needs, and completion status.

Resolve production questions through the plan and the rules in this skill. Return to the user only when a missing decision would alter narrative meaning or required content.

### 3. Implement the Visual System

Build a small set of reusable presentation-wide primitives for the background, titles, body copy, code, accents, presenter attribution, and page markers. Keep those primitives simple and consistent.

Apply exact coordinates and layout choices according to each slide's visual implementation instructions. Preserve intentional continuity between progressive slides by keeping unchanged elements at identical positions and sizes.

### 4. Implement Audience-Facing Content

Use the planned copy exactly. Preserve its words, capitalization, order, hierarchy, and emphasis markers. Do not shorten, lowercase, title-case, or rewrite it for fit. Adjust the layout first; if the exact copy still does not fit at the required size, request an approved plan revision.

Render `**...**` spans in bold cyan and `*...*` spans in bold italic cyan. Verify that the emphasized words remain exact excerpts from the corresponding presenter notes and that the bullets retain script order.

For bullet-driven slides, preserve one main bullet containing the key idea and its indented supporting sub-bullets. Do not flatten the hierarchy, promote a supporting phrase, merge levels, or create additional main bullets.

Never paste presenter narration onto the canvas to solve a sparse layout. Sparse slides are valid.

### 5. Implement Structured Verbatim Presenter Notes

Add the complete `Presenter Notes (Structured Verbatim Script)` content to the corresponding slide without changing any speaker-text character. Preserve source-authored delivery cues, audience prompts, live-demo markers, and spoken bridges.

Translate the Markdown hierarchy into actual PowerPoint note paragraphs:

- Render every top-level Markdown item as a level-0 note bullet.
- Render every nested item as a level-1 note bullet, preserving deeper levels only when the approved plan explicitly uses them.
- Preserve multiple top-level groups on one slide.
- Treat Markdown markers and indentation as formatting instructions; do not paste literal hyphens or Markdown into one plain-text paragraph.
- Preserve each planned sentence or two-sentence unit as its own note paragraph.
- Preserve colon-ended setup clauses as level-0 bullets with their introduced material beneath them.

Verify notes slide by slide after export. Inspect the note paragraph levels, not merely the visible bullet glyph, because different levels may use the same glyph. Confirm that no structured note body was flattened into a prose block. Check the plan's word count, character count, and pacing status. Do not accept notes above 155 words or 900 characters without an explicit exception rationale. If the available output path cannot author or preserve hierarchical presenter notes, report the blocker rather than silently flattening or omitting them.

### 6. Implement Code, Diagrams, Images, and Placeholders

- Render code as the primary visual when planned. Preserve syntax, indentation, line emphasis, and progressive highlights.
- Use native PowerPoint shapes for simple diagrams, Graphviz for complex relational diagrams, and image generation for illustrative or scientific graphics, following the Presentations skill.
- Create connectors before nodes so edges remain behind entities and labels.
- Source or generate only visuals required by the plan. Respect specified composition, crop, aspect ratio, placement, and narrative role.
- Avoid reusing the same image unless the plan identifies it as a persistent background or continuity device.
- Implement a planned visual when feasible. If the plan explicitly calls for a placeholder, create a restrained, clearly labeled placeholder with minimal audience-facing text and place the detailed implementation brief in presenter notes or the deviation log.

### 7. Implement Meaningful Motion

Follow the plan's transition map:

- Keep cuts quiet and immediate.
- Use builds to reveal dependencies in spoken order.
- Use Morph when the same visual system evolves.
- Use Push for sustained catalogues or lateral sequences.
- Use Fade for section changes, reflective beats, emotional resets, or comedic releases.
- Use successive near-duplicate slides when they explain code or diagrams more reliably than internal animation.
- Keep advance manual unless the plan explicitly requires timed playback.

If the implementation tool cannot reproduce a specified transition, preserve the intended semantic relationship through stable positioning, duplicate slides, or staged emphasis, then record the deviation.

### 8. Verify the Finished Deck

Follow the Presentations skill's render-and-inspect workflow. Inspect every slide individually at full size and use a contact sheet only for deck-level rhythm.

Fix all unintended overlap, clipping, wrapping, low contrast, broken connectors, inconsistent alignment, unresolved placeholders, missing notes, and incorrect builds. Verify that titles follow Title Case without Prepositions and Articles and remain on one line, prose bullets retain natural sentence case, emphasized keywords render in bold cyan, every bullet-driven slide preserves one dominant main bullet with indented supporting sub-bullets, bullets retain script order, presenter notes retain their level-0 and level-1 paragraph hierarchy, code remains legible, and diagrams have readable labels.

Review the deck as a sequence. Confirm that progressive visuals preserve continuity, transitions express the planned relationship, and no slide invites the audience to read a wall of text.

### 9. Reconcile against the Plan

Compare the final deck with every slide entry and cross-deck checklist in the approved plan. Confirm that all audience copy, presenter scripts, assets, demonstrations, transitions, and verification items are present.

Record deviations in the scratch workspace and summarize material deviations in the final response. Do not deliver while an unexplained narrative or content deviation remains.

## Plan-to-Deck Mapping

Map plan sections as follows:

- `Presentation Brief` and `Narrative Arc` guide deck-level pacing and quality review; do not render them as slides unless the slide plan says so.
- `Narrative Purpose` guides implementation judgment; do not expose it to the audience.
- `Source Allocation` verifies provenance, note length, and pacing status; do not expose it to the audience.
- `On-Slide Content` becomes visible slide copy.
- `Visual Implementation` determines composition, hierarchy, assets, and builds.
- `Presenter Notes (Structured Verbatim Script)` becomes hierarchical PowerPoint presenter-note paragraphs without rewriting.
- `Transition to the Next Slide` determines spoken continuity and motion.
- `Production Notes` guide sourcing, implementation, verification, and accessibility; do not expose them to the audience.
- `Cross-Deck Production Notes` become the final implementation and QA checklist.

## Completion Criteria

Deliver only when all of the following are true:

- Every planned slide exists in the correct order.
- Every slide preserves its planned narrative purpose.
- Visible copy remains concise, readable, and audience-facing.
- Visible titles and bullets preserve the planned script-derived wording and order.
- Visible prose bullets preserve the plan's natural sentence case; literal identifiers preserve their exact casing.
- Bullet-driven slides preserve one verbatim key-idea bullet and verbatim supporting sub-bullets.
- Markdown emphasis markers render as planned bold-cyan keyword emphasis.
- Presentation, section, and slide titles follow Title Case without Prepositions and Articles.
- Presenter notes contain the complete verbatim planned script with no wording or capitalization changes.
- Presenter-note Markdown becomes actual level-0 and level-1 note paragraphs rather than a flattened prose block or literal Markdown.
- Note-length exceptions include an explicit rationale.
- Code, diagrams, images, placeholders, and demonstrations match the plan.
- Transitions and builds communicate the planned relationships.
- The encoded house style is consistent across the deck.
- Every slide has passed full-size visual inspection.
- Every unintended overlap, clip, wrap, or missing element is fixed.
- Every material deviation is recorded and disclosed.

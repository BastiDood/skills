# Presentation Plan Template

Use this template when writing the final presentation plan. Do not copy this reference title or its contents list into the plan. Start the plan at `# Presentation Plan: <Title>`.

## Contents

- Presentation brief and narrative arc
- Slide-by-slide implementation plan
- Cross-deck production notes

## Template

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

Extract the title and bullets from the source-derived presenter-note passage. Keep bullets in script order and use short contiguous phrases that cue what the speaker says next. Do not paraphrase or synthesize taglines.

For bullet-driven slides, use this hierarchy:

```markdown
- The main idea starts in **natural sentence case**.
  - Supporting phrases preserve source capitalization.
  - Literal identifiers such as `tokio::spawn` keep their exact casing.
```

Use one main bullet. Add only the sub-bullets needed to support that point. Preserve the words and order within every selected source phrase.

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

#### Presenter Notes (Semantically Structured Script)

Format the assigned source passage as a semantic Markdown hierarchy. Use level 0 for independent spoken moves, level 1 for direct support, and level 2 for source-authored nested material. Preserve the words, capitalization, punctuation at split boundaries, and source order within each span. Do not summarize, polish, correct, embellish, or synthesize spoken language.

```markdown
- Independent opening claim.
- A setup with direct support:
  - Supporting sentence or source list item.
    - Nested source list item when required.
- Another independent move from the same source paragraph.

- (First spoken bullet or clause copied from the next slide…)
- [Non-spoken delivery cue.]
```

Allow several level-0 groups when they form one cumulative beat. If a slide begins with level-1 or level-2 bullets continued from the previous slide, preserve that indentation without inventing a parent. End each applicable non-final slide with a level-0 parenthesized preview copied from the next slide's first spoken bullet or natural clause. Append an ellipsis and exclude this non-spoken preview from the outgoing slide's pacing counts and source coverage.

#### Transition to the Next Slide

- Next-thought preview: Copy the exact parenthesized, ellipsis-marked preview used at the end of the presenter notes, or write `None` for the final slide or when no spoken thought follows.
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

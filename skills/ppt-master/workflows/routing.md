---
description: Deterministic route selection rules for PPT Master requests
---

# Routing Rules

Route selection authority for PPT Master. Use this file before entering the main pipeline or any standalone workflow.

**Hard rule**: If this file conflicts with a route summary in `SKILL.md`, `AGENTS.md`, or a user-facing doc, this file wins for route selection. After a route is selected, the target workflow file or `SKILL.md` owns execution details.

---

## 1. Routing Discipline

| Rule | Behavior |
|---|---|
| Deterministic routes | Do not ask the user to choose when a request matches a defined route. Enter the route directly. |
| Missing prerequisite | Complete the prerequisite when it is recoverable within the request. Otherwise identify the missing input and continue independent work; preserve the route's actual artifact contract. |
| Ambiguous deck optimization | Use the request and context to establish whether count/order/wording must be preserved. Ask one focused question only if both materially different outcomes remain plausible. |
| Explicit user override | Honor explicit route instructions only when the route preconditions are satisfied. |
| Summary conflict | Use this file for route choice, then read the route's own workflow file before executing it. |

**Forbidden - route-choice prompts**: Do not present multiple implementation paths when this file already defines the route. Resolve routine style choices from the brief; surface only material unresolved preferences or an explicitly requested approval gate.

---

## 2. Main Route Matrix

| Request shape | Trigger | Route | Forbidden route | Preconditions | Output contract | Stop condition |
|---|---|---|---|---|---|---|
| Topic only, no source facts | User supplies only a topic name or requirement and no substantive source material | [`topic-research`](./topic-research.md), then main `SKILL.md` pipeline | Direct main pipeline with invented facts | Web/source gathering is allowed or user supplies facts | Research material becomes source input for Step 1 | Stop if facts cannot be gathered and the user supplies no source |
| Source material can be reworked into a new story | PDF/DOCX/URL/Markdown/text/conversation content, or PPTX treated as content | Main `SKILL.md` pipeline | Direct PPTX edit workflows | Source content exists or is available in conversation | `design_spec.md`, `spec_lock.md`, `svg_output/`, exported PPTX | Stop at Step gates when required artifacts are missing |
| PPTX as re-architectable source | User allows page count/order/outline to change, or asks to split/merge/drop/reorder slides | Main `SKILL.md` pipeline with `ppt_to_md.py` plus PPTX intake | [`beautify-pptx`](./beautify-pptx.md) | PPTX source exists | Markdown content plus `analysis/source_profile.json`; Strategist may re-outline | Stop if user requires exact 1:1 page preservation |
| Explicit template directory path | User identifies an unambiguous template directory or name that resolves to `design_spec.md` with `kind: brand`, `kind: layout`, or `kind: deck` | Main `SKILL.md` Step 3 | Inventing a template or silently choosing among ambiguous matches | Path resolves and frontmatter kind is valid | Template assets copied/fused into `<project>/templates/` | Stop if the path does not exist or lacks valid `design_spec.md` |

---

## 3. PPTX-Specific Routes

| Request shape | Trigger | Route | Forbidden route | Preconditions | Output contract | Stop condition |
|---|---|---|---|---|---|---|
| Raw PPTX template plus new material/topic | "Use this PPT template to generate a PPTX", "fill this deck", "replace copy", native slide shell reuse | [`template-fill-pptx`](./template-fill-pptx.md) | Main SVG pipeline directly from raw PPTX template | Source PPTX plus content material or topic brief | New native PPTX in `exports/`, cloned/patched by OOXML | Stop if user instead wants a reusable template package |
| Existing PPTX, preserve page split and wording | "Beautify", "re-layout", "make more professional" with same slide count/order and verbatim text | [`beautify-pptx`](./beautify-pptx.md) | Main pipeline if page count/order changes | Single source PPTX | Regenerated deck through SVG pipeline, one source slide to one output slide | Stop if user asks to split/merge/drop/reorder |
| Finished PPTX, native enhancement only | Add notes, recorded narration, auto-advance, transitions, or stable-layout metadata | [`native-enhance-pptx`](./native-enhance-pptx.md) | SVG regeneration | Finished PPTX exists; content/layout should stay stable | Patched PPTX through direct OOXML | Stop if user asks for visual redesign |
| PPTX/reference design should become a reusable template | "Create a template", "make reusable", "build template from this deck/design" | [`create-template`](./create-template.md) | `template-fill-pptx` one-off fill | PPTX or design reference exists | Template directory under `templates/<kind>/<id>/` | Continue into generation when already requested, using the generated directory; template-only work ends with that directory |

**Hard rule**: Raw PPTX template plus "generate PPTX" routes to `template-fill-pptx` by default. A raw PPTX is not a Step 3 template until `create-template` has produced a reusable template directory.

**Hard rule**: Beautify is strictly 1:1. Any page count or page order change is re-architecture and therefore the main pipeline, not beautify.

---

## 4. Optional and Post-Route Workflows

| Request shape | Trigger | Route | Preconditions | Output contract | Stop condition |
|---|---|---|---|---|---|
| Brand identity setup | Brand asset, brand site URL, branded PPTX/PDF, or explicit brand setup request | [`create-brand`](./create-brand.md) | Brand source exists or can be inspected | Brand preset under `templates/brands/<id>/` | Stop if no brand source or brand intent exists |
| Continue a split-mode project | "Continue generating `projects/<name>`" after the planning session | [`resume-execute`](./resume-execute.md) | Project has planning-session artifacts | Execution-session SVG generation and export | Stop if required planning artifacts are missing |
| Refine spec before generation | User explicitly asks to refine/review/revise the spec before SVG work, or confirms `refine_spec: true` | [`refine-spec`](./refine-spec.md) | Strategist confirmation stage completed | Revised `design_spec.md` and `spec_lock.md` before Step 5/6 | Stop until user approves the refined spec |
| Data chart calibration | Generated deck contains data charts | [`verify-charts`](./verify-charts.md) between Step 6 and Step 7 | SVG pages exist | Calibrated chart coordinates before export | Stop on chart geometry errors until fixed |
| Object-level animation tuning | User asks for animation order, timing, effects, or object reveal behavior | [`customize-animations`](./customize-animations.md) | SVG groups / exported context exist | `animations.json` or validated animation config | Stop if requested target objects cannot be identified |
| Live preview / element selection / annotations | User mentions live preview, preview, visual check in browser, clicking/selecting an element, or applying browser annotations | [`live-preview`](./live-preview.md) | Project exists; for annotation apply, generated SVGs exist | Running preview service or applied annotations plus re-export | Stop only if project path or SVGs are missing |
| Visual review | User explicitly asks for per-page visual self-check or visual rubric | [`visual-review`](./visual-review.md) between Step 6 and Step 7 | SVG pages exist | Visual review findings and fixes before post-processing | Do not run without explicit user request |
| Recorded narration / video export | User asks for narration, voiceover, or video-style export | [`generate-audio`](./generate-audio.md) after post-processing | Notes and exported deck exist | Audio files and optional narration-embedded PPTX | Stop for the workflow's single backend/voice confirmation |

---

## 5. Template Name Boundary

| User input | Route behavior |
|---|---|
| Explicit directory path or uniquely resolved template name with valid `design_spec.md` | Enter main Step 3 template option |
| Explicit template name | Resolve against real indexes; ask only if materially ambiguous |
| Broad brand/style description or vague "use a template" | Use as a design brief without inventing a template choice |
| User asks "what templates exist?" | Answer as Q&A by listing indexed paths; do not advance the pipeline |
| Raw `.pptx` called a template | Route by §3, usually `template-fill-pptx`; never treat it as a Step 3 template path |

Resolve explicitly named templates from installed indexes and verify their paths and schema. A style description alone is not a template selection.

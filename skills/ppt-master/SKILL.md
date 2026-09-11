---
name: ppt-master
description: Create or revise presentations and export editable PPTX, including native template filling and SVG-based design.
---

# PPT Master

Deliver the requested deck, template, preview, or revision while preserving source facts, supplied assets, and settled design choices. Existing authorization continues through design, generation, checking, and export. A proposal-only or approval-first request retains its boundary; ask only about missing choices that materially change the result.

Resolve `${SKILL_DIR}` to this physical skill directory. Read the branch and reference sections needed for the task. Role names describe responsibilities; they do not require separate agents, a role-switch announcement, or repeated user approval.

## Choose the route

Read [routing.md](workflows/routing.md) for ambiguous or specialized routing. The important artifact boundaries are:

| Requested result | Route |
| --- | --- |
| New deck from source material, or restructuring a source PPTX | Generation below |
| Fill a native PPTX template with new content | [template-fill-pptx](workflows/template-fill-pptx.md) |
| Improve a PPTX while preserving slide count, order, and wording exactly | [beautify-pptx](workflows/beautify-pptx.md) |
| Add notes, audio, timing, or transitions to a finished deck without redesign | [native-enhance-pptx](workflows/native-enhance-pptx.md) |
| Create a reusable template or brand | [create-template](workflows/create-template.md) or [create-brand](workflows/create-brand.md) |
| Continue a project or refine its spec first | [resume-execute](workflows/resume-execute.md) or [refine-spec](workflows/refine-spec.md) |
| Preview or apply browser changes | [live-preview](workflows/live-preview.md) |

Use [topic-research](workflows/topic-research.md) when source facts are missing. Data charts use [verify-charts](workflows/verify-charts.md). Rubric-based [visual-review](workflows/visual-review.md), [customize-animations](workflows/customize-animations.md), and [generate-audio](workflows/generate-audio.md) are explicit-request capabilities, not a checklist for every deck.

A raw PPTX template enters the native fill route. The SVG route needs a valid template directory; if creating that directory is part of the request, complete the prerequisite and continue without requiring the user to submit the generated path again. Beautification remains 1:1; changed page count, order, or wording requires the new-deck route.

## Source and design contracts

Use `scripts/source_to_md.py` when extraction is needed, then inspect its completeness. Initialize or import a project with `project_manager.py` when needed. Preserve originals: copy source material by default, and use `--move` only when relocation is authorized. Consult [artifact-ownership.md](references/artifact-ownership.md) when distinguishing source facts from derived artifacts.

For PPTX intake, `analysis/source_profile.json` indexes source decks; open a deck's identity or slide-library data only when its detail is needed. Main-generation content comes from converted or user-supplied content in `sources/`; conversion profiles and image manifests are process metadata. Direct-PPTX workflows own native geometry and chart/table contracts. Source palette and typography are design references in a new deck, not automatic constraints.

Use an explicitly selected template or brand, resolving an unambiguous name against real indexes. A broad style description informs the design. For mixed template kinds, brand owns identity, layout owns structure, and deck supplies remaining design; read [templates-architecture.md](../../docs/zh/templates-architecture.md) when fusion details matter.

For a new or materially changed design, read the relevant sections of [strategist.md](references/strategist.md), [design_spec_reference.md](templates/design_spec_reference.md), and [spec_lock_reference.md](templates/spec_lock_reference.md). Preserve the actual English schema headings and field names; values and explanations follow the user's language. `design_spec.md` holds the design narrative and page briefs; `spec_lock.md` owns executable values and assignments. Local revisions update affected fields rather than rebuilding the whole spec.

## Design choices and the confirmation UI

Resolve routine fields from the brief and current choices. Use the interactive confirmation page when a consequential design choice needs the user's judgment and the page is useful, or when requested. Chat is an equivalent surface; a settled brief does not require an approval tour.

When using the page, follow [confirm_ui.md](scripts/docs/confirm_ui.md), including its exact schema and three-stage protocol. Stage 1 settles direction; Stage 2 is derived from those actual anchors; Stage 3 is derived from the chosen system. After an intermediate result, promptly write the next stage so the page does not remain on its polling spinner. The intermediate statuses are not final confirmation. Honor the final `result.json`, including image-source choices, pixel font sizes, `generation_mode`, and `refine_spec`; never replace user-edited values with earlier recommendations. Shut down this project's confirmation server before live preview. Use the runtime's yielding wait mechanism so a long wait does not block communication.

If the user changes an anchor, rederive only dependent fields they did not pin. Preserve explicit split-mode and spec-review choices. Otherwise continue the authorized deck through export without mandatory mode advertisements or a new chat.

## Assets

Use `analyze_images.py` for current dimensions and inventory; regenerate facts when the image folder changed. Inspect images when content, rendered text, or crop fidelity needs visual evidence. Metadata and a successful generation response do not prove those properties. Keep Office EMF/WMF assets for native PowerPoint where supported; a blank browser preview alone does not establish failure.

For resource rows, read [image-base.md](references/image-base.md) and only the applicable [image-generator.md](references/image-generator.md) or [image-searcher.md](references/image-searcher.md). Preserve manifests, provenance, and actual per-row states. The confirmed image source wins: `none` has no image rows; `provided` maps to `Acquire Via: user`; only selected `ai` rows use AI generation. `host-native`, `api`, and `manual` select their actual routes; `image_gen.py --manifest` belongs only to the API route. Image sheets must be generated before their `slice` elements. Read [failure-recovery.md](workflows/failure-recovery.md) for acquisition or export failures; make no unauthorized provider purchase or data transfer.

For formulas, preserve `mixed`, `render-all`, or `text-only`. Render selected source-grounded expressions through `images/formula_manifest.json` and `latex_render.py`; mark formula rows `Acquire Via: formula`, `Status: Rendered`, and `no-crop`. Formula assets do not enter AI image generation.

## Generate and verify

Read [executor-base.md](references/executor-base.md), [shared-standards.md](references/shared-standards.md), and the one locked mode and visual-style reference. Custom modes use their lock behavior fields. Load relevant layouts and charts when needed. Reuse current context; refresh the lock after edits, compaction, or evidence of drift.

One integrating agent owns source fidelity and cross-page consistency. Independent work may be delegated with the same current spec, bounded source scope, and explicit file ownership when useful and permitted. Choose direct SVG authoring or a suitable generation method; inspect each page against its role and shared design. Preserve editable text, chart plot-area and `data-pptx-native` markers, font constraints, image crop rules, and icon contracts.

Start live preview for an interactive generation task and report the actual URL or launch failure. Reuse an existing service, honor an explicit no-preview request, and preserve user edits. Apply submitted annotations when requested through `live-preview`; direct edits saved by the browser may need only revalidation and export.

Run `svg_quality_checker.py <project_path>` against `svg_output/` before finalization. Fix errors and account for material warnings. Calibrate data charts and inspect rendered output for legibility, clipping, source fidelity, and missing assets. Ordinary artifact inspection is distinct from the optional rubric-based visual-review workflow. Recheck affected pages after fixes. Write requested speaker notes to `notes/total.md` in the splitter's required format.

## Export and complete

Before export, verify that required referenced assets exist. Repair recoverable gaps within authorization; otherwise name the missing content instead of claiming completion with broken images. Preserve source SVGs in `svg_output/`; `svg_final/` is derived.

Run dependent operations in order, inspecting each result:

1. When notes are requested or a valid `notes/total.md` should be included, run `python3 ${SKILL_DIR}/scripts/total_md_split.py <project_path>`. The splitter requires nonempty notes for every SVG; repair incomplete requested notes before splitting. Skip this step when there are no notes to include.
2. `python3 ${SKILL_DIR}/scripts/finalize_svg.py <project_path>`
3. `python3 ${SKILL_DIR}/scripts/svg_to_pptx.py <project_path>`

When the user explicitly wants no speaker notes, pass `--no-notes` to the exporter so existing note files are not attached. Existing correctly split per-slide notes need no repeated split when unchanged.

Finalization embeds and processes assets and text; a file copy cannot replace it. Native PPTX reads `svg_output/` and retains a source backup; optional `--svg-snapshot` reads `svg_final/`. Paragraph merging is the current default; use `--no-merge` for required line-layout fidelity. Use `--native-objects` only for requested native editable chart/table objects, with their renderer differences understood. Per-element entrance animation remains opt-in. Consult current script `--help` and [scripts/README.md](scripts/README.md) for other options rather than assuming an older version's flags.

Verify the exported file and deliver its usable path with actual checks and remaining gaps. After authorized browser changes, revalidate and re-export. Continue until all requested pages, notes, formats, or repairs are delivered; report user acceptance only when it occurred.

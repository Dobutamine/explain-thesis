# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A **manuscript repository**, not a software project. There is no build, no test suite, no linter. The deliverables are Markdown/Word manuscripts, SVG/PNG figures, and planning documents. Almost every task here is reading, drafting, verifying references, or checking consistency across ~20 prose files.

**Status: unpublished work under journal submission.** © Tim Antonius, all rights reserved. Do not redistribute or quote manuscript content into external tools/services.

The **repo root is the Obsidian vault root** — open this folder directly. `explain-engine/` is a submodule (`github.com/Dobutamine/explain-engine`) and is **not initialized in this checkout** — `git submodule update --init` first if you need engine sources. It is excluded from the vault via `userIgnoreFilters` in `.obsidian/app.json`, so the engine's source tree stays out of Obsidian's search, graph, and quick switcher; leave that filter in place.

## Two products, one source set

The same files serve both:

1. **The 9-paper *Pediatric Research* series (P1–P8)** — the *main path*. Governed by `pediatric-research-plan/`.
2. **The compilation thesis** (thesis by publication; papers become chapters + new connective chapters). Governed by `THESIS_BLUEPRINT.md`.

Papers are referred to **by P-number everywhere in prose and planning docs, and filenames do not match**. This mapping is the single most load-bearing fact in the repo:

| # | Paper | Manuscript file |
|---|---|---|
| P1 | Cardiovascular (lead) | `ExplainCircPaper(27012026)_WPdB_TA_WvM.docx` ← **canonical master**; `cardiovascular-paper.md` is a working conversion |
| P2 | Respiratory / gas exchange / acid–base | `respiratory-paper.md` |
| P3a | Cerebral haemodynamics & ICP | `cerebral-paper.md` |
| P3b | Homeostatic regulation (renal/endocrine/thermal/glucose/pharma) | `other-systems-paper.md` |
| P4 | Mechanical support devices | `devices-paper.md` |
| P5 | Integrated-model + virtual-patient-library flagship | `integrated-model-paper.md` |
| P6 | AI-parameterization method (headline) | `ai-parameterization-paper.md` + `P6_supplement_sensitivity-analysis.md` |
| P7 | Duct-/FO-dependent CHD (12 lesions) | `chd-paper.md` |
| P8 | Review (invited, last) | not drafted — outline only in `P8_review_preinquiry.md` |

Gotchas in that table: **P3 was split into P3a/P3b (2026-07-13)** — "Paper 3"/`other-systems-paper` in older text may mean the pre-split omnibus. `thesis-ch6-sensitivity-analysis.md` and `thesis-ch7-virtual-patient-library.md` are **thesis chapters that were absorbed into P6 and P5** respectively; they are sources, not live manuscripts. The file inventory in `MAIN_PATH_STATE.md` §6 is slightly stale (predates `cardiovascular-paper.md` and the P3 split).

## Read these first

- **`pediatric-research-plan/MAIN_PATH_STATE.md`** — the durable state checkpoint: per-paper status matrix, open actions, open decisions, what's already done ("not to redo"). **Start here for any series work, and update it when you complete a workstream.** It is the memory of this project.
- `pediatric-research-plan/PR_ARTICLE_SLATE.md` — the programme: wave logic, journal-fit constraints, competitive positioning.
- `THESIS_BLUEPRINT.md` — thesis-side master plan: outline, asset map, figure inventory, gap list.
- `pediatric-research-plan/SKIM_DIGEST.md` — condensed overview if you need orientation fast.

These planning docs are **narrative state, kept current by hand**. When work lands, edit them in the same voice (strike-through + `✅ DONE <date>` is the established idiom) rather than appending a changelog.

## The anti-redundancy spine (do not violate)

Nine papers in one journal is defensible only because of this. Before adding content to any paper, check whether it belongs elsewhere:

1. **Shared Methods S1–S7 in full only in P1** (`_shared-methods.md`); every later paper cites it.
2. **AI-parameterization method in full only in P6.** Every other paper carries the *compact highlight* (Box 1). P5 and P7 add a *showcase*, not a re-derivation.
3. **Whole-body integration narrative told exactly twice**: as concept in P8, as primary research in P5. Subsystem papers stay in their subsystem.
4. **Each paper owns a distinct disease-validation slice** of the virtual-patient library.

`pediatric-research-plan/series_blocks.md` holds the three reusable blocks (A: positioning vs Munneke 2021 / van Willigen 2026 / May 2025 · B: AI-param highlight · C: validation strategy). **Reuse near-verbatim, filling only the `‹slots›` — do not paraphrase for variation.** Consistency is the point; they are meant to read as a series signature.

## Writing conventions

- **Equations** are plain-text Unicode in a blockquote: `> **Eq. 1** &nbsp; *a*_a(*t*) = sin[π·(*t* / *T*_as)]`. Each block names the engine source file it was checked against. All Markdown equations are placeholders to be **re-keyed as native Word OMML at assembly** — do not treat Markdown as the final form.
- **References**: Vancouver. `_references.md` is the shared *pool*; each paper renumbers its own subset in citation order. `P1_references_verified.md` is P1's PubMed-verified list (a companion for the Word/EndNote pass — **not auto-injected into the `.docx`**).
- **`[VERIFY]`** marks an unverified reference. Never invent a PMID/DOI — verify against PubMed and record the confirmed identifier. Some sources are genuinely pre-MEDLINE/textbook and are marked as such; don't chase those in PubMed.
- **`‹angle-bracket slots›`** are unfilled placeholders awaiting a decision or a DOI.
- Manuscripts **cite by author-name in prose**; inline numeric `[N]` citations get wired at final assembly (a known open action, not a defect).
- Cross-paper references are written `[P6]` / `‹P6›` and resolve to real DOIs only once that paper is posted/published.

## *Pediatric Research* constraints (bind every paper)

Basic Science Article: **structured abstract ≤200 words**, **≤5000 words** main text, **≤6 figures**. Review: 4500 words. **Impact Statement ≤100 words** — counted **MS-Word style** (spaced em-dashes count as words), which is stricter than a naive `wc -w`; each carries a `*(N words.)*` tag that must reconcile. Every paper needs a Data & Code availability statement (`explain-modeling.com`, Zenodo concept DOI `10.5281/zenodo.21389097`) and a single AI-use disclosure in Methods (**LLM is a method component, never an author**).

## `.docx` files

Word masters and exports are git-tracked binaries. **P1's `.docx` is canonical** — its Markdown twin is a conversion, so P1 edits ultimately land in Word by hand. Other `.md`/`.docx` pairs (`respiratory-paper`, `devices-paper`, `other-systems-paper`, …) have Markdown as the live draft and the `.docx` as a possibly-stale export. `ExplainCircPaper_WORKING_with_AIparam.docx` is explicitly **reference only, not canonical**. You cannot meaningfully edit `.docx` with text tools — flag Word-pass work for the user rather than attempting it.

## Figures

Regenerate the sensitivity-analysis figures (deterministic — a clean run leaves `git status` untouched; any diff means the campaign results changed):

```bash
node explain-engine/scripts/sa/plot_sa.mjs --out .
```

Other probe/figure scripts (`probe_*.mjs`, `sa/`, `build_patient.mjs`) live under `explain-engine/scripts/` and run from the engine directory.

New figures follow the house style of `Fig1_regulatory_systems.svg` (Helvetica Neue, slate palette, accent bars): author as SVG, rasterize with **headless Chrome** (`--force-device-scale-factor=2 --window-size=1400,1080`). **Do not use `qlmanage`** — it center-crops landscape SVGs to a square.

## Known gap

Manuscripts cite `docs/engine/*.md` (`ARCHITECTURE.md`, `Calibrator.md`, `chd_duct_fo_dependent.md`, per-model derivations). Those files live in the **`explain-ui`** repo, not in the engine submodule — **those citations do not resolve from this checkout**. Moving them to `explain-engine` is an open follow-up.

## Note on the inherited parent CLAUDE.md

`/Users/timantonius/Projects/CLAUDE.md` is auto-loaded and documents an unrelated **NICU CSV dataset** (`data/`, `data_secure/`, PHI handling). None of it applies to this repository — no patient data lives here, and EXPLAIN is validated against published literature, not against that dataset. Ignore it unless the user explicitly bridges the two projects.

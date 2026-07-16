# explain-thesis

PhD thesis and the accompanying *Pediatric Research* paper series on **Explain**, a
whole-body neonatal physiological simulation engine.

> **Status: unpublished work under journal submission.**
> © 2026 Tim Antonius. All rights reserved. Unpublished manuscript.
> Do not redistribute, quote, or cite without permission.

## Layout

```
explain-thesis/            ← the manuscripts and figures (Obsidian vault root)
├── pediatric-research-plan/
└── explain-engine/        ← git submodule: github.com/Dobutamine/explain-engine
```

The **repository root is the Obsidian vault root** — open this folder directly. The engine
submodule is kept out of Obsidian's search, graph view, and quick switcher by the
`userIgnoreFilters` entry in `.obsidian/app.json` (tracked, so it travels with the repo).

`explain-engine` is the simulation engine the thesis describes — a standalone, citable,
MIT-licensed repository, pinned here as a submodule so every number and figure in the
thesis can be regenerated against the exact engine revision it was written against.

## Clone

```bash
git clone --recurse-submodules git@github.com:Dobutamine/explain-thesis.git
```

If you already cloned without `--recurse-submodules`:

```bash
git submodule update --init
```

## Regenerating the sensitivity-analysis figures

`FigSA_onelever_validation.svg` and `FigSA_operating_point_dominance.svg` are generated
from the SA campaign results committed in the engine repo:

```bash
node explain-engine/scripts/sa/plot_sa.mjs --out .
```

The script is dependency-free and deterministic — a clean run leaves `git status`
untouched. Any diff means the underlying campaign results changed.

Other probe and figure scripts live under `explain-engine/scripts/` (`probe_*.mjs`,
`sa/`, `build_patient.mjs`); run them from the engine directory.

## Known gap: `docs/engine/`

Several manuscripts cite `docs/engine/*.md` (`ARCHITECTURE.md`, `Calibrator.md`,
`chd_duct_fo_dependent.md`, the per-model derivations). Those files currently live in
the **`explain-ui`** repository, not in the engine submodule — so those citations do not
resolve from this checkout. Arguably they belong in `explain-engine` alongside the code
they document; moving them is an open follow-up.

## History

Extracted from the `explain-ui` repository with `git-filter-repo`, preserving the full
drafting history of the manuscripts. They lived under a `thesis/` subfolder — in that
repository and initially here — and were flattened to the repository root afterwards, so
tracing a file past that point needs `git log --follow`.

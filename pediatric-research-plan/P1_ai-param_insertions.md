# P1 (Cardiovascular) — AI-parameterization insertion set (SI-routed)

*Paste-ready for the Word pass on the canonical `ExplainCircPaper(27012026)_WPdB_TA_WvM.docx`.*

**Revised 2026-07-17 — the AI-parameterization highlight now goes to Supplementary Information, not the
body.** Per the author's decision, the full **Box 1** callout and **Fig. 6** are moved out of the P1 main
text into a Supplementary Information file (`P1_supplement_AI-parameterization.md`): **Supplemental
Methods S1** (the full box text) and **Supplemental Fig. S1** (the pipeline figure). The body keeps only
(a) a compact 2–3 sentence pointer in §2.3 and (b) the required AI-use disclosure. This de-emphasizes a
method that is P6's contribution, and reclaims a main-text figure slot + ~150–200 words against PR's
5000-word / 6-figure / 200-word-abstract caps. *Anchor sentences verified present in the `.docx`
2026-07-12; the compact-highlight decision (full method lives in P6) is unchanged — only its placement in
P1 changed from body to SI.* Reference numbers assume the list currently ends at [23].

> **Why paste-ready, not auto-applied:** these edits add a new Supplementary Information file, a compact
> body pointer, and three new references, and the Results/Discussion pointers reference *Supplementary
> Methods S1* and *[24]* — they must go in together or they dangle. Placement, box styling in the SI PDF,
> and reference formatting are best done in your Word pass. *(Say the word to inject into the `.docx`.)*

---

### 1. Abstract — REPLACE the AI sentence with a short clause
Current draft trims the full AI sentence to:

> The patient-specific configurations validated here were produced by an automated calibration pipeline rather than by hand.

*(Drops the LLM/deterministic-calibrator detail from the abstract; helps the 200-word cap.)*

### 2. Introduction — INSERT after "It is validated at baseline, and for the PDA and aPH conditions."

> The patient-specific configurations used in these validations were produced not by manual tuning but by an AI-assisted, closed-loop calibration pipeline that maps a small set of measured clinical targets onto the model's parameters; the pipeline is summarized in Supplementary Methods S1 and described in full in a companion paper [24].

### 3. Methods §2.3 — INSERT the compact body pointer (NOT a box; the box goes to Supplemental Methods S1)

> The virtual patients used in this paper were not tuned by hand. A large language model interprets the available clinical targets into a validated, allowlisted specification, and a deterministic calibrator then fits the model by driving one physiologically interpretable lever per target to a clinician-meaningful tolerance, after allometric and gestational-age seeding and baroreflex set-point alignment; the language model performs no numerical fitting and never edits equations or state. The per-target lever assignments for the cardiovascular model, the convergence behaviour, and the sensitivity-analysis justification for the one-lever-per-target design are given in Supplementary Methods S1 and, in full, in the companion paper [24].

### 3b. Supplementary Information — the full box + figure (from `P1_supplement_AI-parameterization.md`)

- **Supplemental Methods S1** = the full series-signature box text (the interpretation-layer /
  calibration-layer description + the five P1 lever pairings + the SA-validation sentence). This is the
  content formerly proposed as in-body Box 1.
- **Supplemental Fig. S1** = the pipeline figure (`Fig6_AI_parameterization.{png,svg}`) with its caption.
- SI file carries the PR-required header (article title, journal, authors, corresponding-author
  affiliation + e-mail) and S-prefixed numbering; legends live in the SI file.

### 4. Results §3.1.2 (PDA) — REPLACE this exact sentence

> ~~EXPLAIN's model parameters were rescaled to match the baseline premature infant physiology reported by Bischoff et al. by adjusting blood volume, unstressed volumes, elastances, and resistances proportional to weight, with minor additional tuning of arterial resistances.~~

with

> The baseline premature-infant configuration reported by Bischoff et al. was produced with the AI-assisted calibration pipeline (Supplementary Methods S1): blood volumes, unstressed volumes, elastances and resistances were first scaled allometrically to the reported weight, after which the calibrator fitted the model to the reported baseline hemodynamic targets.

*(Everything after — the 2.2 mm bidirectional shunt, the LVO/RVO 1.55 left-to-right case — stays.)*

### 5. Results §3.1.3 (PH) — INSERT after "…to represent varying degrees of severity."

> As in the PDA case, the underlying patient baseline was established with the calibration pipeline of Supplementary Methods S1; the pulmonary-resistance and pulmonary-artery-elastance changes representing each stage were then applied on top of that calibrated baseline.

### 6. Discussion §4.1 (originality) — APPEND

> A further element of originality is that EXPLAIN is fitted to a patient not by hand but by an AI-assisted, closed-loop pipeline (Supplementary Methods S1, Fig. S1; companion paper [24]), which makes patient-specific instantiation reproducible and keeps every automated adjustment within the same bounds as a manual edit.

### 7. Discussion §4.5 (future work) — REPLACE this exact sentence

> ~~Following a systematic sensitivity analysis, future work will focus on the development of parameter-optimization pipelines for patient-specific model fitting, using machine-learning and other artificial-intelligence–based approaches.~~

with

> Building on the AI-assisted parameterization pipeline used here (Supplementary Methods S1; companion paper [24]), future work will pursue a systematic sensitivity analysis to identify the most informative parameters, extend calibration from the present one-lever-per-target scheme to joint multi-target optimization for strongly coupled configurations, and undertake prospective validation of patient-specific fits against clinical data.

*(The following sentence on ventilation strategies, pulmonary vasodilators and transitional failure stays.)*

### 8. Methods — AI-use disclosure (required by PR; STAYS IN THE BODY, in §2.3)

> A large language model (Claude, Anthropic) is used as a component of the parameterization method: it interprets clinical inputs and emits validated, allowlisted specifications. It performs no numerical fitting, does not modify the model's equations or state, and is not used to generate the scientific content or text of this study; no authorship is attributed to it.

### 9. New references — APPEND after [23]

> **[24]** Antonius TAJ, van Meurs WL, Westerhof BE, de Boode WP. ‹AI-parameterization paper title — see `A2_ai-parameterization_frontmatter.md`›. Pediatr Res. ‹year; in press / bioRxiv preprint DOI›. *(the P6 companion paper — preprint it early so P1 can cite it)*
> **[25]** Anthropic. Claude [large language model]. Anthropic PBC; 2025. Available from: https://claude.com.
> **[26]** Anthropic. Claude Agent SDK [software]; 2025. Available from: https://platform.claude.com/docs/en/api/agent-sdk.

*(Confirm the exact Claude model/version and access date, and the software-citation format PR/Springer Nature requires. [24]/[25]/[26] are cited from the §2.3 pointer, the AI-use disclosure and the Discussion; they are not orphaned by the SI move.)*

---

## Sibling cross-references — redirected (do at the same pass)

Because P1 no longer carries an in-body "Box 1", the two sibling papers that pointed to it were
redirected (already applied in the `.md` drafts): **`respiratory-paper.md`** and **`cerebral-paper.md`**
now read "(and applied to the cardiovascular models of the lead paper [P1])" and cite **[P6]** for the
full method, instead of "summarized as a compact highlight—Box 1—in the cardiovascular paper [P1]".

## What is intentionally NOT here (lives in P6)

- The full **§2.4 AI-assisted parameterization** Methods subsection with Eqs. 13–15 (circ-paper-additions.md Block A).
- **Table X** (the full lever table) — its content appears only in Supplemental Methods S1's inline lever list.
- The **worked convergence example** and the secant/relaxation-loop detail.

## Data & Availability (decided: public code deposit)

Availability statement: the interactive model is freely available at https://explain-modeling.com; the
complete annotated engine source code is publicly available at https://github.com/Dobutamine/explain-engine
and archived at https://doi.org/10.5281/zenodo.21389097. **DONE 2026-07-16** (repo public + DOI minted).

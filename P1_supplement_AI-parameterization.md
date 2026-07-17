# Supplementary Information

**Article:** An integrated model for simulation of neonatal physiology — The cardiovascular system
**Journal:** *Pediatric Research* (Basic Science Article)
**Authors:** Antonius TAJ, van Meurs WL, Westerhof BE, de Boode WP
**Corresponding author:** T. A. J. Antonius, Radboud University Medical Center / Radboudumc Amalia Children's Hospital, Department of Neonatology, Nijmegen, the Netherlands. ‹full postal address,phone›. E-mail: timantonius@pm.me

*This file is submitted as a single combined Supplementary Information PDF. Supplementary items are numbered with an "S" prefix and are not counted against the main-text word or figure limits. Legends are included here, not in the main manuscript. This SI is not copyedited; style and terminology conform to the main manuscript.*

## Supplemental Methods S1 · How the virtual patients in this paper were parameterized

The patient profiles used here were not tuned by hand. EXPLAIN instantiates a patient with a two-layer, AI-assisted pipeline. An **interpretation layer** — a large language model — reads the available clinical description (free text, monitor values or a report) and emits a validated, bounded *specification*: a baseline, target values and named pathophysiology, expressed only through the same allowlisted, schema-checked commands as the interactive parameter editor. A **calibration layer** — a deterministic root-finder — then fits the model by assigning one physiologically interpretable lever to each target and driving that target to a clinician-meaningful tolerance with a proportional-seed/secant update, after allometric and gestational-age seeding and baroreflex set-point alignment so the model's own control loops defend rather than oppose the fit. The language model performs no numerical fitting and never edits equations or state.

For the cardiovascular targets of this paper the pairings are: mean arterial pressure ← systemic
(arteriolar) resistance; cardiac output ← ventricular contractility (*e*_max); heart rate ← SA-node
rate set-point; central venous pressure ← systemic venous unstressed volume; mean pulmonary artery pressure ← pulmonary vascular resistance.

The one-lever-per-target design is not assumed but **validated by a formal, variance-based sensitivity analysis** (Sobol′/PRCC, estimator-validated against an analytic benchmark), which also characterizes identifiability and the operating points where the design is weakest. The full method — convergence behaviour, this sensitivity-analysis validation, and the offline construction and live-tuning entry points — is given in the companion paper [24]. The use of a large language model as a *method component*, not an author, is disclosed in the Methods of the main manuscript.

## Supplemental Figure S1 · AI-assisted patient-specific parameterization

*(Figure assets: `Fig6_AI_parameterization.png`, `Fig6_AI_parameterization.svg`.)*

An LLM agent interprets the available clinical targets (*x\**) and emits a validated specification and
allowlisted commands; it does not modify equations or state directly. A deterministic calibrator then fits the mechanistic model: a single structural pass scales the model to body size and aligns the baroreflex set-point to the target mean arterial pressure, after which one physiologically interpretable lever per target (Supplemental Methods S1) is nudged — a proportional seed followed by the secant method — as the model is advanced and each quantity is re-measured as a beat-averaged mean, until every residual falls within its clinician-meaningful tolerance. The same loop supports offline construction of a new calibrated patient and live retuning of a running simulation. The full method is given in the companion paper [24].


*Reference [24]: Antonius TAJ, van Meurs WL, Westerhof BE, de Boode WP. An AI-assisted closed-loop method for patient-specific parameterization of a whole-body neonatal physiology model. Pediatr Res. ‹year; in press›. Preprint: bioRxiv ‹preprint DOI pending›. (Numbering follows the main manuscript'sreference list.)*

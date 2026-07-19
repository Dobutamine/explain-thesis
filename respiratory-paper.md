# An integrated model for simulation of neonatal physiology — The respiratory system, gas exchange and metabolism

*Companion to the cardiovascular paper (Antonius TAJ, van Meurs WL, Westerhof BE, de Boode WP).
Target journal: Pediatric Research. Markdown working draft — equations to be re-keyed as native
Word (OMML) objects, matching the style of the cardiovascular paper. Every equation is
transcribed from and checked against the named engine source file. Numbers in Results marked
`[SIM: …]` are to be filled from the reproducible simulation runs of Section 3; do not state a
value until its probe has produced it.*

---

## Abstract

*(Structured abstract — Pediatric Research Basic Science format. The prior long-form draft is superseded.)*

**Background:** Caring for a sick newborn's breathing means reasoning about a tightly linked system — ventilation, inspired oxygen, an immature lung's oxygen uptake, blood flow, tissue oxygen use and acid–base balance — from few bedside measurements. A real-time model can make these links visible.

**Methods:** We describe the respiratory part of EXPLAIN, an integrated neonatal simulator: the lungs and chest-wall mechanics, spontaneous breathing, alveolar oxygen and carbon-dioxide exchange, blood-gas transport and acid–base chemistry, tissue metabolism with lactate, and surfactant-dependent lung recruitment — all computed in the same blood as the circulation. Rather than hand-tuning, an AI-assisted calibration fits the model to each patient's measured values.

**Results:** The model reproduces a normal term newborn's blood gas within reference ranges: oxygenation rises with inspired oxygen and lung oxygen uptake, carbon dioxide follows breathing effort, and adding acid produces a metabolic acidosis independent of respiratory changes. In simulated preterm respiratory distress, surfactant recruits the lung over minutes, raising PaO₂ from 55 to 74 mmHg and SpO₂ from 91 to 96%.

**Conclusion:** Solving breathing, gas exchange, transport, acid–base and metabolism together makes the arterial blood gas an emergent, patient-specific result, not a preset number — a transparent, real-time platform for neonatal respiratory physiology.

---

## 1. Introduction

Respiratory care of the sick newborn means reasoning about quantities that cannot be seen. At the incubator a clinician observes a pulse-oximeter saturation, an end-tidal CO₂ trace and, intermittently, an arterial blood gas, and from these few outputs must infer a tightly coupled system — alveolar ventilation, inspired oxygen, the diffusing capacity of an immature lung, pulmonary blood-flow distribution, tissue oxygen consumption and blood buffering. A fall in saturation may reflect atelectasis, right-to-left shunting, hypoventilation or low cardiac output; a rising CO₂, a problem of drive, dead space or compliance; a metabolic acidosis, tissue hypoxia several steps from the airway. The couplings linking the measured outputs to their causes are exactly what the monitor does not show.

Mechanistic models can make these couplings explicit. Where a monitor shows an output, a model exposes the chain of intermediate variables that produced it and — if it runs in real time and responds to intervention — lets the user perturb one part and watch the consequences propagate. EXPLAIN is an integrated, real-time, whole-body simulator of neonatal physiology built for this explanatory purpose: every quantity is computed from interpretable lumped-parameter compartments and is open to inspection and manipulation as the simulation advances. The cardiovascular subsystem is described in a companion paper. This paper describes the respiratory subsystem and the processes inseparable from it — alveolar gas exchange, blood transport of oxygen and carbon dioxide, acid–base chemistry and tissue metabolism — which unfold in the same blood compartments, so the arterial blood gas the model reports is not prescribed but emerges from ventilation, perfusion, diffusion and metabolism solved together.

The newborn — especially the preterm — gives this integration particular clinical weight. Surfactant deficiency stiffens the lung, lowers functional residual capacity and opens intrapulmonary shunts, so respiratory distress syndrome presents as a coupled failure of compliance, oxygenation and CO₂ clearance that responds over minutes to surfactant and recruiting pressure. Fetal haemoglobin shifts the dissociation curve; permissive hypercapnia and the immature kidney's narrow buffering shape acid–base management; and metabolic rate, thermoregulation and lactate couple the respiratory state to the whole-body oxygen economy. A model for neonatal respiratory care must therefore represent gas exchange, transport, acid–base, metabolism and surfactant-dependent mechanics as one system.

This paper's contribution is a compact but complete mathematical description of that system — governing equations, each with interpretable parameters, for neonatal ventilation, alveolar gas exchange, strong-ion blood-gas and acid–base transport, oxygen consumption and CO₂ production, hypoxia-driven lactate, and dynamic surfactant-dependent recruitment — integrated into one model that runs in real time in a browser and shares the circulation's blood compartments. The plasma acid–base solver at its core we published separately [4]; the contribution here is its integration with gas exchange, oxygen transport, metabolism and surfactant mechanics. A second, series-wide contribution is how the model is fitted to a patient: rather than hand-tuning — the traditional bottleneck — EXPLAIN is parameterized by an AI-assisted, closed-loop pipeline in which a large language model interprets the clinical targets and a deterministic calibrator drives the model onto them within clinician-meaningful tolerances (respiratory targets: arterial PO₂/saturation, PCO₂, pH and base excess; summarized in Section 2.4, full method in [P6]). We then specify the model (Section 2) and show it reproduces neonatal gas-exchange and acid–base behaviour (Section 3).

---
## 2. Methods

### 2.1 Conceptual model

The respiratory subsystem is a chain of lumped compartments (Fig. 1). Inspired gas enters at the airway opening (`MOUTH`, at atmospheric pressure and composition), passes through the conducting airways and anatomical dead space (`DS`) to the left and right alveolar compartments (`ALL`, `ALR`) — all elastic and enclosed by a single elastic thorax (`THORAX`) coupling chest-wall mechanics to the lungs. A respiratory-drive model (`Breathing`) sets rate and tidal volume from a target minute ventilation and applies a respiratory-muscle pressure to the thorax; mechanical ventilation (companion devices paper) acts through the same airway.

At the alveolar–capillary interface two gas-exchange units (`GASEX_LL`, `GASEX_RL`) move O₂ and CO₂ down their partial-pressure gradients between alveolar gas and pulmonary-capillary blood. Blood carries O₂ and CO₂ as total contents (*t*O₂, *t*CO₂); the acid–base/oxygen solver (`BloodComposition`) converts these and the plasma strong ions into partial pressures, pH, bicarbonate, base excess and saturation everywhere blood exists. In the systemic tissues, metabolism (`Metabolism`) removes O₂ and adds CO₂ by each organ's share of whole-body consumption, and under anaerobic conditions a lactate model (`Lactate`) produces lactate, which the solver reads as a strong anion — a lactic metabolic acidosis. A surfactant/recruitment model (`Surfactant`) makes alveolar compliance, functional residual capacity, diffusion and intrapulmonary shunt depend on transpulmonary pressure and surfactant maturity, reproducing respiratory distress syndrome (RDS) and its treatment.


**Fig. 1** (`Fig1_respiratory_subsystem.svg`; PNG export `Fig1_respiratory_subsystem.png`).
Schematic of the neonatal respiratory subsystem and its couplings. Inspired gas passes from the airway opening (`MOUTH`, fixed at atmospheric composition and FiO₂) through the anatomical dead space (`DS`, upper/lower-airway resistances) into the alveolar compartments (`ALL`, `ALR`) enclosed by the elastic thorax. `Breathing` generates the respiratory-muscle pressure; `Surfactant` senses mean transpulmonary pressure and drives lung elastance, FRC, diffusion and intrapulmonary shunt (dashed). Gas-exchange units (`GASEX_LL`, `GASEX_RL`) move O₂ and CO₂ between alveolar gas and pulmonary-capillary blood; an intrapulmonary shunt (dashed red) allows venous admixture. Blood circulates through the companion circulation to the tissue beds, where `Metabolism` and `Lactate` consume O₂, produce CO₂ and, under oxygen debt, generate lactate. The acid–base/oxygen solver (`BloodComposition`; Stewart strong-ion, Hill dissociation, Van Slyke base excess) converts blood contents and strong ions into pH, PCO₂, PO₂, saturation, bicarbonate and base excess (dashed purple). Colour key: gas/mechanics (blue), blood/transport (red), gas exchange/acid–base (purple), control/metabolic (green). Styling matches Figs 1–2 of the cardiovascular paper.

### 2.2 Mathematical model

Notation and units follow the cardiovascular paper (see `_shared-methods.md` S1). Throughout, Δt is the integration step (`modeling_stepsize`, default 5×10⁻⁴ s); all `x ← x + (…)·Δt` updates are explicit forward-Euler steps. Every physical parameter *p* enters through its effective value
*p*_eff (Eq. S1); for brevity we write the base symbol and note where a factor layer is used.

#### 2.2.1 Gas compartments and thoracic mechanics

*Source: `explain/base_models/Capacitance.js`, `explain/component_models/GasCapacitance.js`,
`explain/base_models/Container.js`.*

A gas compartment is an elastic chamber (a `Capacitance`) extended to carry temperature, humidity and the molar concentrations of O₂, CO₂, N₂, H₂O and a lumped "other" species. Its recoil pressure is a linear-plus-quadratic function of the volume above its unstressed volume:

$$p_{\text{in}} = E_{\text{eff}}\,(V - V_{u,\text{eff}}) + K_{2,\text{eff}}\,(V - V_{u,\text{eff}})^2 \tag{1}$$

where *E*_eff (mmHg·L⁻¹) is the effective elastance, *K*₂,eff (mmHg·L⁻²) the non-linear elastance
coefficient, *V* the compartment gas volume (L) and *V*ᵤ,eff the effective unstressed volume (L).
*E*, *K*₂ and *V*ᵤ each carry the three-layer factor composition of Eq. S1. The total compartment pressure adds the external pressures and the atmospheric reference:

$$P = p_{\text{in}} + p_{\text{ext}} + p_{\text{cc}} + p_{\text{mus}} + P_{\text{atm}}, \quad P_{\text{rel}} = P - P_{\text{atm}} \tag{2}$$

with *P*_atm = 760 mmHg, *p*_cc the pressure imposed by the enclosing thorax, and *p*_mus the
respiratory-muscle pressure (Section 2.2.2); *p*_ext, *p*_cc and *p*_mus are additive perturbations reset to zero each step.

The thorax is a `Container`: an elastic shell whose volume is the sum of the (enabled) contained
compartment volumes and which transmits its own recoil pressure back onto each contained
compartment as external pressure,

$$V_{\text{thorax}} = V_{\text{extra}} + \sum_c V_c, \quad p_{\text{ext}}^{(c)} \leftarrow p_{\text{ext}}^{(c)} + P_{\text{thorax}} \tag{3}$$

so a rise in thoracic elastance or a respiratory-muscle effort is felt by every lung compartment.
The chest-wall elastance is derived from a neonatal chest-wall compliance of 4.2 mL·cmH₂O⁻¹·kg⁻¹ [1] (≈ 52.5 mmHg·L⁻¹ for the modelled weight; source comment in `Respiration.js`).

**Gas state.** Total molar concentration follows the ideal-gas law and partial pressures follow
Dalton's law:

$$c_{\text{total}} = \frac{P}{R\,(273.15 + T)}\cdot 10^{3}\ \ [\mathrm{mmol\,L^{-1}}], \quad R = 62.36367\ \mathrm{L\,mmHg\,mol^{-1}\,K^{-1}} \tag{4}$$

$$P_X = \frac{c_X}{c_{\text{total}}}\cdot P, \quad F_X = \frac{c_X}{c_{\text{total}}} \qquad (X \in \{\mathrm{O_2}, \mathrm{CO_2}, \mathrm{N_2}, \mathrm{H_2O}, \text{other}\}) \tag{5}$$

Each compartment is warmed toward its target temperature and humidified toward the saturated
water-vapour pressure, with the accompanying ideal-gas volume changes:

$$P_{\mathrm{H_2O}}^{\text{sat}}(T) = \exp\!\left(20.386 - \frac{5132}{T + 273.15}\right)\ \ [\mathrm{mmHg}] \tag{6}$$

$$\mathrm{d}T = 0.0005\,(T_{\text{target}} - T); \quad \mathrm{d}V_{\text{thermal}} = \frac{c_{\text{total}}\,V\,R\,\mathrm{d}T}{P} \tag{7}$$

$$\mathrm{d(H_2O)} = 10^{-5}\,(P_{\mathrm{H_2O}}^{\text{sat}} - P_{\mathrm{H_2O}})\,\Delta t; \quad c_{\mathrm{H_2O}} \leftarrow \frac{c_{\mathrm{H_2O}}\,V + \mathrm{d(H_2O)}}{V} \tag{8}$$

Here d(H₂O) is the water vapour added over the step (mmol) and 10⁻⁵ a humidification rate constant, so the compartment's vapour pressure relaxes toward saturation — the increment self-limits as *P*_H₂O approaches *P*_H₂O^sat; the added vapour also expands the gas by *R*(273.15+*T*)/*P* · d(H₂O)/10³. Inflowing gas mixes by the incoming-volume fraction, as for blood (Eq. S2). A `fixed_composition` compartment (the atmosphere) holds composition and temperature constant, acting as an infinite reservoir.

#### 2.2.2 Ventilation: the spontaneous breathing drive

*Source: `explain/component_models/Breathing.js`, `explain/component_models/Respiration.js`.*

Spontaneous ventilation is generated from a target minute ventilation scaled to body weight and
modulated by autonomic drive:

$$\dot V_{E,\text{target}} = m_{\text{ref}}\,W\,(1 + (a_{\text{chemo}} - 1))\,a_{\text{ans}} \tag{9}$$

where *m*_ref is the reference minute volume (0.2 L·kg⁻¹·min⁻¹), *W* body weight (kg), *a*_chemo
the chemoreflex factor (`mv_ans_factor`, written by the autonomic model of the companion paper) and *a*_ans a tonic activity factor. Rate and tidal volume are partitioned by an inverted
Mecklenburgh relation [2] (rate rises as the square root of ventilatory demand):

$$\mathrm{RR} = \sqrt{\frac{\dot V_{E,\text{target}}}{k_{vt}\,W}}, \quad V_{T,\text{target}} = \frac{\dot V_{E,\text{target}}}{\mathrm{RR}} \tag{10}$$

with *k*_vt the tidal-volume/rate ratio (1.212×10⁻⁴). The breath is divided into inspiratory and
expiratory intervals by the inspiratory:expiratory ratio IE:

$$T_{\text{breath}} = \frac{60}{\mathrm{RR}}, \quad T_i = \mathrm{IE}\cdot T_{\text{breath}}, \quad T_e = T_{\text{breath}} - T_i \tag{11}$$

A respiratory-muscle pressure waveform is generated over the cycle: a linear ramp during
inspiration and a normalized exponential (Mecklenburgh) decay during expiration [2],

$$p_{\text{mus}} = \begin{cases} \varphi_i\,G & \text{(inspiration)} \\ G\,\dfrac{e^{-4\varphi_e} - e^{-4}}{1 - e^{-4}} & \text{(expiration)} \end{cases} \tag{12}$$

where φ_i, φ_e ∈ [0,1] are the fractional phase positions (step counter ÷ steps-per-phase) and
*G* is the muscle-pressure gain. *p*_mus is applied to the thorax (as an additive perturbation to
its elastance factor, Eq. 3), and the resulting airway flow is integrated to a measured tidal
volume. A slow integral controller adjusts *G* by ±0.1 per breath so the measured expiratory tidal
volume tracks *V*_T,target (bounded to [0, *G*_max]), letting the same drive model work under spontaneous breathing, CPAP and mechanical support. The expiratory volume sums flow across the natural (`MOUTH_DS`) and, when intubated, endotracheal (`VENT_ETTUBE`) inlets, so the feedback is airway-route-agnostic.

The `Respiration` model is a grouping controller (no physics of its own) mapping five clinical
"dials" — lung and thoracic elastance, upper- and lower-airway resistance, and gas-exchange capacity — onto the persistent factor layers of the corresponding compartments, applying changes as deltas so multiple controllers (e.g. Respiration and Surfactant) compose additively.

#### 2.2.3 Alveolar gas exchange and diffusion

*Source: `explain/base_models/GasExchanger.js`, `explain/base_models/GasDiffusor.js`,
`explain/base_models/BloodDiffusor.js`.*

Gas exchange across the alveolar–capillary barrier is partial-pressure-driven (a Fick flux) [3]. For
each gas the molar flux from blood to alveolar gas over one step is

$$\Phi_X = (P_{X,\text{blood}} - P_{X,\text{gas}})\,D_X\,\Delta t\ \ [\mathrm{mmol}] \qquad (X \in \{\mathrm{O_2}, \mathrm{CO_2}\}) \tag{13}$$

where *D*_X (mmol·mmHg⁻¹·s⁻¹) is the diffusion constant (carrying the factor composition of Eq. S1
via `dif_X_factor(_ps/_scaling)`). The exchanged moles update both compartments by conservation of mass on their volumes:

$$(t\mathrm{O_2})_{\text{blood}} \leftarrow \frac{(t\mathrm{O_2})_{\text{blood}}\,V_b - \Phi_{\mathrm{O_2}}}{V_b}, \quad c_{\mathrm{O_2},\text{gas}} \leftarrow \frac{c_{\mathrm{O_2},\text{gas}}\,V_g + \Phi_{\mathrm{O_2}}}{V_g} \tag{14}$$

and analogously for CO₂. Before each exchange the blood partial pressures are refreshed by the solver of Section 2.2.4. Conducting-airway gas transport (`GasDiffusor`) and blood–blood diffusion (`BloodDiffusor`, used for the placenta in the companion "other systems" paper) use the identical partial-pressure-driven flux and mass-conserving update (Eqs. 13–14); the blood diffusor additionally moves arbitrary solutes down their gradients.

The alveolar O₂ diffusion constant `dif_o2` is the primary calibration lever for arterial PO₂/SpO₂
(Section 2.4).

#### 2.2.4 Blood-gas transport and acid–base chemistry (keystone)

*Source: `explain/component_models/BloodComposition.js`. This solver is shared with the
circulation (companion cardiovascular paper) and every organ that carries blood. Its plasma
CO₂/pH/bicarbonate core is the white-box Stewart model we published previously [4]; it is summarized
here and extended with oxygen transport and a Haldane saturation–CO₂ coupling.*

Blood carries oxygen and carbon dioxide as total contents *t*O₂ and *t*CO₂ (mmol·L⁻¹) together with the plasma strong ions and buffers, and it does not merely hold these — it transports them. There is no separate transport solver: whenever the circulation advects a volume Δ*V* between two blood compartments (the resistor flows of the companion cardiovascular model), every transported content *X* of the receiving compartment (volume *V*, upstream content *X*_up) is updated by mixing on the incoming-volume fraction (shared Methods, Eq. S2; engine `BloodCapacitance.volume_in`):

$$X \leftarrow X + (X_{\text{up}} - X)\,\frac{\Delta V}{V}, \qquad X \in \{t\mathrm{O_2},\, t\mathrm{CO_2},\, [\mathrm{Na^+}], [\mathrm{K^+}], [\mathrm{Cl^-}], [\mathrm{Ca^{2+}}], [\mathrm{Mg^{2+}}], [\mathrm{lactate^-}], \text{buffers}\} \tag{15}$$

so the gas contents, the plasma strong ions and weak-acid buffers, and lactate are all carried between compartments as blood flows. The solver then acts, at each compartment and each step, on the contents delivered there — which is what makes the arterial blood gas a whole-body rather than a local property. It converts these, at compartment temperature *T* and haemoglobin concentration, into pH, PCO₂, bicarbonate, base excess, PO₂ and saturation, following the Stewart approach [5,6]: the independent variables are the strong-ion difference, total CO₂, total weak-acid buffers and total O₂; the dependent variables (pH, PCO₂, PO₂, SO₂) follow from chemical equilibrium and electroneutrality.

**Strong-ion difference.** The apparent strong-ion difference is

$$\mathrm{SID} = [\mathrm{Na^+}] + [\mathrm{K^+}] + 2[\mathrm{Ca^{2+}}] + 2[\mathrm{Mg^{2+}}] - [\mathrm{Cl^-}] - [\mathrm{lactate^-}] \tag{16}$$

so lactate enters directly as a strong anion — the coupling exploited by the metabolic-acidosis
model (Section 2.2.5).

**CO₂ speciation and buffering.** For a trial [H⁺], total CO₂ is partitioned among dissolved CO₂,
bicarbonate and carbonate, and buffered by albumin and phosphate (their pH-dependent weak-acid charge *A*⁻ [8]), exactly as in the published solver [4] — extended here with a **Haldane** term (coefficient λ, default 1.0) by which lower haemoglobin saturation raises CO₂-carrying capacity, using the previous step's saturation to break the O₂↔CO₂ circular dependence [7]. Carbon-dioxide tension follows from the dissolved CO₂ and its solubility.

**Electroneutrality.** pH is found as the [H⁺] that makes plasma electrically neutral:

$$g([\mathrm{H^+}]) = [\mathrm{H^+}] + \mathrm{SID} - [\mathrm{HCO_3^-}] - 2[\mathrm{CO_3^{2-}}] - [\mathrm{OH^-}] - A^- - [\mathrm{UMA}] = 0 \tag{17}$$

where UMA is the concentration of unmeasured/unidentified strong anions — the calibration lever for base excess and pH (Section 2.4). The dissociation constants, the bounded [H⁺] root-finder and the solver's verification against 1864 neonatal blood-gas samples are given in the published paper [4]. From the converged solution the base excess is computed by the Van Slyke expression [9]

$$\mathrm{BE} = \big([\mathrm{HCO_3^-}] - 25.1 + (2.3\,\mathrm{Hb} + 7.7)(\mathrm{pH} - 7.4)\big)(1 - 0.023\,\mathrm{Hb}) \tag{18}$$

(Hb in mmol·L⁻¹).

**Oxygen transport.** The oxygen–haemoglobin dissociation curve is a Hill relation [10] whose half-
saturation tension P₅₀ shifts with pH (Bohr effect), PCO₂, temperature and 2,3-DPG [7]:

$$\log_{10} P_{50} = \log_{10} P_{50,0} - 0.48\,\Delta\mathrm{pH} + 0.0015\,\Delta\mathrm{PCO_2} + 0.024\,\Delta T + 0.051\,\Delta\mathrm{DPG} \tag{19}$$

$$S_{\mathrm{O_2}} = \frac{P_{\mathrm{O_2}}^{\,n}}{P_{\mathrm{O_2}}^{\,n} + P_{50}^{\,n}}, \quad n = 2.7 \tag{20}$$

where ΔpH = pH − 7.40, ΔPCO₂ = PCO₂ − 40, Δ*T* = *T* − 37, ΔDPG = DPG − 5, and P₅₀,₀ is the
compartment's intrinsic O₂-haemoglobin affinity baseline (fetal haemoglobin 18.8, neonatal 20.0,
adult 26.7 mmHg — the mechanism by which fetal blood's higher affinity is represented). Total
oxygen content combines dissolved and haemoglobin-bound O₂,

$$t\mathrm{O_2} = (0.0031\,P_{\mathrm{O_2}} + 1.36\,\mathrm{Hb_{gdl}}\,S_{\mathrm{O_2}})\cdot 10\cdot\frac{760}{R\,(273.15 + T)} \tag{21}$$

with Hb_gdl = Hb/0.6206 the haemoglobin in g·dL⁻¹. For known *t*O₂ the arterial *P*_O₂ (and hence
S_O₂) is found by a second Brent root-find of Eq. 21, seeded from the previous *P*_O₂ ±10 mmHg.

This single solver makes gas transport a whole-body property: the same equations run in the pulmonary capillaries (loading O₂, unloading CO₂), the systemic tissues (the reverse), and every monitored blood compartment, so the arterial blood gas is an emergent consequence of ventilation, perfusion, diffusion and metabolism rather than a prescribed output.

#### 2.2.5 Metabolism and lactate

*Source: `explain/component_models/Metabolism.js`, `explain/component_models/Lactate.js`.*

Whole-body oxygen consumption V̇O₂ (default 8.1 mL·kg⁻¹·min⁻¹) [11] is converted to a molar demand per step and distributed across tissue compartments by each site's fractional share *f*_VO₂ (the
fractions sum to one across metabolically active sites):

$$\Delta\mathrm{O_2} = \frac{0.039\,\dot V\mathrm{O_2}\,a_{V\mathrm{O_2}}\,Q_{10}\,W}{60}\cdot\Delta t\ \ [\mathrm{mmol}] \tag{22}$$

where 0.039 mmol·mL⁻¹ is the molar O₂ content at 37 °C and atmospheric pressure, *a*_VO₂ an external demand factor and *Q*₁₀ the temperature factor [12] written by the thermoregulation model (companion paper; 1.0 at 37 °C). Each site's O₂ is decremented and its CO₂ incremented by the respiratory quotient RQ (default 0.8):

$$t\mathrm{O_2} \leftarrow \frac{t\mathrm{O_2}\,V - f_{V\mathrm{O_2}}\,\Delta\mathrm{O_2}}{V}, \quad t\mathrm{CO_2} \leftarrow \frac{t\mathrm{CO_2}\,V + \mathrm{RQ}\,f_{V\mathrm{O_2}}\,\Delta\mathrm{O_2}}{V} \tag{23}$$

When tissue oxygenation falls below an anaerobic threshold, lactate is produced in proportion to the local oxygen debt [13]. For each tissue an anaerobic fraction is computed relative to a threshold set at a fraction of the site's resting-minimum oxygen content *t*O₂,rest (captured over a 90 s warm-up so the model is neutral even in chronically hypoxaemic scenarios):

$$\Theta = \tau_{\text{frac}}\,(t\mathrm{O_2})_{\text{rest}}, \quad a = \mathrm{clamp}\!\left(\frac{\Theta - t\mathrm{O_2}}{\Theta},\, 0,\, 1\right) \tag{24}$$

$$L = a\,D_{\mathrm{O_2},\text{site}}\,Y\,g, \quad [\text{lactate}] \leftarrow [\text{lactate}] + \frac{L}{V} \tag{25}$$

where *τ*_frac = 0.5, *D*_O₂,site is the site's molar O₂ demand over the update interval, *Y* = 0.33
mmol lactate per mmol O₂ deficit (≈ 2 lactate/glucose over 6 O₂/glucose) and *g* a production gain. Lactate is cleared from every blood compartment by first-order relaxation toward a baseline (Cori-cycle/hepatic–renal handling):

$$[\text{lactate}] \leftarrow [\text{lactate}] + (L_{\text{base}} - [\text{lactate}])\,k_{\text{cl}}\,u \tag{26}$$

with *L*_base = 1.0 mmol·L⁻¹, *k*_cl = 2×10⁻³ s⁻¹ (t½ ≈ 6 min) and *u* the update interval.
Because lactate is a strong anion (Eq. 16), a rise lowers SID and hence pH, HCO₃⁻ and base excess: the oxygen-debt → lactate → metabolic-acidosis loop closes with no change to the solver itself. The models run in the fixed order Metabolism → Lactate → blood-composition so each step's oxygen extraction, lactate production and acid–base consequence are consistent.

#### 2.2.6 Surfactant and dynamic alveolar recruitment (RDS)

*Source: `explain/component_models/Surfactant.js`.*

Respiratory distress syndrome is modelled as a dynamic, pressure-driven balance between alveolar recruitment and derecruitment with hysteresis [14], modulated by surfactant maturity *s* ∈ [0,1] (0 = severe RDS, 1 = mature/treated). Surfactant therapy relaxes *s* toward its target with a time constant τ_surf (180 s, the acute recruitment response). The transpulmonary pressure signal is the mean alveolar recoil pressure over both lungs, low-pass-filtered to remove tidal swings:

$$\bar P_{\text{tp}} \leftarrow \bar P_{\text{tp}} + \frac{\Delta t}{\tau_p}\,(P_{\text{tp}} - \bar P_{\text{tp}}), \quad P_{\text{tp}} = \mathrm{mean_{lungs}}(p_{\text{in}}) \tag{27}$$

Opening and closing pressure thresholds are auto-centred on a baseline transpulmonary pressure *P*₀ (captured over a 30 s warm-up) and shifted down by surfactant, so that therapy lowers the pressure needed to recruit alveoli:

$$\mathrm{TOP} = P_0 + m_{\text{open}} - g_{\text{open}}\,(s - s_0), \quad \mathrm{TCP} = P_0 - m_{\text{close}} - g_{\text{close}}\,(s - s_0) \tag{28}$$

with margins *m*_open = *m*_close = 2 mmHg and gains *g*_open = 14, *g*_close = 12 mmHg per unit surfactant. The open fraction evolves by a recruitment/derecruitment ODE with a hysteresis dead zone (for TCP ≤ *P̄*_tp ≤ TOP both terms vanish and the open fraction holds):

$$\frac{\mathrm{d(open)}}{\mathrm{d}t} = k_{\text{open}}\,\max(0,\, \bar P_{\text{tp}} - \mathrm{TOP})\,(1 - \text{open}) - k_{\text{close}}\,\max(0,\, \mathrm{TCP} - \bar P_{\text{tp}})\,\text{open} \tag{29}$$

(*k*_open = *k*_close = 0.5 mmHg⁻¹·s⁻¹). The deviation of the open fraction from its baseline,
*r* = open − *f*₀, drives four effector channels — lung elastance, functional residual capacity
(unstressed volume), alveolar diffusion and intrapulmonary shunt — as bounded linear factors:

$$f_{\text{el}} = 1 - 0.7r, \quad f_{\text{uvol}} = 1 + 1.5r, \quad f_{\text{dif}} = 1 + 2.0r, \quad f_{\text{ips}} = 1 + 6.0r \tag{30}$$

so derecruitment (negative *r*) simultaneously stiffens the lung, lowers FRC, impairs diffusion and increases intrapulmonary shunt — the coupled signature of RDS — while surfactant or recruiting pressure reverses all four. The elastance, FRC and diffusion factors are written to the non-persistent factor layer (composing with the Respiration controller's persistent layer); the shunt uses the persistent resistance layer, released to unity when disabled.

### 2.3 Software implementation and code verification

See shared Methods S5 (reuse verbatim): framework-agnostic JavaScript/TypeScript engine running in a Web Worker, declarative JSON model definitions, real-time step loop, freely available at https://explain-modeling.com; the complete, annotated engine source code is publicly available at https://github.com/Dobutamine/explain-engine and archived with a persistent identifier at https://doi.org/10.5281/zenodo.21389097 [15]. The respiratory models run in the same insertion-ordered step loop as the circulation, sharing the blood compartments so that gas exchange, transport, metabolism and acid–base are solved together each step.

### 2.4 AI-assisted patient-specific parameterization (pointer)

Patient-specific respiratory and acid–base parameters are not hand-tuned but set by the AI-assisted closed-loop pipeline described in full in the companion parameterization paper [P6]: a large language model interprets the clinical targets into a validated specification, and a deterministic calibrator drives one physiologically interpretable lever per target to a clinician-meaningful tolerance. For this paper the levers are: **alveolar O₂ diffusion** *D*_O₂ (`dif_o2`) → arterial PO₂/SpO₂ (positive); **central ventilatory drive** *m*_ref (`minute_volume_ref`) → arterial PCO₂ (negative — lowering drive raises PCO₂ because the chemoreflex defends the setpoint); and **Stewart unmeasured anions** UMA (`uma`) → base excess and pH (negative). Default tolerances: PO₂ ±6, PCO₂ ±4 mmHg, pH ±0.03, base excess ±1.5 mmol·L⁻¹.

---

## 3. Results — illustrative simulations

Each experiment ran headlessly against the calibrated term-neonate baseline (`term_neonate.json`; for surfactant, the preterm 28-week RDS scenario `preterm_28wk.json`) using the shared-Methods harness and probe scripts (S7). The baseline was warmed to steady state and signals cycle-averaged over the reporting window; all values come from the engine's acid–base/oxygen solver (`BloodComposition.js`), not prescribed. Mechanism sweeps (§3.2–3.4) ran with the autonomic chemoreflex disabled to show the pure mechanism; in the closed loop the chemoreflex attenuates the CO₂ response — why ventilatory drive, not diffusion, is the PCO₂ lever (Section 2.4). Scripts: `probe_vitals.mjs` (§3.1), `probe_respiratory.mjs` (§3.2–3.4), `probe_surfactant.mjs` (§3.5).

### 3.1 Baseline gas exchange and acid–base status

The calibrated term neonate (3.5 kg, autonomic control active) reproduces a normal neonatal
respiratory and acid–base state (Table 1): a physiological respiratory rate and a normal arterial
blood gas, all within neonatal reference ranges.

**Table 1. Baseline term-neonate respiratory and acid–base state.** *(`probe_vitals.mjs
term_neonate --seconds 120`.)*

| Quantity | Value | Reference (neonate) |
|---|---|---|
| Respiratory rate | 41 min⁻¹ | 30–60 |
| SpO₂ (pre-ductal) | 96.9 % | 93–100 |
| End-tidal CO₂ | 35.8 mmHg | 35–45 |
| Arterial pH | 7.36 | 7.30–7.42 |
| Arterial PCO₂ | 39.8 mmHg | 35–45 |
| Arterial PO₂ | 74.8 mmHg | 50–85 |
| Bicarbonate | 22.4 mmol·L⁻¹ | 18–24 |
| Base excess | −3.0 mmol·L⁻¹ | −6 to +2 |

### 3.2 Oxygenation vs inspired oxygen fraction and alveolar diffusion

Stepping the inspired oxygen fraction raised arterial PO₂ monotonically with the expected saturating rise of SpO₂ (Table 2a), while PCO₂ was essentially unchanged — the model makes oxygenation, not CO₂ clearance, respond to FiO₂ (Eqs. 5, 13, 21–23). Independently scaling the alveolar O₂ diffusion constant gave a saturating rise in PO₂ from a diffusion-limited regime toward a perfusion/ventilation-limited ceiling (Table 2b), demonstrating the *D*_O₂ → PO₂/SpO₂ lever (Section 2.4).

**Table 2a. Arterial oxygenation vs FiO₂** (alveolar diffusion at baseline). PCO₂ held ≈ 39.8 mmHg
throughout.

| FiO₂ | PO₂ (mmHg) | SpO₂ (%) |
|---|---|---|
| 0.21 | 74.9 | 96.9 |
| 0.30 | 98.7 | 98.5 |
| 0.40 | 132.2 | 99.3 |
| 0.60 | 250.3 | 99.9 |
| 0.90 | 472.1 | 100.0 |

**Table 2b. Arterial oxygenation vs alveolar O₂ diffusion** (FiO₂ = 0.21; diffusion as a multiple
of baseline).

| *D*_O₂ (×base) | PO₂ (mmHg) | SpO₂ (%) |
|---|---|---|
| 0.25 | 59.3 | 94.5 |
| 0.50 | 69.9 | 96.4 |
| 1.0 | 74.3 | 97.0 |
| 2.0 | 76.3 | 97.2 |
| 4.0 | 77.3 | 97.3 |

### 3.3 Carbon dioxide vs ventilatory drive

Scaling the reference minute ventilation produced the expected inverse PCO₂–ventilation relation and respiratory pH shift (Table 3): lowering drive raised PCO₂ toward respiratory acidosis, raising it lowered PCO₂ into alkalosis, with respiratory rate tracking ventilation (Eqs. 9–11 with 13–18).

**Table 3. Arterial CO₂ and pH vs ventilatory drive** (minute-ventilation reference as a multiple of
baseline).

| Ventilation (×base) | PCO₂ (mmHg) | pH   | RR (min⁻¹) |
| ------------------- | ----------- | ---- | ---------- |
| 0.6                 | 42.2        | 7.35 | 39.6       |
| 0.8                 | 40.7        | 7.36 | 40.2       |
| 1.0                 | 39.6        | 7.38 | 40.4       |
| 1.3                 | 35.6        | 7.42 | 42.7       |
| 1.7                 | 32.1        | 7.46 | 45.0       |

### 3.4 Metabolic acid–base perturbation

Adding unmeasured strong anions (Eq. 16, the base-excess/pH lever) produced a graded metabolic acidosis — falling bicarbonate, base excess and pH — with secondary respiratory compensation (falling PCO₂) as open-loop ventilation responded (Table 4), demonstrating the Stewart solver's separation of the metabolic (SID/UMA → HCO₃⁻, BE) from the respiratory (ventilation → PCO₂) component. The same acidosis arises spontaneously from tissue oxygen debt via the lactate pathway (Eqs. 24–26), lactate entering Eq. 16 as a strong anion.

**Table 4. Metabolic acidosis from added unmeasured anions** (UMA increment above baseline,
mmol·L⁻¹).

| ΔUMA | pH | HCO₃⁻ (mmol·L⁻¹) | Base excess (mmol·L⁻¹) | PCO₂ (mmHg) |
|---|---|---|---|---|
| 0 (base) | 7.38 | 23.0 | −2.1 | 39.6 |
| +3 | 7.36 | 20.2 | −4.6 | 35.8 |
| +6 | 7.28 | 14.6 | −10.9 | 31.2 |

*(At increments beyond ≈ +6 mmol·L⁻¹ the pH falls outside the solver's dynamic root-finding window and the reported value becomes unreliable; the clinically relevant range is well within the
converging envelope — see §4.4.)*

### 3.5 Respiratory distress and surfactant therapy

In the preterm 28-week RDS scenario, surfactant (`administer_surfactant`, target maturity 0.9) drove progressive recruitment with coupled improvement in every downstream variable (Table 5): the open fraction rose from 0.50 to 1.0, lung elastance fell (compliance rose), diffusion rose and shunt fell, and arterial PO₂ rose from 54.6 to 74.3 mmHg with SpO₂ from 90.7 to 96.1 % (Eqs. 27–30). The response developed over minutes (τ_surf = 180 s) with the intended hysteresis; at baseline all four effector factors sat at unity, confirming neutrality in the untreated calibrated state.

**Table 5. Surfactant therapy time course** (preterm 28-week RDS; `probe_surfactant.mjs
--scenario preterm_28wk --target 0.9`).

| Phase | Surfactant | Open frac. | Lung elastance (mmHg·L⁻¹) | PaO₂ (mmHg) | PaCO₂ (mmHg) | SpO₂ (%) |
|---|---|---|---|---|---|---|
| Baseline RDS | 0.25 | 0.50 | 558 | 54.6 | 49.5 | 90.7 |
| + 60 s | 0.45 | 0.95 | 394 | 65.8 | 46.9 | 95.2 |
| + 180 s | 0.68 | 1.00 | 363 | 74.3 | 48.2 | 96.1 |
| + 420 s | 0.84 | 1.00 | 363 | 74.3 | 48.3 | 96.1 |

*(Non-invasive and mechanical ventilatory support of the RDS lung — CPAP, pressure support and the ventilator interaction with spontaneous breathing — are demonstrated in the companion devices paper, which owns the ventilator model.)*

---

## 4. Discussion

### 4.1 Model originality

The subsystem is distinctive less in any single component than in their integration. Its elements are grounded in established physiology — elastance-based gas compartments and chest-wall mechanics, Fick alveolar–capillary diffusion, a physicochemical (Stewart) treatment of acid–base equilibrium (published separately [4]), a Hill dissociation curve with the classical P₅₀-shifting factors, and a recruitment model of surfactant-dependent mechanics. What the model adds is to solve all of these together, in real time, in one set of blood compartments shared with the circulation. Because O₂ and CO₂ circulate as total contents and are converted to partial pressures, pH, bicarbonate and saturation everywhere blood exists, the arterial blood gas is not an assigned output but an emergent property: it moves only when ventilation, perfusion, diffusion or metabolism moves. That is what makes the model explanatory rather than descriptive.

A second, series-wide element of originality is how the model is fitted to a patient. Lumped-parameter models expose many parameters against few measurements, and hand-tuning is slow, irreproducible and hard to audit. Here the respiratory and acid–base parameters are set by the companion AI-assisted pipeline [P6]: a large language model interprets the clinical targets and a deterministic calibrator drives one interpretable lever per target onto its value. The lever structure is itself encoded physiology — alveolar diffusion drives oxygenation, ventilatory drive drives CO₂, and unmeasured strong anions drive base excess and pH — chosen to respect the model's control loops rather than fight them (Section 2.4). Because every automated adjustment uses the same bounded, schema-checked parameters as a manual edit, instantiation is rapid and reproducible.

### 4.2 Model validity

The simulations of Section 3 reproduce the expected qualitative and quantitative behaviour of neonatal respiratory and acid–base physiology. The calibrated baseline sits within neonatal reference ranges across the full blood gas (Table 1). Arterial oxygenation rises monotonically with inspired oxygen and with alveolar diffusing capacity, saturation approaching 100 % as the sigmoid curve dictates, while CO₂ is appropriately insensitive to FiO₂ (Tables 2a–b); CO₂ varies inversely with ventilation, carrying pH into respiratory acidosis or alkalosis (Table 3); added unmeasured anions produce a graded metabolic acidosis cleanly separated from the respiratory axis, with secondary respiratory compensation (Table 4); and in preterm RDS, surfactant recruits the lung over minutes, simultaneously improving compliance, diffusion, shunt and oxygenation — the coupled syndrome resolving as one process (Table 5). The parameter values are traceable to standard physiological sources (`_references.md`); where an engine constant departs from a common textbook value — the base-excess offset (25.1 vs 24.4) [9] or the fetal/neonatal/adult P₅₀ baselines (18.8, 20.0, 26.7 mmHg) [7] — we state the value used and cite its source.

These are demonstrations of mechanistic behaviour, not a formal clinical validation: this paper specifies the model and shows it behaves physiologically across its operating range; prospective validation of patient-specific fits against neonatal blood-gas data is future work (Section 4.5).

**Validation strategy (series).** This paper validates pulmonary gas exchange, blood-gas transport and
acid–base physiology in depth — the mechanisms, directions and magnitudes its account is built on — against
the cited literature. Consistent with the series' two-altitude design, comprehensive quantitative validation
of the AI-parameterized cohort as a whole, against published reference ranges and disease signatures, is
centralized in the integrated flagship [P5], and the identifiability and one-lever-per-target basis of the
parameterization is validated by the formal sensitivity analysis of [P6]. Validation throughout the series
is to literature ranges and pattern, not to prospective individual-patient data.

### 4.3 Reproducibility and model expansion

Every quantitative result is reproduced from the engine by a named script — the baseline blood gas by `probe_vitals.mjs`, the oxygenation/ventilation/acid–base sweeps by `probe_respiratory.mjs`, and the surfactant time course by `probe_surfactant.mjs` — so each figure and table can be regenerated and audited. The model is defined declaratively: complete scenarios are JSON model definitions, and each process is a small self-contained module implementing the equations of Section 2. New components — metabolic sites, alternative dissociation chemistry, further lung pathologies — are added as modules without modifying the engine, which is what let the respiratory subsystem be built on the same engine as the circulation.

### 4.4 Limitations

The model makes the simplifications characteristic of a real-time lumped-parameter approach. Gas exchange uses paired alveolar compartments rather than a continuous ventilation-to-perfusion distribution, so V/Q mismatch is captured only in aggregate (via intrapulmonary shunt and the two-lung split) and cannot reproduce the full shape of a shunt or dead-space curve [16]; dead-space and alveolar ventilation are not separately partitioned. Time integration is explicit forward-Euler at a fixed step for real-time performance; the acid–base and oxygen equilibria are solved to convergence each step by a bounded Brent root-finder, but at extreme acid loads the pH can fall outside its search window and become unreliable (Section 3.4) — the clinically relevant range lies well within the converging envelope. The Haldane/Bohr coupling uses the previous step's saturation to break the circular dependence, exact at steady state with a one-step lag in transients. Metabolism distributes a single whole-body oxygen-consumption figure by fixed fractional shares rather than deriving each organ's from its own work and perfusion.

### 4.5 Future work

Several extensions follow. Regional V/Q heterogeneity and an explicit dead-space/alveolar-ventilation partition would reproduce specific lung pathologies more faithfully. Coupling the respiratory model to the mechanical ventilator and ECMO — described in the companion devices paper, which reuses the gas-exchange equations derived here — will support closed-loop simulation of respiratory support and weaning. Building on the AI-assisted pipeline, a systematic sensitivity analysis would identify the most informative respiratory targets, and calibration could extend from one-lever-per-target toward joint optimization for coupled respiratory–circulatory configurations. Finally, prospective validation of patient-specific fits against neonatal blood-gas data would test quantitative accuracy beyond the mechanistic demonstrations here.

---

## Conclusion

The respiratory subsystem of EXPLAIN gives a transparent, real-time, mechanistic account of neonatal ventilation, alveolar gas exchange, blood-gas transport, acid–base chemistry, metabolism and surfactant-dependent recruitment. By solving these together in the same blood compartments as the circulation, it makes the arterial blood gas an emergent consequence of the physiology rather than a prescribed output, and by fitting patients through an AI-assisted closed-loop pipeline it removes the hand-tuning bottleneck. With its cardiovascular companion, it offers an integrated, interpretable, freely available platform for teaching and investigating neonatal cardiorespiratory physiology.

---

## References

*In order of appearance (Vancouver, matching the cardiovascular paper). Companion papers in the
series are cited as unnumbered tokens [P1]/[P5]/[P6]; they resolve to their final numbers at series
assembly. PMIDs verified against PubMed unless marked as a historical primary source or textbook.*

1. Papastamelos C, Panitch HB, England SE, Allen JL. Developmental changes in chest wall compliance in infancy and early childhood. *J Appl Physiol (1985).* 1995;78(1):179–84. PMID 7713809. doi:10.1152/jappl.1995.78.1.179.
2. Mecklenburgh JS, al-Obaidi TA, Mapleson WW. A model lung with direct representation of respiratory muscle activity. *Br J Anaesth.* 1992;68(6):603–12. PMID 1610636. doi:10.1093/bja/68.6.603.
3. Fick A. Ueber Diffusion. *Ann Phys.* 1855;170(1):59–86. doi:10.1002/andp.18551700105. *(Historical primary source; not in PubMed.)*
4. Antonius TAJ, van Meurs WWL, Westerhof BE, de Boode WP. A white-box model for real-time simulation of acid–base balance in blood plasma. *Adv Simul (Lond).* 2023;8(1):16. PMID 37322544. doi:10.1186/s41077-023-00255-2. *(The authors' prior publication of the plasma CO₂/pH/HCO₃⁻ Stewart solver summarized in Section 2.2.4.)*
5. Stewart PA. Modern quantitative acid–base chemistry. *Can J Physiol Pharmacol.* 1983;61(12):1444–61. PMID 6423247. doi:10.1139/y83-207.
6. Stewart PA. Independent and dependent variables of acid–base control. *Respir Physiol.* 1978;33(1):9–26. PMID 27857. doi:10.1016/0034-5687(78)90079-8.
7. Dash RK, Bassingthwaighte JB. Blood HbO₂ and HbCO₂ dissociation curves at varied O₂, CO₂, pH, 2,3-DPG and temperature levels. *Ann Biomed Eng.* 2004;32(12):1676–93. PMID 15682524. doi:10.1007/s10439-004-7821-6. *(Cite with the corrected-equations erratum: Ann Biomed Eng. 2010;38(4):1683–701. PMID 20162361. doi:10.1007/s10439-010-9948-y.)*
8. Figge J, Rossing TH, Fencl V. The role of serum proteins in acid–base equilibria. *J Lab Clin Med.* 1991;117(6):453–67. PMID 2045713.
9. Siggaard-Andersen O. The van Slyke equation. *Scand J Clin Lab Invest Suppl.* 1977;146:15–20. PMID 13478. doi:10.3109/00365517709098927.
10. Hill AV. The possible effects of the aggregation of the molecules of haemoglobin on its dissociation curves. *J Physiol.* 1910;40(Suppl):iv–vii. *(Historical primary source; not in PubMed. Hill coefficient n = 2.7.)*
11. Wahlig TM, Gatto CW, Boros SJ, Mammel MC, Mills MM, Georgieff MK. Metabolic response of preterm infants to variable degrees of respiratory illness. *J Pediatr.* 1994;124(2):283–8. PMID 8301440. doi:10.1016/s0022-3476(94)70321-3.
12. Schmidt-Nielsen K. *Animal Physiology: Adaptation and Environment.* 5th ed. Cambridge: Cambridge University Press; 1997. *(Textbook anchor for the Q₁₀ temperature coefficient / van 't Hoff principle; no single canonical PubMed paper.)*
13. Hall JE, Hall ME. *Guyton and Hall Textbook of Medical Physiology.* 14th ed. Philadelphia: Elsevier; 2021. *(Textbook anchor for anaerobic lactate production under oxygen debt and its Cori-cycle/hepatic–renal clearance; no single canonical primary paper.)*
14. Bachofen H, Schürch S, Urbinelli M, Weibel ER. Relations among alveolar surface tension, surface area, volume, and recoil pressure. *J Appl Physiol (1985).* 1987;62(5):1878–87. PMID 3597262. doi:10.1152/jappl.1987.62.5.1878.
15. Antonius T. *Explain: a whole-body physiological simulation engine* (Version v0.1.0) [Software]. Zenodo; 2026. doi:10.5281/zenodo.21389097 (concept/all-versions DOI). Source: https://github.com/Dobutamine/explain-engine (MIT). Interactive model: https://explain-modeling.com.
16. Wagner PD, Saltzman HA, West JB. Measurement of continuous distributions of ventilation–perfusion ratios: theory. *J Appl Physiol.* 1974;36(5):588–99. PMID 4826323. doi:10.1152/jappl.1974.36.5.588.

*Full working pool and provenance notes: `_references.md`. The AI-use disclosure and the
parameterization-method citations are carried by the companion paper [P6]; this paper's §2.4 is a
pointer, so it does not re-cite the LLM/software-method sources.*

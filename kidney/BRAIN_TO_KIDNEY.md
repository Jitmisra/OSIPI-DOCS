# BRAIN → KIDNEY: what transfers, what breaks, and how to build it

**osipy-qc renal migration analysis**
Agnik Misra · GSoC 2026 · OSIPI
Written 2026-07-31, against `osipy-qc` at `osipy_qc/` (20 registered checks, all passing)

---

## 0. Read this first

Three findings drive every decision in this document.

**1. The renal balance is the inverse of the brain balance.**
In brain, Stream B (the CBF map) carried the anchor metric: QEI has a published
formula, published constants and a published cut-off. In kidney, Stream B has
**no published quality threshold of any kind** — not one tSNR floor, CoV cutoff,
negative-voxel fraction or QEI analogue. What kidney *does* have is a hard,
fully-quotable **Stream A protocol specification**: 59 consensus statements with
per-statement agreement percentages, covering timing, geometry, M0, readout,
breathing, registration and reporting. So the renal work should start where the
evidence is, and Stream A is where the evidence is.

**2. Stream A needs no segmentation. Stream B is blocked on one.**
Every renal Stream B check that matters (cortical RBF, cortex CoV, cortex:medulla
ratio, a QEI analogue) requires a cortex/medulla segmentation. No released tool
produces one. `UKRIN-MAPS/ukat` — the flagship renal MRI toolbox and the only
software listed on renalmri.org — has `ukat/segmentation/whole_kidney.py` and
nothing else; it promised automated cortico-medullary segmentation "by the end of
2021" in ISMRM abstract 3765 and it is still absent at `master`. TotalSegmentator's
`total_mr` task emits `kidney_right` and `kidney_left` only. The one published
automatic renal-ASL cortex pipeline (Bones et al., MRM 2022, a 3-U-net cascade)
released no code and no weights, and achieved Dice 0.78 ± 0.04 against a reference
that a *second human observer* matched at only 0.77 ± 0.02. Meanwhile the renal
consensus itself says manual ROI selection is the default (R9.10, 100% agreement).

**3. For kidney, acquisition context dominates population.**
In brain, one population axis (adult / neonate) was enough to key the bands. In
kidney it is not, and by a wide margin:

| Factor | Effect on cortical RBF | Evidence |
|---|---|---|
| **Labelling scheme** | **~80 %** | FAIR 362 ± 57 vs pCASL 201 ± 72 mL/min/100 g in the **same 16 subjects, same session** (Harteveld, MAGMA 2019) |
| Age (adult) | ~20 % | 383.9 ± 61.8 (< 40 y) vs 306.1 ± 41.3 (≥ 40 y), 1.5 T single-delay (Garcia-Ruiz, JMRI 2025) |
| Field strength | ~11 % | 347.7 ± 54.1 @1.5 T vs 310.6 ± 52.7 @3 T, **same subjects, same day** (Garcia-Ruiz) |

A single unconditioned renal CBF band is therefore not merely uncalibrated — it is
*incoherent*. Renal bands must be keyed on **labelling scheme × field strength**
before population is even considered. This is the single most important
architectural consequence in this document, and §2 is designed around it.

> **Positioning.** OSIPI's own 2023–2025 roadmap states: *"We do not have any
> pipelines able to process human clinical non-brain data. While there are many
> applications for kidneys, liver ... there is no large free available pipeline for
> processing these data."* TF2.2 (led by María) is explicitly soliciting non-brain
> code; TF1.1 (co-led by Sudipto) is expanding to body ASL. `ukat`'s entire QA
> subpackage is one `snr.py`. There is no renal ASL QC tool. A Europe PMC sweep run
> for this document (July 2026) returns no automated renal quality index and no
> renal pass/fail threshold. This is genuinely greenfield, and both mentors own the
> task forces it belongs to.

---

## 1. The transfer table

Checks enumerated from the registry, not from memory:
`grep -rn "@register_qc_check" osipy_qc/` → 20 checks.

Verdicts:
**AS-IS** = works on kidney unchanged (after a parameter rename where noted)
**NEW BANDS** = identical maths, different thresholds
**REWORK** = concept applies, implementation assumes brain anatomy
**NO** = does not transfer

### Summary

| | Count | Checks |
|---|---|---|
| AS-IS | 5 | 2.3, 5.2, 5.3, 6.3, 6.5 |
| NEW BANDS | 7 | 3.1, 3.3, 3.5, 4.1, 5.1, 6.1, 6.2 |
| REWORK | 6 | 2.1, 2.2, 3.2, 4.2, 7.1, 8.2 |
| NO | 2 | 1.qei, 3.4 |

Note the shape of that: **11 of 20 need no new science** (AS-IS + most of the
NEW BANDS group is Stream A metadata). The 6 REWORK checks are where the
engineering is. The 2 NO checks are where the honesty is.

---

### 1.qei — Quality Evaluation Index → **DOES NOT TRANSFER**

The prompt asks specifically about this one, so it gets the full treatment.

QEI is three terms combined by a geometric mean. Take them one at a time.

**ρ (structural similarity).** Brain builds a template
`spCBF = 2.5·GM_prob + 1.0·WM_prob` and correlates the smoothed CBF against it.
Two things make that work: continuous tissue *probability* maps exist as routine
pipeline output, and the 2.5 : 1 coefficient ratio approximates the GM:WM CBF
ratio, which is stably 2–4 across the literature.

Neither holds for kidney.
- **No cortex/medulla probability maps exist.** Not in `ukat`, not in Renal
  Segmentor, not in TotalSegmentator, not in any released renal tool. Renal
  Segmentor *can* emit a per-voxel probability (`binary=False`) — but of the
  **whole kidney**, which is a single compartment and cannot form a two-tissue
  contrast template.
- **The coefficient ratio is not knowable.** The renal analogue of 2.5 : 1 would
  be the cortex:medulla perfusion ratio. Published values span **1.10 to ~8.5**,
  and the low end is *known segmentation failure*: Hammon 2016 reports cortex 337
  / medulla 280 and states outright that "the segmentation of the medulla may
  contain parts of the cortex"; Shirvani 2019 reports 1.10 with renal T1 values of
  799.6 (cortex) vs 807.1 ms (medulla) at 3 T, against Gillis's measured medullary
  T1 of 1651 ± 86 ms — their T1-threshold segmentation was not separating the
  compartments at all. You cannot fit a template coefficient to a quantity whose
  spread is dominated by mask error.

**DI (dispersion index).** Brain pools within-tissue variance across **three**
compartments (GM, WM, CSF at prob > 0.7) and normalises by mean GM CBF. Kidney has
**two** candidate compartments, and the renal consensus declares one of them
unreliable: *"Medullary renal blood flow values are not considered reliable with
current measurement approaches"* (R10.2, 89 % agreement) — corroborated by
measurement, with medullary test-retest ICC of **0.08** at 3 T multi-delay
(Garcia-Ruiz). There is no CSF analogue. A pooled within-tissue variance over two
compartments, one of which is noise, measures segmentation error rather than image
quality.

**p (negative fraction).** This one *does* transfer — negative perfusion is
non-physical in kidney too — and it is already a separate check (`3.3`).

**The constants and the cut-off.** `qei_a … qei_f` and the ≈ 0.5 cut-off were fit
to expert-rated **brain** data. No rater-labelled renal ASL cohort exists. Refitting
them is a multi-year data-collection project, not a GSoC task.

**Decision: do not ship a renal QEI, and do not fake one.** The three components
that *are* computable ship as separate, independently-reported checks and are
**never combined into a single score** — because the geometric-mean combination and
its cut-off are precisely what carry the calibration, and neither exists for kidney.
A number called "renal QEI" with invented constants would be the single worst thing
this project could produce.

**What a real renal QEI would need** (worth putting to Sudipto directly, since he
built the brain one): (i) a cortex/medulla segmentation with published accuracy
better than inter-observer agreement, (ii) the renal equivalent of his rater-labelled
training set, sized for ROC derivation of a cut-off, (iii) a ruling on whether medulla
can enter the model at all given R10.2. Item (ii) is the same work he did for brain,
and it is the blocker.

> The existing `ORGANS["kidney"]` stub already lists `1.qei` in `skip_checks` with
> the right reason. This analysis confirms that call and sharpens it: it is not just
> the template that fails, it is the template **and** the dispersion term **and** the
> calibration.

---

### 2.1.spatial_cov — spatial CoV in GM → **REWORK**

Two independent failures, either of which alone is disqualifying.

**The mask.** sCoV is defined over a GM mask. Run it on a whole-kidney mask and the
**normal** cortico-medullary gradient (cortex is 2.3–2.6 × medulla in the
best-segmented studies) guarantees a large CoV by construction — you would be
measuring anatomy, not artefact, and every healthy kidney would flag. The check only
means anything restricted to **cortex**, which returns us to the segmentation blocker.

**The bands.** `scov_vascular = 0.67` / `scov_artifact = 1.0` are ExploreASL brain
conventions (and the citation chain for 0.67 is already flagged as broken in
`config.py`). A Europe PMC sweep for spatial coefficient of variation combined with
renal/kidney/arterial-spin returns hits that are **without exception cerebral** —
including the ones matching on "kidney", which are brain perfusion studies in
kidney-transplant recipients. **No renal sCoV has ever been published.**

**Renal version:** `2.1K.cortex_cov`, computed in a supplied cortex ROI, reported as
**INFO with no verdict**. There is nothing to threshold against, and inventing a
tier boundary here would repeat exactly the error the brain histogram check was
rewritten to avoid.

---

### 2.2.snr — spatial SNR + tSNR → **REWORK**

The check does two different things and they transfer differently.

**Spatial SNR** is `1/sCoV` in GM — inherits every problem of 2.1, plus the implicit
band `snr > 1/0.67` which has no renal basis. Same rework.

**tSNR** — `mean_t / std_t` over a mask — is mathematically organ-agnostic. The
`brain=` parameter is already just a boolean array; rename it `roi=` and the code is
unchanged. Renal reference values exist and are worth reporting:

| Source | ROI | tSNR | Conditions |
|---|---|---|---|
| Garcia-Ruiz 2025 | cortex | 3.54 ± 0.71 / 3.33 ± 0.54 | 1.5 T / 3 T, PCASL, SE-EPI, BS |
| Garcia-Ruiz 2025 | medulla | 2.00 ± 0.44 / 1.89 ± 0.41 | same |
| Buchanan 2018 | cortex | 1.5–2.6 | 3 T FAIR, by readout (bFFE/GE-EPI/SE-EPI/TSE) |
| Bones 2019 | whole kidney | 0.60 ± 0.15 → 0.93 ± 0.22 | 1.5 T pCASL, no BGS → heavy BGS |

**These cannot become a threshold**, for a reason that is easy to miss: they use
**incompatible definitions**. Buchanan's tSNR is per-ASL-pair (SD across 25 pairs),
so the averaged map is ~√25 ≈ 5 × higher; Garcia-Ruiz's is of the averaged perfusion
signal. Same word, ~7 × spread, and several have SDs approaching their means
(bFFE 2.4 ± 2.0). Report tSNR with its definition and acquisition context attached;
grade nothing.

---

### 2.3.histogram — skewness + negative fraction → **TRANSFERS AS-IS**

The one check whose renal justification is **stronger** than its brain one.

It is already INFO-only with no thresholds — the exact posture the renal evidence
demands — so there is nothing to recalibrate. Swap the GM mask for a generic ROI and
the body is unchanged.

And the renal consensus explicitly asks for what it reports: *"a minimum of mean and
standard deviation of cortical renal ASL perfusion values should be reported ... The
median should also be considered in the presence of skewed RBF distributions."*
That is a published mandate to report distribution shape. The brain version had to
justify its own existence after its skewness cutoff was removed; the renal version
is directly requested by the consensus. Ship it unchanged.

---

### 3.1.cbf_level — absolute GM/WM CBF → **NEW BANDS**

Identical maths: mean within two compartments, compared against bands, worst-of.
Everything else changes.

- **Compartments** GM/WM → cortex/medulla, and per R10.2 the **medulla arm reports
  INFO, never a graded verdict**.
- **Reporting granularity** — R10.1 (100 % agreement, 0 % abstentions): *"Cortical
  renal blood flow values (not whole-kidney) should be reported, separately for left
  and right kidney."* Two verdicts per subject, not one. See §2.5.
- **Bands.** The temptation is Odudu's 139–427 mL/100 g/min. **Do not use it as a
  PASS band.** It is the spread of *study-level means* across 53 heterogeneous
  studies, and the patient range (83–412) overlaps it almost entirely — it has
  essentially no discriminative power and would pass anything. The one
  individual-subject range available is Olsen 2025's PET-validated 150–422
  **mL/min/100 mL** (note: per volume, not per gram — do not silently pool with
  per-100 g figures).
- Consequently the renal `3.1K` should follow the **`3.5.brain_cbf` doctrine**, not
  the `3.1` doctrine: grade gross implausibility only, report the mean, and let the
  reader judge it against their own cohort. The one usable bound is Gullaksen 2023's
  voxel rejection rule, *"Perfusion values outside 0–500 ml (100 g)⁻¹ min⁻¹ were
  rejected"* — IMPLEMENTATION tier, and note it is **two-sided**, its lower half
  being the direct analogue of the negative-voxel check.

---

### 3.2.gm_wm_ratio — GM/WM contrast → **REWORK** (arithmetic transfers; meaning does not)

The prompt is right that cortex:medulla is the natural analogue. The arithmetic is
literally unchanged. But the check's **role inverts**, which is more than a band
change.

In brain, a low GM/WM ratio is a soft finding about the *map* (weak contrast, maybe
smoothing, maybe pathology). In kidney, a ratio near 1 is a reliable signature of a
**broken cortico-medullary segmentation** — Hammon admits it in print, and Shirvani's
implausible T1 values demonstrate it independently. So the renal check reports on the
**mask**, not the map.

Three further constraints:
- The denominator is a quantity the consensus says not to report (R10.2).
- It is **not** a disease discriminator: across the two staged CKD cohorts it moves
  in **opposite directions** (Zhang 1.57 → 2.01 rising with CKD stage; Shi 2.08 →
  1.75 falling with severity). Anything that moves both ways with disease cannot
  carry a verdict.
- Published spread 1.1 → 8.5 is driven by ROI convention (Haddock's strict
  inner-medulla ROI gives 7.2; whole-medulla ROIs give 2.3–4.9).

**Renal version:** `4.4K.cortex_medulla_ratio`, filed under **coregistration/mask
integrity** rather than CBF level, INFO or WARN only, **never FAIL**, with an
UNCALIBRATED trip point around ~1.5 whose only justification is "physiology says this
should be ≥ 2 and the studies reporting ~1.1 admitted their masks were leaking".

---

### 3.3.negative_gm — negative-voxel fraction → **NEW BANDS**

Concept and maths transfer cleanly: negative perfusion is non-physical in kidney too,
and Gullaksen's `0–500` rejection rule is the closest published renal analogue with
its lower bound at exactly this. Mask becomes cortex.

Honest about the "new bands": there is **no renal number**. `neg_gm_warn = 0.10` /
`neg_gm_fail = 0.20` are already UNCALIBRATED in brain and stay UNCALIBRATED in
kidney. They may need loosening — renal ASL has far worse temporal SNR than brain and
free breathing is the *recommended* condition (R7.4), so a higher negative fraction is
expected — but nothing published says by how much. Keep `provisional=True`.

---

### 3.4.deep_gm_ratio — deep vs cortical GM → **DOES NOT TRANSFER**

Explicitly a neonatal **brain** finding (Miranda 2006: basal ganglia + thalami vs
cortical GM). No renal structure corresponds; the renal cortex/medulla relationship is
already covered by 3.2's replacement. Under the organ dimension this must return
`N/A` for `organ != "brain"` — not be silently dropped, so the report stays honest
about why it did not run.

---

### 3.5.brain_cbf — whole-brain implausibility over a self-derived mask → **NEW BANDS**

**This is the most transferable Stream B check, and it is the template for the entire
renal Stream B.** Read its docstring: it refuses to grade against a normal range
because none is published and because the mask percentile moves the number by ~18
mL/100 g/min. It grades only "can this be a quantified perfusion map at all".

That reasoning is the renal situation exactly, only more so. And a whole-kidney mask
is the one segmentation that **is** available today (Renal Segmentor, Dice 0.93 ± 0.01,
< 10 s, wrapped by `ukat`, and it can emit a probability map).

**Renal version:** `3.5K.kidney_perfusion` over a whole-kidney mask, absurd bounds
`0` and `~500–600`. Unlike the brain version's invented 300, the upper bound has an
IMPLEMENTATION source (Gullaksen 2023, reused by Olsen 2025). Carry the unit caveat
in the metric: per 100 mL vs per 100 g are not interchangeable without a density
assumption.

---

### 4.1.coregistration — Dice / Jaccard → **NEW BANDS**

The maths is genuinely organ-agnostic — `np.asarray(mask, dtype=bool)`, a shape
check, then set overlap. ASLPrep's own `dice` docstring says its inputs *"Can be any
type but will be converted into binary."* Nothing brain-specific.

Two changes:
- **Invocation multiplicity.** R8.1: *"the kidneys should be registered separately if
  using rigid/affine transformations as they move independently."* The check runs
  twice, once per kidney. See §2.5.
- **Bands.** `dice_pass = 0.9` would fail every published renal segmentation. Bones
  2022 reports whole-kidney Dice 0.80–0.93 and **cortex 0.75–0.78, against
  inter-observer 0.77**. A renal PASS bar above ~0.75 is asking for better-than-human
  agreement. Both cutoffs stay UNCALIBRATED; the renal ones must be lower and the
  metric should carry the inter-observer figure so a reader can see what "good" means
  here.

---

### 4.2.coverage — fraction of the tissue ROI covered by CBF data → **REWORK**

The arithmetic is pure set fraction and transfers. The **denominator changes meaning**,
which breaks the check.

In brain, a T1-derived GM mask *should* be almost fully covered; a shortfall means a
cropped FOV, which is a bug. In kidney the consensus default readout is **2D
single-slice** (R6.1, 95 % agreement) — so a whole-kidney ROI is *deliberately* mostly
uncovered. Running the brain check with `coverage_warn = 0.90` would FAIL every
consensus-compliant renal acquisition by construction.

**Renal version:** split into two metrics.
1. *In-slab coverage* — fraction of the ROI **within the acquired slices** that has
   data. This preserves the original intent (catch the mask/FOV mismatch) without
   penalising a deliberate single-slice protocol.
2. *Usable-slice count* — with a real reference distribution: Olsen 2025 found only
   **48.3 %** of 60 single-kidney scans retained all 5 slices (1 slice 1.7 %, 2 slices
   8.3 %, 3 slices 16.7 %, 4 slices 25.0 %). That is a genuine, published,
   directly-comparable renal QC quantity — and it argues for **per-slice verdicts**,
   not just per-scan.

---

### 5.1.schema — BIDS sidecar validation → **NEW BANDS** (new required-field set)

Key-presence checking transfers trivially. The required set does not, and the
surrounding standard is worse than the brain case.

**BIDS does not cover the kidney.** Verified against the released spec (v1.11.1,
schema 1.2.1): `kidney`, `renal` and `abdom` appear **0 times**; `brain` appears 64.
No numbered BEP covers body/abdominal/renal imaging (issue #1569, "BIDS-BodyPart
extracted from BEP025", is open but unnumbered). The `cbf` volume_type is defined as
*"the cerebral blood flow (CBF) image"* citing the brain White Paper — there is no
RBF equivalent. The `voi` entity, which would force `BodyPart` to be declared, is
MRS-only. **`BodyPart` (optional, free-text, DICOM 0018,0015) is the single legal hook**
for recording "kidney".

So the renal schema check has two jobs:
1. Validate what BIDS *does* cover, with an organ- **and labelling-conditional**
   required list. Note the brain check currently requires `PostLabelingDelay`
   unconditionally, which is wrong even for brain PASL and would fail every renal
   FAIR acquisition — FAIR uses TI/TI1 and `BolusCutOffFlag`.
2. Check the **Nery Table 4 minimum reporting set** (25 items, 81–100 % agreement) as
   a clearly-labelled **non-standard extension**, because roughly 8 of its items have
   no BIDS field at all: physiological triggering/gating, fat suppression, pixel
   bandwidth, slice gap, field of view, number of slices, image orientation,
   quantification model. Plus organ, laterality and breathing strategy.

> **Free upstream contribution, unreported and renal-relevant.** `LabelingSlabThickness`
> tells you to enter `0` for non-selective FAIR, but the schema sets
> `exclusiveMinimum: 0`. I traced this through `bids-validator`
> (`src/schema/applyRules.ts` passes the raw metadata object into ajv), so a `0` raises
> a validation error. Zero existing BIDS issues mention the field. FAIR is the dominant
> renal PASL variant, so this sits squarely on the renal path — a cheap, visible first
> PR to the standard.

---

### 5.2.volume_integrity — even volume count → **TRANSFERS AS-IS** (+ one additive renal rule)

The even/odd pair rule is arithmetic on a volume count. Unchanged.

**Add** an organ-conditional minimum: ≥ 20 ASL pairs (R4.7 FAIR 89 %, R5.12 pCASL
83 %). **Gate it on readout.** The Discussion scopes it: *"a minimum of 20 ASL pairs
... when using the recommended 2D readout is advised"*. Both paediatric renal studies
used 3D-GRASE — one with 6 pairs, explicitly trading SNR for a 3 min 42 s scan
instead of 12 min 6 s in children. An ungated `n_pairs ≥ 20` rule would flag
legitimate 3D and paediatric acquisitions.

---

### 5.3.swap — control brighter than label → **TRANSFERS AS-IS**

Mean of the even slab vs the odd slab. Organ-agnostic, and the renal signal is
*larger* than brain's: renal cortex perfusion-weighted signal is ~3–5 % of the control
image (measured 2.95–3.09 % cortex) against ~1 % in brain.

Two coverage notes rather than transfer problems: BS is *recommended* for renal ASL
(R7.2, 80 %), and the check already returns `N/A` when BS is on — so it will be N/A on
most consensus-compliant renal data. And the "even = control" assumption is equally
unfounded in kidney, since renal data ships without `aslcontext.tsv` too.

---

### 6.1.m0_present → **NEW BANDS** (the "band" is verdict severity)

Same maths, harder verdict. Brain BIDS permits `M0Type: "Absent"`, so the brain check
WARNs. The renal consensus is categorical: **R9.1 "M0 acquisition is mandatory"**
(94 % agreement, 0 % abstentions), reinforced in the body text as *"a mandatory step
for ASL quantification"*. For kidney this should be a FAIL, or at minimum a
non-provisional WARN. This is one of the few places where a renal check is legitimately
**stricter** than its brain counterpart, with a citation.

---

### 6.2.m0_tr — TR ≥ 5 s + T1 correction → **NEW BANDS**

The threshold does not change and gets *stronger*. Nery 2020 states the same rule and
the same equation verbatim: *"If this image is acquired without waiting for a
sufficiently (> 5 s) long recovery time (TR), the SI_PD should be corrected for
incomplete relaxation using the equation: SI_PD,corr = SI_PD / (1 − exp(−TR/T1,tissue))"*.
So `m0_tr_min_s = 5.0` is now PUBLISHED for **both** organs.

What changes is `t1_tissue_s`. Brain uses 1.4 s (already flagged UNCALIBRATED because
it matches no published value). The consensus supplies **no kidney number at all** —
it says only *"where T1,tissue is an estimate of the kidney T1"*, a genuine gap in the
consensus. Available values:

| | 1.5 T | 3 T |
|---|---|---|
| Cortex | ~0.83–1.08 s (Garcia-Ruiz measured 1.023 ± 0.039) | ~1.12–1.41 s (measured 1.356 ± 0.039) |
| Medulla | ~1.05–1.43 s | ~1.39–1.69 s |

The correction factor therefore becomes **organ × field-strength × compartment**
dependent, with a 300–400 ms cortex/medulla gap. Concretely: `t1_tissue_s` must stop
being one scalar.

---

### 6.3.m0_no_bs → **TRANSFERS AS-IS** ⚠️ read the warning

The cleanest transfer in the set. Nery 2020: *"The PD image should be acquired without
labelling or BGS and using a similar readout and acquisition parameters, with the
exception that a long TR should be used."* **Identical rule, identical direction**, and
it upgrades from PUBLISHED-for-brain to PUBLISHED-for-both.

> ⚠️ **Do not "fix" this check for kidney.** A plausible-sounding but wrong reading is
> circulating: *"background suppression is recommended for renal ASL (R7.2), which is
> the opposite of the brain M0 rule."* It is not the opposite. R7.2 recommends BS on
> the **ASL label/control pairs** — and so does the brain White Paper. The rule that
> M0 must be acquired **without** BS is the same in both organs. Inverting this check
> for kidney would break a working, doubly-published check. Flagging it here because
> it is exactly the kind of thing that gets "corrected" during a port.

---

### 6.5.m0_geometry → **TRANSFERS AS-IS**

`tuple(m0_shape[:3]) == tuple(asl_shape[:3])`. There is nothing organ-specific in a
shape comparison. Renal relevance is if anything higher, since the consensus advises
smoothing the M0 to blunt ASL↔M0 misregistration.

---

### 7.1.motion — framewise displacement + DVARS → **REWORK** (the biggest one)

FD as implemented is
`|Δtx|+|Δty|+|Δtz| + R·(|Δrx|+|Δry|+|Δrz|)` with `R = 50 mm`. Four independent
reasons this does not describe a kidney.

1. **`head_radius_mm = 50` is literally a head.** Power 2012 defines it as displacement
   on the surface of a 50 mm sphere. A kidney is ~11 × 5 cm and is not the object being
   rotated — the *subject* is.
2. **Kidney motion is strongly anisotropic.** 4D-CT over 39 kidneys: cranio-caudal
   11.1 ± 4.8 mm (range 2.5–20.5), antero-posterior 3.6 ± 2.1, right-left 1.7 ± 1.4 —
   a CC : AP : RL ratio of roughly 6.5 : 2 : 1, and respiration-locked at r > 0.97. FD
   collapses all three axes into one scalar, averaging away the only axis that matters.
3. **The two kidneys move independently** (R8.1). One 6-DOF parameter set cannot
   describe both. Motion must be estimated *and graded* per kidney.
4. **Motion is the operating condition, not the defect.** Breath-hold is *not*
   recommended (R7.3, 94 %); free breathing is preferred (R7.4, 76 %). The brain framing
   "motion is a fault" becomes "motion is expected — did registration and outlier
   rejection handle it?"

And the empirical point: **no renal paper applies FD or DVARS.** A Europe PMC query
combining framewise displacement with kidney/renal MRI and excluding brain returns
**zero** hits; every FD/DVARS hit alongside renal terms is a brain study in CKD
patients.

**What replaces it — and this is the good news.** R8.2 mandates outlier rejection
(100 % agreement, 0 % abstentions) with no method specified, and the literature
supplies four concrete, implementable rules:

| Rule | Source | Notes |
|---|---|---|
| Reject a subtraction if **> 20 % of kidney voxels exceed ± 2 SD** of the voxel-wise temporal mean | Harteveld, MAGMA 2019 | Fired in 18/27 FAIR and 19/27 pCASL datasets — *normal* data, max 2 pairs excluded per delay |
| Keep ΔM only if **> 80 % of voxels within ± 2 SD** | Bones, MRM 2021 | Authors call it "an empirically chosen threshold" |
| Reject if **> 20 % of voxels beyond ± 1.5 SD** | Harteveld, MAGMA 2021 (paediatric) | ~1 of 10 pairs rejected |
| ± 2 SD time-series + **per-slice fit RMSE > mean + 2 SD** | Garcia-Ruiz, JMRI 2025 | 1 % of slices excluded |

**Renal version:** `7.1K.outlier_rejection` — an image-derived rejection *rate*, not a
parameter-derived displacement. `dvars()` itself is organ-agnostic (RMS of the
frame-to-frame difference in a mask) and survives as an INFO metric. Note carefully
from Harteveld that the rule firing is *not* a defect signal — only the count matters,
and the observed ceiling in good data is 2 pairs per delay.

---

### 8.2.data_type — vendor / readout / structure inference → **REWORK**

The concept — infer everything from shapes and filenames because there is no metadata
— is *more* necessary for kidney than for brain. The vocabulary is brain-flavoured.

- **`t1` role tokens** are `mprage, t1, anat, struct`. Renal structural imaging is
  typically **T2-weighted HASTE** (Renal Segmentor is a T2w CNN; `ukat`'s
  `whole_kidney.py` takes a T2-weighted FSE). Generalise `t1` → `structural` with
  organ-conditional tokens.
- **`guess_readout`'s `slice_mm >= 5.0 → 2D`** mis-classifies renal data outright: the
  consensus gives 2D slice thickness **4–8 mm** (R6.8) and 3D **3–6 mm** (R6.9) — the
  bands **overlap**, so thickness alone cannot separate them.
- **New things to detect**, all renal-specific and all consequential downstream:
  laterality (left/right), native vs transplant, FAIR vs pCASL, breathing strategy,
  and **coronal-oblique orientation from the NIfTI affine** (R6.7, 93 % — and its
  rationale is dual: FAIR needs it to avoid labelling inflowing vessels, *and* it keeps
  respiratory motion in-plane so 2D registration can correct it).
- **Organ detection itself belongs here.** This is already the check that infers
  context with no metadata; inferring organ from affine orientation, FOV and filename
  is the natural extension, and it is what lets `run_qc` pick a profile automatically
  rather than requiring the user to declare it.

---

## 2. Architecture

### 2.0 The current state, precisely

- `cfg.organ` exists (`QCConfig.organ: str = "brain"`), and **no check reads it**.
  Grepping outside `config.py`, it appears only in `api.py` (echoed into report
  metadata, twice) and `report_html.py` (printed in a header). It is decorative.
- `ORGANS` / `skipped_for_organ()` exist, but **`run_qc` never calls them**. The skip
  list is advisory: the caller must translate it into `run_qc(checks=[...])` by hand.
  Nothing in the package does.
- The registry key is a bare name; `register_qc_check` raises on a duplicate, so
  organ-variant checks cannot share an ID.
- `QCConfig` is one flat dataclass, ~40 fields, brain-named throughout (`gm_cbf_lo`,
  `wm_cbf_hi`, `scov_vascular`, `head_radius_mm`).

So this is the moment to design it, before there are two organs' worth of code
depending on the wrong shape.

### 2.1 Registry: declare organs on the decorator

```python
def register_qc_check(
    name: str,
    stream: str | None = None,
    required: bool = True,
    organs: tuple[str, ...] = ("brain",),   # deliberately narrow default
) -> Callable:
```

**The default is `("brain",)`, not "all organs", and that is a safety decision.**
Two failure modes are possible when a contributor forgets to declare:
a brain-assuming check silently grades a kidney and emits a *wrong number*, or a
transferable check silently does not run and emits a *missing number*. This project's
whole thesis is that a wrong number is worse than an absent one. Narrow default.

Retro-fitting is 20 one-line edits, and the audit in §1 is exactly the list of what
each line should say.

### 2.2 Same ID ⟺ same maths *and* same meaning

The rule for whether a renal check reuses a brain check's ID:

- **Reuse the ID** when the maths and the interpretation are both unchanged.
  `4.1.coregistration` with `organs=("brain", "kidney")` — Dice is Dice.
  Same for `6.3.m0_no_bs`, `6.5.m0_geometry`, `5.2`, `5.3`, `2.3`.
- **New ID** when the meaning changes, even if the arithmetic does not.
  `3.2.gm_wm_ratio` → `4.4K.cortex_medulla_ratio`: identical division, but it moves
  from Module 3 (CBF level) to Module 4 (mask integrity), it can never FAIL, and its
  verdict semantics invert. Filing that under the same ID would make a JSON report
  ambiguous across organs and silently break any consumer comparing them.

Concretely: `1.qei`, `2.1`, `2.2`, `3.1`, `3.2`, `3.4`, `3.5`, `4.2`, `7.1`, `8.2` get
`organs=("brain",)`; renal counterparts get `K`-suffixed IDs
(`2.1K.cortex_cov`, `3.1K.cortical_rbf`, `3.5K.kidney_perfusion`,
`4.4K.cortex_medulla_ratio`, `7.1K.outlier_rejection`, `8.2K.data_type`).

### 2.3 Organ mismatch is `N/A`, never a silent skip

```python
for name, entry in all_checks().items():
    if cfg.organ not in entry["organs"]:
        results.append(CheckResult(
            name, Verdict.NA,
            reason=f"not applicable to organ {cfg.organ!r} "
                   f"(registered for {', '.join(entry['organs'])})"))
        continue
```

`coverage()` already excludes `N/A` from its denominator, so this costs nothing in
the coverage figure — but a reader of a kidney report can see that QEI exists, that it
was considered, and why it did not run. Silently dropping checks would give brain and
kidney reports different check counts with no explanation, which is exactly the kind
of unexplained difference that erodes trust in a QC tool.

This also replaces the current advisory `skipped_for_organ()` with something
`run_qc` actually enforces.

### 2.4 Band resolution: a function, not a dict

This is where the §0 finding bites. Population alone cannot key renal bands, because
labelling scheme moves cortical RBF by ~80 % — four times the age effect and eight
times the field-strength effect.

```python
@dataclass(frozen=True)
class BandKey:
    organ: str                    # brain | kidney
    population: str = "adult"     # adult | neonate | paediatric
    labelling: str | None = None  # PCASL | PASL | FAIR
    field_t: float | None = None  # 1.5 | 3.0
    variant: str | None = None    # native | transplant

def resolve_bands(key: BandKey) -> tuple[dict, list[str]]:
    """Returns (band overrides, fallback trail).

    Walks most-specific → least-specific and RECORDS which specificity it
    actually landed on, so the report can say
    'no band exists for FAIR @1.5T paediatric transplant kidney;
     fell back to the widest renal envelope (UNCALIBRATED)'.
    """
```

Resolution order (most specific first):
`(organ, population, labelling, field_t, variant)` →
`(organ, population, labelling, field_t)` →
`(organ, population, labelling)` →
`(organ, population)` →
`(organ,)` → dataclass defaults.

**The fallback trail is the important part**, and it is the natural extension of the
existing provenance system. A band reached by falling back three levels is weaker
evidence than one matched exactly, and the report must say so. Without this, a renal
band silently applies to an acquisition it was never derived for — which is precisely
how the 139–427 figure would get misused.

Organ is applied **before** population, because "neonate" means different bands in
brain and kidney; and `POPULATIONS` becomes keyed by `(organ, population)`.

### 2.5 Laterality: `CheckResult.instance`

R10.1 is unanimous (100 %, 0 % abstentions) that cortical values are reported
separately per kidney, and R8.1 requires the kidneys be registered separately. So a
renal report is **one verdict per (check, kidney)**, not one per check.

```python
@dataclass
class CheckResult:
    check: str
    verdict: Verdict
    metric: dict[str, Any] = field(default_factory=dict)
    reason: str = ""
    provisional: bool = False
    instance: str | None = None      # e.g. "left" | "right"; None = whole-subject
```

`run_qc` fans ROI-scoped checks out over the ROI set; `aggregate()` is unchanged
(worst-of already handles it). `instance` defaults to `None`, so every existing brain
report and its JSON schema are untouched. This is the minimum change that satisfies a
100 %-agreement consensus statement.

### 2.6 ROIs: a dict, not three named kwargs

Checks currently take `gm=`, `wm=`, `csf=`. Introduce:

```python
inputs["rois"] = {
    # brain
    "gm": prob, "wm": prob, "csf": prob,
    # kidney
    "kidney_l": prob, "kidney_r": prob,          # Renal Segmentor / ukat, available today
    "cortex_l": mask, "cortex_r": mask,          # user-supplied or manual (R9.10)
    "medulla_l": mask, "medulla_r": mask,        # optional; INFO only (R10.2)
}
```

with a per-organ ROI schema declaring which are required, which are optional and which
are INFO-only. Keep `gm=`/`wm=`/`csf=` as backwards-compatible aliases so no brain
check changes. Organ-agnostic checks (Dice, coverage, histogram, tSNR) take
`roi=` + `roi_name=` and stop caring what tissue they are looking at — which is what
makes `2.3`, `4.1` and the tSNR half of `2.2` transfer as-is at all.

### 2.7 Threshold provenance gains a fourth tier

`THRESHOLD_PROVENANCE` currently has PUBLISHED / IMPLEMENTATION / UNCALIBRATED. The
renal work needs one more, because a recurring renal situation has no honest home in
the existing three:

```python
BORROWED = "borrowed"   # published for a DIFFERENT organ/population and
                        # adopted here because nothing organ-specific exists
```

The motivating case is λ = 0.9 mL/g. It is a numbered consensus statement (R9.4, 90 %
agreement) — so "UNCALIBRATED" understates it — but the panel says in print:
*"Since a reliable reference for the partition coefficient value in kidney was not
known to this group, we recommend the use of a value of 0.9 mL/g, the average value
for brain tissue."* Calling that PUBLISHED-for-kidney would be exactly the stretched
citation this project exists to avoid. `BORROWED` says what it is.

Same tier for the renal PASL labelling efficiency (95 %, vs brain's 98 % — genuinely
different, so *not* borrowed) versus pCASL 85 % and T1blood 1.65 s @3 T (identical to
brain — worth recording as convergent, not assumed).

### 2.8 What stays exactly as it is

Worth stating, because the temptation in a port is to rebuild everything:
`Verdict`, `aggregate()`, `coverage()`, `CheckResult.provisional`, the two-stream
split, the runner's "a check must never crash the whole run" contract, and the
pure-NumPy / no-scipy rule all transfer untouched. The organ dimension is additive.

---

## 3. Phased plan

**Time remaining: 24 days.** GSoC coding ends 2026-08-24; today is 2026-07-31.
Brain v1.0 is built, deployed and passing. What follows is scoped to that, not to an
ideal roadmap.

### Phase 1 — Architecture (Aug 1–7) · **certain**

Pure refactor of existing code, no new science, fully testable against the brain suite.

- `organs=` on the decorator; all 20 checks annotated per §1's table.
- `run_qc` enforces organ → `N/A` (§2.3); `skipped_for_organ` retired.
- `BandKey` + `resolve_bands()` with the fallback trail (§2.4).
- `CheckResult.instance` (§2.5); `rois` dict with back-compat aliases (§2.6).
- `BORROWED` provenance tier (§2.7).
- Regression gate: **every existing brain test passes unchanged.** That is the
  acceptance criterion — if the brain report changes at all, the refactor is wrong.

This alone delivers what María asked for at Meeting 2 ("it must be modular for other
organs"), and `tests/test_meeting_followups.py` already has a test named for it.

### Phase 2 — Stream A renal (Aug 8–14) · **high confidence**

All PUBLISHED, all consensus-numbered, **none requires a segmentation or any renal
image data to implement**. This is the highest value-per-risk work available.

- `5.1K` — Nery Table 4 conformance + organ/labelling-conditional BIDS required set.
- `6.1/6.2/6.3/6.5` renal wiring — `6.3` and `6.5` transfer as-is; `6.1` hardens to
  FAIL (R9.1); `6.2` gains compartment- and field-dependent kidney T1.
- `5.2` + the readout-gated ≥ 20 pairs rule.
- **New: `8.3K.acquisition_conformance`** — the consensus protocol, each with its
  agreement percentage carried into the report:
  FAIR TI 1.8–2.0 s (89 %) · TI1 1.0–1.2 s (92 %) · QUIPSS II mandatory (100 %) ·
  pCASL τ 1.5–1.8 s (100 %) · PLD 1.2–1.5 s (100 %) · TR 4–6 s (94 %) ·
  2D slice 4–8 mm (100 %) / 3D 3–6 mm (100 %) · in-plane 2–4 mm (93 %) ·
  acceleration ≤ 2 (100 %) · coronal-oblique from the affine (93 %) ·
  fat suppression (90 %) · breathing strategy (94 % / 76 % / 95 %).
- `8.2K` — renal vocabulary, T2w structural role, organ + laterality inference.

**Design rule for this module:** map the agreement percentage onto verdict severity.
100 %-agreement statements can FAIL; 75–85 % statements (SE-EPI 75 %, free breathing
76 %) can only WARN. That is a defensible, wholly novel way to ground verdict strength
in the source — and it is only possible because the renal consensus publishes
per-statement agreement, which the brain White Paper does not.

### Phase 3 — Stream B renal, unblocked subset (Aug 15–21) · **medium confidence**

Only the checks that need no cortex/medulla segmentation.

- `3.5K.kidney_perfusion` — whole-kidney implausibility, 0–500 bound.
- `3.3K` negative fraction, `2.3` histogram (unchanged), tSNR as INFO.
- `4.1` per-kidney Dice with renal bands.
- **`7.1K.outlier_rejection`** — Harteveld's rule. Highest-value single item in this
  phase: it is the only published, reproducible, numeric renal QC rule in existence,
  and R8.2 mandates the operation at 100 % agreement.
- `synth_kidney()` — synthetic renal data with known answers, mirroring `synth.py`.
  Ground truth from the XCAT renal phantom work: cortex 250, medulla 50 mL/100 g/min
  (**note: those are the phantom's *input* values, not a pipeline's recovered
  output — the recovered figures of ~210/49 must not be used as ground truth or the
  test oracle bakes in a 16 % underestimation**).

### Phase 4 — Documentation and handover (Aug 22–24) · **certain**

Renal `THRESHOLD_PROVENANCE` with the `BORROWED` tier populated; this document
updated to reflect what shipped; mentor-facing summary; PR prepared per OSIPI policy
(Slack first, AI use disclosed, every line understood and owned).

---

### Explicitly **not** achievable by Aug 24 — stretch goals

Stated plainly, because over-promising here would be worse than under-delivering.

1. **Automatic cortex/medulla segmentation.** The field has not solved it: no released
   tool does it, the one published pipeline released no code, and its accuracy (0.78)
   sits at inter-observer agreement (0.77). Not a GSoC-sized problem.
2. **A renal QEI.** Needs a rater-labelled renal cohort that does not exist. §1.
3. **Any calibrated renal threshold.** Needs the renal specialist plus labelled data.
   Everything renal ships UNCALIBRATED or BORROWED, and says so.
4. **Validation on real renal data.** See the blocker below.
5. **Medullary anything, graded.** R10.2 plus ICC 0.08. INFO only, permanently.

### The one blocker that gates Phase 3 — please raise with the mentors

**There is no renal test data in this project.** `Data_for_Agnik/` is three brain
datasets. Every renal Stream B check would be tested against synthetic data only.
Phases 1 and 2 are unaffected (architecture and metadata conformance need no images),
but Phase 3 cannot be *validated*, only *written*.

Asks, in priority order:
1. **One renal ASL dataset** — ideally one FAIR and one pCASL, since the 80 %
   labelling difference is the largest single effect in the renal literature and a
   single-scheme dataset would silently calibrate to one branch.
2. **An introduction to the renal specialist** (María offered this) — the two
   questions worth their time are: *is the ~1.5 cortex:medulla trip point sensible as
   a segmentation-integrity flag?*, and *which of the four published outlier rules do
   renal groups actually use?*
3. **For Sudipto specifically:** the QEI question in §1 — what would a renal QEI need,
   and is the negative-fraction term alone worth reporting on its own for kidney?
4. **Taso et al., MRM 2023;89(5):1754–1776** (the ISMRM body-ASL "Grey Paper") is
   paywalled with no PMC record. Its abstract says it gives per-organ acquisition
   recommendations *starting with kidneys*, and its senior author is the senior author
   of the renal consensus. It is the largest unread gap in this analysis. Institutional
   access via either mentor would close it before Phase 2 hard-codes the protocol bands.

---

## 4. Provenance discipline for the renal phase

Unchanged from brain, with one addition. Every renal threshold ships tagged:

- **PUBLISHED** — the renal consensus states this number for this purpose. Carry the
  R-number *and* the agreement percentage (e.g. R6.12, 94 %), because agreement
  strength is itself evidence and the renal consensus is the only source in this
  project that publishes it.
- **IMPLEMENTATION** — a group's code/protocol uses it. Harteveld's > 20 % / ± 2 SD
  rule; Gullaksen's 0–500 clip.
- **BORROWED** — published for another organ and adopted for want of a renal value.
  λ = 0.9 mL/g, by the panel's own admission.
- **UNCALIBRATED** — an engineering default. Every renal *image-quality* threshold in
  the toolbox will be this one, and the docs must say so as plainly as the brain docs
  say it about their 23 of 43.

The honest headline for the renal release: **the renal consensus gives a hard,
quotable protocol specification and zero image-quality thresholds — so osipy-qc
renal grades protocol conformance with citations, and reports image quality without
pretending to grade it.** That is a smaller claim than the brain release makes, and it
is the only one the evidence supports.

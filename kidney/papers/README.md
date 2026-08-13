# Kidney / renal ASL source library

Source material for the **kidney phase** of osipy-qc. Mirrors the structure of the brain-side
`researchpaper/` + `paper_screenshots/` directories so kidney docs can cite pages the same way.

- **51 PDFs** in this directory, all verified (`%PDF` magic bytes, >40 KB, first page read and
  confirmed to be the intended paper).
- **68 page screenshots** in `../screenshots/`, rendered at 130 dpi with
  `pdftoppm -r 130 -png -f N -l N`, named `<paper>_pN.png`.
- **5 supplementary files** in `./supplementary/`.
- **9 papers could not be obtained** — listed at the bottom with DOIs, for institutional request.

## How these were retrieved

`pmc.ncbi.nlm.nih.gov/articles/PMCxxxxxxx/pdf/` now serves a JavaScript proof-of-work anti-bot
challenge, so it cannot be scripted. Everything here came from routes intended for programmatic
access instead:

- `https://europepmc.org/articles/PMCxxxxxxx?pdf=render` (Europe PMC) — the main route.
- `https://link.springer.com/content/pdf/<doi>.pdf` for CC-BY Springer content.
- `https://www.ebi.ac.uk/europepmc/webservices/rest/PMCxxxxxxx/supplementaryFiles` for supplementary.
- `https://www.ncbi.nlm.nih.gov/pmc/utils/oa/oa.fcgi?id=PMCxxxxxxx` to confirm OA licence status.

**No paywall was circumvented.** Five papers that PMC hosts as free-to-read author manuscripts
are *not* in the PMC Open Access subset (`oa.fcgi` returns `idIsNotOpenAccess`); they are recorded
as not obtained rather than scraped.

---

## ⚠️ Citation errors found in the working paper list

Caught while verifying that each downloaded PDF was the paper it claimed to be. Fix these before
any of them reaches a doc.

| Claimed | Actually |
|---|---|
| "Cox EF, Buchanan CE, Bradley CR, et al." for the Kidney Int Rep CKD RBF paper (PMC5575771) | **Li L-P, Tan H, Thacker JM, Li W, Zhou Y, Kohn O, Sprague SM, Prasad PV.** Different group entirely (NorthShore / Prasad). The *numbers* quoted were right (HV cortex 207.3±41.8 vs CKD 108.4±36.4; medulla 42.6±15.8 vs 23.2±8.9 → ratios 4.87 and 4.67), the *attribution* was wrong. |
| Nery 2020 consensus has "166 numbered consensus statements" | **59** consensus statements (Table 2) + 2 statements where consensus was not reached (Table 3). Stated on p.6: "The 59 final consensus statements are listed in Table 2". |
| "Franklin SL, Bones IK, et al." for MRM 2020;84:1919-1932 (VSASL labelling + respiratory motion) | First author is **Bones IK**; Franklin is second. File named `bones2020_*`. |
| "Hueper K, Nery F (Cutajar/Gutberlet group)" for PMC4839924 | **Hammon M, Janka R, Siegl C, Seuss H, Grosso R, Martirosian P, Schmieder RE, Uder M, Kistner I.** |
| Bones 2022 workflow paper as "MRM 2022;88(1):515" / "88:336" / "87:800-812" | **MRM 2022;87:800-809**, DOI 10.1002/mrm.29016. |
| Bones 2021 VSASL label dynamics as "86:1334-1346" | **MRM 2021;86:131-142.** |
| Harteveld 2022 paediatric tumour pCASL as "35(2):297-306" or "35:75-87" | **MAGMA 2022;35:235-246.** |
| Odudu 2018 "supplementary Table S1" at `.../bin/gfy180_table_s1.xlsx` | That URL 404s; the file is retrievable via the Europe PMC supplementaryFiles API and is saved here. It is real: **969 rows × 23 columns**. |

Also worth noting for the mentor meeting: **David C. Alsop** co-authored both the 2015 brain ASL
White Paper and the 2020 renal ASL consensus, and is senior author on Robson 2009 (renal
respiratory motion). That lineage is verified from the author lists in these PDFs.

---

## Tier 1 — the anchor documents

| File | Citation | DOI | OA | Facts it supports | Screenshots |
|---|---|---|---|---|---|
| `nery2020_renal_asl_consensus.pdf` | Nery F, Buchanan CE, Harteveld AA, Odudu A, Bane O, Cox EF, Derlin K, Gach HM, Golay X, Gutberlet M, Laustsen C, Ljimani A, Madhuranthakam AJ, Pedrosa I, Prasad PV, Robson PM, Sharma K, Sourbron S, Taso M, Thomas DL, Wang DJJ, Zhang JL, Alsop DC, Fain SB, Francis ST, Fernández-Seara MA. *MAGMA* 2020;33(1):141-161 | 10.1007/s10334-019-00800-z | CC BY | **THE renal White Paper.** 59 Delphi consensus statements with abstention/agreement %. Verified on p.8: R7.2 BS recommended (80%), R7.3 breath-hold not recommended (94%), R7.4 free breathing preferred (76%), R7.5 respiratory triggering (95%), R8.1 retrospective registration highly recommended (100%), R8.2 outlier rejection recommended (100%), R9.1 M0 mandatory (94%), R9.4 λ=0.9 mL/g (90%), R9.5 T1b=1.65 s @3T (100%), R9.6 T1b=1.48 s @1.5T (93%), R9.7 α=95% PASL (100%), R9.8 α=85% PCASL (86%), R9.10 manual ROI default (100%), R9.11 ROI from M0/structural (93%), R10.1 report cortical L/R separately (100%), R10.2 medullary RBF not reliable (89%), R6.12 TR 4-6 s (94%). ≥20 ASL pairs is **[R4.7] in body text, not a numbered Table-2 row**. RBF equations for PCASL and FAIR-QUIPSSII on p.13. Table 4 = minimum reporting set. | p7, p8, p9, p12, p13, p14 |
| `odudu2018_renal_asl_systematic_review.pdf` | Odudu A, Nery F, Harteveld AA, Evans RG, Pendse D, Buchanan CE, Francis ST, Fernández-Seara MA. *Nephrol Dial Transplant* 2018;33(suppl_2):ii15-ii21 | 10.1093/ndt/gfy180 | CC BY-NC | 53-study systematic review. **Table 1 (p.4) = the reproducibility table**: cortex vs medulla, intravisit vs intervisit ICC and CV, per study. Verified quotes: "range of reported renal cortical perfusion values ranging from (139-427 mL/100 g/min) in healthy volunteers" and "we found no studies of reproducibility between centres". Table 3 = 14-item minimum reporting checklist. | p2, p3, p4, p5 |
| `mendichovszky2020_parenchima_umbrella_consensus.pdf` | Mendichovszky I, Pullens P, Dekkers I, et al. *MAGMA* 2020;33(1):131-140 | 10.1007/s10334-019-00784-w | CC BY | Delphi process paper for the whole PARENCHIMA renal consensus series. p.2 carries the "calibration and quality control ... not common practice and not mandatory" framing sentence. | p2 |
| `dekkers2020_renal_t1_t2_consensus.pdf` | Dekkers IA, de Boer A, Sharma K, et al. *MAGMA* 2020;33:163-176 | 10.1007/s10334-019-00797-5 | CC BY | Sister consensus; the only one addressing cortex-vs-medulla **ROI methodology** directly (Tables 2 and 3, manual vs automated segmentation recommendations with agreement %). | p9, p10 |

## Tier 2 — normative values, reproducibility, SNR

| File | Citation | DOI | OA | Facts it supports | Screenshots |
|---|---|---|---|---|---|
| `garciaruiz2025_field_strength_sex_age_kidney.pdf` | Garcia-Ruiz L, Echeverria-Chasco R, Aramendía-Vidaurreta V, Solis-Barquero SM, Garcia-Fernandez N, Mora-Gutiérrez JM, Vidorreta M, Bastarrika G, Fernández-Seara MA. *JMRI* 2025;62(4):1180-1195 | 10.1002/jmri.70009 | OA | Same 16 subjects at 1.5T **and** 3T. Publishes tSNR, spatial SNR, perfusion-weighted-signal % and background-suppression efficiency for cortex and medulla — the only renal SNR baselines found. Two-stage automatic rejection rule. Field/age/sex effects on RBF. | p4, p5, p8, p9 |
| `harteveld2020_multidelay_fair_vs_pcasl.pdf` | Harteveld AA, de Boer A, Franklin SL, Leiner T, van Stralen M, Bos C. *MAGMA* 2020;33(1):81-94 | 10.1007/s10334-019-00806-7 | CC BY | FAIR vs pCASL in the same subjects; cortex/medulla RBF and ATT (Table 2, p.8); the ">20% of voxels beyond ±2 SD → reject that pair" outlier rule; FAIR vs pCASL CVws. Scan parameters Table 1 (p.3). | p3, p4, p8 |
| `buchanan2018_2d_imaging_schemes_kidney_cortex.pdf` | Buchanan CE, Cox EF, Francis ST. *Diagnostics* 2018;8(3):43 | 10.3390/diagnostics8030043 | CC BY | **Table 2 (p.7)**: PWI-SNR, tSNR and perfusion variability per 2D readout (bFFE / GE-EPI / SE-EPI / TSE) in the same 10 subjects at 3T. The readout-conditioned SNR reference. | p7 |
| `gillis2014_interstudy_reproducibility_3t.pdf` | Gillis KA, McComb C, Foster JE, Taylor AHM, Patel RK, Morris STW, Jardine AG, Schneider MP, Roditi GH, Delles C, Mark PB. *BMC Nephrol* 2014;15:23 | 10.1186/1471-2369-15-23 | CC BY | 3T FAIR True-FISP, n=12, between-day. Cortex and whole-kidney perfusion, ICC and CVws (Table 2, p.5). | p5 |
| `hammon2016_reproducibility_15t_semiautomatic.pdf` | Hammon M, Janka R, Siegl C, Seuss H, Grosso R, Martirosian P, Schmieder RE, Uder M, Kistner I. *Medicine (Baltimore)* 2016;95(11):e3083 | 10.1097/MD.0000000000003083 | CC BY-NC-ND | Best-case repeatability (CV ~3-4%, ICC ~0.97) **and** the cautionary near-1 cortex:medulla ratio that suggests segmentation contamination. Semi-automatic segmentation described. | p3, p5 |
| `echeverriachasco2023_allograft_reproducibility.pdf` | Echeverria-Chasco R, Martin-Moreno PL, Garcia-Fernandez N, Vidorreta M, Aramendia-Vidaurreta V, Cano D, Villanueva A, Bastarrika G, Fernández-Seara MA. *NMR Biomed* 2023;36(2):e4832 | 10.1002/nbm.4832 | OA | Transplant repeatability: cortex and medulla CVws / ICC (Table 3), renal tSNR reference values, ±2 SD cortical-ROI outlier rule. | p9, p10 |
| `olsen2025_renal_asl_vs_o15_pet.pdf` | Olsen NE, Mariager CØ, Arildsen MM, Nielsen S, Vendelbo MH, Pedersen M, Laustsen C, Ringgaard S, Tolbod LP, Buus NH. *Magn Reson Med* 2025 | 10.1002/mrm.30638 | OA | ASL vs [15O]H2O PET in the same subjects; Bland-Altman LoA; the published >500 mL/min/100 mL voxel exclusion; per-slice usable-slice statistics. **Units are mL/min/100 mL (volume-normalised), not per 100 g** — a real unit trap. | p4, p6 |
| `cox2017_multiparametric_renal_mri_validation.pdf` | Cox EF, Buchanan CE, Bradley CR, Prestwich B, Mahmoud H, Taal M, Selby NM, Francis ST. *Front Physiol* 2017;8:696 | 10.3389/fphys.2017.00696 | CC BY | Large healthy cohort normative cortical perfusion, age split, repeatability sub-study, and the Nottingham quantification constants (which differ from the consensus). | p9 |
| `li2017_ckd_renal_blood_flow_kireports.pdf` | **Li L-P**, Tan H, Thacker JM, Li W, Zhou Y, Kohn O, Sprague SM, Prasad PV. *Kidney Int Rep* 2017 | 10.1016/j.ekir.2016.09.003 | CC BY-NC-ND | Healthy vs stage-3 CKD, cortex **and** medulla, n=30 HV / 33 CKD. Source of the ~4.87 (health) and ~4.67 (CKD) derived cortex:medulla ratios. | p5 |
| `haddock2019_furosemide_cortex_medulla.pdf` | Haddock B, Larsson HBW, Francis S, Andersen UB. *Acta Physiol* 2019;227:e13292 | 10.1111/apha.13292 | OA | Tight medullary values obtained by restricting the medullary ROI to the innermost half — the ROI convention that makes the cortex:medulla ratio behave. | p3, p4 |
| `deboer2021_multiparametric_test_retest.pdf` | de Boer A, Harteveld AA, Stemkens B, et al. *JMRI* 2021;53:859-873 | 10.1002/jmri.27167 | OA | Modern intrasubject test-retest; cortical ASL ICC and repeatability coefficient; the FAIR-unplannable-anatomy problem. | p5, p6 |
| `shirvani2019_motioncorrected_multiparametric_renal_asl.pdf` | Shirvani S, Tokarczuk P, Statton B, Quinlan M, Berry A, Tomlinson J, Weale P, Kühn B, O'Regan DP. *Eur Radiol* 2019;29:232-240 | 10.1007/s00330-018-5628-3 | CC BY | Single-TI vs multi-TI bias in the same subjects; the "outer 3 voxels" cortex definition (Table 1, p.6) — evidence that cortex definition is unstandardised. | p6 |
| `wolf2018_renal_t1_t2_systematic_review.pdf` | Wolf M, de Boer A, Sharma K, Boor P, Leiner T, Sunder-Plassmann G, Moser E, Caroli A, Jerome NP. *Nephrol Dial Transplant* 2018 | 10.1093/ndt/gfy198 | CC BY-NC | Tabulates kidney cortex and medulla **T1 per study at 1.5T and 3T** (Tables 1-2) — the source for T1,tissue in the M0 incomplete-relaxation correction. | p4, p5 |
| `hillaert2024_dog_fair_asl_lambda.pdf` | Hillaert A, Sanmiguel Serpa LC, Xu Y, Hesta M, Bogaert S, Vanderperren K, Pullens P. *Animals* 2024;14(12):1810 | 10.3390/ani14121810 | CC BY | The only paper that *measures* a kidney λ rather than borrowing the brain value, and documents the human/pig literature spread. Best citation for "renal λ is genuinely UNCALIBRATED". | p6 |

## Tier 3 — motion, artefacts, outlier rejection

| File | Citation | DOI | OA | Facts it supports | Screenshots |
|---|---|---|---|---|---|
| `bones2019_freebreathing_bgs_renal_pcasl.pdf` | Bones IK, Harteveld AA, Franklin SL, van Osch MJP, Hendrikse J, Moonen CTW, Bos C, van Stralen M. *Magn Reson Med* 2019;82(1):276-288 | 10.1002/mrm.27723 | OA | tSNR definition and actual renal tSNR values (tSNR < 1 is normal in kidney); BGS-vs-registration trade-off; per-kidney translation-only Elastix registration; a real scan failure. | p7, p8 |
| `bones2020_vsasl_labeling_respiratory_motion.pdf` | **Bones IK**, Franklin SL, Harteveld AA, van Osch MJP, Schmid S, Hendrikse J, Bos C, van Stralen M. *Magn Reson Med* 2020;84:1919-1932 | 10.1002/mrm.28252 | OA | The clearest published artefact signature: "homogeneously high ΔM over the entire kidney ROI" from spurious labelling; incidence rates; 5/15 subjects excluded; bellows amplitude does **not** predict artefact (so QC must be image-derived). | p6, p9 |
| `bones2021_vsasl_label_dynamics_kidney.pdf` | Bones IK, Franklin SL, Harteveld AA, van Osch MJP, Schmid S, Hendrikse J, Moonen C, Bos C, van Stralen M. *Magn Reson Med* 2021;86:131-142 | 10.1002/mrm.28683 | OA | The ">80% of kidney voxels within ±2 SD of the voxel-wise mean" keep-rule; 2×2 mask erosion for partial volume; peak cortical PWS. | p4, p6 |
| `harteveld2022_paediatric_neuroblastoma_nephroblastoma_pcasl.pdf` | Harteveld AA, Littooij AS, van Noesel MM, et al. *MAGMA* 2022;35:235-246 | 10.1007/s10334-021-00943-y | CC BY | A second, independent formulation of the same outlier rule family (">20% of voxels beyond ±1.5 SD"); paediatric abdominal ASL; **ROI voxel counts (Table 3, p.8)** for a minimum-ROI-size check. | p8 |
| `nery2019_paediatric_ckd_3dgrase_motion_correction.pdf` | Nery F, De Vita E, Clark CA, Gordon I, Thomas DL. *Magn Reson Med* 2019;81:2972-2984 | 10.1002/mrm.27614 | UCL Discovery green OA (accepted version) | The only dedicated paediatric native-kidney renal ASL study; cited by the consensus as an automatic outlier-rejection implementation. Repeatability table p.9. | p8, p9 |
| `song2017_respiratory_motion_prediction.pdf` | Song H, Ruan D, Liu W, Stenger VA, Pohmann R, Fernández-Seara MA, Nair T, Jung S, Luo J, Motai Y, Ma J, Hazle JD, Gach HM. *Med Phys* 2017;44(3):962-973 | 10.1002/mp.12099 | OA | The ±1 mm kidney-position tolerance target; residual error growing within an acquisition (argues for per-volume, not scan-level, motion metrics). | — |
| `siva2013_kidney_motion_4dct.pdf` | Siva S, Pham D, Gill S, Bressel M, Dang K, Devereux T, Kron T, Foroudi F. *Radiat Oncol* 2013;8:248 | 10.1186/1748-717X-8-248 | CC BY | Largest kidney-motion cohort (n=62) with a **distribution** (median, IQR, percentiles), Table 1 p.4 — what you need for a WARN/FAIL split rather than a point threshold. | p4 |
| `yamashita2014_renal_motion_4dct.pdf` | Yamashita H, Yamashita M, Futaguchi M, et al. *SpringerPlus* 2014;3:131 | 10.1186/2193-1801-3-131 | CC BY | All three motion axes with mean±SD and range (Table 2, p.4) plus a literature table of prior kidney-mobility measurements (Table 3, p.5). The ~6:1 CC:RL anisotropy is why a scalar brain-style FD is the wrong shape. | p4, p5 |
| `franklin2021_multiorgan_flowbased_asl.pdf` | Franklin SL, Bones IK, Harteveld AA, et al. *Magn Reson Med* 2021;85:2580-2594 | 10.1002/mrm.28603 | OA | The only paper computing the same metric (tSNR) in brain **and** kidney in the same subjects with the same pipeline — the bridge for arguing which brain QC concepts transfer. (Listed as unobtainable in the working notes; it is in fact open.) | p8 |
| `nery2018_challenges_opportunities.pdf` | Nery F, Gordon I, Thomas DL. *Diagnostics* 2018;8(1):2 | 10.3390/diagnostics8010002 | CC BY | Narrative review of renal ASL failure modes. **Table 4 (p.6)** = multi-PLD renal ASL studies overview; **Table 5 (p.8)** = motion-correction strategies. Contains the "misinterpreted as low RBF in single-PLD studies" mechanism sentence. | p6, p8 |

## Tier 4 — pipelines, phantoms, prior art

| File | Citation | DOI | OA | Facts it supports | Screenshots |
|---|---|---|---|---|---|
| `bones2022_automatic_renal_perfusion_workflow.pdf` | Bones IK, Bos C, Moonen C, Hendrikse J, van Stralen M. *Magn Reson Med* 2022;**87**:800-809 | 10.1002/mrm.29016 | OA | The reference automated renal ASL pipeline (3-U-net cascade). Automatic cortex Dice vs **inter-observer** Dice (Table 1, p.6) — the irreducible floor under any cortex-dependent QC metric. Quality still assessed *visually*: the evidence the niche is unfilled. | p6 |
| `brumer2022_synthetic_renal_asl_phantom.pdf` | Brumer I, Bauer DF, Schad LR, Zöllner FG. *Diagnostics* 2022;12(8):1854 | 10.3390/diagnostics12081854 | CC BY | XCAT-based synthetic renal ASL with known ground-truth perfusion — the kidney analogue of `synth.py`. MSSIM for registration quality, Dice for segmentation. | p3, p5 |
| `zhaowt2025_rat_liver_multi_ti_asl_qa_protocol.pdf` | Zhao W-T, Herrmann K-H, Wei W, Krämer M, Dahmen U, Reichenbach JR. *MAGMA* 2025;38:503-517 | 10.1007/s10334-024-01223-1 | CC BY | The only paper calling itself a **QA protocol** for body-organ ASL. Transferable idea: T1-fit residual (MSE) map as a per-voxel quality map. Preclinical 9.4T — label thresholds accordingly. | p9 |
| `chuang2021_quantitative_analysis_renal_perfusion_asl_chapter.pdf` | Chuang K-H, Kober F, Ku M-C. Ch.39 in *Preclinical MRI of the Kidney* (Methods Mol Biol 2216), 2021 | 10.1007/978-1-0716-0978-1_39 | CC BY | The only published renal ASL **QC checklist** found: discard for "severe movement, spikes, banding artifacts, or sudden changes in SNR" (p.3). Also FAIR pseudo-motion fooling registration, and receiver-gain drift between ASL and M0. Cleanest citation for "every kidney threshold ships UNCALIBRATED". | p3 |
| `chuang2021_renal_blood_flow_asl_protocol_chapter.pdf` | Chuang K-H, Meier M, Fernández-Seara MA, Kober F, Ku M-C. Ch.26 in *Preclinical MRI of the Kidney*, 2021 | 10.1007/978-1-0716-0978-1_26 | CC BY | Acquisition-side detail: rectangular FOV / frequency-encode direction to avoid aliasing, fat-suppression rationale, flow-saturation bands, shimming troubleshooting for weak labelling. | p5 |
| `clement2022_asl_bids.pdf` | Clement P, Castellaro M, Okell TW, Thomas DL, Vandemaele P, Elgayar S, Oliver-Taylor A, Kirk T, Woods JG, Vos SB, Kuijer JPA, Achten E, van Osch MJP. *Sci Data* 2022;9:543 | 10.1038/s41597-022-01615-9 | CC BY | The limitation sentence that settles the metadata-schema scope question: ASL-BIDS is validated for brain only (p.6). | p6 |
| `suzuki2024_osipi_asl_lexicon.pdf` | Suzuki Y, Clement P, Dai W, Dolui S, Fernández-Seara MA, Lindner T, Mutsaerts HJMM, Petr J, Shao X, Taso M, Thomas DL. *Magn Reson Med* 2024 | 10.1002/mrm.29815 | free on PMC | OSIPI's own lexicon (mentor Sudipto Dolui is a co-author). Brain-scoped. **Table 8 (p.26)** one-compartment model parameters, **Table 9 (p.27)** reporting recommendation — the vocabulary a renal Stream-A schema check should adopt. | p26, p27 |
| `fan2023_osipi_asl_pipeline_inventory.pdf` | Fan H, Mutsaerts HJMM, Anazodo U, et al. (incl. **Dolui S**), Petr J. *Magn Reson Med* 2023 | 10.1002/mrm.29869 | free on PMC | The OSIPI pipeline inventory osipy-qc would eventually appear in; defines the feature vocabulary (incl. presence of a QC report) it will be judged against. Brain-only because nobody registered a non-brain pipeline. | — |
| `taso_slides_renal_asl_symposium.pdf` | Taso M. "Arterial Spin Labeling (ASL) perfusion MRI", 3rd International Symposium on Renal Imaging, Nottingham (undated, ~2019) | — | public web PDF | **Not peer-reviewed — do not cite as a published value.** Useful only as a taxonomy of renal-specific challenges. Note his practical constants (α 0.6-0.8, T1b 1.3 s @1.5T) *differ from the consensus*. | p8, p10 |

## Tier 5 — disease cohorts (plausibility-band context)

All open access; none screenshotted except where a specific table is load-bearing.

| File | Citation | DOI | Screenshots |
|---|---|---|---|
| `zhang2024_ckd_oxygenation_perfusion.pdf` | Zhang X, Ye C, Lu F, et al. *Renal Failure* 2024;46(2):2428337 — CKD stages 1-5, cortex + medulla RBF with matching eGFR (Table 2, p.4) | 10.1080/0886022X.2024.2428337 | p4 |
| `fang2026_3d_pcasl_diabetes_kidney.pdf` | Fang W, Song Q, Cao F, et al. *Quant Imaging Med Surg* 2026;16(4):299 — healthy values *below* the PARENCHIMA floor (short 700 ms PLD): the exhibit for why absolute-perfusion gating fails across protocols (Table 2, p.7) | 10.21037/qims-2025-1776 | p7 |
| `radovic2022_paediatric_allograft_asl.pdf` | Radovic T, Spasojevic B, et al. *Sci Rep* 2022;12:828 — paediatric/young-adult allograft band; paediatric PWS quality metric; realistic paediatric motion-exclusion rate | 10.1038/s41598-022-04794-y | p6 |
| `radovic2025_donor_recipient_paediatric_allograft.pdf` | Radovic T, Spasojevic B, Cvetkovic M, et al. *Sci Rep* 2025 — donor–recipient paired paediatric transplant | 10.1038/s41598-025-22660-5 | — |
| `demul2025_paediatric_kidney_functional_mri_review.pdf` | De Mul A, et al. *Pediatr Nephrol* 2025;40(5):1539-1548 — citable authority that paediatric renal MRI reference values do not exist | 10.1007/s00467-024-06560-w | — |
| `shimizu2017_att_corrected_rbf_pcasl.pdf` | Shimizu K, Kosaka N, Yamamoto T, et al. *Magn Reson Med Sci* 2017;16(1):38-44 — cortical ATT lengthening with age; single-PLD RBF losing correlation with the reference | 10.2463/mrms.mp.2015-0117 | p4 |
| `chhabra2022_allograft_asl_systematic_review.pdf` | Chhabra J, et al. *Cureus* 2022 — allograft systematic review | 10.7759/cureus.25428 | — |
| `peng2023_roc_asl_bold_allograft.pdf` | Peng J, et al. *Transl Androl Urol* 2023;12(4):612-621 — largest single cohort (n=135) | 10.21037/tau-23-136 | — |
| `que2026_bold_asl_early_transplant.pdf` | Que C, Zhang H, Ma J, et al. *Quant Imaging Med Surg* 2026;16(2):165 | 10.21037/qims-2025-180 | — |
| `ma2025_asl_dti_allograft_dysfunction.pdf` | Ma J, Cao C, Que C, et al. *Quant Imaging Med Surg* 2025;15(8):6882-6896 — ⚠️ has an internal unit inconsistency (table says mL/100 g/min, text says mL/min/1.73 m²) | 10.21037/qims-2025-604 | — |
| `zhang2025_early_allograft_dysfunction_prediction.pdf` | **Zhang L**, Ding Z, Pu X, Yue X, Li Y, Zhuang S, Xie S. *Quant Imaging Med Surg* 2025;15(5):4333-4342 | 10.21037/qims-24-2182 | — |
| `jiang2024_asl_t1mapping_longterm_transplant.pdf` | **Jiang B**, Li J, Wan J, et al. *Quant Imaging Med Surg* 2024;14(3):2415-2425 | 10.21037/qims-23-1577 | — |
| `zhao2025_renal_artery_stenosis.pdf` | Zhao L, Xu FB, Liu JY, et al. *Renal Failure* 2025;47(1):2444403 — native unilateral RAS: why an L/R symmetry check must never hard-FAIL | 10.1080/0886022X.2024.2444403 | — |
| `shi2026_membranous_nephropathy.pdf` | Shi R, Wang H, Xu H, et al. *Insights into Imaging* 2026;17:35 — reports exact ROI voxel counts for cortex and medulla | 10.1186/s13244-026-02207-6 | — |

---

## Supplementary material (`./supplementary/`)

| File | Source | What it is |
|---|---|---|
| `odudu2018_supp_table_s1_53_studies.xlsx` | Odudu 2018, via Europe PMC supplementaryFiles API | **969 rows × 23 columns.** Per-study extraction of all 53 renal ASL studies: reference, PubMed ID, n patients/controls, age, B0, labelling scheme, PLD, readout, slices + orientation, voxel size, breathing scheme, … This is the ready-made calibration dataset for a kidney reference table. |
| `odudu2018_supp_data.docx` | Odudu 2018 | Supplementary text/methods. |
| `nery2020_supp_figS1_ckd_examples.tif` | Nery 2020, Springer ESM MOESM1 | Figure S1 — renal ASL in CKD patients. |
| `nery2020_supp_figS2_artefacts_readouts.tif` | Nery 2020, Springer ESM MOESM2 | Figure S2 — referenced from the TI recommendation [R4.4] discussion. |
| `nery2020_supp_survey_questions.docx` | Nery 2020, Springer ESM MOESM3 | The complete Delphi survey questions behind the 59 statements. |

---

## Could not obtain — request through an institution

| Paper | DOI | Why not |
|---|---|---|
| Taso M, Aramendia-Vidaurreta V, Englund EK, Francis S, Franklin SL, Madhuranthakam AJ, Martirosian P, Nayak KS, Qin Q, Shao X, Thomas DL, Zun Z, Fernández-Seara MA. "Update on state-of-the-art for ASL human perfusion imaging outside of the brain." *Magn Reson Med* 2023;89(5):1754-1776 | 10.1002/mrm.29609 | **Unpaywall says `is_oa: true`, licence CC-BY, publisher-hosted.** Wiley's Cloudflare returns a challenge page to non-browser clients, and there is no PMC copy. A human browser should be able to download it directly from `onlinelibrary.wiley.com/doi/pdfdirect/10.1002/mrm.29609` — **highest priority, and it is legitimately free.** Also mirrored (submitted version) at University of Nottingham repository output 17651600 (returned 403 here). |
| Robson PM, Madhuranthakam AJ, Dai W, Pedrosa I, Rofsky NM, Alsop DC. "Strategies for reducing respiratory motion artifacts in renal perfusion imaging with ASL." *Magn Reson Med* 2009;61(6):1374-1387 (PMC2946256) | 10.1002/mrm.21960 | PMC free-to-read author manuscript but **not in the PMC OA subset** (`oa.fcgi` → `idIsNotOpenAccess`). |
| Tan H, Koktzoglou I, Prasad PV. "Renal perfusion imaging with 2D navigator gated ASL." *Magn Reson Med* 2014;71(2):570-579 (PMC4429520) | 10.1002/mrm.24692 | Same — not in the OA subset. Contains the 8 mm navigator window and acquisition-efficiency metric. |
| Artz NS, Sadowski EA, Wentland AL, Djamali A, Grist TM, Seo S, Fain SB. "Reproducibility of renal perfusion MR imaging in native and transplanted kidneys." *JMRI* 2011;33(6):1414-21 (PMC3098463) | 10.1002/jmri.22552 | Same — not in the OA subset. The canonical cortex-good/medulla-poor reproducibility paper. |
| Artz NS, Sadowski EA, Wentland AL, Grist TM, Seo S, Djamali A, Fain SB. "ASL MRI for assessment of perfusion in native and transplanted kidneys." *Magn Reson Imaging* 2011;29(1):74-82 (PMC3005910) | 10.1016/j.mri.2010.07.018 | Same — not in the OA subset. Richest paired cortex+medulla means by subgroup. |
| Conlin CC, Oesingmann N, Bolster B Jr, Huang Y, Lee VS, Zhang JL. "Renal plasma flow measured with multiple-TI ASL." *Magn Reson Imaging* 2017;37:51-55 (PMC5316347) | 10.1016/j.mri.2016.11.010 | Same — not in the OA subset. |
| Cutajar M, Thomas DL, Banks T, Clark CA, Golay X, Gordon I. "Repeatability of renal ASL MRI in healthy subjects." *MAGMA* 2012;25(2):145-153 | 10.1007/s10334-011-0300-9 | Paywalled; no PMC record. Needed to check whether the review's age-effect claim is actually supported. |
| Kim DW, Shim WH, Yoon SK, et al. "Measurement of ATT and RBF using pCASL with multiple PLDs." *JMRI* 2017;46(3):813-819 | 10.1002/jmri.25634 | Paywalled. Reports the longest renal pCASL ATT — matters because it exceeds the consensus single-PLD recommendation. |
| Daniel AJ, Buchanan CE, Allcock T, et al. "Automated renal segmentation ... convolutional neural network." *Magn Reson Med* 2021;86:1125-1136 | 10.1002/mrm.28768 | Paywalled. The paper behind `renalsegmentor` / UKAT. |
| Zhang K, Triphan SMF, Ziener CH, et al. "Navigator-based slice tracking for kidney pCASL." *Magn Reson Med* 2023;90(1):231-239 | 10.1002/mrm.29617 | Paywalled. |
| Mora Álvarez MG, Madhuranthakam AJ, Udayakumar D. "Quantitative non-contrast perfusion MRI in the body using ASL." *MAGMA* 2024 | 10.1007/s10334-024-01188-1 | Paywalled. **Written by mentor María Mora Álvarez — ask her directly.** |
| Ghoul et al. "Automated Coregistered Segmentation for Volumetric Analysis of Multiparametric Renal MRI." *Magn Reson Med* 2026 | 10.1002/mrm.70288 | Paywalled. |
| Alhummiany BA, Shelley D, Saysell M, et al. "Bias and Precision in MRI-Based Estimates of Renal Blood Flow." *JMRI* 2022;55(4):1130-1141 | 10.1002/jmri.27888 | Paywalled. |
| Zhang J, Kong X, Lin X, Li Y, Zhang JL, Zong X. "Deep learning-based perfusion quantification and large vessel exclusion for renal multi-TI ASL." *Magn Reson Imaging* 2026;126:110573 | 10.1016/j.mri.2025.110573 | Paywalled, no PMC record. Prior art for a transit/vessel-artefact check. |
| de Bazelaire CMJ, Duhamel GD, Rofsky NM, Alsop DC. "MR imaging relaxation times of abdominal and pelvic tissues at 3.0 T." *Radiology* 2004;230(3):652-9 | 10.1148/radiol.2303021331 | Paywalled. Canonical kidney cortex/medulla T1. **Partly substitutable**: `wolf2018_renal_t1_t2_systematic_review.pdf` tabulates these values second-hand. |
| Zhang X, Petersen ET, Ghariq E, De Vis JB, Webb AG, Teeuwisse WM, Hendrikse J, van Osch MJP. "In vivo blood T1 measurements at 1.5 T, 3 T, and 7 T." *Magn Reson Med* 2013;70:1082-1086 | 10.1002/mrm.24550 | Paywalled. **This is reference [98] of the renal consensus — the sole primary source for T1b = 1.65 s @3T and 1.48 s @1.5T.** Until it is read, those two constants are PUBLISHED-via-consensus, not PUBLISHED-at-primary-level. |
| Lu H, Clingman C, Golay X, van Zijl PCM. "Determining the longitudinal relaxation time (T1) of blood at 3.0 Tesla." *Magn Reson Med* 2004;52(3):679-82 | 10.1002/mrm.20178 | Paywalled. Independent support for T1b @3T plus the haematocrit regression. |
| Ahn HS, Yu HC, Kwak HS, Park SH. "Assessment of Renal Perfusion in Transplanted Kidney Patients Using pCASL with Multiple PLDs." *Eur J Radiol* 2020;130:109200 | 10.1016/j.ejrad.2020.109200 | Paywalled. Only source for transplant-kidney ATT (n=4). |
| Liang C, Loster I, Ursprung S, et al. "Multiparametric functional MRI of the kidneys — test-retest repeatability." *RoFo* 2025 | 10.1055/a-2480-4885 | Paywalled. The pessimistic repeatability bound. |
| *Advanced Clinical MRI of the Kidney* (Springer, 2023) | 10.1007/978-3-031-40169-5 | Not open access (unlike its preclinical sibling, chapters 26 & 39 of which are here). |

### Non-paper resources noted but not downloaded
- **UKAT** (UKRIN Kidney Analysis Toolbox), `https://github.com/UKRIN-MAPS/ukat` — the only existing
  renal MRI QA code (`ukat/qa/snr.py`). No ASL module. Clone separately if needed; note it depends
  on scikit-learn, which osipy's no-scipy rule would forbid.
- **OSIPI ASL Lexicon living online version** (Google Doc) — Section 7 "Outside of Brain" holds the
  only OSIPI-branded renal ASL content. Two rows. Web resource, no PDF.

---

## Regenerating

```bash
# text extraction
/tmp/pdfvenv/bin/python -c "from pypdf import PdfReader; ..."

# re-render a page
pdftoppm -r 130 -png -f N -l N papers/<name>.pdf screenshots/<name>_pN
```

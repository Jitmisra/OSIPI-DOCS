# Placenta ASL source corpus

Acquired 2026-08-14 for the placenta round of the OSIPI QC ToolBox. Same
convention as `../../kidney/papers/`: every PDF here was verified to start with
`%PDF`, and every page cited in a design doc has a rendered, highlighted
screenshot in `../screenshots/`.

**58 PDFs downloaded. 84 verbatim phrases highlighted across 70 pages of 54
papers, 0 not found.** Nine papers could not be obtained at all and 14 more are
readable only as plain text — both lists are at the bottom, with DOIs, so they
can be requested through an institution.

## How to reproduce

```
bash ../fetch.sh ../fetch2.sh ../fetch3.sh ../fetch4.sh ../fetch5.sh  # download
python3 ../build_evidence.py     # resolve phrases -> evidence.json + highlights.json
python3 ../highlight.py          # draw the amber bands
python3 ../find.py "some phrase" # check a phrase before adding it
```

`build_evidence.py` holds the claim list. It does **not** let you assert a page
number: it searches every page of the named PDF with the same matcher
`highlight.py` uses and fails loudly if a phrase is not there. That is what
makes "0 not found" meaningful rather than a formatting accident.

## Provenance caveats you must carry into the docs

- **`taso2023_body_asl_outside_brain.pdf` is the UCL Discovery accepted
  manuscript**, not the typeset Wiley article. Section *numbers* (3.1.3, 3.3.3)
  therefore do not appear; the section *structure* does, and it is the structure
  that carries the argument. Wiley returns 403 to automated access. Check the
  published version before quoting a section number.
- **`hutter2020_perfox_arxiv_preprint.pdf` is the arXiv preprint**; the PMC
  version is also here as `hutter2020_perfox_perfusion_oxygenation.pdf`. Cite
  the published one — wording differs.
- **`dewick2026_placental_contractions.pdf` and
  `turnbull2026_t2star_susceptibility_placental_health.pdf` are arXiv
  PREPRINTS**, not peer reviewed. Label them as such every time.
- **`pietsch2021_applause_SUPPLEMENT_ONLY.pdf` and
  `leon2025_fair_asl_chd_SUPPLEMENT_emethods.pdf` are supplementary files
  only** — the main articles are not machine-retrievable. Do not cite them as
  if they were the papers.
- 13 PDFs (Zun 2017, ASL-BIDS, OSIPI lexicon, FetMRQC, dHCP, MHRA, and others)
  were re-written with `pdftocairo -pdf` because `pdftotext -bbox` aborted on
  the originals. Untouched originals are kept in `../papers_original/`. The
  text layer is preserved; highlights were spot-checked against the originals.

## Papers with highlighted evidence

| File | Citation | DOI | OA | Supports | Pages rendered |
|---|---|---|---|---|---|
| `taso2023_body_asl_outside_brain.pdf` | Taso M, Aramendía-Vidaurreta V, Englund EK, … Zun Z, Fernández-Seara MA; ISMRM Perfusion Study Group. Update on state-of-the-art for ASL human perfusion imaging outside of the brain. *Magn Reson Med* 2023;89(5):1754-1776 | 10.1002/mrm.29609 (from the record; not printed on this AM) | hybrid; AM via UCL Discovery | Kidney gets **Recommendations**, placenta gets only a **Summary** → no placental ASL consensus; pCASL labels maternal supply, VSASL both circulations; pCASL labelling efficiency ~77% in the aorta; Gd contraindicated in pregnancy | 13, 16, 17, 18, 19 |
| `harteveld2020_vsasl_settings_placenta.pdf` | Harteveld AA, Hutter J, Franklin SL, et al. Systematic evaluation of VSASL settings for placental perfusion measurement. *Magn Reson Med* 2020;84(4):1828-1843 | 10.1002/mrm.28240 | CC BY | The >20%-of-uterus-voxels / ±1.5 SD rejection rule; ~4 of ~16 pairs rejected; tSD 6.7±3.1% after MoCo; 9-fold PWS swing across V_cut; tSNR halves 1.6→4.4 cm/s; whole-placenta 2.4% vs focal 4.3% (ROI trap); VSASL labels moving tissue; 70/30 maternal/fetal; 25-30 s intervillous transit; ~1.6 cm/s suggestion | 5, 8, 9, 12 |
| `zun2017_vsasl_fetal_heart_disease.pdf` | Zun Z, Zaharchuk G, Andescavage NN, Donofrio MT, Limperopoulos C. Non-invasive placental perfusion imaging in pregnancies complicated by fetal heart disease using VSASL MRI. *Sci Rep* 2017;7:16126 | 10.1038/s41598-017-16461-8 | CC BY | Healthy whole-placenta 188±40 mL/100 g/min (1.5 T VSASL); maternal position lateral 207±39 vs supine 171±32; placental spatial CoV 0.58±0.10 healthy / 0.62±0.20 CHD | 2, 3, 4 |
| `qin2022_vsasl_review_recommendations.pdf` | Qin Q, Alsop DC, Bolar DS, … Zun Z, Guo J; ISMRM Perfusion Study Group. VSASL perfusion MRI: review and recommendations. *Magn Reson Med* 2022;88(4):1528-1547 | 10.1002/mrm.29371 | yes | Placenta section leaves V_cut, encoding direction, labelling and readout under study → placental VSASL thresholds must be UNCALIBRATED; no spatial labelling plane definable in placenta | 12, 15 |
| `seiter2024_vsasl_2nd_trimester_bmi.pdf` | Seiter D, Chen R, Ludwig KD, Zhu A, Shah D, Wieben O, Johnson KM. VSASL perfusion measurements in 2nd trimester human placenta with varying BMI. *Placenta* 2024;150:72-79 | 10.1016/j.placenta.2024.03.012 | author MS | GA-stratified VSASL values 216→257 (non-obese) and 249→336 (obese) mL/100 g/min at 15→21 wk; BMI confound at fixed GA | 6 |
| `linksourani2026_late_gestation_structure_perfusion.pdf` | Link-Sourani D, Avisdris N, Shao X, … Ben Bashat D. In vivo assessment of placental structure and perfusion in late gestation. *NMR Biomed* 2026;39(8):e70346 | 10.1002/nbm.70346 | CC BY | Third-trimester pCASL PBF 149.2±16.6 mL/100 g/min, ATT 1670.5±124.7 ms; no GA correlation within a narrow window | 6 |
| `hutter2020_perfox_perfusion_oxygenation.pdf` | Hutter J, Harteveld AA, Jackson LH, et al. Perfusion and apparent oxygenation in the human placenta (PERFOX). *Magn Reson Med* 2020;83(2):549-560 | 10.1002/mrm.27950 | yes | Deliberate refusal to quantify in physiological units (blood T1/T2 depend on oxygenation, haematocrit, maternal-vs-fetal); between-session CoV 9.8±6.3% vs 3.5% back-to-back | 5, 10 |
| `herrera2023_human_placenta_project.pdf` | Herrera CL, Kim MJ, Do QN, Owen DM, Fei B, Twickler DM, Spong CY. The human placenta project: funded studies, imaging technologies, and future directions. *Placenta* 2023 | 10.1016/j.placenta.2023.08.067 | author MS (fei-lab mirror) | NIH programme states GA-adjusted normal ranges still need establishing, and the correct ROI (cotyledon / maternal / fetal / intervillous / composite) remains undetermined | 5 |
| `himoto2025_toward_clinical_placental_function.pdf` | Himoto Y, Fujimoto K, Chigusa Y, et al. Toward clinical implementation of MRI for placental function. *Magn Reson Med Sci* 2025;24(3):343-353 | 10.2463/mrms.rev.2024-0154 | CC BY | Independent confirmation of the standardisation gap; no consensus definition even for placental insufficiency | 1, 2 |
| `suzuki2024_osipi_asl_lexicon.pdf` | Suzuki Y, Clement P, Dai W, Dolui S, et al.; ISMRM Perfusion SG. ASL lexicon and reporting recommendations: an OSIPI consensus report. *Magn Reson Med* 2024;91(5):1926-1943 | 10.1002/mrm.29815 | yes (UGent AM) | OSIPI's own standard is scoped to BRAIN perfusion, and explicitly invites extension — the framing for placental work inside OSIPI | 1 |
| `clement2022_asl_bids.pdf` | Clement P, Castellaro M, Okell TW, et al. ASL-BIDS, the BIDS extension for arterial spin labeling. *Sci Data* 2022;9:543 | 10.1038/s41597-022-01615-9 | CC BY | "ASL-BIDS is validated in ASL images of the brain only"; velocity-selective ASL deferred to a future release | 5, 6 |
| `chappell2023_basil_toolbox.pdf` | Chappell MA, Kirk TF, Craig MS, et al. BASIL: a toolbox for perfusion quantification using ASL. *Imaging Neuroscience* 2023 | 10.1162/imag_a_00041 | CC BY | "the BASIL toolbox does not currently provide any form of automated quality control" — the niche is real | 12 |
| `fan2023_osipi_pipeline_inventory.pdf` | Fan H, Mutsaerts HJMM, Anazodo U, … Dolui S, Petr J. The OSIPI ASL pipeline inventory. *Magn Reson Med* 2023 | 10.1002/mrm.29869 | yes (Amsterdam UMC AM) | The inventory records per-pipeline non-brain usage — basis for "no non-brain ASL pipeline is registered with OSIPI" | 4 |
| `dellschaft2020_haemodynamics_human_placenta.pdf` | Dellschaft NS, Hutchinson G, Shah S, et al. The haemodynamics of the human placenta in utero. *PLoS Biol* 2020;18(5):e3000676 | 10.1371/journal.pbio.3000676 | CC BY | Two-tier motion rejection (>5 mm volume; >4 low-b or >8 total scan-level); utero-placental pump removing up to 40% of placental volume | 9, 13 |
| `martin2020_uterine_motion_tracking.pdf` | Martin T, et al. (Sung K, corr.). Characterization of uterine motion in early gestation using MRI-based motion tracking. *Diagnostics* 2020;10(10):840 | 10.3390/diagnostics10100840 | CC BY | Contraction motion U_max 7.8±5.5 mm vs maternal 2.1±2.1 mm; >1 mm shift as the contraction-onset definition | 5, 7 |
| `dewick2026_placental_contractions.pdf` | Dewick L, Turnbull A, Walker KF, et al. Placental contractions in uncomplicated pregnancies. arXiv:2511.19547 — **PREPRINT** | arXiv:2511.19547 | CC | Contractions in ≥60% of healthy pregnancies, ~2/hour, median 2.4 min; >10% placental-volume-decrease criterion | 2 |
| `hutter2022_t2star_deformation_prelabour_contractions.pdf` | Hutter J, Kohli V, Dellschaft N, et al. Dynamics of T2* and deformation in the placenta and myometrium during pre-labour contractions. *Sci Rep* 2022;12:18542 | 10.1038/s41598-022-22008-3 | CC BY | 226 placental scans searched by eye — no automated contraction detector exists | 1 |
| `ji2023_fetal_behavior_mri.pdf` | Ji L, et al. Fetal behavior during MRI changes with age and relates to network dynamics. *Hum Brain Mapp* 2023;44(4) | 10.1002/hbm.26167 | CC BY | Fetal mean FD 4.78 mm, max 44.72 mm — an order of magnitude above adult fMRI. Caveat: fetal BRAIN | 6 |
| `uus2020_deformable_svr_placenta.pdf` | Uus A, Zhang T, Jackson LH, et al. Deformable slice-to-volume registration for motion correction of fetal body and placenta MRI. *IEEE TMI* 2020;39(9):2750-2759 | 10.1109/TMI.2020.2974844 | yes | Placenta-validated slice-rejection thresholds: NCC 0.75 global, SSIM 0.6 local | 8 |
| `ebner2020_niftymic_fetal_brain.pdf` | Ebner M, Wang G, Li W, et al. An automated framework for localization, segmentation and SRR of fetal brain MRI (NiftyMIC). *NeuroImage* 2020 | 10.1016/j.neuroimage.2019.116324 | CC BY | Annealed NCC slice-rejection schedule 0.5→0.65→0.8. Fetal BRAIN — does not transfer unre-derived | 7 |
| `kuklisovamurgasova2012_outlier_removal_fetal.pdf` | Kuklisova-Murgasova M, Quaghebeur G, Rutherford MA, Hajnal JV, Schnabel JA. Reconstruction of fetal brain MRI with intensity matching and complete outlier removal. *Med Image Anal* 2012 | 10.1016/j.media.2012.07.004 | yes | EM-fitted inlier/outlier mixture — the threshold-free template that cannot penalise older hardware | 2 |
| `karolis2025_dhcp_fetal_fmri_release.pdf` | Karolis VR, Cordero-Grande L, Price AN, et al. The dHCP fetal functional MRI release. *Imaging Neuroscience* 2025 | 10.1162/imag_a_00512 | CC BY | Exclusion by Tukey fences computed WITHIN the cohort (DVARS > P75+1.5·IQR) — grades the scan against its peers, not the hardware | 12 |
| `kim2025_volume_censoring_fetal_connectivity.pdf` | Kim JH, De Asis-Cruz J, Cook KM, Limperopoulos C. Effects of volume censoring on fetal functional connectivity. *Sci Rep* 2025;15:13181 | 10.1038/s41598-025-96538-x | CC BY | FD swept at 0.5-2.5 mm; in-utero thresholds must be re-derived, not inherited from adults | 3 |
| `sanchez2024_fetmrqc.pdf` | Sanchez T, Esteban O, Gomez Y, et al. FetMRQC: a robust QC system for multi-centric fetal brain MRI. *Med Image Anal* 2024 | 10.1016/j.media.2024.103282 | CC BY (arXiv) | The architectural precedent for in-house QC: >1600 rated images, 4 centres, 13 scanners, 332 IQMs, ships metrics not images. Fetal BRAIN T2w — borrow architecture, not metrics | 1, 4 |
| `specktorfadida2025_segqc.pdf` | Specktor-Fadida B, Ben-Sira L, Ben-Bashat D, Joskowicz L. SegQC: segmentation quality control without ground truth. *Med Image Anal* 2025 | arXiv:2411.07601 (journal DOI not verified) | arXiv | Prior art for grading a mask with no ground truth, on fetal MRI including placenta | 4 |
| `snoussi2025_haitch_fetal_dwi.pdf` | Snoussi H, Karimi D, Afacan O, Utkur M, Gholipour A. HAITCH: distortion and motion correction in fetal multi-shell DWI. 2025 | 10.1162/imag_a_00490 | CC BY | Modified z-score on MAD with soft down-weighting rather than hard rejection; its z-thresholds are never given numerically (UNCALIBRATED) | 6 |
| `avilesverdera2025_heron_realtime_fetal_dwi.pdf` | Aviles Verdera J, et al. HERON: high-efficiency real-time motion quantification and re-acquisition for fetal diffusion MRI. *IEEE Trans Med Imaging* 2025;44(11):4171-4180 | 10.1109/TMI.2025.3569853 | CC BY | Real-time on-scanner QC triggering re-acquisition — the endgame for a tool that must run where the scan is | 4 |
| `costanzo2025_fetas_platform.pdf` | Costanzo A, Lim A, et al. Fetal Assessment Suite (FetAS): a web-based platform for automatic fetal MRI analysis using AI. *Sci Rep* 2025 | 10.1038/s41598-025-32298-y | CC BY | The one tool combining artifact detection + MoCo + placenta segmentation is a **hosted service** — unusable under the ethics constraint | 1 |
| `schabel2022_longitudinal_t2star_normative.pdf` | Schabel MC, Roberts VHJ, Gibbins KJ, et al., Frias AE. Quantitative longitudinal T2* mapping for placental function. *PLoS One* 2022;17(7):e0270360 | 10.1371/journal.pone.0270360 | CC BY | Copyable amniotic-fluid contamination rule (exclude T2* ≥ 250 ms); template for a GA-referenced normative model. T2*, not ASL | 5 |
| `hall2024_placental_t2star_field_strength.pdf` | Hall M, Aviles Verdera J, Cromb D, et al., Hutter J. Placental T2* across field strength from 0.55T to 3T. *Sci Rep* 2024;14:28594 | 10.1038/s41598-024-77406-6 | CC BY | Automatic placenta segmentation Dice 0.807/0.796/0.815 at 3/1.5/0.55 T; GA-and-field z-scores as the architecture to imitate | 1 |
| `jacobwitz2026_normative_placental_volume_zscores.pdf` | Jacobwitz M, Ngwa J, Kapse K, Limperopoulos C, Andescavage N. Charting normative reference values and Z-scores for MRI-derived in vivo placental growth. *Pediatr Radiol* 2025;56:384-392 | 10.1007/s00247-025-06469-y | CC BY | Normative placental references exist for VOLUME, not perfusion — the shape of the missing prerequisite | 1 |
| `sorensen2020_t2star_placental_review.pdf` | Sørensen A, Hutter J, Seed M, Grant PE, Gowland P. T2*-weighted placental MRI: basic research tool or emerging clinical test? *Ultrasound Obstet Gynecol* 2020;55:293-302 | 10.1002/uog.20855 | AM via KCL Pure | The only actionable placental ROI guidance: use the largest possible ROI. EXPERT RECOMMENDATION, not consensus, and about T2* not ASL | 9 |
| `turnbull2026_t2star_susceptibility_placental_health.pdf` | Turnbull A, Hutchinson G, Dewick L, et al., Gowland P. T2* and susceptibility mapping as indicators of placental health. arXiv:2603.22092 — **PREPRINT** | arXiv:2603.22092 | CC | Normalised distance to basal/chorionic plates as an implementable maternal/central/fetal region recipe | 4 |
| `abulnaga2023_shape_aware_placenta_segmentation.pdf` | Abulnaga SM, Dey N, Young SI, et al., Golland P. Shape-aware segmentation of the placenta in BOLD fetal MRI time series. *MELBA* 2023 | 10.59275/j.melba.2023-g3f8 | CC BY | Openly downloadable, CPU-capable placenta segmentation on EPI time series, Dice 82.8 — same readout family as placental ASL | 8 |
| `liu2023_consistency_regularization_placenta_seg.pdf` | Liu Y, Karani N, Dey N, Abulnaga SM, Xu J, Grant PE, Abaci Turk E, Golland P. Consistency regularization improves placenta segmentation in fetal EPI MRI time series. PIPPI@MICCAI 2023 | arXiv:2310.03870 | CC | Temporal Dice — a ground-truth-free consistency check. Its 0.8/0.7 cut-offs are UNCALIBRATED defaults | 7 |
| `wang2016_slicseg_placenta_segmentation.pdf` | Wang G, Zuluaga MA, Pratt R, et al., Ourselin S. Slic-Seg: minimally interactive segmentation of the placenta. *Med Image Anal* 2016 | 10.1016/j.media.2016.04.009 | CC BY | One-slice scribble segmentation as a legitimate design target for a tool run by someone who cannot send you the scan | 1 |
| `zhong2025_contrast_invariant_placental_segmentation.pdf` | Zhong X, Liu R, Nichols ES, Zhang X, Laine AF, Duerden EG, Wang Y. Contrast-invariant self-supervised segmentation for quantitative placental MRI. PIPPI@MICCAI 2025 | arXiv:2505.24739 | CC | Segmentation degrades as contrast degrades — the exact failure mode for an ASL difference image | 1 |
| `jittou2025_placenta_segmentation_review.pdf` | Jittou A, El Fazazy K, Riffi J. Placenta segmentation redefined: review of deep learning… *Vis Comput Ind Biomed Art* 2025;8:17 | 10.1186/s42492-025-00197-8 | CC BY | Annotated placental datasets are scarce partly for **ethical data-sharing** reasons — published backing for in-house-only architecture. SECONDARY source for all its numbers | 4 |
| `avilesverdera2023_low_field_055t_pregnancy.pdf` | Aviles Verdera J, Story L, Hall M, et al., Hutter J. Reliability and feasibility of low-field-strength fetal MRI at 0.55 T during pregnancy. *Radiology* 2023 | 10.1148/radiol.223050 | CC BY | Same protocol consumes 93.3% of the SAR limit at 3 T vs 11.4% at 0.55 T | 7 |
| `yetisir2025_fetal_rf_safety_3t.pdf` | Yetisir F, Abaci Turk E, Feldman HA, et al., Grant PE. Fetal MRI: radiofrequency safety assessment at 3 Tesla. *JMRI* 2025 | 10.1002/jmri.29797 | CC BY | Fetal RF safety is an active protocol constraint at 3 T, not a formality | 4 |
| `zhu2025_dielectric_pads_fetal_3t.pdf` | Zhu Z, et al. Improving image quality and decreasing SAR with high dielectric constant pads in 3 T fetal MRI. *JMRI* 2025;61(6):2505-2515 | 10.1002/jmri.29677 | CC BY | Standing-wave shading in the gravid abdomen — a physical reason groups chose 1.5 T. Its SNR numbers are fetal-brain ROIs | 2 |
| `mhra2014_mri_safety_guidelines.pdf` | MHRA. Safety guidelines for magnetic resonance imaging equipment in clinical use, Nov 2014 | — | UK gov, free | Pregnancy treated as a special thermal-exposure case. Cite as **MHRA quoting IEC/ICNIRP**, never as the primary standard | 13 |
| `puris2025_fetal_safety_mri_review.pdf` | Puris G, Chetrit A, Katorza E. Fetal safety in MRI during pregnancy: a comprehensive review. *Diagnostics* 2025;15(2):208 | 10.3390/diagnostics15020208 | CC BY | Navigational review of MRI safety in pregnancy — every number in it is second-hand | 1 |
| `avilesverdera2024_t1_fetal_brain_placenta_055t.pdf` | Aviles Verdera J, Tomi-Tricot R, Story L, et al., Hutter J. Characterizing T1 in the fetal brain and placenta over gestational age at 0.55 T. *Magn Reson Med* 2024 | 10.1002/mrm.30193 | AM | Placental T1 is both field- and GA-dependent → no single constant can be hard-coded | 3 |
| `chandrasekhar2026_adc_perfusion_iugr_3t.pdf` | Chandra Sekhar P, et al. Assessment of ADC and perfusion values of the placenta in IUGR using 3 T MRI. *Ethiop J Health Sci* 2026;36(1) | 10.4314/ejhs.v36i1.6 | CC BY | The only published placental perfusion cut-off (93.75 mL/100 g/min) — a worked example of a threshold to **flag, not adopt** (n=60, AUC 0.703, small elliptical ROIs) | 5 |
| `ho2020_t2star_preterm_preeclampsia.pdf` | **Ho AEP**, Hutter J, Jackson LH, Seed PT, McCabe L, Al-Adnani M, Marnerides A, George S, Story L, Hajnal JV, Rutherford MA, Chappell LC. T2* placental MRI in preterm preeclampsia: an observational cohort study. *Hypertension* 2020 | 10.1161/HYPERTENSIONAHA.120.14701 | CC BY-NC | Placental disease IS cleanly detectable by MRI — with T2*, not ASL perfusion | 1 |
| `deloison2021_dce_placental_perfusion.pdf` | Deloison B, Salomon LJ, Siauve N, et al. Human placental perfusion measured using DCE MRI. *PLoS One* 2021;16(9):e0256769 | 10.1371/journal.pone.0256769 | CC BY | Total placental flow separates IUGR where flow per unit volume does not — perfusion density is often the wrong statistic | 1 |
| `sohlberg2015_ivim_placental_perfusion_fgr.pdf` | Sohlberg S, Mulic-Lutvica A, Olovsson M, Weis J, Axelsson O, Wikström J, Wikström A-K. MRI-estimated placental perfusion in fetal growth assessment. *Ultrasound Obstet Gynecol* 2015;46:700-705 | 10.1002/uog.14786 | CC BY-NC | Clear disease gradient in IVIM perfusion **fraction (%)** — different units from ASL, never mix | 1 |
| `lu2022_ivim_dki_placenta_accreta.pdf` | Lu T, Wang Y, Guo A, Cui W, Chen Y, et al. Monoexponential, biexponential and diffusion kurtosis MR imaging models: quantitative biomarkers in the diagnosis of placenta accreta spectrum disorders. *BMC Pregnancy Childbirth* 2022;22:349 | 10.1186/s12884-022-04644-9 | CC BY | Placenta accreta is NOT a low-perfusion state — bounds the clinical reach of a perfusion-level check | 1 |
| `seiter2022_ferumoxytol_dce_cotyledon_macaque.pdf` | Seiter DP, Nguyen SM, Morgan TK, et al., Wieben O. Ferumoxytol DCE MRI identifies altered placental cotyledon perfusion in rhesus macaques. *Biol Reprod* 2022;107(6):1517-1527 | 10.1093/biolre/ioac168 | CC BY-NC | Cotyledon-level perfusion domains validated against photographed delivered placentas. **Macaque, not human** | 1 |
| `chernyavsky2010_intervillous_flow_placentone.pdf` | Chernyavsky IL, Jensen OE, Leach L. A mathematical model of intervillous blood flow in the human placentone. *Placenta* 2010;31:44-52 | 10.1016/j.placenta.2009.11.003 | AM, Manchester | Placentone reference radius ~2 cm → cotyledon-scale ROIs are resolvable at placental ASL resolution | 4 |
| `slator2019_placenta_imaging_workshop_2018.pdf` | Slator P, et al. Placenta Imaging Workshop 2018 report: multiscale and multimodal approaches. *Placenta* 2019;79:78-82 | 10.1016/j.placenta.2018.10.010 | AM | The community's own statement that reproducibility and protocol standardisation are unsolved | 5 |
| `sadiku2024_advanced_placental_mri_fgr_chd.pdf` | Sadiku E, Sun L, Macgowan CK, Seed M, Morrison JL. Advanced MRI in human placenta: FGR and CHD. *Front Cardiovasc Med* 2024 | 10.3389/fcvm.2024.1426593 | CC BY | Maternal position (supine vs left lateral) as an oxygenation confounder; orientation across T2/T2*/BOLD/IVIM/ASL | 8 |
| `driver2026_vsasl_bolus_duration.pdf` | Driver ID, Chandler HL, Patitucci E, et al. VSASL bolus duration measurements: implications for consensus recommendations. *Imaging Neuroscience* 2026 | 10.1162/imag_a_00506 | CC BY | Bolus duration shortens under high flow, challenging the recommended τ. The placenta is a high-flow organ whose bolus duration has never been measured | 1 |

## Downloaded, not yet cited

Held for the design round; no highlighted page yet.
`hutter2020_perfox_arxiv_preprint.pdf` (preprint duplicate of PERFOX) ·
`uus2020_deformable_svr_arxiv.pdf` (preprint duplicate of Uus 2020) ·
`pietsch2021_applause_SUPPLEMENT_ONLY.pdf` (supplement only) ·
`leon2025_fair_asl_chd_SUPPLEMENT_emethods.pdf` (eMethods only — carries the FAIR sequence detail).

## Readable as text only — no retrievable PDF

These are NIHMS author manuscripts. They are free to read on the PMC website but
their PDFs are served only behind PMC's proof-of-work bot challenge, which was
not circumvented. Europe PMC returns *"Failed to retrieve PDF"* for each, and
the PMC AWS Open Data mirror carries only `.txt`/`.xml` for them. **Full text is
saved in `../fulltext_no_pdf/`** — quotes can be verified, but no page can be
rendered or highlighted until someone downloads the PDF manually from PMC (one
click, free, in a browser).

| Paper | DOI | PMCID | What is lost |
|---|---|---|---|
| **Zun Z, Limperopoulos C. Placental perfusion imaging using VSASL. *Magn Reson Med* 2018;80(3):1036-1047** | 10.1002/mrm.27100 *(unverified)* | PMC5980687 | **The single biggest loss.** The only placental ASL repeatability study (wsCV ~3.5%, repeatability 19.7 mL/100 g/min, ICC 0.97) and the head-to-head showing pCASL reads 9-16% of VSASL. All verified present in the text dump |
| **Shao X, Liu D, Martin T, et al. Multi-delay 3D GRASE pCASL at 3 T. *JMRI* 2018;47(6):1667-1676** | 10.1002/jmri.25893 *(unverified)* | PMC5951737 | PBF 111.4±26.7, ATT ~1387 ms, λ=1.0, labelling efficiency 63.8%, tSNR ~1.4 |
| **Liu D, Shao X, Danyalov A, et al. Human placenta blood flow during early gestation with pCASL. *JMRI* 2020;51(4):1247-1257** | 10.1002/jmri.26944 *(unverified)* | PMC7654100 | The ROI trap: whole-placenta 104.9 vs hPBF 278.1 mL/100 g/min in the same women |
| **Herrera CL, Wang Y, Udayakumar D, et al. Longitudinal pCASL in normal and hypertensive pregnancies. *Eur Radiol* 2023;33(12):9223-9232** | 10.1007/s00330-023-09945-x *(unverified)* | PMC10796849 | Table 3, the only cross-study compilation of placental ASL values; the dissenting decreasing GA trend |
| Ludwig KD, Fain SB, Nguyen SM, et al. Placental ASL vs ferumoxytol DCE in the rhesus macaque. *Magn Reson Med* 2019;81(3):1964-1978 | 10.1002/mrm.27548 *(unverified)* | PMC6715150 | ASL-vs-contrast validation; ferumoxytol arrival 34±25 s vs any ASL label lifetime |
| Stout JN, Liao C, Gagoski B, et al. Quantitative T1/T2 by MRF of the placenta before and after maternal hyperoxia. *Placenta* 2021;114:124-132 | 10.1016/j.placenta.2021.08.007 *(unverified)* | PMC8511125 | Placental T1 at 3 T (1825±141 ms) |
| Lee B, et al. Early pregnancy imaging predicts ischemic placental disease. *Placenta* 2023 | 10.1016/j.placenta.2023.07.010 *(unverified)* | PMC11090111 | Largest control group (n=147); mean perfusion does not separate IPD |
| Abaci Turk E, Abulnaga SM, Luo J, et al. Effect of maternal position and uterine contractions on placental BOLD MRI. *Placenta* 2020;95:69-77 (+ erratum 100:171-172) | 10.1016/j.placenta.2020.04.008 *(unverified)* | PMC7358045 | 57% Braxton-Hicks prevalence; position changes global R2* |
| Abaci Turk E, et al. Recent innovations in placental MRI. *Placenta* 2026 | — | PMC13317445 | Most current review; the field's own challenge list |
| Frias AE, Schabel MC, Roberts VH, et al. DCE MRI of maternal vascular organization in the primate placenta. *Magn Reson Med* 2015;73:1570-8 | 10.1002/mrm.25264 *(unverified)* | PMC4487918 | 16 perfusion domains in one macaque placenta matching cotyledons at delivery |
| Wang Y, Greer JS, Zhou L, et al., Madhuranthakam AJ. A 3D-printed phantom for QC of ASL perfusion. *Magn Reson Med* 2024;91:819-827 | 10.1002/mrm.29900 *(unverified)* | PMC10841664 | Body-ASL group (Mora Álvarez's collaborators) stating the cross-protocol comparison problem |
| Xu J, Lala S, Gagoski B, et al. Semi-supervised learning for fetal brain MRI quality assessment. MICCAI 2020 | 10.1007/978-3-030-59725-2_37 *(unverified)* | PMC9652031 | The fetal-IQA model FetMRQC ships as a checkpoint |
| Xu J, Moyer D, Gagoski B, et al. NeSVoR: implicit neural representation for SVR. *IEEE TMI* 2023 | 10.1109/TMI.2023.3236216 *(unverified)* | PMC10287191 | Reconstruction that emits per-voxel uncertainty |
| Abaci Turk E, Stout JN, Ha C, et al., Grant PE. Placental MRI: developing accurate quantitative measures of oxygenation. *Top Magn Reson Imaging* | — | PMC7323862 | Mined; contains no bowel-gas or artifact quantification (negative result, recorded so it is not re-searched) |

## Could not obtain at all

Paywalled, or blocked by publisher bot protection (HTTP 403). No paywall was
circumvented. Request through an institution.

| Paper | DOI / ID | Why it matters | Barrier |
|---|---|---|---|
| **Jungelson A, Bartin R, Arthuis C, Henry C, Taso M, Alsop DC, Bussières L, Ville Y, Salomon LJ, Grévent D. Reproducibility and factors affecting perfusion measurement in normal pregnancies with single-slice FAIR ASL. *Placenta* 2026;179:121-127** | 10.1016/j.placenta.2026.04.006 *(unverified)* | **Highest priority.** 66 women, one scanner, one session: FAIR inversion-slab thickness 30/50/70 mm moves placental blood flow 323.5 → 213.8 → 175.6 mL/100 g/min. The knock-down argument that the protocol grades itself. Alsop is a co-author | Elsevier, abstract only |
| **Mora Álvarez MG, Madhuranthakam AJ, Udayakumar D. Quantitative non-contrast perfusion MRI in the body using ASL. *MAGMA* 2024;37(4):681-695** | 10.1007/s10334-024-01188-1 *(unverified)* | First-authored by reviewer **María Mora Álvarez**, with a placenta section — her own framing of the problem. Ask her directly | Springer paywall + bot challenge |
| Jha P, Pōder L, Bourgioti C, et al. SAR/ESUR joint consensus statement for MR imaging of placenta accreta spectrum. *Eur Radiol* 2020;30(5):2604-2615 | 10.1007/s00330-019-06617-7 *(unverified)* | The **only** true placental MRI consensus — RAND-UCLA, 80% agreement, seven T2w anatomical signs, and **nothing** about perfusion, ASL or quantification. Cite precisely to make the negative finding airtight | Springer paywall |
| Gowland PA, Francis ST, Duncan KR, et al. In vivo perfusion measurements in the human placenta using EPI at 0.5 T. *Magn Reson Med* 1998;40(3):467-473 | 10.1002/mrm.1910400318 *(unverified)* | Origin of the much-repeated "176 ± 24" — which is a **standard error**, n=16, SD 96. A citation-hygiene trap worth documenting from the primary | Wiley paywall |
| Derwig I, Lythgoe DJ, Barker GJ, et al., Nicolaides K. Placental perfusion by MRI and uterine artery Doppler. *Placenta* 2013;34(10):885-891 | 10.1016/j.placenta.2013.07.006 *(unverified)* | FAIR reported in **arbitrary units** with separate basal/central/whole-placental ROIs | Elsevier paywall |
| Meakin JA, Jezzard P (2013) 10.1002/mrm.24345 · Guo J, Meakin JA, Jezzard P, Wong EC (2015) 10.1002/mrm.25227 · Wong EC, et al. (2006) 10.1002/mrm.20906 · Wu WC, Wong EC (2006) 10.1016/j.neuroimage.2006.03.001 | as listed | VSASL eddy-current sensitivity (up to 2× GM perfusion overestimation), sym-BIR-8, the origin paper, and the "keep V_cut below 4 cm/s" guidance | Wiley / Elsevier paywalls |

Also unretrieved and worth noting: Otake 2019 (EJOG, cotyledons not evident on
MRI before the third trimester), Liu 2023 JMRI (spatial-attentive placental
segmentation), Kulseng 2023 (60-90 min manual annotation cost), Aviles Verdera
2025 *Placenta* (821-scan contractility study), Sinding 2016 (UOG, placental T2*
repeatability), Barrera 2020 (*Radiology*, SAR equivalence 1.5 T vs 3 T), Ray
2016 (*JAMA*, gadolinium in pregnancy), Maralani 2022 (CAR recommendations),
Gagoski 2022 (real-time HASTE reacquisition), Liu 2026 (OR-KAN), Neves Silva
2026 (on-scanner volumetry), and *RadioGraphics* 2023 "Fetal MRI at 3 T"
(HTTP 403 — no bowel-gas claim should rest on it until read).

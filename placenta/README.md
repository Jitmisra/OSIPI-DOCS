# 🫃 Placenta QC — extending the ASL QC ToolBox to placental perfusion

Research and design for taking the QC toolbox (`qc-design.md`, `qc-detailed.md`) to **placental ASL**,
after brain and kidney.

---

## The two documents

| Document | Words | What it is |
|---|---|---|
| **[PLACENTA_QC_DESIGN.md](PLACENTA_QC_DESIGN.md)** | 17,000 | The proposed check set — 15 checks, each with inputs, output schema, method, verdict rule, sources and provenance tier. Same structure as `qc-detailed.md`. |
| **[PLACENTA_ASL_EXPLAINED.md](PLACENTA_ASL_EXPLAINED.md)** | 15,100 | Placental ASL from zero, for someone who knows brain ASL. Carries the highlighted source pages. |

---

## The finding that shapes the whole design

**There is no consensus, guideline or recommendation document for placental ASL.** Not a weaker one —
none.

The evidence is in the ISMRM Perfusion Study Group's own body-ASL review (Taso et al. 2023). Of the
organs it covers, kidney and eye receive explicit **"Recommendations"** sections. The placenta is the
**only organ in the paper that receives neither recommendations nor summarised practice** — its
Supporting Table S2 tabulates study means without endorsing any value or any protocol.

So **every threshold in this design is UNCALIBRATED**, and each one says so where it appears. Zero
thresholds are tagged PUBLISHED, because none can be.

### And the acquisition moves the number more than the biology does

| Effect | Magnitude |
|---|---|
| Published "normal" whole-placenta perfusion, across 14 studies | **71 – 324 mL/100 g/min** (~4.5×) |
| pCASL vs VSASL, **in the same women** | pCASL reads **9–16%** of VSASL |
| FAIR inversion-slab thickness, 70 mm → 30 mm | **1.84×** change |
| VSASL cut-off velocity, across its published range | **9-fold** change in signal |
| Maternal position (between-subject) | **21%** |

An absolute magnitude threshold here would grade the protocol, not the patient. That is the same
reason the task force ruled preclinical ASL out of scope — where coil quality, not data quality, moves
the number.

---

## What makes the placenta different

- **It is a temporary organ.** Perfusion changes across gestation, so every value carries a
  gestational age or it is unusable. No fixed band can be defensible.
- **Two circulations.** Maternal (spiral arteries) and fetal (umbilical) do not mix, and what ASL
  labels is not obvious. The explainer takes this slowly because it is the hardest idea in the organ.
- **No labelling plane.** There is no single feeding artery, which is why velocity-selective ASL and
  FAIR appear here instead of pCASL.
- **Motion is threefold** — maternal breathing, fetal movement, and uterine contractions. No
  rigid-body assumption survives.
- **Gadolinium is contraindicated in pregnancy**, which is exactly why non-contrast ASL matters here.

---

## Evidence

**[screenshots/](screenshots/)** holds 70 rendered pages from the source papers with the **exact
sentences highlighted** that each claim rests on. Every phrase was confirmed present in the PDF's own
text layer before being marked.

- **[evidence.json](evidence.json)** — 84 quotes, each with its paper, page, the claim it supports and
  why it matters.
- **[highlights.json](highlights.json)** — the machine-readable spec, so the highlighting reproduces.
- **[papers/README.md](papers/README.md)** — the 58 source papers with DOIs.

Paper PDFs are not committed: they are open-access and reachable from the DOIs. The highlighted pages
are here because they *are* the evidence.

---

## Provenance

| Tier | Meaning | Count here |
|---|---|---|
| 📄 **PUBLISHED** | a paper states this number for this purpose | **zero** |
| 💻 **IMPLEMENTATION** | one study's method or code uses it | a few |
| 🔧 **UNCALIBRATED** | an engineering default, no published derivation | the rest |

Where a number could not be verified in the paper corpus it is marked unverified with its DOI rather
than presented as established. Where the literature supports no threshold at all, that is stated as
the finding it is.

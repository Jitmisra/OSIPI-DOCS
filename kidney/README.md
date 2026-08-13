# 🫘 Kidney QC — extending the ASL QC ToolBox from brain to kidney

Research and design for taking the brain QC toolbox (`qc-design.md`, `qc-detailed.md` in this repo)
to **renal ASL**.

---

## Start here

| Document | Words | Why read it |
|---|---|---|
| **[KIDNEY_QC_BRIEF.md](KIDNEY_QC_BRIEF.md)** ⭐ | 2,800 | **The one to read.** The whole design in ten minutes, with the module map and the brain→kidney mapping as diagrams. |
| [KIDNEY_QC_ONE_PAGER.md](KIDNEY_QC_ONE_PAGER.md) | 1,300 | The 30-second version. |
| [KIDNEY_QC_DESIGN.md](KIDNEY_QC_DESIGN.md) | 25,700 | The full spec — 23 checks, each with inputs, output schema, method, verdict rule, sources and provenance. Same structure as `qc-detailed.md`. |
| [KIDNEY_ASL_EXPLAINED.md](KIDNEY_ASL_EXPLAINED.md) | 18,400 | Renal ASL from zero, for someone who knows brain ASL. Carries the highlighted source pages. |
| [BRAIN_TO_KIDNEY.md](BRAIN_TO_KIDNEY.md) | 7,000 | Every one of the 20 brain checks: transfers as-is / needs new bands / needs rework / does not transfer. |

Each has a PDF alongside it with the diagrams rendered.

**[deck/](deck/)** — a 33-slide presentation, one slide per check, each carrying the diagram, the
method, and the real input/output JSON. `KIDNEY_QC_DECK.html` opens in a browser and scales to any
screen; `KIDNEY_QC_DECK.pdf` is the same deck for sending.

---

## The finding that shapes the whole design

**Nery et al. 2020** (*MAGMA*, PARENCHIMA COST Action) is the renal equivalent of the 2015 ASL White
Paper — 59 consensus statements, all of which were read for this work. It contains **zero numeric
image-quality thresholds**: no tSNR cut-off, no CoV cut-off, no motion limit, no QEI equivalent.

So it gives a hard, quotable **protocol-conformance spec** for the acquisition (Stream A) and
**nothing at all** for judging the perfusion map (Stream B). Every renal CBF-map threshold proposed
here is therefore labelled **UNCALIBRATED**, and says so on the page it appears.

That is a statement about the state of the field, not a gap in the work.

### Two consensus rules do transfer, and both are load-bearing

- **R10.1** (0% abstentions, **100% agreement**) — report **cortical** RBF, not whole-kidney, and
  **separately for left and right kidney**. This fixes the report schema: every metric is
  `{left, right}` and never a pooled scalar.
- **R10.2** (14% abstentions, **89% agreement**) — medullary RBF values *"are not considered reliable
  with current measurement approaches"*. This is why the cortex:medulla ratio is a
  **segmentation-integrity flag** and never a perfusion verdict — the obvious grey/white-matter
  analogue is undercut by the consensus itself.

---

## Evidence

**[screenshots/](screenshots/)** holds 77 rendered pages from the source papers with the **exact
sentences highlighted** that each claim rests on. Every phrase was confirmed present in the PDF's own
text layer before being marked.

- **[evidence.json](evidence.json)** — each quote with its paper, page, the claim it supports, and why
  it matters. All 77 verified correctly attributed.
- **[highlights.json](highlights.json)** — the machine-readable spec, so the highlighting is reproducible.
- **[papers/README.md](papers/README.md)** — all 51 source papers with DOIs and what each supports.

The paper PDFs themselves are not committed: they are open-access and reachable from the DOIs, and
85 MB of publisher PDFs is not worth the repo weight. The highlighted page images are here because
they *are* the evidence.

---

## Provenance

Every number carries a tier, exactly as in the brain documents:

| Tier | Meaning |
|---|---|
| **PUBLISHED** | a paper states this number for this purpose |
| **IMPLEMENTATION** | a reference pipeline's code uses it |
| **UNCALIBRATED** | an engineering default, no published derivation |

For kidney, **most Stream B thresholds are UNCALIBRATED** — and each one says so where it appears
rather than being quietly presented as settled. Where the literature supports no threshold at all,
that is stated as the finding it is.

Two citations (Tan 2014, Robson 2009) are not in the paper corpus; their numbers were verified against
the PMC records and they are marked ⚠️ not-in-corpus with DOI and PMCID so they can be re-checked.

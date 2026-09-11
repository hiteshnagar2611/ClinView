# ClinView — ClinVar Missense 3D Visualizer

A single-file web app that maps **ClinVar missense variants onto AlphaFold-predicted 3D protein structures**, so you can see the residue in its spatial context and judge structural plausibility at a glance.

It also overlays **AlphaMissense pathogenicity scores** (via MyVariant.info → dbNSFP) so you can compare machine-learning pathogenicity against expert ClinVar curation side-by-side.

![preview](https://img.shields.io/badge/clientside-100%25-blue) ![data](https://img.shields.io/badge/data-ClinVar%20%2B%20UniProt%20%2B%20AFDB-green)

## Features

- 🔎 **Gene → ClinVar**: pull up to 500 missense records via NCBI E-utilities; parses 1- and 3-letter HGVS `p.` notation, classification, review status, conditions, VCV / rsID
- 🧬 **Gene → UniProt → AlphaFold DB**: resolves the canonical human protein, downloads the predicted PDB
- 🎨 **3D viewer (3Dmol.js)**:
  - Cartoon colored by **pLDDT confidence**
  - One sphere per variant position, colored by clinical significance (red = Pathogenic → green = Benign)
  - Click a sphere or list row to zoom to the residue
- 🧠 **AlphaMissense overlay** (DeepMind 2023):
  - Per-variant AM score badge in the list
  - Dedicated score panel in details
  - **Color: ClinVar / AlphaMissense** toggle re-colors spheres by AM score
  - Filter & sort by AM category and score
- 🟢 **Coverage badges**, filter chips (Pathogenic / VUS / Benign / With AM / etc.), and pLDDT confidence legend
- 100% client-side — no build, no backend, no API keys

## Quick start

```bash
# serve the folder (any static server works)
python3 -m http.server 8000

# open http://localhost:8000
# it auto-loads BRCA1; type any gene (TP53, CFTR, KRAS…) and press Load
```

## Data sources

| Source | Endpoint | Used for |
|---|---|---|
| NCBI E-utilities | `eutils.ncbi.nlm.nih.gov/entrez/eutils` | ClinVar missense search + summaries |
| UniProt REST | `rest.uniprot.org/uniprotkb/search` | Canonical human protein for the gene |
| AlphaFold DB | `alphafold.ebi.ac.uk/api/prediction` | Predicted structure (PDB) |
| MyVariant.info | `myvariant.info/v1/variant` (POST) | dbNSFP AlphaMissense scores |

All four endpoints serve `Access-Control-Allow-Origin: *`, so the app runs entirely from the browser.

## Caveats

- **Isoform mismatch**: residue numbers come from ClinVar HGVS p. (RefSeq-based). If the RefSeq isoform differs from the UniProt canonical sequence, highlights may be offset. The app shows a ⚠️ / `*` warning when the reference amino acid at that position doesn't match the AFDB sequence.
- **AlphaMissense coverage is partial**. dbNSFP is a static dataset that lags behind ClinVar — only ClinVar UIDs indexed by dbNSFP return scores. For well-curated genes (TP53, BRCA1 for classic variants, KRAS) coverage is good; for very recent ClinVar submissions it's sparse. The legend shows `n/total scored`.
- **AlphaFold confidence**: AFDB models include disordered / low-confidence regions (orange/red pLDDT). Structural conclusions in those regions should be treated with caution.

## Citations

ClinView is a thin client-side visualizer — it queries public APIs and renders their data. **If you publish work that used this app, please cite the underlying data sources and tools, not just this repo.**

### Data sources

- **ClinVar** — public archive of clinical variant interpretations.
  Landrum MJ, Lee JM, Benson M, et al. *ClinVar: improving access to variant interpretations and supporting evidence.* Nucleic Acids Research. 2020;48(D1):D835–D844.
  <https://doi.org/10.1093/nar/gkz1022>

- **AlphaFold Protein Structure Database** — predicted 3D structures used here.
  Jumper J, Evans R, Pritzel A, et al. *Highly accurate protein structure prediction with AlphaFold.* Nature. 2021;596:583–589.
  <https://doi.org/10.1038/s41586-021-03819-2>
  Varadi M, Bertoni D, Magana P, et al. *AlphaFold Protein Structure Database in 2024: providing structure coverage for over 214 million protein sequences.* Nucleic Acids Research. 2024;52(D1):D368–D375.
  <https://doi.org/10.1093/nar/gkad1051>

- **UniProt** — canonical protein sequence mapping.
  The UniProt Consortium. *UniProt: the Universal Protein Knowledgebase in 2025.* Nucleic Acids Research. 2025;53(D1):D609–D617.
  <https://doi.org/10.1093/nar/gkae1010>

- **AlphaMissense** — pathogenicity scores overlaid on each variant.
  Cheng J, Novati G, Pan J, et al. *Accurate proteome-wide missense variant effects with 3D genome language models.* Science. 2023;381:eadl1208.
  <https://doi.org/10.1126/science.adg7492>
  *(Scores are served via MyVariant.info → dbNSFP; the AlphaMissense data itself is licensed CC-BY 4.0 by DeepMind.)*

- **dbNSFP / MyVariant.info** — pre-computed pathogenicity-prediction aggregator used to fetch AlphaMissense.
  Liu X, Li C, Mou C, Dong Y, Tu Y. *dbNSFP v4: a comprehensive database of transcript-specific functional predictions for human nonsynonymous and splice-site variants.* Genome Biology. 2020;21:270.
  <https://doi.org/10.1186/s13059-020-02127-x>

### Rendering tool

- **3Dmol.js** — WebGL molecular viewer used in this app.
  Rego N, Koes D. *3Dmol.js: molecular visualization with WebGL.* Bioinformatics. 2015;31(8):1322–1324.
  <https://doi.org/10.1093/bioinformatics/btu829>

### This app

If you want to cite ClinView itself in a methods section:

> Nagar, H. *ClinView: a single-file web visualizer for ClinVar missense variants on AlphaFold structures with AlphaMissense overlays.* <https://github.com/hiteshnagar2611/ClinView> (2026).

## Files

- `index.html` — the entire app (HTML + CSS + JS, with Tailwind + 3Dmol.js from CDN)
- `LICENSE` — MIT

## License

MIT

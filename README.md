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

## Files

- `index.html` — the entire app (HTML + CSS + JS, with Tailwind + 3Dmol.js from CDN)
- `LICENSE` — MIT

## License

MIT

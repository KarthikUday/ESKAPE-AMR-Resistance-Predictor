# ESKAPE AMR Resistance Predictor

**Predicting antibiotic resistance in ESKAPE pathogens using 
Random Forest classification on real clinical isolate data.**

---

## Overview

This project applies machine learning to predict antibiotic resistance 
phenotypes across all six ESKAPE pathogens — the leading cause of 
hospital-acquired infections globally. Using 17,904 phenotypic AMR 
records sourced directly from the PATRIC database (NIH), a Random Forest
classifier was trained to distinguish Resistant from Susceptible isolates 
based on species identity, antibiotic, and drug class features.

## Key Results

| Metric | Value |
|--------|-------|
| Test ROC-AUC | 0.759 |
| Cross-validated ROC-AUC | 0.724 ± 0.029 |
| Test Accuracy | 67.5% |
| Training records | 17,904 |
| Pathogens covered | 6 (all ESKAPE) |
| Drug classes | 12 |

## Key Findings

### Finding 1: The Genomic Feature Gap
A Random Forest classifier trained exclusively on phenotypic features 
achieved a mean cross-validated ROC-AUC of 0.724 — meaningful but bounded.

This ceiling is biologically expected. AMR is mechanistically driven by 
genomic events: acquisition of resistance genes (e.g. bla_KPC, mcr-1, vanA), 
chromosomal mutations (e.g. gyrA in fluoroquinolone resistance), and mobile 
genetic elements. Phenotypic labels alone cannot encode this mechanistic 
diversity. This finding supports the growing consensus that genomic features 
are essential for high-accuracy AMR prediction 
(Moradigaravand et al., 2018; Kouchaki et al., 2019).

### Finding 2: Species-Specific Predictability

| Species | AUC | Interpretation |
|---------|-----|----------------|
| E. cloacae | 0.823 | Resistance driven by inducible AmpC beta-lactamase — a consistent, class-level mechanism phenotypic data can partially capture |
| A. baumannii | 0.791 | Carbapenem resistance via OXA-type carbapenemases produces strong class-level signals |
| E. faecium | 0.733 | vanA/vanB operons and HLAR — partially predictable but variable across strains |
| S. aureus | 0.731 | MRSA mecA-mediated resistance is consistent, but co-resistance varies by lineage (CC8, CC22) |
| K. pneumoniae | 0.670 | Highly plastic resistome — ESBL, KPC, NDM, OXA-48 producers co-exist with overlapping phenotypes |
| P. aeruginosa | 0.625 | Intrinsic MDR via constitutive efflux pumps (MexAB-OprM, MexCD-OprJ, MexXY) — hardest ESKAPE pathogen to model without genomic data |

### Finding 3: Feature Importance
The two most important features were `combo_resist_rate` and `abx_resist_rate` 
— population-level resistance rates derived from the dataset itself. The model 
is essentially learning *how resistant is this species to this drug class on 
average across clinical isolates* — epidemiological knowledge, not mechanistic 
prediction.

This distinction is critical: the model captures resistance epidemiology well, 
but cannot predict resistance in a novel isolate without prior population data. 
This is precisely the gap that WGS-based tools like ResFinder, AMRFinderPlus, 
and Kleborate are designed to address.

### Wet Lab Correlation
In our own laboratory work, we observed differential inhibition of ESKAPE 
pathogens by candidate biomolecules — consistent with this model's finding 
that resistance profiles vary substantially across the ESKAPE group. Pathogens 
with more variable resistance profiles (P. aeruginosa, K. pneumoniae) also 
tend to be harder to inhibit with novel compounds, reinforcing the clinical 
relevance of computational resistance profiling.

---
## Visualisations

![ESKAPE AMR Analysis](ESKAPE_AMR_Predictor.png)

## Repository Contents

| File | Description |
|------|-------------|
| `ESKAPE_AMR_Predictor.ipynb` | Full analysis notebook (data fetch -> cleaning -> ML -> interpretation) |
| `ESKAPE_AMR_Predictor.png` | Publication-quality figure panel |

## Data Source

All AMR records are fetched live from the 
[PATRIC database](https://www.patricbrc.org/) — a free, 
NIH-funded pathogen bioinformatics resource. No static data 
files are stored in this repository; the notebook fetches 
data directly via the PATRIC API.

## Author

**Karthik Uday**  
M.Sc. Biotechnology — AI & Biomedical Data Analysis  
Vellore Institute of Technology, India

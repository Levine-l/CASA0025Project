# CASA0025 – Scam Compound Explorer

## Project Overview

This project develops a Google Earth Engine (GEE)–based application for exploring candidate locations that are spatially similar to documented scam-compound sites in Southeast Asia, with the main implemented workflow focused on the **Cambodia–Vietnam AOI**.

The application combines Google Satellite Embedding similarity, Sentinel-2 change indicators, VIIRS night-time lights, and contextual distance variables to support a two-stage remote-sensing screening and prioritisation workflow. The results are intended to help users explore possible compound-like development patterns, not to provide definitive confirmation of scam-compound activity.

---

## Live Links

- **Web report:** https://levine-l.github.io/CASA0025Project/
- **Earth Engine App:** https://orbital-kit-415514.projects.earthengine.app/view/scam-compound-explorer
- **Source repository:** https://github.com/Levine-l/CASA0025Project
- **Workspace repository:** https://github.com/Levine-l/CASA0025_project_workspace

--- 

## Team Roles 

- **Preprocessing:** Piyapa, Shuting
- **Analysis:** Hongxi, Jiayi
- **Visualisation:** Siyi, Wanqi

All team members contribute to the final presentation. 

---

## Problem Statement

Scam compounds are difficult to systematically monitor because of:

- limited verified ground-truth data;
- rapid development in border regions;
- overlap with legitimate urban, industrial, and commercial environments;
- fragmented reporting across countries and organisations.

This project aims to support researchers, investigative journalists, human-rights monitors, and policy-oriented users by providing an interpretable geospatial tool for exploring potential high-risk areas based on satellite-derived spatial patterns. The workflow is designed for screening and prioritisation rather than definitive classification.

---

## Objectives

- Compile and structure reported confirmed, suspected, and control locations for use in GEE.
- Use Google Satellite Embedding similarity to generate a broad candidate screening layer.
- Refine candidate locations using Sentinel-2 vegetation and built-up change indicators, VIIRS night-time lights, and contextual distance variables.
- Validate the workflow using suspected sites, non-reference confirmed sites, held-out confirmed samples, and control points.
- Build an interactive GEE application that allows users to inspect candidate density, reported sites, validation results, evidence indicators, and priority tiers.

---

## Study Area

The wider study design considers several Southeast Asian border regions, including:

- Cambodia–Vietnam corridor;
- Myanmar–Thailand border;
- Golden Triangle region.

The fully implemented and validated analysis workflow currently focuses on the **Cambodia–Vietnam AOI**, where the available reported-site dataset contains:

- 53 confirmed reported sites;
- 45 suspected reported sites;
- 22 control points.

Myanmar–Thailand and the Golden Triangle are retained as regional context or possible extension AOIs, rather than fully validated candidate-detection outputs at this stage.

---

## Methodology Summary

The analysis is organised as a two-stage workflow.

### Stage 1: Satellite Embedding Similarity

Stage 1 uses the **Google Satellite Embedding V1 Annual** dataset for 2024. Each pixel is represented by a 64-dimensional embedding vector. Three reproducible confirmed Cambodia reference samples are used to construct a similarity surface over the Cambodia–Vietnam AOI.

For each reference sample, the workflow computes dot-product similarity between the reference embedding vector and each AOI pixel embedding vector. The per-reference similarity images are then averaged. Pixels above the 97th percentile of the sampled similarity distribution are retained as broad Stage 1 candidates.

The preferred Stage 1 configuration is:

- analysis year: 2024;
- AOI: Cambodia–Vietnam;
- reference samples: 3 confirmed Cambodia sites;
- sample scale: 20 m;
- threshold: p97;
- minimum connected pixels: 4.

### Stage 2: Indicator-Based Refinement

Stage 2 converts the broad Stage 1 candidate output into candidate-level evidence. Candidate points are buffered into 500 m zones, then summarised against a 2021–2024 metric stack containing:

- NDVI change;
- NDBI change;
- VIIRS night-time-light change;
- 2024 night-time-light intensity;
- distance to nearest confirmed reported site;
- distance to national border.

Candidate tiering is based on two main evidence flags:

- **development flag:** built-up signal increases while vegetation signal decreases;
- **activity flag:** night-time-light radiance increases.

Candidates are assigned to:

- **high tier:** both development and activity flags triggered;
- **medium tier:** one of the two flags triggered;
- **low tier:** neither flag triggered, retained for uncertainty.

A smaller operational shortlist is used for default app display. This shortlist is narrower than the analytical high tier and applies stricter distance and night-time-light conditions.

---

## Validation Summary

Validation is treated as a check on screening behaviour, not as proof of site status.

Stage 1 achieved a mean held-out confirmed recall of 1.0 across repeated splits, indicating that the embedding screening layer recovered known confirmed-site patterns effectively. However, Stage 1 also captured control points, so it is interpreted as a high-recall screening surface rather than a precise classifier.

Stage 2 refinement reduced control hits from 22/22 under Stage 1 to 7/22 for all refined candidates within 500 m. This improved specificity but also reduced recall for known sites. Therefore, the final output should be interpreted as a prioritisation layer for further investigation, not as a verified list of scam compounds.

---

## Key Outputs

| Output group | Count | Meaning |
|-------------|------:|---------|
| Stage 1 / all refined candidate zones | 24,464 | Broad embedding-derived candidate universe passed into Stage 2 refinement |
| Stage 2 high tier | 8,425 | Development and activity flags both triggered |
| Stage 2 medium tier | 13,059 | One of the two flags triggered |
| Stage 2 low tier | 2,980 | Neither flag triggered, retained for uncertainty |
| Operational shortlist | 15 | Narrow default-display subset using stricter distance and night-time-light conditions |

---

## Repository Structure

```text
CASA0025Project/
│
├── index.qmd                 # Main Quarto web report
├── _quarto.yml               # Quarto project configuration
├── scam.css                  # Custom web styling
├── README.md                 # Project documentation
│
├── images/                   # Banner, screenshots, figures and report images
│
├── data/           # Cleaned and exported data tables used by the app/report
│   ├──Processed
│   │   ├──README.md
│   │   ├── scam_points_update.csv
│   │   └── scam_sites.csv
│   ├──raw
│   │   ├──README.md
│   │   ├──ASA2394472025ENGLISH.pdf
│   │   ├──Scamland-Myanmar.pdf
│   │   └──scam_points_update.xlsx
│   │
│   └──README.md
│
├── gee_scripts/              # Google Earth Engine source code
|   ├── Preprocessing/
│   │   ├── 01_data_setup_and_indicators.txt
│   │   └──README.md
│   ├── Analysis/
│   │   ├── 0_data_setup_and_indicators_for_analysis.js
│   │   ├── 1_embedding_similarity.js
│   │   ├── 2_candidate_metrics_temporal_distance.js
│   │   ├── 3_final_tiering&summary.js
│   │   ├── 4_validation.js
│   │   └── 5_app_ready_layers.js
│   │
│   └── visualization/
│       ├── scam_compound_gee_app_v3.js
│       └── scam_compound_gee_app_v3_3.js
│
└── docs/                     # Rendered GitHub Pages output

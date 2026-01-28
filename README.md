# Community-Based Spectral Filtering for Anomaly Detection in Attributed Graphs Framework

A community-aware anomaly detection framework for **attributed graphs** (e.g., citation networks).  
The core method, **SpecF (Spectral Filtering)**, measures how much a node disrupts **signal smoothness within its community** using the **Graph Fourier Transform (GFT)** and a **low-pass spectral filter**.

> Implemented as modular **Google Colab** notebooks (GCN / GraphSAGE × dataset), producing anomaly scores, plots, and Excel summaries.
<img width="790" height="1066" alt="Screenshot 2026-01-28 at 23 32 33" src="https://github.com/user-attachments/assets/dec8841f-8cbc-4036-be88-f77c1db13601" />

---

## Table of Contents
- [Motivation](#motivation)
- [Method Overview](#method-overview)
- [Pipeline](#pipeline)
- [Datasets](#datasets)
- [How to Run (Google Colab)](#how-to-run-google-colab)
- [Outputs](#outputs)
- [Key Findings](#key-findings)
- [Maintenance & Limitations](#maintenance--limitations)
- [Contributors](#contributors)

---

## Motivation

Traditional anomaly detection often focuses on **global** irregularities and may overlook subtle, context-dependent deviations that only stand out **within local groups**.  
This project targets **community-based anomalies**: nodes that look “off” relative to the community they belong to.

---

## Method Overview

**Key components:**
- **Graph representation learning:** Train **GCN** or **GraphSAGE** to produce node embeddings capturing structure + attributes.
- **Community discovery:** Run **K-means** on embeddings to group nodes into communities.
- **SpecF (Spectral Filtering):**
  - Transform node signals to the spectral domain using **GFT**
  - Apply a **low-pass filter** to compute a smoothed version of the signal
  - Use the **residual** (original − smoothed) as an anomaly signal and derive anomaly scores
- **(Semi-)Unsupervised detection:** Produces meaningful anomaly scores even without labels; supports synthetic anomaly injection for evaluation.

---

## Pipeline

1. **Load dataset** (nodes, edges, features; optional metadata like titles)
2. **Train GNN** (GCN / GraphSAGE) and extract node embeddings
3. **Cluster embeddings** using K-means → community assignment
4. **Construct node-level signal** for filtering
5. *(Optional)* **Inject synthetic anomalies** using:
   - anomaly ratio (percentage of nodes)
   - anomaly intensity (perturbation strength)
6. **Run SpecF per community:**
   - spectral smoothing via low-pass filtering
   - residual-based anomaly score
7. **Threshold / rank anomalies** (often per-community) and export results + plots

---

## Datasets

Evaluated on multiple graph datasets including:
- **Cora**
- **PubMed**
- **CoraFULL**
- **OpenAlex** (exploratory / real-world unsupervised analysis)

Datasets in the Dataset folders were recostructed to include titles and abstracts.
Datasets are downloaded automatically during notebook execution (commonly via `gdown` from Google Drive), so **no manual upload** is required.

---

## How to Run (Google Colab)

1. Open a notebook in **Google Colab**
2. Run all cells (end-to-end):
   - downloads data
   - trains embeddings (GCN / GraphSAGE)
   - clusters communities (K-means)
   - runs SpecF anomaly scoring
   - exports figures + tables to `/Results`

### Dependencies
The notebooks typically use:
- `torch`, `torch-geometric`
- `scikit-learn`
- `numpy`, `pandas`, `scipy`
- `matplotlib`
- `gdown`, `openpyxl`

---

## Outputs

All outputs are saved to a **/Results** directory created during execution.

### Common plots (`.png`)
Examples (names may vary by notebook):
- K-means clustering vs. labels/structure visualization
- SpecF anomaly scores (global and per-community)
- Input signal (before/after injection)
- Community visualization with detected anomalies highlighted
- Per-community threshold/score plots

### Tables (`.xlsx`)
Examples:
- Detected anomalies list (node ids, scores, community id)
- Community summaries (counts, thresholds)
- Top-k items per community (normal vs anomalies), when metadata exists (e.g., titles)

---

## Key Findings

- **Best stability** when anomalies are ~**5–10%** of nodes.
- SpecF is particularly strong at finding **subtle, structurally embedded anomalies**, not only obvious high-magnitude outliers.
- In real-world exploration, the method can highlight **bridge nodes** (e.g., interdisciplinary papers) and nodes that are structurally in one community but topically closer to another.
- Very high anomaly intensity or large anomaly ratios can distort “community normality,” increasing false negatives due to aggressive smoothing.
- Reults can be seen under "results" directory.
<img width="660" height="373" alt="Screenshot 2026-01-28 at 23 30 34" src="https://github.com/user-attachments/assets/5b2ef39a-ea9f-4a10-8445-89c09e49875a" />

---

## Maintenance & Limitations

- **Manual choice of K:** The number of K-means clusters must currently be chosen manually if data has no subject classes labeled.
- **Smoothing sensitivity:** Strong deviations may be partially suppressed by the low-pass filter.
- **Dataset sensitivity:** Changes in dataset structure (feature dimensionality / graph construction) may require updates to preprocessing and modeling.
- Research workflow (not production-optimized).

---


## Contributors

- **Students:** Itay Mohabati, Shai Yosef
- **Supervisors:** Dr. Renata Avros, Prof. Zeev Volkovich


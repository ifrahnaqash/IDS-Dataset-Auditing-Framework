# IDS Dataset Auditing Framework

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![Status](https://img.shields.io/badge/Status-Under%20Review-yellow)
![ATT&CK](https://img.shields.io/badge/MITRE%20ATT%26CK-v17.1-red)
![Jupyter](https://img.shields.io/badge/Notebooks-Jupyter-orange)

---

> **Paper:** *A Context-Aware Quantitative Framework for Auditing the Completeness and Realism of IDS Datasets*
> **Authors:** Ifrah Sanober, Roohie Naaz Mir — National Institute of Technology Srinagar
> **Venue:** Under review at *Computers & Security* (Elsevier)

---

## Overview

Intrusion detection datasets underpin the development and benchmarking of network security models, yet their structural properties — attack coverage, sophistication, feature completeness, protocol diversity, class balance, and traffic realism — are rarely subjected to rigorous, reproducible audit. Ad hoc or purely qualitative dataset comparisons make it difficult to diagnose why a model that achieves near-perfect performance on one benchmark fails to generalise to operational environments.

This repository provides the **reference implementation** of a context-aware quantitative framework that addresses this gap. The framework defines six diagnostic metrics, each grounded in established information-theoretic, taxonomic, or statistical principles, and evaluates them against eight widely-used IDS benchmark datasets spanning enterprise, IoT, encrypted-flow, and SDN telemetry deployment contexts. All metrics operate deterministically on observed inputs, with no model fitting, learned parameters, or heuristic inference.

The repository is intended for **reproducibility, independent verification, and future extension**. It is not intended for model training or performance optimisation.

The complete algorithmic contract, execution constraints, parameter definitions, and reproducibility guarantees are formalised in the accompanying Supplementary Material (S1–S8), which this repository directly implements.

---

## Framework Architecture

The auditing framework evaluates each dataset against a fixed six-metric diagnostic battery. All metrics are computed independently; no metric output is used as input to another.

```
Dataset D = (D, F, L, M, P)
          |
          v
  Purpose Profile Filter (S1.2)
  Defines: observable features F_P,
           observable protocols Π_P,
           in-scope ATT&CK techniques T_P
          |
          +-------+-------+-------+-------+-------+-------+
          |       |       |       |       |       |       |
         ATC      AS     FCPA    PDR     CIS     TRI
          |       |       |       |       |       |
     ATT&CK   Rubric  Feature  Protocol  Class  Traffic
     coverage sophist. complete diversity imbal. realism
     (S2)     (S3)    (S4)     (S5)     (S6)   (S7)
          |       |       |       |       |       |
          +-------+-------+-------+-------+-------+
                          |
                  Empirical Validation
               Spearman ρ vs. classifier
               Macro PR-AUC / Macro F1
                         (S8)
```

**Key design constraints (S1):**
- All parameters are fixed a priori — no per-dataset tuning
- No learning, sampling, imputation, or probabilistic inference
- Missing metrics do not penalise or inflate other scores
- All results are deterministic given identical inputs

---

## Metrics

### ATC — Attack Taxonomy Coverage
Measures the breadth, depth, and evenness of ATT&CK tactic and technique coverage evidenced by dataset attack labels, after filtering for contextually observable techniques under the dataset's purpose profile.

$$\text{ATC} = \alpha \cdot B + \beta \cdot C + \gamma \cdot E$$

where $B$ = tactic breadth, $C$ = technique coverage, $E$ = evenness across covered tactics, with weights $(\alpha, \beta, \gamma)$ fixed a priori. Labels are mapped to ATT&CK techniques via a manually curated mapping covering 95 labels across 8 datasets (`Framework/label_technique_mapping.csv`).

---

### AS — Attack Sophistication
Evaluates the technical complexity of attacks present in the dataset using a fixed four-dimensional rubric applied per ATT&CK technique: Engineering Complexity (EC), Required Resources (RR), Stealth and Evasiveness (SE), and Coordination and Depth (CD), each on a 0–3 ordinal scale.

$$s(a) = \frac{EC + RR + SE + CD}{12}, \quad AS = \alpha\mu + \beta H + \gamma \text{HSCT} + \delta \text{CF} + \varepsilon r_{\text{modern}} - \zeta \text{DP}$$

Per-technique rubric scores for all 31 evaluated techniques are reported in Supplementary Table S3.1.

---

### FCPA — Feature Completeness and Purpose Alignment
Measures how well the dataset's feature set covers the categories required by its intended deployment purpose profile, using reference sufficiency thresholds $N_c^{\text{ref}}$ per category.

$$\text{FCPA} = \sum_c w_c \cdot \min\\left(1,\, \frac{n_c}{N_c^{\text{ref}}}\right)$$

Feature categorisation is performed deterministically using a rule-based mapper archived in `Framework/mapping_output/`.

---

### PDR — Protocol Diversity and Representativeness
Combines Shannon entropy of the observed protocol distribution with L1 similarity to an empirical baseline, measuring both internal diversity and fidelity to real-world protocol composition.

$$\text{PDR} = \alpha \cdot H + (1-\alpha) \cdot \text{Rep}, \quad \text{Rep} = 1 - \frac{1}{2}\sum_i |p_i - q_i'|$$

Robustness across values of $\alpha$ is reported in `Framework/pdr_outputs/PDR_sensitivity_*.csv`.

---

### CIS — Class Imbalance Severity
Quantifies imbalance using a composite internal score (HHI-based concentration across all classes, attack-only classes, and attack/benign ratio) combined with Jensen–Shannon divergence to a purpose-profile-specific prior distribution.

$$\text{CIS}_{\text{CA}} = (1-\beta) \cdot \text{CIS}_{\text{base}} + \beta \cdot \text{JS}(p \| q_P)$$

---

### TRI — Traffic Realism Index
Assesses how closely the dataset's traffic characteristics match empirically observed operational traffic, across three components: volume realism ($\text{TR}_{\text{vol}}$), temporal realism ($\text{TR}_{\text{tmp}}$), and protocol/service realism ($\text{TR}_{\text{pro}}$).

$$\text{TRI} = \alpha \cdot \text{TR}_{\text{vol}} + \beta \cdot \text{TR}_{\text{tmp}} + \gamma \cdot \text{TR}_{\text{pro}}$$

Three purpose-profile-specific empirical baselines (enterprise, IoT, encrypted-flow) are provided in `Framework/baselines/`. UNR-IDD is evaluated via a leave-one-switch-out (LOSO) strategy given its switch-telemetry format.

---

## Evaluated Datasets

| Dataset | Context | Purpose Profile |
|---|---|---|
| NSL-KDD | Enterprise | General enterprise NIDS |
| UNSW-NB15 | Enterprise | General enterprise NIDS |
| CIC-IDS2017 | Enterprise | General enterprise NIDS |
| CSE-CIC-IDS2018 | Enterprise | General enterprise NIDS |
| BoT-IoT | IoT | IoT gateway NIDS |
| ToN-IoT | IoT | IoT gateway NIDS |
| HIKARI-2021 | Encrypted-flow | Encrypted-flow-only IDS |
| UNR-IDD | SDN telemetry | SDN switch telemetry |

All datasets are evaluated against the MITRE ATT&CK Enterprise corpus (v17.1). Dataset files are not included in this repository; sources are documented in the paper.

---

## Repository Structure

```
IDS-Dataset-Auditing-Framework/
│
├── Framework/                          # Metric implementations and outputs
│   ├── atc_metric.ipynb                # ATC implementation
│   ├── as_metric.ipynb                 # AS implementation
│   ├── fcpa_metric.ipynb               # FCPA implementation
│   ├── pdr_metric.ipynb                # PDR implementation
│   ├── cis_metric.ipynb                # CIS implementation
│   ├── tri_metric.ipynb                # TRI implementation
│   ├── TRI_Baseline_Builder_Enterprise.ipynb
│   ├── TRI_Baseline_Builder_iot.ipynb
│   ├── TRI_Baseline_Builder_Encrypted.ipynb
│   ├── Dataset Information.ipynb       # Dataset-level exploratory analysis
│   ├── Diagnostic_Analysis.ipynb       # Metric behaviour diagnostics
│   │
│   ├── label_technique_mapping.csv     # Full 95-label ATT&CK mapping (S2.1)
│   ├── fcpa_coverage_summary.csv       # FCPA dataset-level results (S4.2.1)
│   ├── fcpa_components_long.csv        # FCPA component decomposition (S4.2.2)
│   ├── AS_results.csv                  # AS dataset-level summary
│   │
│   ├── atc_exports/                    # ATC summary + top-technique CSVs (S2.3.1–2)
│   ├── atc_exports_all/                # Complete ATC audit traces (S2.3.3)
│   ├── atc_figs_unit/                  # ATC figures (S2.4)
│   ├── atc_report_*.json               # Per-dataset ATC JSON reports (S2.3.4)
│   ├── AS_figures/                     # AS figures (S3.4)
│   ├── mapping_output/                 # Per-dataset feature-to-category mappings (S4.3.1)
│   ├── fcpa_figures/                   # FCPA figures (S4.4)
│   ├── pdr_outputs/                    # PDR results + sensitivity analysis (S5.2–3)
│   ├── cis_results/                    # CIS results + figures (S6.2–3)
│   ├── baselines/                      # TRI empirical baselines (S7.2)
│   ├── tri_outputs/                    # TRI scores + service diagnostics (S7.3)
│   ├── tri_visuals/                    # TRI per-dataset CDF and mix figures (S7.4)
│   └── figures/                        # Cross-metric summary figures
│
├── Validation/                         # Empirical validation (S8)
│   ├── Empirical_Validation.ipynb      # Validation pipeline
│   ├── framework_scores.csv            # Compiled metric scores per dataset
│   ├── ids_performance_results.csv     # Classifier performance results
│   └── validation_results/
│       ├── merged_metrics_performance.csv          # Combined input matrix (S8.2.1)
│       ├── correlation_results.csv                 # Spearman ρ results (S8.2.2)
│       ├── primary_correlation_results.csv         # Primary correlation table
│       ├── robustness_exclusion_results.csv        # Near-perfect exclusion check (S8.2.3)
│       ├── atc_weight_sensitivity_results.csv      # ATC weight sensitivity (S8.2.4)
│       ├── dataset_ranking_under_weight_sensitivity.csv
│       └── figures/                                # Validation figures (S8.3)
│
├── CITATION.cff                        # Machine-readable citation
├── requirements.txt                    # Python dependencies
├── LICENSE                             # MIT License
└── README.md                           # This file
```

---

## External Dependency: MITRE ATT&CK

The MITRE ATT&CK Enterprise knowledge base file `enterprise-attack.json` is required for ATC and AS computation. Due to repository size constraints, it is not included here.

Download it from the official MITRE ATT&CK website:
👉 https://attack.mitre.org/resources/enterprise-attack/

This implementation was developed and validated against:

| Property | Value |
|---|---|
| ATT&CK Domain | Enterprise |
| ATT&CK Version | 17.1 |
| STIX Specification | 2.1 |
| ATT&CK Spec Version | 3.2.0 |

Once downloaded, place `enterprise-attack.json` at the path specified in the inline comments of `atc_metric.ipynb` and `as_metric.ipynb`. No modifications to the file are required; it is used as a read-only knowledge base.

---

## Reproduction Guide

### 1. Requirements

```bash
pip install -r requirements.txt
```

Python 3.8+ and Jupyter / JupyterLab are required. Core dependencies include `numpy`, `pandas`, `matplotlib`, `scipy`, and `pyyaml`.

### 2. Prepare External Data

- Download `enterprise-attack.json` (v17.1) and place it as instructed in `atc_metric.ipynb`.
- Obtain the raw dataset files (sources in paper). Dataset files are not distributed with this repository.

### 3. Build TRI Baselines (if re-running TRI from scratch)

```bash
jupyter notebook Framework/TRI_Baseline_Builder_Enterprise.ipynb
jupyter notebook Framework/TRI_Baseline_Builder_iot.ipynb
jupyter notebook Framework/TRI_Baseline_Builder_Encrypted.ipynb
```

Pre-built baselines are already included in `Framework/baselines/` and can be used directly without re-running these notebooks.

### 4. Run Metric Notebooks

Each metric is implemented as a standalone notebook. Run them in any order:

```bash
jupyter notebook Framework/atc_metric.ipynb
jupyter notebook Framework/as_metric.ipynb
jupyter notebook Framework/fcpa_metric.ipynb
jupyter notebook Framework/pdr_metric.ipynb
jupyter notebook Framework/cis_metric.ipynb
jupyter notebook Framework/tri_metric.ipynb
```

All outputs are written to the corresponding subdirectories under `Framework/` as documented in the repository structure above.

### 5. Run Empirical Validation

```bash
jupyter notebook Validation/Empirical_Validation.ipynb
```

This notebook reads `framework_scores.csv` and `ids_performance_results.csv`, performs Spearman correlation analysis, robustness checks, and ATC weight sensitivity analysis, and writes all outputs to `Validation/validation_results/`.

### 6. Verify Against Pre-computed Outputs

All outputs from the paper's evaluation run are already present in the repository. Running the notebooks should reproduce them exactly, given identical inputs, as all metrics are deterministic with fixed parameters.

---

## Code ↔ Supplementary Material

| Component | Supplementary Section |
|---|---|
| Algorithmic contract and execution guarantees | S1 |
| `atc_metric.ipynb` + `atc_exports/` + `atc_report_*.json` | S2 |
| `as_metric.ipynb` + `AS_figures/` | S3 |
| `fcpa_metric.ipynb` + `mapping_output/` + FCPA CSVs | S4 |
| `pdr_metric.ipynb` + `pdr_outputs/` | S5 |
| `cis_metric.ipynb` + `cis_results/` | S6 |
| `tri_metric.ipynb` + `baselines/` + `tri_outputs/` | S7 |
| `Empirical_Validation.ipynb` + `validation_results/` | S8 |

---

## Citation

If you use this framework, implementation, or results in your research, please cite:

```bibtex
@article{sanober2026ids,
  title   = {A Context-Aware Quantitative Framework for Auditing the
             Completeness and Realism of {IDS} Datasets},
  author  = {Sanober, Ifrah and Mir, Roohie Naaz},
  journal = {Computers \& Security},
  year    = {2026},
  note    = {Under review}
}
```

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Contact

For questions about the framework, implementation, or reproduction of results, please contact the authors via the National Institute of Technology Srinagar.

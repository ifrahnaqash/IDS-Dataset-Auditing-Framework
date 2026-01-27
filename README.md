# IDS-Dataset-Auditing-Framework

This repository contains the **reference implementation** of the dataset auditing framework presented in the paper:

> **A Context-Aware Quantitative Framework for Auditing the Completeness and Realism of IDS Datasets**  
> *(Ifrah Sanober, Roohie Naaz Mir, 2026)*

The corresponding **Supplementary Material** provides the complete **algorithmic contract, execution constraints, and reproducibility guarantees** that this repository implements. The intended use of this repository is for **reproducibility, independent verification, and future extension**, not for model training or performance optimization.

---

## 🔍 Project Overview

The IDS Dataset Auditing Framework enables systematic evaluation of intrusion detection datasets with respect to:

1. **Attack Taxonomy Coverage (ATC)**  
2. **Attack Sophistication (AS)**  
3. **Feature Completeness and Purpose Alignment (FCPA)**  
4. **Protocol Distribution Representativeness (PDR)**  
5. **Class Imbalance Severity (CIS)**  
6. **Traffic Realism Index (TRI)**

Each metric is implemented as a standalone component and adheres strictly to the reproducibility contracts defined in Supplementary Sections S1–S7 of the paper.

---

## 📁 Repository Structure

IDS-Dataset-Auditing-Framework/
├── atc_exports/ # ATC metric outputs (JSON files)
├── atc_exports_all/ 
├── atc_figs_unit/ # ATC unit figures
├── AS_figures/ # Attack Sophistication figures
├── cis_results/ # Class Imbalance Severity outputs
├── fcpa_figures/ # FCPA metric visual outputs
├── mappings_output/ # Label-to-ATT&CK mapping artifacts
├── pdr_outputs/ # Protocol Diversity results
├── tri_outputs/ # TRI score outputs
├── tri_visuals/ # TRI visualization artifacts
├── baselines/ # Traffic baselines used for TRI
├── Dataset Information.ipynb # Dataset-level exploratory & context analysis
├── Diagnostic_Analysis.ipynb # Diagnostic scripts to inspect metric behavior
├── *.ipynb # Metric implementation notebooks
├── *.json / *.csv # Exported audit artifacts
├── README.md # This file
└── LICENSE # MIT License                                                                                                
    
 

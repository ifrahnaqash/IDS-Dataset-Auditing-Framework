{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "f4eaf639-d96d-4801-9ecb-fd57e3170c25",
   "metadata": {},
   "source": [
    "```mermaid\r",
    "graph TD\r\n",
    "    A[Start: Evolution of IDS Datasets] --> B{Generation 1: Legacy/First-generation Datasets};\r\n",
    "\r\n",
    "    B --> C[Examples: DARPA'98/'99, KDD Cup'99, NSL-KDD];\r\n",
    "    C --> D[Key Characteristics: Collected in Controlled Testbeds, Labeled Traffic, Clear Binary/Multi-class Tasks];\r\n",
    "    D --> E[Limitations/Shortcomings: Synthetic Traffic Profiles, Outdated Protocols, Redundancy, Artifact-Driven Separability];\r\n",
    "    E --> F{Impact: Catalyzed a decade of ML-based IDS work but Models Fail to Generalize to Modern Networks};\r\n",
    "\r\n",
    "    F --> G{Generation 2: Mid-generation Enterprise Datasets};\r\n",
    "    G --> H[Examples: UNSW-NB15, CICIDS2017];\r\n",
    "    H --> I[Key Characteristics: Improved Realism & Diversity, Broader Protocol Mixes, Wider Attack Scenarios, Richer Feature Sets (Flow/Statistical)];\r\n",
    "    I --> J[Limitations/Shortcomings: Class Imbalance, Scenario-wise Temporal Grouping (Confounding Evaluation Splits), Labeling/Taxonomy Inconsistencies];\r\n",
    "\r\n",
    "    J --> K{Generation 3: Contemporary/Domain-specific Datasets};\r\n",
    "    K --> L[Examples: CSE-CIC-IDS2018, Bot-IoT, ToN-IoT, HIKARI-2021];\r\n",
    "    L --> M[Key Characteristics: Target Modern Environments (IoT, ICS, Encrypted Traffic), High Ecological Validity in Specific Niches];\r\n",
    "    M --> N[Trade-offs/Issues: Narrower Attack Taxonomy Coverage (Breadth traded for Depth), High Imbalance by Design (in some), Variable Labeling Practices];\r\n",
    "\r\n",
    "    N --> O[End: Current State of IDS Datasets];\r\n",
    "\r\n",
    "    style B fill:#f9f,stroke:#333\r\n",
    "    style G fill:#ccf,stroke:#333\r\n",
    "    style K\n",
    " fill:#9f9,stroke:#333\r\n",
    "```"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.11.9"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}

# Reply Sandbox 2026 — The Eye 👁️

![Python](https://img.shields.io/badge/Python-3.10%2B-blue?style=flat-square&logo=python)
![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)
![Challenge](https://img.shields.io/badge/Challenge-Reply%20Sandbox%202026-red?style=flat-square)
![ML](https://img.shields.io/badge/Approach-Multi--Agent%20AI-purple?style=flat-square)
![Metric](https://img.shields.io/badge/Metric-F1%20Score-orange?style=flat-square)
![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square)
> *"In 2087, the city watches. The question is whether it watches to protect — or to control."*

---

## 📖 Overview

**The Eye** is our solution to the **Reply Sandbox 2026 — Propedeutical Sandbox** challenge.

Set in a dystopian near-future (2087), the challenge unfolds inside **Reply Mirror** — a hyper-connected digital metropolis where every citizen is continuously monitored through the **MirrorLife** public health infrastructure. MirrorLife ingests real-time biometric, behavioral, and environmental data for every individual in the city, with the declared goal of enabling proactive, personalized healthcare at scale.

Our task: design and implement an intelligent system capable of analyzing this continuous data stream and **classifying each citizen** into one of two states:

| Class | Label | Meaning |
|-------|-------|---------|
| `0` | Standard Monitoring | Citizen remains on the standard MirrorLife surveillance protocol |
| `1` | Preventive Activation | Citizen is flagged for a **personalized preventive health pathway** |

To solve this, **The Eye** wraps state-of-the-art ML algorithms — including **XGBoost** and **LightGBM** — inside a **cooperative multi-agent architecture**, where specialized agents handle feature engineering, probabilistic prediction, and final decision-making independently yet in coordination.

The primary evaluation metric is the **F1 Score**, ensuring a rigorous balance between precision and recall across both classes.

> ⚠️ **Data notice:** Raw challenge data is **not included** in this repository in compliance with Reply's competition rules. See the [Setup](#️-setup--installation) section for instructions on where to place the data files.
 

---

## 🌆 Challenge Context

| Field | Details |
|-------|---------|
| **Challenge Name** | Reply Sandbox 2026 — Propedeutical Sandbox |
| **Theme** | Reply Mirror — Digital Metropolis, 2087 |
| **System** | MirrorLife — Public Health Data Infrastructure |
| **Goal** | Binary classification per citizen (0 / 1) |
| **Metric** | F1 Score |
| **Levels** | 3 progressive difficulty tiers |

The challenge is structured across **3 progressive levels**, each introducing greater data complexity, noisier signals, and higher classification difficulty.

---
## 📂 Repository Structure

```
reply-sandbox-2026-the-eye/
│
├── data/                        # ⚠️ NOT tracked by Git — add raw data here
│   ├── raw/
│   │   ├── Status.csv           # Monitoring events stream
│   │   ├── Locations/           # GPS BioTag records
│   │   └── Users/               # Citizen demographic data
│   └── processed/               # Cleaned & feature-engineered datasets
│
├── notebooks/                   # Jupyter notebooks for exploration & analysis
│   ├── 01_eda.ipynb             # Exploratory Data Analysis
│   ├── 02_feature_engineering.ipynb
│   └── 03_modelling.ipynb       # Model training & evaluation
│
├── src/                         # Core source code
│   ├── main.py                  # Pipeline entry point — runs all agents
│   │
│   ├── agents/                  # Multi-agent AI system
│   │   ├── __init__.py
│   │   ├── feature_agent.py     # Feature engineering agent
│   │   ├── prediction_agent.py  # ML prediction agent
│   │   └── decision_agent.py    # Final classification decision agent
│   │
│   ├── models/                  # ML model definitions & loaders
│   │   ├── __init__.py
│   │   └── classifier.py
│   │
│   └── utils/                   # Shared utilities
│       ├── __init__.py
│       ├── preprocessing.py
│       └── metrics.py
│
├── output/                      # Submission files (ASCII format)
│   └── submission.txt
│
├── requirements.txt             # Python dependencies
├── .gitignore                   # Excludes data/, .venv/, __pycache__/, etc.
└── README.md
```
 

---

## 📊 Input Data

The solution consumes three primary data sources provided by MirrorLife:

### `Status.csv` — Monitoring Event Stream

The core time-series dataset recording continuous biometric and environmental readings for each citizen.

| Field | Type | Description |
|-------|------|-------------|
| `Event ID` | string | Unique identifier for the monitoring event |
| `Citizen ID` | string | Reference to the monitored citizen |
| `Timestamp` | datetime | Date and time of the recorded event |
| `Event Type` | categorical | Type/category of the monitoring event |
| `Physical Activity Index` | float | Aggregated physical activity score |
| `Sleep Quality Index` | float | Composite sleep quality score |
| `Environmental Exposure Level` | float | Exposure to environmental risk factors |

### `Locations/` — GPS BioTag Records

Spatial tracking data collected via each citizen's BioTag implant.

| Field | Type | Description |
|-------|------|-------------|
| `BioTag` | string | Unique device identifier |
| `Datetime` | datetime | Timestamp of the GPS reading |
| `Lat` | float | Latitude coordinate |
| `Lng` | float | Longitude coordinate |
### `Users/` — Citizen Registry

Demographic and profile data for all registered citizens.

> Contains personally identifiable attributes used for feature enrichment and population segmentation.

---

> ⚠️ **None of the above data files are committed to this repository.** They are provided exclusively through the official Reply Sandbox 2026 challenge platform and must be placed manually inside `data/raw/` before running the pipeline.
 

---

## 📤 Output Format

The expected submission is a **plain ASCII text file** listing the `Citizen ID` of every individual classified as **class `1`** (preventive pathway activation), one per line.

```
CITIZEN_00042
CITIZEN_00117
CITIZEN_00388
...
```

The output file is saved to `output/submission.txt`.

---

## 🤖 Solution Approach — Multi-Agent AI System

**The Eye** implements a **cooperative multi-agent AI architecture** where each agent is responsible for a distinct stage of the classification pipeline. Agents communicate through shared state and operate in a coordinated, sequential fashion.

```
Raw Data
   │
   ▼
┌─────────────────────┐
│   Feature Agent     │  ← Ingests raw streams, engineers temporal &
│                     │    spatial features, handles missing values
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Prediction Agent   │  ← Runs trained ML classifiers, generates
│                     │    probability scores per citizen
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  Decision Agent     │  ← Applies threshold logic, resolves conflicts,
│                     │    produces final binary classification (0 / 1)
└────────┬────────────┘
         │
         ▼
    submission.txt
```

### Agent Responsibilities

| Agent | Module | Role |
|-------|--------|------|
| **Feature Agent** | `src/agents/feature_agent.py` | Raw data ingestion, temporal aggregation, spatial feature extraction, encoding, and imputation |
| **Prediction Agent** | `src/agents/prediction_agent.py` | Loads trained classifier, computes per-citizen risk scores and class probabilities |
| **Decision Agent** | `src/agents/decision_agent.py` | Applies classification threshold, resolves edge cases, formats and writes submission output |

---

## ⚙️ Setup & Installation

### Prerequisites

- Python `3.10+`
- `pip` or `conda`
- Git

### 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/reply-sandbox-2026-the-eye.git
cd reply-sandbox-2026-the-eye
```

### 2. Create a Virtual Environment

```bash
python -m venv .venv
source .venv/bin/activate        # On Windows: .venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```
### 4. Add the Data

Download the challenge datasets from the official **Reply Sandbox 2026** platform and place them inside the `data/raw/` directory, preserving the following structure:

```
data/
└── raw/
    ├── Status.csv
    ├── Locations/
    └── Users/
```

> ⚠️ The `data/` directory is listed in `.gitignore` and is **never tracked by Git**.  
> Challenge data is proprietary to Reply S.p.A. — do not commit it to any public or private repository.
 

---

## 🚀 Usage

### Run the Full Pipeline

```bash
python src/main.py
```

This will sequentially invoke all three agents and produce the final submission file at `output/submission.txt`.

### Run Individual Agents

```bash
# Feature engineering only
python src/agents/feature_agent.py

# Prediction only (requires processed features)
python src/agents/prediction_agent.py

# Decision & output generation
python src/agents/decision_agent.py
```

### Explore Notebooks

```bash
jupyter notebook notebooks/
```

---

## 📈 Evaluation Metric

The challenge is evaluated using the **F1 Score** — the harmonic mean of Precision and Recall.

$$F1 = 2 \cdot \frac{\text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$

| Metric | Formula | Focus |
|--------|---------|-------|
| **Precision** | TP / (TP + FP) | Avoid false activations |
| **Recall** | TP / (TP + FN) | Avoid missing at-risk citizens |
| **F1 Score** | Harmonic mean of P & R | Balance between both |

> A high F1 Score ensures MirrorLife correctly identifies citizens who genuinely need preventive intervention, while minimizing unnecessary pathway activations.

---

## 🔧 Requirements

Key dependencies (see `requirements.txt` for full list):

```
pandas>=2.0
numpy>=1.24
scikit-learn>=1.3
lightgbm>=4.0
xgboost>=2.0
geopy>=2.4
jupyter>=1.0
matplotlib>=3.7
seaborn>=0.13
```

---
## 🤝 Contributing

This is a competitive challenge repository.  
- **During the active competition phase:** the repository is closed to external contributions.  
- **Post-competition:** pull requests for improvements, refactoring, alternative approaches, and documentation enhancements are warmly welcome. Please open an issue first to discuss your proposed change.
 

---

## 📄 License

This project is licensed under the **MIT License**.

```
MIT License

Copyright (c) 2026 The Eye Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
```

---

## 👥 Team

> *Built with ☕ and dystopian optimism for Reply Sandbox 2026.*

---

*Reply Sandbox 2026 — Propedeutical Sandbox. All challenge data and intellectual property belong to Reply S.p.A.*

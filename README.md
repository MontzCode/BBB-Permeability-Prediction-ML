# Blood-Brain Barrier Permeability Prediction

A machine learning classification pipeline for predicting blood-brain barrier (BBB) permeability of drug compounds using RDKit molecular descriptors. The project covers cheminformatics feature engineering, exploratory data analysis, class imbalance handling with SMOTE, and comparative evaluation of four classifiers.

---

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Dataset](#dataset)
- [Installation](#installation)
- [Usage](#usage)
- [Pipeline Architecture](#pipeline-architecture)
- [Model Performance](#model-performance)
- [Key Findings](#key-findings)
- [Limitations and Future Work](#limitations-and-future-work)

---

## Overview

BBB permeability is a critical property in CNS drug development. Compounds that cannot cross the blood-brain barrier are ineffective for treating neurological conditions, while unexpected BBB penetration can cause unwanted CNS side effects. This project builds and evaluates binary classifiers to predict whether a compound is BBB-permeable (BBB+) or non-permeable (BBB-) based on RDKit physicochemical and topological molecular descriptors computed from SMILES strings.

The pipeline handles class imbalance using SMOTE on the training set, evaluates four classifiers via cross-validation and held-out test set assessment, and includes statistical analysis of the most discriminative molecular descriptors between BBB+ and BBB- compounds.

---

## Project Structure

```
bbb-permeability-prediction/
│
├── BBB-Permeability-Prediction-ML.ipynb    # Full pipeline notebook
│
├── data/                                    # Input data (not tracked)
│   └── BBB_datasets.xlsx                   # Drug compounds with SMILES and BBB class labels
│
├── README.md
└── requirements.txt
```

---

## Dataset

The dataset contains 604 drug compounds labelled as BBB-permeable (BBB+) or BBB-impermeable (BBB-), each represented by a SMILES string.

- **604 compounds** total, no missing values or duplicate rows
- **Class distribution**: 483 BBB+ (80%), 121 BBB- (20%) — imbalanced
- **Features**: 208 molecular descriptors computed via RDKit's `MolecularDescriptorCalculator`, covering physicochemical properties (MolWt, TPSA, MolLogP), electrotopological state indices (EState), VSA descriptors, Lipinski rule features, and structural fragment counts

The class imbalance was addressed using SMOTE on the training set, resampling the minority class (BBB-) to match the majority class before fitting classifiers.

---

## Installation

```bash
git clone https://github.com/MontzCode/bbb-permeability-prediction.git
cd bbb-permeability-prediction
pip install -r requirements.txt
```

**Key dependencies:**

```
pandas
numpy
scikit-learn
imbalanced-learn
rdkit
matplotlib
seaborn
scipy
```

---

## Usage

Open and run `BBB-Permeability-Prediction-ML.ipynb` end-to-end. The notebook loads the dataset, computes all 208 molecular descriptors, runs EDA, applies SMOTE, trains and evaluates all four classifiers, and outputs classification reports.

---

## Pipeline Architecture

```
Drug compounds (SMILES + BBB class labels)
        |
        v
RDKit MolecularDescriptorCalculator
  → 208 descriptors per compound
  (EState indices, VSA, TPSA, MolWt, Lipinski features, fragment counts)
        |
        v
EDA
  → Random Forest feature importance to identify top descriptors
  → Correlation matrix of top features
  → Distribution plots and boxplots by BBB class
  → T-tests: TPSA (p=3.9e-38), VSA_EState2 (p=8.9e-19),
             VSA_EState3 (p=5.2e-15), MolWt (p=8.8e-11)
  → PCA visualisation of full descriptor space
        |
        v
Train/test split (80/20, random_state=42)
        |
        v
SMOTE on training set
  → BBB-: 160 → 323 (balanced to match BBB+)
        |
        v
5-fold cross-validation + test set evaluation
  ├── Random Forest
  ├── Gradient Boosting
  ├── Support Vector Machine
  └── Logistic Regression
```

---

## Model Performance

**Cross-validation accuracy (5-fold, SMOTE-balanced training set):**

| Model | CV Accuracy | CV Std (±2σ) |
|---|---|---|
| Random Forest | 87.6% | ±6.2% |
| Gradient Boosting | 87.3% | ±5.9% |
| Support Vector Machine | 50.3% | ±0.8% |
| Logistic Regression | 50.2% | ±0.6% |

**Test set performance:**

| Model | Accuracy | BBB+ Precision | BBB+ Recall | BBB- Precision | BBB- Recall |
|---|---|---|---|---|---|
| Random Forest | 80.2% | 0.85 | 0.85 | 0.71 | 0.71 |
| Gradient Boosting | 76.0% | 0.80 | 0.85 | 0.68 | 0.60 |
| SVM | 65.3% | 0.65 | 1.00 | 0.00 | 0.00 |
| Logistic Regression | 34.7% | 0.00 | 0.00 | 0.35 | 1.00 |

Random Forest achieved the best overall performance with 80% test accuracy and balanced precision/recall across both classes. SVM and Logistic Regression failed to learn meaningful decision boundaries on this descriptor space, predicting only a single class on the test set.

---

## Key Findings

TPSA (topological polar surface area) was the single most important descriptor by Random Forest feature importance and showed the largest statistical separation between BBB+ and BBB- compounds (t = -14.9, p = 3.9e-38). BBB- compounds had substantially higher TPSA values, consistent with the established pharmacological principle that high polarity reduces passive membrane diffusion.

MolWt, VSA_EState2 and VSA_EState3 also showed highly significant differences between classes, all with p-values below 1e-10. NOCount and NumHeteroatoms were strongly correlated with TPSA (r = 0.93 and 0.82 respectively), suggesting that heteroatom content drives much of the polarity-permeability relationship captured by the model.

The failure of SVM and Logistic Regression, both with ~50% accuracy defaulting to single-class prediction, indicates that the descriptor space is not linearly separable and requires the non-linear decision boundaries that ensemble methods provide.

---

## Limitations and Future Work

The dataset has a substantial class imbalance (80/20) which, even after SMOTE, may inflate cross-validation accuracy for the majority class. A more rigorous evaluation would use stratified cross-validation with explicit BBB- recall as the primary metric given the clinical cost of false negatives.

The 208 RDKit descriptors include highly correlated features (TPSA and NOCount at r=0.93) which can introduce redundancy. Dimensionality reduction or explicit feature selection before model training could improve generalisation.

Potential improvements include adding Morgan fingerprints alongside physicochemical descriptors, tuning Random Forest and Gradient Boosting hyperparameters, and testing graph-based molecular representations with GNNs for richer structural encoding.

# BBB-Permeability-Prediction-ML

# Overview

This project predicts Blood Brain Barrier (BBB) permeability of drug compounds using machine learning models. It utilizes molecular descriptors extracted from the SMILES notation of chemical compounds to classify their BBB permeability. The project implements a full pipeline for data preprocessing, feature extraction, exploratory data analysis (EDA), and model evaluation with classifiers like Random Forest, Gradient Boosting, SVM, and Logistic Regression.

# Dataset

The dataset includes the name of drug compounds, their chemical structures represented by SMILES notation, and their classification regarding blood-brain barrier permeability (BBB+ or BBB-). The class is encoded into binary format, where BBB+ is labeled as 0 and BBB- as 1. Additionally, 208 molecular descriptors were extracted from the SMILES notations, with several key features being used to predict the permeability class.

# Findings

From the Random Forest model, it was determined that the top molecular descriptors most predictive of BBB permeability are TPSA, NOCount, VSA_EState3, and MinAbsPartialCharge, among others. The Random Forest classifier achieved the highest accuracy of approximately 80% in predicting permeability. Gradient Boosting followed closely with about 76% accuracy, but models like SVM and Logistic Regression performed poorly, with accuracies of 65% and 35%, respectively. Statistical tests revealed significant differences between the BBB+ and BBB- classes based on key molecular descriptors such as MolWt and TPSA. Furthermore, the PCA analysis indicated partial clustering between BBB+ and BBB- drug

# Installation

Clone the repository:

git clone https://github.com/MontzCode/BBB-Permeability-Prediction-ML.git

- Programming language used: Python

- main libraries: rdkit-pypi, scikit-learn and imbalanced-learn

# Contributing

Feel free to submit pull requests or report issues to improve the project.

# License

This project is licensed under the MIT License.

# Contact

For queries, please contact Monty Nkpa. Connect with me on LinkedIn: https://www.linkedin.com/in/monty-nkpa-7a4a7623b/?originalSubdomain=uk

# Multi-Level Phishing URL Risk Scoring Using Explainable AI

**A Leakage-Aware Study of Generalization and Dataset Shift**

This repository contains the source code and reproducibility materials
for the research project:

> **Multi-Level Phishing URL Risk Scoring Using Explainable AI: A
> Leakage-Aware Study of Generalization and Dataset Shift**

The system performs **URL-only phishing detection** and produces:

-   Binary phishing/benign prediction
-   Phishing probability
-   A **0--100 risk score**
-   Five risk levels: **SAFE, LOW, MEDIUM, HIGH, CRITICAL**
-   **SHAP-based explanations** for model decisions

The project focuses on leakage-aware evaluation, bias--variance
analysis, explainability, and testing on an independent external dataset
rather than relying only on high accuracy from a random train/test
split.

------------------------------------------------------------------------

## Repository

**GitHub:** https://github.com/SIShahed16261/ML-Phishing-Link-Scoring

### Main source file

``` text
ml-project-2 (1).ipynb
```

The notebook contains the complete machine-learning workflow, including
data preparation, feature extraction, feature selection, model training,
model comparison, evaluation, SHAP analysis, risk scoring, and external
validation.

------------------------------------------------------------------------

## Project Overview

The project builds an end-to-end phishing URL risk-scoring pipeline
using only information contained in the URL string.

The development corpus combines:

-   **Majestic Million** --- used as the benign-domain source
-   **Malicious URLs Dataset** --- benign and phishing URLs are retained
-   **PhiUSIIL** --- kept completely separate and used for external
    validation

After normalization and deduplication, the development corpus contains:

-   **1,510,543 URLs**
-   **1,418,416 benign**
-   **92,127 phishing**
-   Approximately **6.1% phishing prevalence**
-   **999,200 unique domains**

The final feature set contains **47 lexical URL features**.

------------------------------------------------------------------------

## Methodology

### 1. URL Normalization

URLs are normalized before feature extraction:

-   Whitespace is removed
-   Missing schemes are assigned `http://`
-   URLs are converted to lowercase
-   Leading `www.` is removed
-   Trailing `/` is removed
-   Query strings and fragments are preserved

This creates a canonical representation and helps identify duplicate
URLs.

### 2. Domain-Aware Data Splitting

Instead of randomly splitting individual URLs, domains are used as
groups.

The development corpus is divided into approximately:

  Split             URLs Purpose
  ------------ --------- ------------------
  Training       996,327 Model training
  Validation     240,023 Model selection
  Test           274,193 Final evaluation

There is **zero domain overlap** between the three splits.

This reduces the possibility that the model simply memorizes domains
that appear in both training and test data.

### 3. Class Balancing

Only the **training set** is rebalanced.

The training data is undersampled to approximately a **3:1
benign-to-phishing ratio**, producing:

-   179,580 benign URLs
-   59,860 phishing URLs
-   239,440 training samples

The validation and test sets retain their natural approximately 6%
phishing prevalence.

### 4. Feature Engineering

A total of **63 lexical features** are initially extracted from the URL
string.

Feature groups include:

-   URL structure
-   Character counts
-   Security indicators
-   Token and entropy statistics
-   Phishing-related keywords

Feature selection is performed using the training set only.

After removing constant, low-variance, and highly correlated features,
**47 features** remain.

No network request is required to extract these lexical features.

------------------------------------------------------------------------

## Machine Learning Models

Five models are compared:

1.  Logistic Regression
2.  Random Forest
3.  XGBoost
4.  LightGBM
5.  CatBoost

The models are compared using a bias--variance perspective.

**XGBoost** is selected using the validation set and is then evaluated
once on the untouched test set.

------------------------------------------------------------------------

## Risk Scoring

The selected XGBoost model outputs:

``` text
p = P(phishing | URL features)
```

The risk score is:

``` text
Risk Score = round(100 × p)
```

The score is mapped to five levels:

  Risk Level       Score
  ------------ ---------
  SAFE             0--20
  LOW             21--40
  MEDIUM          41--60
  HIGH            61--80
  CRITICAL       81--100

The binary phishing decision uses:

``` text
p >= 0.5
```

------------------------------------------------------------------------

## Explainability

The project uses **SHAP (SHapley Additive exPlanations)** to explain the
XGBoost predictions.

Two forms of explanation are included:

### Global explanations

The model analyzes feature importance across 500 test URLs.

Important features include:

-   Number of path segments
-   URL length
-   Number of special characters
-   Dot count
-   Hyphen count
-   Minimum token length
-   URL entropy
-   HTTPS indicator
-   Suspicious TLD
-   Path length

### Local explanations

SHAP is also used to explain individual URL predictions by showing which
features push the prediction toward phishing or benign.

------------------------------------------------------------------------

## Dataset Access

The datasets are **not required to be stored directly in this
repository**. Download them from their original sources and place them
where the notebook expects them.

### 1. Majestic Million

Used as the primary benign-domain source.

Official source:

https://majestic.com/reports/majestic-million

The paper uses the top 1,000,000 domains from Majestic Million and
treats these entries as benign.

### 2. Malicious URLs Dataset

The original dataset contains 651,191 URLs across benign, defacement,
phishing, and malware classes.

For this project, only:

-   benign
-   phishing

are retained.

Kaggle:

https://www.kaggle.com/datasets/sid321axn/malicious-urls-dataset

### 3. PhiUSIIL Phishing URL Dataset

PhiUSIIL is used **only for external validation**, not for training or
model selection.

Dataset size:

-   235,795 URLs
-   134,850 legitimate
-   100,945 phishing

Important label convention:

``` text
PhiUSIIL:
1 = legitimate
0 = phishing
```

This is the opposite of the label convention used by the development
data, so the notebook must map the labels correctly before calculating
external-validation metrics.

Dataset information:

https://archive.ics.uci.edu/dataset/967/phiusiil+phishing+url+dataset

------------------------------------------------------------------------

## Environment Setup

The experiments were run on **Kaggle using Python 3.12**.

Recommended setup:

``` bash
python -m venv venv
```

### Windows

``` bash
venv\Scripts\activate
```

### Linux/macOS

``` bash
source venv/bin/activate
```

Install the dependencies:

``` bash
pip install -r requirements.txt
```

The main packages used by the project are:

``` text
numpy
pandas
scikit-learn
xgboost
lightgbm
catboost
shap
matplotlib
jupyter
```

The paper specifically reports using **SHAP 0.51**.

------------------------------------------------------------------------

## Reproducing the Results

### Step 1 --- Clone the repository

``` bash
git clone https://github.com/SIShahed16261/ML-Phishing-Link-Scoring.git
cd ML-Phishing-Link-Scoring
```

### Step 2 --- Install dependencies

``` bash
pip install -r requirements.txt
```

### Step 3 --- Obtain the datasets

Download:

1.  Majestic Million
2.  Malicious URLs Dataset
3.  PhiUSIIL

Place the files in the locations expected by the notebook.

> Dataset filenames/paths may need to be adjusted in the notebook
> depending on where the downloaded files are stored.

### Step 4 --- Open the notebook

Open:

``` text
ml-project-2 (1).ipynb
```

You can use:

-   Jupyter Notebook
-   JupyterLab
-   VS Code
-   Google Colab
-   Kaggle Notebook

### Step 5 --- Run the notebook from beginning to end

The notebook performs the following workflow:

``` text
Raw datasets
     ↓
URL normalization
     ↓
Deduplication
     ↓
Domain extraction
     ↓
Domain-grouped train/validation/test split
     ↓
Training-only class balancing
     ↓
63 lexical features
     ↓
Feature selection
     ↓
47 final features
     ↓
Train 5 ML models
     ↓
Validation comparison
     ↓
Select XGBoost
     ↓
Untouched test evaluation
     ↓
SHAP explanations
     ↓
Multi-level risk scoring
     ↓
PhiUSIIL external validation
     ↓
Dataset-shift analysis
```

All random operations use **seed 42**.

------------------------------------------------------------------------

## Reported Results

### Validation

The four tree-ensemble models perform closely on validation data.

XGBoost achieved:

-   Validation accuracy: **85.12%**
-   Validation ROC-AUC: **0.8975**
-   Validation phishing F1: **0.4221**

It was selected as the final model.

### Untouched Test Set

The final XGBoost model was evaluated on **274,193 previously untouched
URLs**.

  Metric                       Result
  ---------------------- ------------
  Accuracy                 **86.89%**
  ROC-AUC                  **0.8965**
  Precision (phishing)     **28.20%**
  Recall (phishing)        **79.79%**
  F1 (phishing)            **0.4167**
  Average Precision        **0.5478**
  Balanced Accuracy        **0.8356**
  MCC                      **0.4239**
  False-positive Rate      **12.67%**
  False-negative Rate      **20.21%**

The model detects **12,843 of 16,097 phishing URLs**, while **3,254
phishing URLs are missed**.

Because the real test set is highly imbalanced, the model produces a
substantial number of false positives at the 0.5 threshold. Therefore,
ROC-AUC and precision-recall metrics are important when interpreting
performance.

------------------------------------------------------------------------

## Multi-Level Risk Results

The risk levels show a monotonic increase in observed phishing rate:

  Risk Level       Score   Phishing Rate
  ------------ --------- ---------------
  SAFE             0--20            0.9%
  LOW             21--40            2.3%
  MEDIUM          41--60            6.2%
  HIGH            61--80           16.1%
  CRITICAL       81--100           44.5%

This means the risk score provides more information than a single binary
prediction.

------------------------------------------------------------------------

## External Validation and Dataset Shift

PhiUSIIL is intentionally kept separate from the development process and
is used to evaluate cross-dataset generalization.

With the correct PhiUSIIL label mapping:

  Metric          Development Test   PhiUSIIL
  ------------- ------------------ ----------
  Accuracy                  86.89%     39.08%
  Precision                 28.20%     40.59%
  Recall                    79.79%     91.28%
  Specificity               87.33%      0.00%
  F1                        0.4167     0.5620
  ROC-AUC                   0.8965     0.3435

The model predicts phishing for **96.3% of all PhiUSIIL URLs**.

The analysis identifies important dataset/source artefacts, including:

-   HTTPS usage differences
-   `www` prefix differences
-   Bare-domain benign URLs in the training data
-   A mismatch in URL normalization between training and external
    validation

This demonstrates that a small train--validation gap does not guarantee
real-world cross-dataset generalization.

------------------------------------------------------------------------

## Bias--Variance and Overfitting Analysis

The project does not evaluate overfitting only by looking at training
accuracy.

The train--validation ROC-AUC gaps were approximately:

  Model                   ROC-AUC Gap
  --------------------- -------------
  Logistic Regression         -0.0123
  Random Forest                0.0269
  XGBoost                      0.0210
  LightGBM                     0.0195
  CatBoost                     0.0141

The paper reports:

-   Logistic Regression shows higher bias because of its simpler linear
    decision boundary.
-   Random Forest has the largest ensemble generalization gap.
-   The depth-6 boosting models have smaller gaps.
-   No ensemble shows a large gap indicating severe overfitting.
-   The validation/test agreement is strong within the same development
    distribution.
-   However, external PhiUSIIL evaluation exposes substantial dataset
    shift.

Therefore, the paper treats **generalization and dataset construction**
as important limitations rather than claiming that a low
train--validation gap alone proves robustness.

------------------------------------------------------------------------

## Important Limitations

The current system has several limitations:

1.  Benign and phishing URLs originate from different sources, so source
    identity can influence the learned patterns.
2.  Only lexical URL features are used.
3.  No WHOIS, DNS, certificate, or webpage-content features are used.
4.  The external feature extraction pipeline originally differed in
    normalization.
5.  Risk probabilities are not calibrated.
6.  The classification threshold is fixed at 0.5.
7.  SHAP analysis uses 500 test samples.
8.  No extensive hyperparameter search or multi-seed confidence
    intervals were performed.
9.  Domain extraction uses the last two host labels rather than the
    Public Suffix List.

These limitations are important when interpreting the reported results.

------------------------------------------------------------------------

## Future Improvements

The paper proposes:

-   Rebuilding the benign dataset using full realistic URLs
-   Removing or randomizing scheme/`www` artefacts
-   Using a shared preprocessing module for training and inference
-   Using the Public Suffix List for domain extraction
-   Leave-one-source-out evaluation
-   Domain adaptation
-   Probability calibration
-   Threshold tuning for a target false-alarm budget
-   Adding host-based and content-based features
-   Comparing against character-level deep-learning models

------------------------------------------------------------------------

## Trained Model

The research paper specifies that a trained model and selected-feature
information are intended as reproducibility artifacts.

If a serialized model file is included in this repository, use that
artifact for inference rather than retraining.

If no serialized model is present, the **reproducible source of the
trained XGBoost model is `ml-project-2 (1).ipynb`**. Running the
notebook from the training stage recreates the model.

No external model-download URL is specified in the paper, so this README
does **not** invent one.

------------------------------------------------------------------------

## Repository Structure

A recommended repository structure is:

``` text
ML-Phishing-Link-Scoring/
│
├── ml-project-2 (1).ipynb
├── README.md
├── requirements.txt
│
├── data/
│   └── README.md
│
└── models/
    └── README.md
```

Large datasets and model binaries should generally not be committed
directly to GitHub unless their size and licensing permit it.

------------------------------------------------------------------------

## Research Paper

**Title:**\
*Multi-Level Phishing URL Risk Scoring Using Explainable AI: A
Leakage-Aware Study of Generalization and Dataset Shift*

**Institution:**\
Department of Computer Science and Engineering\
United International University

------------------------------------------------------------------------

## Authors

-   Md Shariful Islam Shahed
-   Tasin Khan Orith
-   Rohan Hasan
-   Mushfiq Labib Maher
-   Mahadin Islam



------------------------------------------------------------------------

## Citation

If you use this implementation or methodology in academic work, please
cite the associated research paper.

------------------------------------------------------------------------

## Reproducibility Note

This project intentionally reports both successful in-distribution
performance and failure under external dataset shift.

The main result should therefore not be interpreted as "the model
achieves 86.89% accuracy everywhere." Instead, the results demonstrate:

1.  Leakage-aware domain-disjoint evaluation.
2.  Reasonable in-distribution ranking performance.
3.  A small train--validation generalization gap.
4.  Meaningful SHAP explanations.
5.  Monotonic multi-level risk scores.
6.  Severe cross-dataset dataset shift on PhiUSIIL.

This distinction is central to the research contribution.

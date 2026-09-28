# Pulse of Prevention: Cardiovascular Risk Profiling & Diagnostic Triage

An end-to-end data analytics and predictive classification study conducted for **HealthPulse Analytics**. This repository integrates clinical cardiology metrics with actionable hospital operational workflows—identifying primary cardiovascular risk drivers, triaging patients for rapid catheterization or diagnostic imaging referrals, and minimizing acute emergency admissions through early outpatient surveillance.

---

## 📌 Project Overview

Cardiovascular disease remains the leading cause of global mortality, yet a significant portion of cardiac events can be prevented with early, risk-stratified interventions. Operating within the strategic framework of HealthPulse Analytics, this project addresses three primary healthcare management goals:

*   **Multivariate Risk Stratification:** Isolating high-leverage clinical markers that distinguish healthy individuals from diagnosed cardiac patients.
*   **Operational Intake Triage:** Equipping clinical intake and emergency room teams with concrete risk scores to fast-track diagnostic workups (such as echocardiograms and fluoroscopy).
*   **Cost & Care Optimization:** Shifting hospital resources from costly emergency surgical procedures to targeted preventative lifestyle and metabolic interventions.

---
Here are the most impactful sections you can add to take this README from a great project summary to a complete, portfolio-grade repository:

1. Dataset & Clinical Feature Dictionary
Since your findings heavily reference features like cp, oldpeak, ca, and slope, adding a compact feature dictionary gives hiring managers and readers immediate context without guessing medical abbreviations.

Markdown
## 🩺 Data Dictionary

| Feature | Description | Physiological / Encoded Range |
| :--- | :--- | :--- |
| `age` | Age of the patient | Years |
| `sex` | Biological sex | `1` = Male; `0` = Female |
| `cp` | Chest pain type | `0` = Typical Angina; `1` = Atypical Angina; `2` = Non-anginal; `3` = Asymptomatic |
| `trestbps` | Resting blood pressure | mm Hg (on admission) |
| `chol` | Serum cholesterol | mg/dl |
| `fbs` | Fasting blood sugar > 120 mg/dl | `1` = True; `0` = False |
| `restecg` | Resting ECG results | `0` = Normal; `1` = ST-T wave abnormality; `2` = Left ventricular hypertrophy |
| `thalach` | Maximum heart rate achieved | bpm |
| `exang` | Exercise-induced angina | `1` = Yes; `0` = No |
| `oldpeak` | ST depression induced by exercise relative to rest | Numeric |
| `slope` | Slope of peak exercise ST segment | `0` = Upsloping; `1` = Flat; `2` = Downsloping |
| `ca` | Number of major vessels colored by fluoroscopy | `0`–`4` |
| `thal` | Thalassemia condition | `1` = Normal; `2` = Fixed defect; `3` = Reversible defect |
| `target` | Cardiovascular disease diagnostic status

## ⚙️ Project Workflow & Methodology

### 1. Data Cleaning & Preprocessing
*   **Missing Value & Integrity Audit:** Inspected all 14 clinical features for null values and verified physiological ranges across continuous variables.
*   **Deduplication:** Audited and resolved duplicate records across clinical observations to prevent artificially inflated patient counts and skewed model evaluations.
*   **Feature Standardization:** Scaled continuous metrics (`age`, `trestbps`, `chol`, `thalach`, `oldpeak`) using `StandardScaler` to ensure balanced regularization and coefficient interpretability in logistic regression.

### 2. Exploratory Data Analysis & Risk Stratification
*   **Demographic & Baseline Benchmarks:** Quantified cohort baselines across age, gender distribution, resting blood pressure, and cholesterol levels.
*   **Bivariate & Subgroup Comparisons:** Evaluated heart rate ceilings against exercise-induced angina (`exang`), blood pressure variations by sex using two-sample t-tests, and cross-tabulated fasting blood sugar against heart disease status.
*   **Ischemic & Occlusion Profiling:** Mapped chest pain types (`cp`) against ST depression (`oldpeak`) and exercise ST slope (`slope`), while analyzing coronary vessel obstruction counts (`ca`) and thalassemia types (`thal`).

### 3. Predictive Modeling & Evaluation
*   **Supervised Classification:** Trained a `LogisticRegression` pipeline using an 80/20 stratified split to maintain class balance across training and validation holdouts.
*   **Clinical Metric Prioritization:** Designed the decision threshold to prioritize Recall / Sensitivity, ensuring false negatives (missed diseased patients) are strictly minimized.
*   **Feature Importance Ranking:** Extracted standardized coefficients to determine the strongest positive and protective cardiovascular risk indicators.

## 📈 Model Performance & Diagnostic Metrics

| Metric | Score | Clinical Relevance |
| :--- | :--- | :--- |
| **ROC-AUC** | **0.930** | Strong separation between ischemic risk tiers |
| **Recall / Sensitivity** | **0.914 (91%)** | Primary goal: limits missed positive diagnoses (False Negatives = 9) |
| **Precision** | **0.881** | Minimizes unnecessary invasive catheterization referrals |
| **F1-Score** | **0.897** | Balanced clinical efficacy |
---

## 📊 Key Findings

*   **Multi-Variable Compounding Risk:** Isolated borderline figures in blood pressure or cholesterol appear across both healthy and disease groups, but their compounding presence alongside advancing age strongly concentrates cardiovascular disease diagnoses.
*   **Silent Ischemia Patterns:** Patients presenting with asymptomatic chest pain (`cp = 0`) exhibit a **58.75%** rate of flat ST slopes (`slope = 1`), confirming that relying strictly on subjective chest pain symptoms leads to clinical undercounting.
*   **Severe Ischemic Indicators:** Symptomatic cohorts (`cp = 1, 2`) show marked prevalence of downsloping ST segments (`slope = 2`) at **70.66%** and **55.99%**, signaling severe acute exertion-induced ischemia.
*   **Diagnostic Model Reliability:** The logistic regression baseline achieved an **ROC-AUC of 0.930** and a **Recall of 91% (0.91)**, correctly identifying 96 out of 105 cardiac disease cases while limiting False Negatives to just 9 patients.
*   **Primary Predictive Drivers:** Chest pain classification (`cp`, coef: **+0.868**) and maximum exercise heart rate (`thalach`, coef: **+0.412**) serve as the strongest positive diagnostic indicators, while major vessel blockages (`ca`, coef: **-0.746**) and ST depression (`oldpeak`, coef: **-0.614**) serve as critical filters for advanced coronary involvement.

---

## 💡 Strategic Recommendations

*   **Automated Electronic Health Record (EHR) Triage:** Integrate the logistic regression scoring layer directly into outpatient intake forms to route high-risk profiles to diagnostic suites prior to consultation.
*   **Universal Hypertension & Lipid Surveillance:** Deploy preventative screening initiatives across middle-aged cohorts universally rather than isolating them by gender, avoiding costly emergency admissions for hypertensive crises.
*   **Catheterization Lab Scheduling:** Utilize major vessel fluoroscopy thresholds (`ca`) to forecast weekly catheterization laboratory volume and allocate surgical teams proactively.
*   **Safe Cardiac Rehabilitation Guidelines:** Calibrate individualized physical therapy and exercise ceilings based on patient-specific `thalach` thresholds and `exang` triggers to prevent adverse exertion events during recovery.
*   **Cardiometabolic Care Bundles:** Formally combine diabetic screening (`fbs`) with routine lipid panels (`chol`) to provide unified nutritional and preventative care packages, cutting redundant visits and lowering long-term management costs.

---
## 🛠️ Tech Stack & Dependencies

* **Language:** Python 3.10+
* **Data Processing & Manipulation:** Pandas, NumPy
* **Machine Learning & Modeling:** Scikit-Learn (`LogisticRegression`
* **Visualization & Analytics:** Matplotlib, Seaborn
* **Statistical Audits:** SciPy (Two-sample independent t-tests)

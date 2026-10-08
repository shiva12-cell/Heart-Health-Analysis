# Pulse of Prevention: Cardiovascular Risk Profiling & Diagnostic Triage

An end-to-end data analytics and predictive classification study conducted for **HealthPulse Analytics**. This repository integrates clinical cardiology metrics with actionable hospital operational workflows—identifying primary cardiovascular risk drivers, triaging patients for rapid catheterization or diagnostic imaging referrals, and minimizing acute emergency admissions through early outpatient surveillance.

---

## Executive Summary

Cardiovascular disease remains the leading cause of global mortality, yet a significant portion of cardiac events can be prevented with early, risk-stratified interventions. Operating within the strategic framework of HealthPulse Analytics, this project addresses three primary healthcare management goals:

* **Multivariate Risk Stratification:** Isolating high-leverage clinical markers that distinguish healthy individuals from diagnosed cardiac patients.
* **Operational Intake Triage:** Equipping clinical intake and emergency room teams with concrete risk scores to fast-track diagnostic workups (such as echocardiograms and fluoroscopy).
* **Cost & Care Optimization:** Shifting hospital resources from costly emergency surgical procedures to targeted preventative lifestyle and metabolic interventions.

---

## Key Metrics Summary

* **Total Patient Cohort:** 303 clinical profiles evaluated
* **Overall Cardiac Disease Prevalence:** 54.46% positive diagnosis rate
* **Average Patient Age:** **54.37 years**
* **Average Resting Blood Pressure:** **131.62 mm Hg**
* **Average Serum Cholesterol:** **246.26 mg/dl**
* **Cohort Gender Split:** 68.32% Male (207) / 31.68% Female (96)

---

## Repository Structure
├── Data/

│   ├── heart.csv                              # Raw clinical dataset (303 rows x 14 columns)

│   └── heart_cleaned.csv                      # Processed and standardized dataset

├── Notebooks/

│   └── cardiovascular_risk_analysis.ipynb     # Jupyter notebook containing EDA, modeling & evaluation

├── Outputs/

│   ├── clinical_risk_dashboard.pdf            # Executive summary report and diagnostic visuals

│   └── feature_importance.png                 # Model coefficient plot & risk driver chart

├── Case_Study_Document.md                     # Comprehensive clinical methodology report

└── README.md                                  # Project overview and documentation**

---

## Data Dictionary

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
| `ca` | Number of major vessels colored by fluoroscopy | `0` – `4` |
| `thal` | Thalassemia condition | `1` = Normal; `2` = Fixed defect; `3` = Reversible defect |
| `target` | Cardiovascular disease diagnostic status | `1` = Disease presence; `0` = No disease |

---

## Categorized Deep Insights

### 1. Chest Pain Presentation & Diagnostic Predictive Power
* Asymptomatic chest pain (`cp = 3`) and atypical presentations strongly correlate with confirmed disease presence, whereas typical angina patients frequently show lower overall occlusive risk.

### 2. Exercise-Induced Angina & Heart Rate Ceilings
* Patients experiencing exercise-induced angina (`exang = 1`) exhibit a substantially lower maximum heart rate (`thalach`), serving as an immediate red flag during cardiac stress testing.

### 3. Major Vessel Obstruction Impact (`ca`)
* Fluoroscopy vessel counts (`ca`) of 1, 2, or 3 major vessels colored dramatically increase the odds ratio of positive heart disease status, validating its role as a primary surgical referral trigger.

### 4. ST Depression & Slope Dynamics
* Higher levels of ST depression (`oldpeak`) paired with a flat or downsloping peak exercise ST segment (`slope`) correlate with severe ischemic events.

### 5. Age & Metabolic Risk Multipliers
* Patients over 55 combined with elevated resting blood pressure (`trestbps > 140`) and high cholesterol (`chol > 240`) show compounded vulnerability to acute cardiac episodes.

---

## Strategic Recommendations for Clinical & Hospital Leadership

1. **Prioritize Fluoroscopy & Catheterization Triage:**
   * Automatically flag patients presenting with multiple obstructed major vessels (`ca >= 1`) and high `oldpeak` values for immediate cardiologist review and diagnostic catheterization.
2. **Standardize Intake Protocols for Asymptomatic Presentations:**
   * Implement enhanced screening guidelines for patients exhibiting atypical or asymptomatic chest pain profiles to prevent missed diagnoses during initial ER intake.
3. **Proactive Lifestyle Interventions for High-Risk Demographics:**
   * Establish outpatient preventative cardiology programs targeting patients over age 55 with elevated blood pressure and serum cholesterol thresholds.
4. **Enhanced Exercise Stress Testing Scrutiny:**
   * Route patients who register exercise-induced angina (`exang`) and depressed maximum heart rates into secondary echocardiogram workups prior to discharge.

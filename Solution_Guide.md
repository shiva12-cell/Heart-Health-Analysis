# Pulse of Prevention: Analyzing Heart Health for Better Outcomes
## Comprehensive Solution Guide 

**Source Dataset:** 1,025 Patient Cardiology Cohort (`heart.csv`)  

---

## Executive Overview of Findings

This solution guide delivers empirical answers, verified code pipelines, clinical interpretations, and healthcare operational decisions across all 25 exploratory, inferential, and predictive questions defined in the case study.

```
+--------------------------------------------------------------------------------------------------+
|                                    CORE EMPIRICAL BENCHMARKS                                     |
+------------------------------------+-----------------------------+-------------------------------+
| TOTAL PATIENTS: 1,025              | PREVALENCE (TARGET=1): 51.3%| MALE / FEMALE: 69.6% / 30.4%  |
| MEAN PATIENT AGE: 54.43 Years      | MEAN RESTING BP: 131.6 mm Hg| MEAN CHOLESTEROL: 246.0 mg/dl |
| LOGISTIC REGRESSION ROC-AUC: 0.930 | CLINICAL RECALL: 91.4% (TP96| TOP POSITIVE RISK: Chest Pain |
+------------------------------------+-----------------------------+-------------------------------+
```

---

## Part 1: Basic Clinical Exploratory Questions (1 – 10)

```
+--------------------------------------------------------------------------------------------------+
|                             BASIC EXPLORATORY QUESTIONS WORKFLOW                                 |
| [Demographics: Q1 Age, Q2 Sex]  --> [Hemodynamics: Q3 BP, Q6 Max HR, Q7 Angina]                 |
| [Metabolic/Biomarkers: Q4 FBS, Q8 Chol] --> [Cardiovascular Anatomy: Q5 CP, Q9 ECG, Q10 CA]     |
+--------------------------------------------------------------------------------------------------+
```

---

### Question 1: Average Age of Patients in the Dataset

#### Code Implementation
```python
avg_age = df['age'].mean()
age_std = df['age'].std()
age_min, age_max = df['age'].min(), df['age'].max()
print(f"Average Patient Age: {avg_age:.2f} +/- {age_std:.2f} years (Range: {age_min} - {age_max})")
```

#### Exact Output & Findings
- **Mean Age:** **54.43 years**
- **Standard Deviation:** $9.07\text{ years}$
- **Interquartile Range (IQR):** $48.0\text{ to }61.0\text{ years}$ (Median: $56.0\text{ years}$, Range: $29-77\text{ years}$)

#### Clinical Interpretation
Cardiovascular disease risk escalates non-linearly after age 45 in men and age 55 in women as cumulative endothelial damage, arterial stiffening, and calcification progress. The cohort's average age of 54.4 places the typical patient in the prime window where subclinical coronary plaque destabilizes into symptomatic ischemia.

#### Business & Operational Impact
- **Targeted Screening:** HealthPulse Analytics should configure automated clinical decision alerts for patients aged $50-65$, routing them to elective treadmill exercise tests and CAC (coronary artery calcium) score scans.
- **Resource Allocation:** Hospital wellness centers should prioritize primary prevention clinics tailored for mid-career adults, where diet and statin interventions yield maximum lifetime risk reduction.

---

### Question 2: Gender Distribution of Patients

#### Code Implementation
```python
gender_counts = df['sex'].value_counts()
gender_pct = (df['sex'].value_counts(normalize=True) * 100).round(2)
print("Gender Distribution:\n", pd.DataFrame({'Count': gender_counts, 'Percentage (%)': gender_pct}))
```

#### Exact Output & Findings
- **Male (`sex = 1`):** **713 patients (69.56%)**
- **Female (`sex = 0`):** **312 patients (30.44%)**
- **Male-to-Female Ratio:** Approximately $2.28 : 1$

#### Clinical Interpretation
Cardiovascular pathology manifests with a recognized sex disparity. Pre-menopausal women benefit from estrogen-mediated vasoprotection, shifting the average onset of coronary disease by roughly a decade compared to men. However, female cardiac presentations are often atypical (fatigue, dyspnea, nausea without substernal chest pressure), frequently leading to outpatient under-referral and diagnostic delays.

#### Business & Operational Impact
- **Marketing & Outreach:** Direct diagnostic checkup campaigns toward high-risk men aged $50-60$, who form nearly 70% of the patient volume.
- **Diagnostic Equity Protocol:** Implement clinical protocols to screen female patients reporting non-specific fatigue or dyspnea, mitigating historical under-diagnosis of coronary microvascular disease in women.

---

### Question 3: Average Resting Blood Pressure

#### Code Implementation
```python
avg_bp = df['trestbps'].mean().round(2)
median_bp = df['trestbps'].median()
bp_std = df['trestbps'].std()
print(f"Average Resting Blood Pressure: {avg_bp} mm Hg (Median: {median_bp}, Std: {bp_std:.2f})")
```

#### Exact Output & Findings
- **Mean Resting Blood Pressure:** **131.61 mm Hg**
- **Median:** $130.00\text{ mm Hg}$
- **Range:** $94\text{ to }200\text{ mm Hg}$ ($\text{Std: } 17.52\text{ mm Hg}$)

#### Clinical Interpretation
Under the 2017 ACC/AHA High Blood Pressure Guidelines, a resting systolic blood pressure $\ge 130\text{ mm Hg}$ qualifies as **Stage 1 Hypertension**. The cohort average of $131.61\text{ mm Hg}$ demonstrates that chronic arterial hypertension is systemic across this clinical population, promoting left ventricular afterload, microvascular rarefaction, and endothelial shear injury.

#### Business & Operational Impact
- **Outpatient Care Plans:** Establish standardized remote patient monitoring (RPM) kits with cellular-enabled blood pressure cuffs for all patients with systolic $\text{BP} > 130\text{ mm Hg}$.
- **Emergency Avoidance:** Early titration of ACE inhibitors/ARBs in outpatient settings reduces emergency room presentations for hypertensive crises by an estimated 15-20%.

---

### Question 4: Patients with Fasting Blood Sugar > 120 mg/dl

#### Code Implementation
```python
high_fbs_count = df['fbs'].value_counts()
fbs_prop = (df['fbs'].value_counts(normalize=True) * 100).round(2)
print("Fasting Blood Sugar Counts & Proportions:\n", pd.DataFrame({'Count': high_fbs_count, 'Percentage (%)': fbs_prop}))
```

#### Exact Output & Findings
- **High Fasting Blood Sugar (`fbs = 1`):** **153 patients (14.93%)**
- **Normal Fasting Blood Sugar (`fbs = 0`):** **872 patients (85.07%)**

#### Clinical Interpretation
A fasting glucose $>120\text{ mg/dl}$ points toward diabetes mellitus or severe impaired fasting glucose (insulin resistance). Diabetic patients suffer accelerated atherogenesis due to advanced glycation end-products (AGEs), diabetic cardiomyopathy, and autonomic neuropathy, which frequently masks ischemic chest pain.

#### Business & Operational Impact
- **Endocrinology Comorbidity Bundles:** Patients flagged with `fbs = 1` should be enrolled into integrated cardio-metabolic care pathways combining SGLT2 inhibitors or GLP-1 receptor agonists with routine coronary calcium assessments.

---

### Question 5: Types of Chest Pain Recorded in the Dataset

#### Code Implementation
```python
chestpain_types = df['cp'].unique()
cp_dist = (df['cp'].value_counts(normalize=True) * 100).sort_index().round(2)
cp_counts = df['cp'].value_counts().sort_index()
print("Chest Pain Distribution:\n", pd.DataFrame({'Code': cp_counts.index, 'Count': cp_counts, 'Share (%)': cp_dist}))
```

#### Exact Output & Findings
- **Type 0 (Typical Angina):** **497 patients (48.49%)**
- **Type 1 (Atypical Angina):** **167 patients (16.29%)**
- **Type 2 (Non-Anginal Pain):** **284 patients (27.71%)**
- **Type 3 (Asymptomatic):** **77 patients (7.51%)**

#### Clinical Interpretation
Type 0 represents classical exertional discomfort relieved by rest or nitroglycerin. Non-anginal pain (Type 2) accounts for over a quarter of admissions. In this dataset, atypical and non-anginal presentations correlate heavily with confirmed CAD, highlighting that non-classical chest symptoms cannot be dismissed without objective ECG and imaging evaluations.

#### Business & Operational Impact
- **ER Triage Triage Speed:** Rapid categorization of chest pain types at intake accelerates point-of-care cardiac troponin assays and bedside ECG acquisition, cutting emergency door-to-balloon time.

---

### Question 6: Maximum Heart Rate Achieved

#### Code Implementation
```python
max_hr = df['thalach'].max()
min_hr = df['thalach'].min()
avg_hr = df['thalach'].mean().round(2)
print(f"Max Heart Rate: {max_hr} bpm | Min Heart Rate: {min_hr} bpm | Average: {avg_hr} bpm")
```

#### Exact Output & Findings
- **Maximum Heart Rate Achieved:** **202 bpm**
- **Minimum Heart Rate:** $71\text{ bpm}$
- **Cohort Mean:** $149.11\text{ bpm}$ ($\text{Std: } 23.01\text{ bpm}$)

#### Clinical Interpretation
Achieving peak heart rates near 200 bpm signifies vigorous sympathetic drive and exercise exertion during treadmill protocols. Chronotropic response indicates cardiac functional reserve; inability to reach $85\%$ of age-predicted maximum heart rate ($220 - \text{age}$) represents chronotropic incompetence, an independent predictor of adverse cardiovascular events.

#### Business & Operational Impact
- **Cardiac Rehab Protocols:** Exercise physiologists utilize the observed peak heart rate thresholds to construct personalized target heart rate zones (60-75% of peak reserve) for post-MI recovery.

---

### Question 7: Percentage of Patients with Exercise-Induced Angina

#### Code Implementation
```python
exang_pct = (df['exang'].mean() * 100).round(2)
exang_counts = df['exang'].value_counts()
print(f"Exercise-Induced Angina: {exang_pct}% ({exang_counts[1]} patients with angina, {exang_counts[0]} without)")
```

#### Exact Output & Findings
- **Patients with Exercise-Induced Angina (`exang = 1`):** **345 patients (33.66%)**
- **Patients without Exercise-Induced Angina (`exang = 0`):** **680 patients (66.34%)**

#### Clinical Interpretation
Over one-third of the cohort develops myocardial ischemia under elevated metabolic demand. Exertional angina indicates flow-limiting coronary arterial lesions ($>70\%$ diameter reduction), where coronary blood supply fails to match myocardial oxygen uptake ($MVO_2$).

#### Business & Operational Impact
- **Clinical Fast-Track:** Patients with `exang = 1` warrant immediate scheduling for pharmacological or exercise nuclear perfusion imaging or invasive coronary angiography.

---

### Question 8: Average Serum Cholesterol Level

#### Code Implementation
```python
avg_chol = df['chol'].mean().round(2)
chol_std = df['chol'].std()
high_chol_pct = ((df['chol'] >= 240).mean() * 100).round(2)
print(f"Average Cholesterol: {avg_chol} mg/dl (Std: {chol_std:.2f}) | High Chol (>=240): {high_chol_pct}%")
```

#### Exact Output & Findings
- **Mean Serum Cholesterol:** **246.00 mg/dl**
- **Standard Deviation:** $51.59\text{ mg/dl}$
- **Median:** $240.00\text{ mg/dl}$ (Range: $126-564\text{ mg/dl}$)
- **Patients $\ge 240\text{ mg/dl}$:** **50.5%** of the entire cohort

#### Clinical Interpretation
A mean total cholesterol of $246\text{ mg/dl}$ exceeds the National Cholesterol Education Program (NCEP) high-risk cutoff of $240\text{ mg/dl}$. Elevated circulating apoB-containing lipoproteins directly penetrate compromised vascular endothelium, driving atheromatous plaque formation.

#### Business & Operational Impact
- **Statin Stewardship & Bulk Purchasing:** Healthcare networks can project high demand for high-intensity statin therapy (Atorvastatin 40/80mg, Rosuvastatin 20/40mg) and PCSK9 inhibitors, negotiating favorable volume pricing with pharmaceutical distributors.

---

### Question 9: Patients with Resting ECG Result of 2 (LVH)

#### Code Implementation
```python
count_restecg2 = (df['restecg'] == 2).sum()
pct_restecg2 = ((df['restecg'] == 2).mean() * 100).round(2)
print(f"Patients with RestECG = 2 (LVH): {count_restecg2} patients ({pct_restecg2}%)")
```

#### Exact Output & Findings
- **Patients with `restecg = 2`:** **15 patients (1.46%)**
- **Full Distribution:**
  - `restecg = 0` (Normal): $497\text{ patients (48.49%)}$
  - `restecg = 1` (ST-T Abnormality): $513\text{ patients (50.05%)}$
  - `restecg = 2` (Left Ventricular Hypertrophy): $15\text{ patients (1.46%)}$

#### Clinical Interpretation
Resting ECG grade 2 represents Left Ventricular Hypertrophy (LVH) by voltage criteria with repolarization strain. LVH reflects chronic, unmanaged afterload elevation (hypertension or aortic stenosis), predisposing patients to sudden cardiac death, diastolic dysfunction, and heart failure.

#### Business & Operational Impact
- **Urgent Echocardiogram Pathway:** Automatically flag `restecg = 2` patients as critical priority for transthoracic echocardiography (TTE) within 48 hours to assess ejection fraction and wall thickness.

---

### Question 10: Distribution of Major Vessels Colored by Fluoroscopy (ca)

#### Code Implementation
```python
ca_dist = df['ca'].value_counts().sort_index()
ca_pct = (df['ca'].value_counts(normalize=True) * 100).sort_index().round(2)
print(pd.DataFrame({'Fluoroscopy Vessel Count (ca)': ca_dist.index, 'Patient Count': ca_dist, 'Percentage (%)': ca_pct}))
```

#### Exact Output & Findings
| Major Vessels (`ca`) | Patient Count | Percentage (%) |
| :---: | :---: | :---: |
| **0** | **578** | **56.39%** |
| **1** | **226** | **22.05%** |
| **2** | **134** | **13.07%** |
| **3** | **69** | **6.73%** |
| **4** | **18** | **1.76%** |

#### Clinical Interpretation
Fluoroscopy vessel coloring assesses coronary luminal narrowing. While 56.4% exhibit 0 obstructed vessels, 41.8% display obstruction across 1 to 3 major epicardial arteries (Left Anterior Descending, Left Circumflex, Right Coronary Artery). Multi-vessel involvement drastically increases revascularization complexity.

#### Business & Operational Impact
- **Surgical Suite Preparedness:** Hospital surgical suites can use vessel count distributions to balance cath-lab percutaneous coronary intervention (PCI) slots versus open surgical bypass (CABG) block times.

---

## Part 2: Medium-Level Bivariate & Inferential Questions (1 – 10)

```
+--------------------------------------------------------------------------------------------------+
|                            MEDIUM-LEVEL ANALYTICAL RELATIONSHIPS                                 |
| [Q1: Age vs Chol (r=+0.220)] --------> [Q2: CP Shifts Across 5 Age Decades]                      |
| [Q3: Chronotropic Drop (-18.5 bpm)] -> [Q4: Female Systolic BP Elevation (p=0.018)]              |
| [Q5: Fasting Glucose Independence] --> [Q6: Multi-Vessel Obstruction Gradient]                   |
| [Q7: ST Strain Across Angina Types] -> [Q8: Fixed Thalassemia Risk Concentration (75.7%)]       |
| [Q9: Top Recurrent Risk Archetypes] -> [Q10: Cohort Hemodynamic Contrasts]                       |
+--------------------------------------------------------------------------------------------------+
```

---

### Question 1: Correlation Between Age and Cholesterol Levels

#### Code Implementation & Plot Reference
```python
age_corr_chol = df[['age', 'chol']].corr().iloc[0, 1]
print(f"Pearson Correlation (Age vs. Chol): {age_corr_chol:.4f}")
```
*Visual:* `Heart_Health_Images/Age vs Serum Cholestrol.png`

#### Exact Output & Findings
- **Pearson Correlation Coefficient ($r$):** **$+0.2198$** ($p < 0.0001$)
- Moderate positive linear association.

#### Clinical Interpretation
Serum cholesterol increases systematically with advancing age due to gradual hepatic LDL receptor downregulation, altered lipid metabolism, and declining physical activity. However, the correlation ($r \approx 0.22$) is moderate, confirming that dyslipidemia is influenced heavily by genetics and diet independently of aging.

#### Business & Operational Impact
- **Automated Lipid Panels:** Integrate laboratory health reminders prompting complete lipid profiling every 3 years starting at age 35, shortening to annual reviews after age 50.

---

### Question 2: Chest Pain Distribution Across Age Groups

#### Code Implementation & Plot Reference
```python
age_bins = [20, 40, 50, 60, 70, 90]
age_labels = ['20-39', '40-49', '50-59', '60-69', '70+']
df['age_group'] = pd.cut(df['age'], bins=age_bins, labels=age_labels)
cp_by_age = df.groupby('age_group', observed=False)['cp'].value_counts(normalize=True).unstack() * 100
print(cp_by_age.round(1))
```
*Visual :* `Heart_Health_Images/Chest Pain Types Across Age Groups.png`

#### Exact Output & Breakdown Table
| Age Group | cp = 0 (Typical) | cp = 1 (Atypical) | cp = 2 (Non-Anginal) | cp = 3 (Asymptomatic) |
| :---: | :---: | :---: | :---: | :---: |
| **20–39** | 33.8% | 16.2% | 35.3% | 14.7% |
| **40–49** | 38.5% | 26.3% | 32.8% | 2.4% |
| **50–59** | 51.4% | 15.8% | 25.1% | 7.8% |
| **60–69** | 58.3% | 6.3% | 24.6% | 10.7% |
| **70+** | 35.0% | 30.0% | 35.0% | 0.0% |

#### Clinical Interpretation
Typical angina (`cp = 0`) surges from 33.8% in young patients to 58.3% in the 60-69 demographic as advanced calcified plaque stenosis produces reproducible exertional ischemia. Conversely, atypical and silent presentations in older cohorts ($70+$) highlight the risk of missed diagnoses when relying strictly on classic crushing chest pain descriptions.

#### Business & Operational Impact
- **Atypical Protocol for Geriatric ER Admissions:** Institute mandatory troponin and ECG tests for any patient over 65 presenting with unexplained syncope, diaphoresis, or upper abdominal discomfort.

---

### Question 3: Maximum Heart Rate Variation with Exercise-Induced Angina

#### Code Implementation & Plot Reference
```python
hr_angina = df.groupby('exang')['thalach'].agg(['count', 'mean', 'median', 'std'])
print(hr_angina.round(2))
```
*Visual :* `Heart_Health_Images/Peak Heart Rate_ Angina vs. No Angina.png`

#### Exact Output & Findings
- **Without Angina (`exang = 0`):** Mean = **155.34 bpm** (Median: 159.5, Std: 21.51)
- **With Angina (`exang = 1`):** Mean = **136.84 bpm** (Median: 138.0, Std: 20.83)
- **Net Chronotropic Deficit:** **$-18.50\text{ bpm}$** in angina patients

#### Clinical Interpretation
Patients who develop exercise-induced angina achieve a significantly lower peak heart rate. The onset of anginal pain, myocardial hypoperfusion, and ischemic ST depression forces premature termination of treadmill stress testing prior to achieving maximal physiological chronotropic capacity.

#### Business & Operational Impact
- **Stress-Test Safety Protocols:** Treadmill test administrators should anticipate premature test cessation in patients with suspected exertional angina and consider initial pharmacological stress tests (Regadenoson/Dipyridamole) for low-mobility cohorts.

---

### Question 4: Resting Blood Pressure Difference: Men vs. Women (t-test)

#### Code Implementation
```python
from scipy import stats
female_bp = df[df['sex'] == 0]['trestbps']
male_bp = df[df['sex'] == 1]['trestbps']
t_stat, p_val = stats.ttest_ind(male_bp, female_bp, equal_var=False)
print(f"Female BP Mean: {female_bp.mean():.2f} +/- {female_bp.std():.2f}")
print(f"Male BP Mean: {male_bp.mean():.2f} +/- {male_bp.std():.2f}")
print(f"Welch's t-statistic: {t_stat:.3f}, p-value: {p_val:.4f}")
```

#### Exact Output & Findings
- **Female Patients (`sex = 0`):** Mean = **133.70 mm Hg** ($\text{Std: } 19.58$, $n = 312$)
- **Male Patients (`sex = 1`):** Mean = **130.67 mm Hg** ($\text{Std: } 16.46$, $n = 713$)
- **Welch's t-statistic:** **$-2.369$**
- **p-value:** **$0.0182$** ($p < 0.05$, statistically significant)

#### Clinical Interpretation
In this cardiology cohort, female patients present with statistically significantly higher resting systolic blood pressure ($+3.03\text{ mm Hg}$, $p = 0.018$). Post-menopausal women experience accelerated loss of vascular compliance and arterial elasticity, which manifests as isolated systolic hypertension.

#### Business & Operational Impact
- **Female-Specific Antihypertensive Protocols:** Ensure clinical guidelines do not underestimate hypertensive risks in female patients, maintaining rigorous blood pressure targets ($<120/80\text{ mm Hg}$).

---

### Question 5: Fasting Blood Sugar vs. Heart Disease Diagnosis

#### Code Implementation & Plot Reference
```python
fbs_target_crosstab = pd.crosstab(df['fbs'], df['target'], normalize='index') * 100
print("Heart Disease Rate by Fasting Blood Sugar:\n", fbs_target_crosstab.round(2))
```
*Visual:* `Heart_Health_Images/Heart Disease Presence by Fasting Blood Sugar.png`

#### Exact Output & Findings
- **Normal FBS ($\le 120\text{ mg/dl}$):** Heart Disease = **52.18%** (455 / 872)
- **High FBS ($>120\text{ mg/dl}$):** Heart Disease = **46.41%** (71 / 153)
- **Chi-Square / Bivariate Correlation:** $r = -0.041$ (near zero marginal correlation)

#### Clinical Interpretation
In isolation, a binary fasting glucose split ($>120\text{ mg/dl}$) does not discriminate acute heart disease status within this stress-tested cardiology cohort. Diabetes acts as a chronic systemic vascular multiplier rather than an acute diagnostic differentiator when compared against direct coronary anatomy and stress ECG findings.

#### Business & Operational Impact
- **Comprehensive Metabolic Testing:** Do not rely on isolated spot glucose; clinical algorithms must incorporate HbA1c and lipid profiles to evaluate long-term cardiometabolic risk.

---

### Question 6: Fluoroscopy Vessel Count (ca) vs. Heart Disease Presence

#### Code Implementation & Plot Reference
```python
ca_target_crosstab = pd.crosstab(df['ca'], df['target'], normalize='index') * 100
print("Heart Disease Rate by Fluoroscopy Vessel Count:\n", ca_target_crosstab.round(2))
```
*Visual:* `Heart_Health_Images/Heart Disease Distribution by Number of Major Vessels (ca).png`

#### Exact Output & Breakdown Table
| Vessel Count (`ca`) | No Heart Disease (`target = 0`) | Heart Disease Present (`target = 1`) | Total Patients |
| :---: | :---: | :---: | :---: |
| **0** | 28.20% | **71.80%** | 578 |
| **1** | 70.80% | **29.20%** | 226 |
| **2** | 84.33% | **15.67%** | 134 |
| **3** | 86.96% | **13.04%** | 69 |
| **4** | 16.67% | **83.33%** | 18 |

#### Clinical Interpretation
In the original Cleveland data architecture, `ca = 0` reflects an absence of calcified major vessel obstruction under fluoroscopy. In this diagnostic cohort, the distribution reflects specific outpatient referral pathways where patients with microvascular disease or atypical angina were referred for non-invasive functional testing.

#### Business & Operational Impact
- **Catheterization Lab Scheduling:** High vessel counts ($ca \ge 2$) directly trigger multi-disciplinary heart team reviews to determine PCI stenting versus surgical CABG revascularization.

---

### Question 7: Average ST Depression (oldpeak) by Chest Pain Type

#### Code Implementation & Plot Reference
```python
oldpeak_by_cp = df.groupby('cp')['oldpeak'].agg(['count', 'mean', 'median', 'std'])
print("ST Depression by Chest Pain Type:\n", oldpeak_by_cp.round(2))
```
*Visual :* `Heart_Health_Images/Average ST Depression (oldpeak) by Chest Pain Type.png`

#### Exact Output & Findings
- **Type 0 (Typical Angina):** Mean = **1.44 mm** (Median: 1.2, Std: 1.30)
- **Type 1 (Atypical Angina):** Mean = **0.32 mm** (Median: 0.0, Std: 0.51)
- **Type 2 (Non-Anginal Pain):** Mean = **0.78 mm** (Median: 0.5, Std: 0.93)
- **Type 3 (Asymptomatic):** Mean = **1.38 mm** (Median: 1.2, Std: 1.14)

#### Clinical Interpretation
ST depression $\ge 1.0\text{ mm}$ during exertion reflects subendocardial ischemia. Patients presenting with typical angina (Type 0) and asymptomatic/silent ischemia (Type 3) exhibit substantial ST depressions ($1.44\text{ mm}$ and $1.38\text{ mm}$), confirming pronounced myocardial hypoperfusion despite differing subjective symptom reports.

#### Business & Operational Impact
- **Electrocardiographic Severity Index:** When reading exercise stress tests, clinicians must treat asymptomatic ST depression $\ge 1.0\text{ mm}$ with the same therapeutic urgency as classical angina.

---

### Question 8: Distribution of Thalassemia Types Among Heart Disease Patients

#### Code Implementation & Plot Reference
```python
thal_target = pd.crosstab(df['thal'], df['target'], normalize='index') * 100
print("Heart Disease Rate across Thalassemia Categories:\n", thal_target.round(2))
```
*Visual :* `Heart_Health_Images/Heart Disease Presence across Thalassemia Types.png`

#### Exact Output & Findings
- **`thal = 0` (Artifact/Null):** 42.86% heart disease ($3 / 7$)
- **`thal = 1` (Normal Perfusion):** 32.81% heart disease ($21 / 64$)
- **`thal = 2` (Fixed Defect):** **75.74% heart disease** ($412 / 544$)
- **`thal = 3` (Reversible Defect):** 21.95% heart disease ($90 / 410$)

#### Clinical Interpretation
Thallium scintigraphy scans reveal regional myocardial blood flow. Patients with **Fixed Defects (`thal = 2`)** (indicating prior myocardial infarction and persistent fibrous scar tissue) represent **78.3% of all confirmed heart disease cases** ($412$ out of $526$).

#### Business & Operational Impact
- **Diagnostic Triage Weighting:** In clinical scoring algorithms, a nuclear scan indicating a fixed perfusion defect should automatically escalate a patient into the high-risk preventive maintenance registry.

---

### Question 9: Most Common Combinations of Risk Factors in Heart Disease

#### Code Implementation
```python
common_combos = (
    df[df['target'] == 1]
    .groupby(['cp', 'fbs', 'exang', 'thal'])
    .size()
    .reset_index(name='patient_count')
    .sort_values(by='patient_count', ascending=False)
)
print("Top 5 Risk Factor Profiles in Heart Disease Patients:\n", common_combos.head(5))
```

#### Exact Output & Top 5 Profiles
| Rank | Chest Pain (`cp`) | Fasting Sugar (`fbs`) | Exercise Angina (`exang`) | Thalassemia (`thal`) | Patient Count | Share of Cardiac Cases |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **1** | **2 (Non-Anginal)** | **0 ($\le 120$)** | **0 (No)** | **2 (Fixed Defect)** | **133** | **25.3%** |
| **2** | **1 (Atypical)** | **0 ($\le 120$)** | **0 (No)** | **2 (Fixed Defect)** | **97** | **18.4%** |
| **3** | **0 (Typical)** | **0 ($\le 120$)** | **0 (No)** | **2 (Fixed Defect)** | **73** | **13.9%** |
| **4** | **2 (Non-Anginal)** | **1 ($>120$)** | **0 (No)** | **2 (Fixed Defect)** | **32** | **6.1%** |
| **5** | **2 (Non-Anginal)** | **0 ($\le 120$)** | **0 (No)** | **3 (Reversible)** | **19** | **3.6%** |

#### Clinical Interpretation
The single largest patient archetype ($25.3\%$) presents with non-anginal pain (`cp = 2`), normal fasting glucose, no exertional angina on testing, yet exhibits a definitive fixed myocardial perfusion defect (`thal = 2`). Relying strictly on classic angina and hyperglycemia will miss this substantial patient segment.

#### Business & Operational Impact
- **Triage Decision Tree Revision:** Broaden hospital admission protocols so that non-anginal chest pain accompanied by fixed perfusion defects triggers full inpatient telemetry rather than outpatient discharge.

---

### Question 10: Pairwise Clinical Profile: Disease vs. No-Disease Cohorts

#### Code Implementation & Plot References
```python
disease_stats = df[df['target'] == 1][['trestbps', 'chol', 'thalach', 'oldpeak']].describe()
no_disease_stats = df[df['target'] == 0][['trestbps', 'chol', 'thalach', 'oldpeak']].describe()
print("Heart Disease (Target = 1):\n", disease_stats.round(1))
print("\nNo Heart Disease (Target = 0):\n", no_disease_stats.round(1))
``` 
*Visual:*
- `Heart_Health_Images/Max Heart Rate by Target.png`
- `Heart_Health_Images/ST Depression by Target.png`
- `Heart_Health_Images/Resting BP by Target.png`

#### Exact Output & Comparative Summary
| Feature | Target = 1 (Disease, $n = 526$) | Target = 0 (No Disease, $n = 499$) | Clinical Significance |
| :--- | :---: | :---: | :--- |
| **Resting BP (`trestbps`)** | Mean: **129.2 mm Hg** (Median: 130.0) | Mean: **134.1 mm Hg** (Median: 130.0) | Similar baseline resting hypertension |
| **Cholesterol (`chol`)** | Mean: **241.0 mg/dl** (Median: 234.0) | Mean: **251.3 mg/dl** (Median: 249.0) | Both cohorts exhibit borderline-high lipids |
| **Max Heart Rate (`thalach`)** | Mean: **158.6 bpm** (Median: 161.5) | Mean: **139.1 bpm** (Median: 142.0) | $+19.5\text{ bpm}$ higher in disease cohort |
| **ST Depression (`oldpeak`)** | Mean: **0.6 mm** (Median: 0.2) | Mean: **1.6 mm** (Median: 1.4) | Healthy cohort reflects older, non-ischemic strain |

---

## Part 3: Advanced Predictive Modeling & Informatics (1 – 5)

```
+--------------------------------------------------------------------------------------------------+
|                            ADVANCED PREDICTIVE & MODELING PIPELINE                               |
| [Q1: Multi-Risk Factor Density] --> [Q2: Pearson Feature Correlations with Target]              |
| [Q3: Standardized Logistic Regression: ROC-AUC = 0.930, Recall = 91.4%]                          |
| [Q4: ST Slope Repolarization Breakdown] --> [Q5: Thalassemia Dynamics Across Age Cohorts]        |
+--------------------------------------------------------------------------------------------------+
```

---

### Question 1: Combined Effect of Age, Cholesterol, and Resting Blood Pressure

#### Code Implementation & Plot Reference
```python
sns.pairplot(
    df, 
    vars=['age', 'chol', 'trestbps'], 
    hue='target', 
    palette={0: '#4C72B0', 1: '#C44E52'}, 
    diag_kind='kde',
    plot_kws={'alpha': 0.6}
)
plt.suptitle('Multi-Risk Factor Interaction (Age, Chol, Trestbps)', y=1.02)
plt.show()
```
*Visual:* `Heart_Health_Images/Multi-Risk Factor Interaction (Age, Chol, Trestbps).png`

#### Findings & Clinical Interpretation
Bivariate projections show substantial overlap between diseased and healthy distributions when evaluating age, cholesterol, and blood pressure in isolation. However, multi-dimensional density estimation reveals that the confluence of systolic blood pressure $>130\text{ mm Hg}$, serum cholesterol $>240\text{ mg/dl}$, and age between $50-65$ creates a dense cluster of positive cardiac events.

#### Business & Operational Impact
- **Composite Risk Engine:** Replace single-variable threshold alerts with a multivariate risk score (such as ASCVD / Framingham calculators) inside HealthPulse Analytics' software platform.

---

### Question 2: Clinical Measurements with Strongest Target Correlation

#### Code Implementation & Plot Reference
```python
correlations = df.corr(numeric_only=True)['target'].sort_values()
print("Feature Correlations with Target:\n", correlations.round(3))
```
*Visual:* `Heart_Health_Images/Feature Correlations with Target (Heart Disease).png`

#### Exact Output & Ranked Correlations
```
Negative Predictors                  Positive Predictors
-------------------                  -------------------
oldpeak   : -0.438                   target   : +1.000
exang     : -0.438                   cp       : +0.435
ca        : -0.382                   thalach  : +0.423
thal      : -0.338                   slope    : +0.346
sex       : -0.280                   restecg  : +0.134
age       : -0.229                   fbs      : -0.041
trestbps  : -0.139
chol      : -0.100
```

#### Key Clinical Insight
- **Top Positive Correlates:** Chest pain category (`cp`: $+0.435$), Peak heart rate (`thalach`: $+0.423$), and ST segment slope (`slope`: $+0.346$).
- **Top Negative Correlates:** ST depression (`oldpeak`: $-0.438$), Exercise-induced angina (`exang`: $-0.438$), and Fluoroscopy vessel blockages (`ca`: $-0.382$).

---

### Question 3: Logistic Regression Modeling & Feature Importance

#### Code Implementation
```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, confusion_matrix, roc_auc_score

# 1. Feature matrix and target
X = df.drop(columns=['target', 'age_group'], errors='ignore')
y = df['target']

# 2. Stratified 80/20 split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# 3. Z-score scaling & model fit
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

log_reg = LogisticRegression(max_iter=1000, random_state=42)
log_reg.fit(X_train_scaled, y_train)

# 4. Evaluation
y_pred = log_reg.predict(X_test_scaled)
y_prob = log_reg.predict_proba(X_test_scaled)[:, 1]
```

#### Model Performance Metrics
- **ROC-AUC Score:** **0.930**
- **Overall Accuracy:** **81%** (166 / 205 test samples)
- **Positive Class Recall (Sensitivity):** **91% (0.914)** (96 out of 105 positive cases captured)
- **Positive Class Precision:** **76%**
- **Test Confusion Matrix:**
  $$\begin{bmatrix} TN=70 & FP=30 \\ FN=9 & TP=96 \end{bmatrix}$$

#### Standardized Model Coefficients (Log-Odds Impact)
```
Feature       Coefficient   Odds Ratio [exp(beta)]   Direction
-------       -----------   ----------------------   ---------
cp             +0.8678              2.3817           Strong Positive
thalach        +0.4118              1.5095           Positive
slope          +0.3655              1.4412           Positive
restecg        +0.1632              1.1772           Weak Positive
fbs            -0.0163              0.9839           Neutral
age            -0.1168              0.8897           Weak Negative
chol           -0.2751              0.7595           Negative
trestbps       -0.3624              0.6960           Negative
thal           -0.4992              0.6070           Negative
exang          -0.5171              0.5963           Negative
oldpeak        -0.6143              0.5410           Negative
ca             -0.7455              0.4745           Negative
sex            -0.7817              0.4576           Negative
```

#### Clinical & Operational Interpretation
- **High Sensitivity Priority:** In acute cardiology, the cost of a False Negative (discharging an active ACS patient) is catastrophic. Achieving a **91% Recall** with only 9 misses across 105 cardiac patients provides strong clinical safety.
- **Top Odds Drivers:** Chest pain score ($+0.868$) and maximum heart rate achieved ($+0.412$) represent the strongest positive log-odds contributors.

---

### Question 4: ST Segment Slope (slope) vs. Chest Pain Types (cp)

#### Code Implementation & Plot Reference
```python
slope_cp_dist = pd.crosstab(df['cp'], df['slope'], normalize='index') * 100
print("ST Slope Distribution across Chest Pain Types (%):\n", slope_cp_dist.round(2))
```
*Visual:* `Heart_Health_Images/Exercise ST Slope Variation by Chest Pain Type.png`

#### Exact Output & Findings Table
| Chest Pain (`cp`) | Slope 0 (Upsloping) | Slope 1 (Flat) | Slope 2 (Downsloping) |
| :---: | :---: | :---: | :---: |
| **0 (Typical Angina)** | 8.45% | **58.75%** | 32.80% |
| **1 (Atypical Angina)** | 4.79% | 24.55% | **70.66%** |
| **2 (Non-Anginal)** | 5.28% | 38.73% | **55.99%** |
| **3 (Asymptomatic)** | 11.69% | **50.65%** | 37.66% |

#### Clinical Interpretation
- **Typical & Asymptomatic (`cp = 0, 3`):** Dominated by **Flat ST slopes (`slope = 1`)** ($58.8\%$ and $50.7\%$), indicative of diffuse repolarization abnormality.
- **Atypical & Non-Anginal (`cp = 1, 2`):** Predominated by **Downsloping ST segments (`slope = 2`)** ($70.7\%$ and $56.0\%$), a classic electrocardiographic hallmark of acute exercise-induced transmural myocardial hypoperfusion.

#### Business & Operational Impact
- **Automated ECG Escalation Rule:** Any patient exhibiting a downsloping ST slope (`slope = 2`) accompanied by non-anginal or atypical pain should bypass general observation and receive urgent cardiology consultation.

---

### Question 5: Thalassemia Dynamics Across Patient Age by Diagnosis Status

#### Code Implementation & Plot Reference
```python
sns.lineplot(
    data=df, 
    x='age', 
    y='thal', 
    hue='target', 
    style='target', 
    markers=True, 
    dashes=False, 
    palette={0: '#4C72B0', 1: '#C44E52'},
    errorbar=None
)
plt.title('Thalassemia Category Trends Across Patient Age by Diagnosis')
plt.xlabel('Age (years)')
plt.ylabel('Thalassemia Type Code')
plt.grid(True, linestyle='--', alpha=0.5)
plt.show()
```
*Visual :* `Heart_Health_Images/Thalassemia Category Trends Across Patient Age by Diagnosis.png`

#### Findings & Clinical Interpretation
Tracking thalassemia nuclear scan categories across ages 30 to 75 illustrates that confirmed heart disease patients consistently average lower `thal` scores centered tightly around **2.0 (Fixed Defect / Infarcted Scar)** across all age brackets. Non-diseased patients average higher scores ($2.4 - 2.6$, reflecting reversible defect or normal scans). This stability across decades proves that fixed myocardial defects are disease-driven rather than simple age-related degenerative changes.

#### Business & Operational Impact
- **Long-Term Disease Monitoring Registry:** Patients identified with fixed defects (`thal = 2`) require lifetime secondary prevention registries, regular ejection fraction checks, and aggressive antiplatelet/statin regimens.

---

## Part 4: Executive Implementation Playbook

```
+---------------------------------------------------------------------------------------------------+
|                                  OPERATIONAL TRIAGE DECISION TREE                                 |
+---------------------------------------------------------------------------------------------------+
| 1. INTAKE (ER / Outpatient Clinic):                                                               |
|    Assess Chest Pain Type ('cp') & Resting BP ('trestbps')                                        |
|    - If 'trestbps' > 180 mm Hg --> Hypertensive Emergency Pathway                                 |
|                                                                                                   |
| 2. POINT-OF-CARE DIAGNOSTICS:                                                                     |
|    ECG & Stress Testing ('restecg', 'oldpeak', 'slope', 'thalach')                                |
|    - If 'restecg' == 2 (LVH) --> Urgent Echocardiography within 48 hours                          |
|    - If 'oldpeak' >= 1.0 mm OR 'slope' == 2 --> High-Risk Ischemia Protocol                       |
|                                                                                                   |
| 3. ADVANCED IMAGING & CATH-LAB ALLOCATION:                                                        |
|    Nuclear Thallium Perfusion ('thal') & Fluoroscopy ('ca')                                       |
|    - If 'thal' == 2 (Fixed Defect) + 'ca' >= 2 --> Immediate Cath-Lab Interventional Review       |
|                                                                                                   |
| 4. AI-ASSISTED TRIAGE SCORE:                                                                      |
|    Deploy Logistic Regression Model (AUC = 0.930, Recall = 91.4%)                                 |
|    - Low Risk (<0.20): Outpatient primary wellness & lifestyle monitoring                         |
|    - Moderate Risk (0.20 - 0.60): Stress echocardiogram & outpatient cardiology consult           |
|    - High Risk (>0.60): Urgent inpatient telemetry & invasive coronary angiogram                  |
+---------------------------------------------------------------------------------------------------+
```

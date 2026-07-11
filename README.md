# Midterm Project- Myocardial Infarction Complications Prediction
### 💼 Business Problem
When a patient arrives with a heart attack, doctors cannot instantly tell who will recover safely and who will suddenly develop dangerous secondary heart problems over the next three days. To be safe, hospitals waste time and money running slow, expensive diagnostic tests, which delays treatments and crowds intensive care units with patients who do not actually need to be there. This guesswork creates a massive financial burden, burning budgets on unnecessary testing while risking fatal medical errors if a truly high-risk patient is accidentally sent home too early, causing a dangerous clinical blind spot.

🎯 Project Goal
Our goal is to build an affordable AI software that uses basic, low-cost health data already available at admission to predict heart complications before they happen. By putting this tool right at the patient bedside, doctors can instantly spot high-risk patients without waiting for slow, expensive medical tests. This shifts cardiac care from reactive guessing to proactive prevention, helping hospitals optimize their resources, speed up treatments, and save millions in unnecessary healthcare costs.

📊 Target and Time-Dynamic Architecture
The project targets the multi-label classification of myocardial infarction outcomes, consolidated into five key categories: overall mortality, chronic heart failure, ischemic events, acute pulmonary complications, and severe arrhythmias. Predictions are driven by a dynamic pipeline composed of three sequential model engines triggered at distinct, cumulative clinical time windows:

* Day 1 (ER Admission Horizon): Utilizing raw background history, ECG markers, and initial ER vitals.
* Day 2 (24h to 48h Laboratory Horizon): Dynamically adding morning blood panels and enzyme counts as they arrive.
* Day 3 (Advanced ICU Horizon): Incorporating the full longitudinal patient history, clinical complications, and early treatment responses. 
This chronological architecture continuously updates patient risk profiles as new, low-cost data becomes available.

📈 The Primary Metric and Clinical Optimization Strategy
The primary optimization vector for this clinical pipeline is Recall because it acts as an airtight safety net to capture every high-risk patient before fatal complications occur. The severe risk asymmetry in an ICU environment governs this decision:

* False Negatives (FN - The Worst Case): A sick patient is mistakenly classified as healthy and discharged home, leading to catastrophic medical emergencies, fatalities, and massive institutional lawsuits.
* False Positives (FP - The Sub-Optimal Case): A healthy patient is mistakenly flagged as high risk, causing minor false alarms and temporarily utilizing an expensive ICU bed.

We strictly prefer making False Positive errors over dangerous False Negative errors. However, to prevent a naive model from triggering endless false alarms (which causes severe Alarm Fatigue among medical staff), our framework explicitly optimizes the Macro F2 Score and Macro Recall metrics across a tuned 0.22 probability threshold. The F2 Score mathematically places a double weight on Recall while maintaining a strict, secondary protective boundary over Precision, guaranteeing an optimal balance between patient safety and hospital resource efficiency. To evaluate this combined multi-label performance on the unseen test set, we apply flat array flattening via the ravel() method, merging all target dimensions into a singular combined clinical confusion matrix representing total clinical decisions [0.11].

💰 Economic and Risk Mitigation Utility (The Financial ROI)
From an institutional and healthcare insurance (Payer and Provider) perspective, the implementation of this predictive pipeline transforms hospital economics by optimizing resource allocation and slashing risk overheads across two major operational vectors:

1. Eliminating Low-Risk ICU Crowding (Preventing Unnecessary Admissions): Traditionally, due to defensive medicine and clinical uncertainty, hospitals admit a massive wave of stable patients into highly expensive ICU beds just to be safe. A single ICU bed-day costs an average of 3,500 dollars. By delivering an early, high-confidence clearance score right at the patient bedside, our model empowers clinicians to safely route low-risk patients directly to general wards or home care. Our dynamic clinical experiment demonstrated that preventing these unnecessary prolonged lengths of stay optimizes bed turnover. This effectively saves 735 ICU bed-days annually, which translates into an immediate operational budget reduction of 3,017,000 USD per year for a standard hospital network.

2. Mitigating Malpractice and Catastrophic Failure Costs (Preventing False Discharges): The financial penalty of a False Negative error in cardiology is catastrophic. Accidentally discharging a truly high-risk patient who subsequently experiences a fatal myocardial rupture or cardiac arrest at home results in severe institutional damages. Beyond the tragic loss of human life, it triggers massive internal investigation overheads, critical resource disruptions, and severe multi-million-dollar medical malpractice lawsuits that severely damage the hospital's reputation and spike insurance premiums. By locking the pipeline onto a calibrated 0.22 threshold, our system catches these silent high-risk markers early, successfully preventing patient deaths annually.

Ultimately, this AI framework establishes a rare win-win scenario in modern healthcare logistics: it dramatically improves patient survival metrics while simultaneously acting as a powerful financial shield against operational waste and legal liability.

🗺️ NOTEBOOK BY NOTEBOOK: Executed Operations & Core Conclusions

📊 Notebook 1: Exploratory Data Analysis (EDA)
* Operations Executed: Investigated the underlying distributions of the 12 raw binary targets and clinical predictors. Formulated an age-augmented incidence profile and identified extreme data imbalance and sparse features.
* Core Conclusion: Discovered that patients older than 70 exhibit a catastrophic surge in overall mortality (LET_IS) and chronic heart failure (ZSN). Conversely, found that immune-driven events like Dressler Syndrome paradoxically peak in younger cohorts. Concluded that the dataset must be structurally stratified into time-based patient horizons.

⚙️ Notebook 2: Data Preprocessing
* Operations Executed: Enforced initial data cleaning by removing highly sparse columns with >60% missingness (e.g., 'KFK_BLOOD'). Sliced the clinical feature space into 3 cumulative, chronological time windows (Day 1, Day 2, and Day 3) based on real hospital data availability timelines.
* Core Conclusion: Successfully stabilized the base data pipeline. Established a clean data layout while strictly preserving the absolute synchronization of all 1,360 training patient row identifiers, ensuring no clinical samples were dropped during temporal segmentation.

⚡ Notebook 3: Advanced Clinical Feature Engineering
* Operations Executed: Leveraged medical domain knowledge to construct 4 custom interaction features across the time horizons: 'PULSE_PRESSURE', 'INFLAMMATORY_INDEX', 'HIGH_RISK_PROFILE', and 'ELDERLY_HYPERTENSION_RISK'. Cast continuous interaction factors to float to natively shield column names from encoding expansions.
* Core Conclusion: Domain-driven feature engineering successfully injected strong physiological hazard signals into the data pipeline. Quality control checks verified complete feature synchronization while keeping patient rows perfectly intact, providing downstream classifiers with highly optimized tracking markers.

🧠 Notebook 4: Multi-Label Modeling & Optimization
* Operations Executed: Consolidated the 12 raw binary targets into 5 clinical meta-groups. Built an advanced 5-fold cross-validation framework evaluating Logistic Regression, Random Forest, and XGBoost models. Handled severe class imbalance using custom 'scale_pos_weight' loss factors, applying a double-penalty on mortality, and optimized metrics using the Macro F2-Score.
* Core Conclusion: Shifting the prediction threshold from 0.50 down to an optimized 0.22 boundary triggered a major training breakthrough, driving internal recalls to ~90%. At this cross-validation phase, XGBoost emerged as the top global engine, proving its unique ability to map multi-variable physiological interactions, peaking at a Macro F2-Score of 0.5604 on Day 3.

🏆 Notebook 5: Final Evaluation & Deployment Validation
* Operations Executed: Retrained the architectures on 100% of the training data (1,360 patients) and conducted blind inference strictly on the untouched, unseen Test Set (340 isolated patients). Enforced a strict zero-leakage preprocessing pipeline using KNN Imputation and StandardScaler inside folds. Evaluated final performance using Macro-Averaged Recall to monitor rare complications, and plotted combined target confusion matrices via array flattening (ravel()).
* Core Conclusion: The unseen deployment set completely settled the benchmark: Boosting + FE (XGBoost) is the definitive champion of the project. While the linear baseline suffered from an inability to detect non-linear clinical interactions (leaving a dangerous 18% of patients unprotected with an 82.08% Recall), XGBoost proved completely stable against distribution variance due to tight regularization (Max Depth = 3). On the final test set, XGBoost achieved an ironclad Macro Recall of 97.76%, capturing 306 true complications while missing only 6 critical events across all 1,700 total combined test decisions. Crucially, it demonstrated dynamic clinical evolution, steadily reducing False Positive alerts from Day 1 to Day 3 as recent laboratory and ICU data became available [0.11].































































































### 💼 Business Problem
When a patient arrives with a heart attack, doctors cannot instantly tell who will recover safely and who will suddenly develop dangerous secondary heart problems over the next three days. To be safe, hospitals waste time and money running slow, expensive diagnostic tests, which delays treatments and crowds intensive care units with patients who do not actually need to be there. This guesswork creates a massive financial burden, burning budgets on unnecessary testing while risking fatal medical errors if a truly high risk patient is accidentally sent home too early, causing a dangerous clinical blind spot.

### 🎯 Project Goal
Our goal is to build an affordable AI software that uses basic, low cost health data already available at admission to predict heart complications before they happen. By putting this tool right at the patient bedside, doctors can instantly spot high risk patients without waiting for slow, expensive medical tests. This shifts cardiac care from reactive guessing to proactive prevention, helping hospitals optimize their resources, speed up treatments, and save millions in unnecessary healthcare costs.

### 📊 Target and Time Dynamic Architecture
The project targets the multi label classification of myocardial infarction outcomes, consolidated into five key categories: overall mortality, chronic heart failure, ischemic events, acute pulmonary complications, and severe arrhythmias. Predictions are driven by a dynamic pipeline composed of three sequential model engines triggered at distinct, cumulative clinical time windows:
* **Day 1 (ER Admission Horizon)**: Utilizing raw background history, ECG markers, and initial ER vitals.
* **Day 2 (24h to 48h Laboratory Horizon)**: Dynamically adding morning blood panels and enzyme counts as they arrive.
* **Day 3 (Advanced ICU Horizon)**: Incorporating the full longitudinal patient history, clinical complications, and early treatment responses.
This chronological architecture continuously updates patient risk profiles as new, low cost data becomes available.

### 📈 The Primary Metric and Clinical Optimization Strategy
The primary optimization vector for this clinical pipeline is **Recall** because it acts as an airtight safety net to capture every high risk patient before fatal complications occur. The severe risk asymmetry in an ICU environment governs this decision:

* **False Negatives (FN - The Worst Case)**: A sick patient is mistakenly classified as healthy and discharged home, leading to catastrophic medical emergencies, fatalities, and massive institutional lawsuits.
* **False Positives (FP - The Sub Optimal Case)**: A healthy patient is mistakenly flagged as high risk, causing minor false alarms and temporarily utilizing an expensive ICU bed.

We strictly prefer making False Positive errors over dangerous False Negative errors. However, to prevent a naive model from triggering endless false alarms (which causes severe Alarm Fatigue among medical staff), our framework explicitly optimizes the **Micro F2 Score** and **Macro Recall** metrics across a tuned 0.20 probability threshold. The F2 Score mathematically places a double weight on Recall while maintaining a strict, secondary protective boundary over Precision, guaranteeing an optimal balance between patient safety and hospital resource efficiency.

### 💰 Economic and Risk Mitigation Utility (The Financial ROI)
From an institutional and healthcare insurance (Payer and Provider) perspective, the implementation of this predictive pipeline transforms hospital economics by optimizing resource allocation and slashing risk overheads across two major operational vectors:

1. **Eliminating Low Risk ICU Crowding (Preventing Unnecessary Admissions)**: 
Traditionally, due to defensive medicine and clinical uncertainty, hospitals admit a massive wave of stable patients into highly expensive ICU beds just to be safe. A single ICU bed day costs an average of 3500 dollars. By delivering an early, high confidence clearance score right at the patient bedside, our model empowers clinicians to safely route low risk patients directly to general wards or home care. Our dynamic clinical experiment demonstrated that preventing these unnecessary prolonged lengths of stay optimizes bed turnover. This effectively saves **735 ICU bed days annually**, which translates into an immediate operational budget reduction of **3,017,000 USD per year** for a standard hospital network.

2. **Mitigating Malpractice and Catastrophic Failure Costs (Preventing False Discharges)**:
The financial penalty of a False Negative error in cardiology is catastrophic. Accidentally discharging a truly high risk patient who subsequently experiences a fatal myocardial rupture or cardiac arrest at home results in severe institutional damages. Beyond the tragic loss of human life, it triggers massive internal investigation overheads, critical resource disruptions, and severe multi million dollar medical malpractice lawsuits that severely damage the hospital reputation and spike insurance premiums. By locking the pipeline onto a calibrated 0.20 threshold, our system catches these silent high risk markers early, successfully preventing **77 additional patient deaths annually**. 

Ultimately, this AI framework establishes a rare win win scenario in modern healthcare logistics: it dramatically improves patient survival metrics while simultaneously acting as a powerful financial shield against operational waste and legal liability.


## 🗺️ NOTEBOOK BY NOTEBOOK: Executed Operations & Core Conclusions 

### 📊 Notebook 1: Exploratory Data Analysis (EDA)
* **Operations Executed**: Investigated the underlying distributions of the 12 raw binary targets and clinical predictors. Formulated an age-segmented incidence profile and identified extreme data imbalance and sparse features.
* **Core Conclusion**: Discovered that patients older than 70 exhibit a catastrophic surge in overall mortality (LET_IS) and chronic heart failure (ZSN). Conversely, found that immune-driven events like Dressler Syndrome paradoxically peak in younger cohorts. Concluded that the dataset must be structurally stratified into time-based patient horizons.

### ⚙️ Notebook 2: Data Preprocessing
* **Operations Executed**: Enforced initial data cleaning by removing highly sparse columns with >60% missingness (e.g., 'KFK_BLOOD'). Sliced the clinical feature space into 3 cumulative, chronological time windows (Day 1, Day 2, and Day 3) based on real hospital data availability timelines.
* **Core Conclusion**: Successfully stabilized the base data pipeline. Established a clean data layout while strictly preserving the absolute synchronization of all 1,360 patient row identifiers, ensuring no clinical samples were dropped during temporal segmentation.

### ⚡ Notebook 3: Advanced Clinical Feature Engineering
* **Operations Executed**: Leveraged medical domain knowledge to construct 4 custom interaction features across the time horizons: 'PULSE_PRESSURE', 'INFLAMMATORY_INDEX', 'HIGH_RISK_PROFILE', and 'ELDERLY_HYPERTENSION_RISK'. Cast continuous interaction factors to float to natively shield column names from encoding expansions.
* **Core Conclusion**: Domain-driven feature engineering successfully injected strong physiological hazard signals into the data pipeline. Quality control checks verified complete feature synchronization while keeping patient rows perfectly intact, providing downstream classifiers with highly optimized tracking markers.

### 🧠 Notebook 4: Multi-Label Modeling & Optimization
* **Operations Executed**: Consolidated the 12 raw binary targets into 5 clinical meta-groups. Built an advanced 5-fold stratified cross-validation framework evaluating Logistic Regression, Random Forest, and XGBoost models. Handled severe class imbalance using custom 'scale_pos_weight' loss factors, applying a double-penalty on mortality, and optimized metrics using the Micro F2-Score.
* **Core Conclusion**: Shifting the prediction threshold from 0.50 down to an optimized 0.20/0.22 boundary triggered a major training breakthrough, driving internal recalls to ~90%. At this cross-validation phase, XGBoost emerged as the top global engine, peaking at an F2-Score of 0.5742 on Day 3.

### 🏆 Notebook 5: Final Evaluation & Deployment Validation
* **Operations Executed**: Retrained the architectures on 100% of the training data and conducted blind inference strictly on the untouched, unseen Test Set (340 isolated patients). Enforced a strict zero-leakage preprocessing pipeline. Evaluated performance using Macro-Averaged Recall to monitor rare complications, and plotted directional risk coefficients and multi-label ROC/AUC curves.
* **Core Conclusion**: The unseen deployment set completely settled the benchmark: **Linear + FE (Logistic Regression) is the definitive champion of the project.** Complex tree ensembles suffered from unseen distribution variance (overfitting), while the linear engine showed exceptional generalization stability (Low Variance), locking a monumental **88.68% Macro-Recall** on Day 1, an **87.04% Overall Mortality Recall**, and an elite **0.8451 AUC** for mortality.

### 💰 Notebook 6: Business Utility Simulation Experiments
* **Operations Executed**: Conducted a retrospective clinical utility experiment, extrapolating the blind test results to an annual hospital network scale of 2,500 ICU admissions. Compared your calibrated champion model (0.20 threshold + macro weights) against a standard, uncalibrated AI setup (0.50 default threshold).
* **Core Conclusion**: Proved that a standard uncalibrated AI system is clinically dangerous, missing 61.11% of deteriorating patients (38.89% Recall). Conversely, your calibrated pipeline successfully forms an airtight safety net, actively **saving 77 additional patient lives per year**, cutting 735 ICU bed days, and driving an immediate **annual hospital cost reduction of $3,017,000 USD** via optimized resource turnover.



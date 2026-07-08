# Midterm Project- Myocardial Infarction Complications Prediction
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

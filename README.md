# 🏥 Cardiovascular Patient Risk & Data Quality Ecosystem

## 💼 Clinical Problem Statement
The Chief Medical Officer (CMO) required a robust patient-risk monitoring dashboard to identify high-risk cardiovascular cases early across regional medical clinics. The core objective is to analyze 7,000 patient records across 22 distinct variables—correlating vital clinical diagnostics (Blood Pressure, Cholesterol, Max Heart Rate) against lifestyle, comorbidities, and demographic factors.

Crucially, patient intake data extracted from Electronic Health Records (EHR) systems was highly fragmented, containing mixed string formats and missing rows. This project focuses heavily on **Data Quality Assurance (DQA)**, clinical data cleaning, and mapping demographic risk profiles without losing structural data integrity.

---

## 🛠️ Data Infrastructure & Tech Stack
* **Database Engine:** SQL Server Management Studio (SSMS) 
* **BI Architecture:** Power BI Desktop
* **Source Systems:** Synthetically Generated Cardiovascular EHR System (7,000 Records, CSV)

---

## 🗂️ Stage 1: The Raw CSV Ingestion & Local Database Staging
Because clinical Electronic Health Record (EHR) data can contain hidden strings and anomalies, trying to force strict numeric data types during an active file import will cause standard server streams to crash. To protect the pipeline, the raw Kaggle CSV file was successfully staged into a localized SQL Server environment (`dbo.RawHeartAttackData`) using a highly strategic data engineering ingestion method.

#### 🔧 Infrastructure & Driver Wall Resolutions:
* **The Standalone Wizard Bypass (`DTSWizard.exe`):** Due to strict localized user access parameters unbundling background runtime engines, the standard SSMS context menu import items were completely greyed out. This restriction was bypassed by running a manual Windows command line execution layer to force open the core standalone utility: **`DTSWizard.exe`**.
* **Driver 19 Encryption Barrier:** The modern Microsoft OLE DB Driver 19 enforces strict mandatory security handshakes, resulting in a connection failure stating: *"The certificate chain was issued by an authority that is not trusted."* This was resolved by swapping the pipeline provider to a universal, high-compatibility connection driver: **`SQL Server Native Client 11.0`**. 
* **The Raw String Staging Rule:** To bypass immediate formatting rejections on numeric variables (caused by the source CSV appending trailing `.0` notation strings like `105.0`), every single incoming column property was explicitly mapped as a raw text string: **`string [DT_STR]`**. This forced SQL Server to accept all 7,000 rows flawlessly into a staging layer without a single format-rejection error.

---

## 🧪 Stage 2: Database Transformation & Integrity Controls
Once the raw rows safely landed as text strings, an advanced SQL script was written to encapsulate the data logic inside a live-updating virtual abstraction layer: **`dbo.v_CleanedHeartAttackData`**.

* **The Zero-Default Medical Risk Trap:** Empty text cells (`''`) natively default to mathematical `0` values during flotation casting in T-SQL. A resting blood pressure or cholesterol level of `0` is clinically impossible and destroys data model averages. This was resolved by applying explicit **`NULLIF(column, '')`** filters across every targeted metric to turn empty spaces into true database `NULL` markers.
* **Staircase Data-Casting:** Guided strict SQL conversion protocols down a multi-layered path, casting raw string elements to `FLOAT` first to clear literal decimal dots, and then converting them into whole number `INT` flags for Power BI model performance.

---

## 📐 Power Query Transformation & Clinical Hierarchy Logic
To prepare the dataset for the executive canvas, the true database `NULL` markers had to be transformed into human-readable corporate labels without triggering evaluation bugs.

* **The Conditional Operator Priority Check:** Power Query crashes with `[Error]` flags when running mathematical operations (e.g., `<` or `<=`) directly against true `NULL` rows. This was resolved by configuring a case-sensitive priority rule that catches **`null` fields first**, safely renaming them to **`Not Disclosed`** before executing any numeric vitals profiling.
* **Categorical Segmentation:** Streamlined continuous metrics into clinically distinct groupings:
  * *Age Group:* Young Adult (35 & Under) | Middle-Aged (36-55) | Senior (56+)
  * *Blood Pressure Risk:* Normal (<120) | Elevated (120-129) | High Risk (>=130) | Not Disclosed (Missing Intake rows)

---
## 🚀 The Live Dashboard Preview
<img width="1917" height="1022" alt="Dashboard_Screenshot" src="https://github.com/user-attachments/assets/31c17d91-b866-4169-a9ef-4cd9c3d93dc2" />

---
## 📊 Strategic Clinical Insights (Executive Review)
* **Comorbidity Interaction:** Patients categorized inside the *High Risk (Hypertension)* tier carry a significantly elevated average cholesterol level of **197.75 mg/dL**, showing a direct medical correlation with cardiovascular stress markers.
* **Demographic Vulnerability:** The *Senior (56+)* generation holds the highest overall concentration of high-risk cardiac events, followed closely by *Middle-Aged* patients.
* **Behavioral Distribution:** High-risk volume is driven by an interaction of factors—proven by the fact that the *Never Smoked* patient group accounts for **1,182** high-risk cases, signifying that non-smoking attributes (genetics, diabetes, high stress) are massive network factors.

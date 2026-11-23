# 📊 Financial Aid & Compliance Audit Automation Engine (2025 & 2026)

<img width="1492" height="920" alt="Dashboard_Preview" src="https://github.com/user-attachments/assets/a9ea7779-15d3-4adf-8506-f54c4c401641" />

## 🎥 Interactive Dashboard Demo

https://github.com/user-attachments/assets/6e52a11c-2e3b-4c17-b7e6-fa88c61a1e3e


## 🎯 Executive Summary
Financial aid offices must make sure every disbursement follows compliance rules. Checking this by hand across separate systems is slow and lets errors slip through. This project automates the audit for Academic Years 2025 & 2026 and shows in one dashboard how much aid was paid out, how much of it broke a rule, and where the risk is concentrated.

To solve this, I engineered an **end-to-end automated reconciliation and compliance audit system** using **Excel Power Query, Dynamic Lookup & Aggregation Formulas, and an Interactive Executive Dashboard**. Across **5,118 disbursement transactions** spanning 2025 and 2026 (**$13.86M total volume**), the engine automatically cleaned raw multi-campus exports and flagged **$7.90M (57.0%) in non-compliant disbursements** across **2,778 transactions**, reducing monthly multi-campus audit preparation to a single click (`Data > Refresh All`).


## 🏗️ Data Architecture & Automated ETL Pipeline (Power Query)
The workbook ingests five separate raw CSV exports (`Toronto.csv`, `Markham.csv`, `Ajax.csv`, `Disbursement_Ledger.csv`, and `Compliance_Logs.csv`) through a repeatable **Get & Transform (Power Query)** pipeline:

* **Multi-File Folder Ingestion:** Stacks regional campus rosters into a unified `Master_Roster` connection and extracts campus identifiers dynamically from source filenames.
* **Data Standardization & Hygiene:** Trims trailing whitespace, standardizes mixed-case `Student_ID` strings to uppercase, and parses composite `Program_Code` strings (`BUS-101-FT`) into `Department`, `Course_Code`, and `Study_Load`.
* **Locale-Aware Date Parsing:** Resolves mixed `DD-MM-YYYY` and `YYYY-MM-DD` date formats across regional exports without date-shift errors.
* **Referential Integrity & Exception Handling:** Executes Left Outer Joins across the Disbursement Ledger, Master Roster, and Compliance Logs—explicitly mapping unmatched IDs as `Unregistered` rather than dropping orphaned transactions.


## ⚙️ Automated Compliance Rule Engine (`Audit_Engine`)
Using structured Excel Tables (`tbl_Audit`) and dynamic `SUMIFS` / `IFS` logic, every transaction is automatically tested against five hierarchical compliance rules:

1. **`FAIL: Unregistered Student ID`** — Flags disbursements issued to student IDs missing from official campus enrollment rosters.
2. **`FAIL: Disbursed to Withdrawn Student`** — Identifies funds released to students whose enrollment status is `Withdrawn`.
3. **`FAIL: Premature Disbursement`** — Catches payouts dated before the student's official financial aid `Approval_Date`.
4. **`FAIL: Exceeds Approved Funding Cap`** — Uses cumulative `SUMIFS` across multi-term payouts (excluding reversed transactions) to flag students exceeding their approved funding ceiling.
5. **`FAIL: Missing Compliance Docs`** — Flags disbursements where Identity Verification (`ID_Verified`), Income Verification (`Income_Verified`), or Satisfactory Academic Progress (`SAP_Met`) is marked `N`.


## 🔍 Key Audit Findings (2025 & 2026)

### 1. Portfolio & Risk Overview
| Metric | Result | Business Context |
| :--- | :--- | :--- |
| **Total Disbursed Volume** | **$13,864,300** | **5,118** total transactions across **2,838** unique student IDs (2025–2026). |
| **Registered Campus Volume** | **$13,508,700** | **4,987** transactions across Toronto, Markham, and Ajax (**+2.9% YoY** growth in 2026). |
| **Total Dollars at Risk** | **$7,899,700** | **57.0%** of total disbursed funds triggered at least one compliance breach (**2,778** transactions). |
| **Clean Audit Pass Volume** | **$3,335,300** | **1,370** transactions passed all five compliance checks (**34.1%** pass rate across active registered disbursements; **970** transactions were reversed). |

### 2. Breakdown by Compliance Breach Type
| Audit Flag | Transactions | Dollars at Risk | Share of Total Risk |
| :--- | :---: | :---: | :---: |
| **Exceeds Approved Funding Cap** | 1,321 | **$4,024,800** | 50.9% |
| **Disbursed to Withdrawn Student** | 618 | **$1,677,700** | 21.2% |
| **Premature Disbursement** | 387 | **$1,033,100** | 13.1% |
| **Missing Compliance Docs** | 321 | **$808,500** | 10.2% |
| **Unregistered Student ID** | 131 | **$355,600** | 4.5% |

### 3. Regional, Funding & Departmental Highlights
* **Campus Volume & Growth:** **Toronto** leads all campuses with **$5.39M** disbursed (**+8.2% YoY**, rising from $2.59M in 2025 to $2.80M in 2026) and **$3.00M** at risk, followed by **Markham** ($4.37M disbursed, +1.3% YoY) and **Ajax** ($3.75M disbursed, -2.3% YoY).
* **Funding Sources:** Disbursements are balanced across **Microloan** ($4.82M / 34.8%), **Institutional Grant** ($4.70M / 33.9%), and **OSAP** ($4.35M / 31.4%).
* **Academic Departments:** **Business (`BUS`)** accounts for the highest disbursement volume (**$4.46M**, 1,655 transactions) and risk exposure (**$2.52M**), roughly double the individual volumes of `HLT` ($2.40M), `ACC` ($2.35M), `IT` ($2.15M), and `ENG` ($2.15M).


## 💡 Actionable Recommendations
1. **Enforce Hard Pre-Disbursement Cap Controls:** Implement an automated cumulative cap check inside the payment batching workflow to prevent **$4.02M** (50.9% of all risk) in over-cap payouts.
2. **Automate Registrar-to-Finance Status Holds:** Trigger an immediate disbursement freeze whenever a student's registrar status updates to `Withdrawn`, mitigating **$1.68M** in post-withdrawal leakage.
3. **Block Unregistered IDs at Ingestion:** Require master roster ID validation before payment release to eliminate **$355.6K** in payouts to unregistered IDs.
4. **Prioritize Toronto Campus & Business Department Audits:** Focus initial compliance training and pre-audit sampling on Toronto (+8.2% YoY volume growth) and the Business (`BUS`) program.


## 📂 Workbook Structure
* **`Audit_Engine`:** Power Query-fed master reconciliation table (`5,118` rows) with dynamic `SUMIFS` and `IFS` audit flags.
* **`Pivot_Workings`:** Backend PivotTables powering campus YoY comparisons, risk rankings, funding mix, and monthly trends.
* **`Executive_Dashboard`:** Single-screen interactive report featuring KPI cards, 4 executive visuals, dynamic insight helpers, and connected slicers (`Academic Year`, `Campus`, `Funding Source`, and `Department`).



## 🛠️ Tools & Skills Demonstrated
* **ETL & Data Cleaning:** Excel Power Query (Folder Ingestion, Locale Date Parsing, Left Outer Joins, String Manipulation)
* **Financial Auditing & Modeling:** `SUMIFS`, `IFS`, Variance Analysis, Exception Flagging, YoY Growth Tracking
* **BI & Data Visualization:** PivotTables, PivotCharts, Multi-Connected Slicers, Executive KPI Cards, Storytelling Titles
* **Data Generation:** Python (`pandas`, `numpy`)

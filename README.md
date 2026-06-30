# 📊 2026 S-Corp Payroll Calculator, Ledger and Verifier (Federal + NM State)

## Human Note:
The code in this Python (Jupyter Notebook) was mostly AI-generated through many revisions, corrections, and feature development conversations (by / from / with a human). Via Gemini via web browser interface. Even this readme is mostly AI Gen from Cell K, except comments here up top ("Human Note") and possibly tweaks elsewhere. 

FWIW, this code is actively being used to run payroll "tests" by at least one party, **but**, see disclaimer next of course, and note that **this software is for no particular purpose at all**.

State taxes are populated with brackets, constants, and rules for New Mexico.

Values are for 2026.

Seed values are included (Cell H) for an example employee (EE) with inconsistent payroll runs and benefits to be able to test various scenarios. Primarily tested with an example EE who is (or can be) classified as an owner for health insurance and wages treatment.

To customize and attempt to playfully apply this code, you would have to carefully step through and update state tax settings and various rules.

The examples here also include basic choices for taxpayer statuses of Single and MFJ.

This project targets needs (Outputs):
- Interactive GUI with additional withholdings, some status selections, etc to tune net income
- Storage (ledger) of committed PPDs into an sqlite database, local
- Text outputs including:
    - Pay period (PPD) withholding calculations including check or transfer breakouts depending on what the employer (ER) pays directly or reimburses and whether those are combined or separated payments to the EE.
    - Form 940 FUTA (unemployment)
    - Form 941 Monthly Breakouts, Quarterly Reporting
    - State SUI and associated unemployment and admin assessment fees
    - State quarterly reporting
- PDF YTD type of summary output

Run using .venv for example.

**Disclaimer:** If anyone plays with this, basically assume it is all wrong generally, and then also wrong specifically for you. NO WARRANTY. NO GUARANTEES. Nothing. The only thing implied about this code is that it is all wrong.

Be in touch if you are interested to chat about this project.


## AI-Gen Readme:

A self-contained, lightweight Python accounting platform built directly within a Jupyter Notebook ecosystem. This tool automates corporate payroll calculations, splits complex disbursement checks, and handles tax filing tracking for an S-Corporation shareholder-employee based in **New Mexico (2026 Tax Rules)**.

---

## 🛠️ System Architecture & Notebook Cell Outline

The application is structured into modular, independent cells accessible via the VS Code **Outline View** to prevent memory caching conflicts and maximize performance:

* **Cell A [GUI Control Panel]:** Provides an interactive dashboard for filing status selection, annual salary tracking, progressive deduction toggle limits, flat tax withholding adjustments, and calendar date management.
* **Cell B [Master Math & Visualization Engine]:** The core calculator. Performs live progressive tax matrix matching and renders a clean visual distribution breakdown.
* **Cell C [Database Persistence Engine]:** Snapshot asset container. Logs rows securely to SQLite and copies files to a flat CSV backup ledger on a manual save trigger.
* **Cell D [Year-End W-2 Forecast Engine]:** Scans real historical entries and models remaining period variables to map out year-end W-2 box obligations with smart null-fallbacks.
* **Cell E [Database Purge Utility]:** Secure data administration tool to clear out testing rows or reset database states cleanly before production runs.
* **Cell F [Quarterly Form 941 & NM State RPD-41285 Ledger]:** Pools transactions by quarter to calculate Federal lines and New Mexico monthly Taxpayer Access Point (TAP) portal deposit obligations.
* **Cell G [Annual Form 940 & NM SUTA Module]:** Automates Federal Unemployment (FUTA) tracking and maps New Mexico Department of Workforce Solutions (NMDWS) ES-903A quarterly obligations.
* **Cell H [Historical QuickBooks Data Seeding Utility]:** Chronological data injection module to seed the backend database with dynamic historical checks for perfect YTD alignment.
* **Cell I [General Ledger Journal Entry Engine]:** Automatically structures the latest paycheck run into a flawless double-entry bookkeeping debit and credit accounting grid.
* **Cell J [Automated PDF Compliance Report Generator]:** Compiles your SQLite database entries into a presentation-quality monthly summary PDF report.

---

## ⚖️ Embedded S-Corp Compliance Tax Rules

This platform strictly implements several critical IRS and state tax rules:
1. **FICA/FUTA/SUTA Exemption on Benefits:** S-Corp owner Health Insurance Premiums (HIP) and Dental Insurance Premiums (DIP) are added to gross wages for federal and state income tax calculations, but are completely exempt from Social Security, Medicare, FUTA, and SUTA taxes.
2. **Dual-Check Bookkeeping Breaks:** Supports separating take-home compensation into a regular labor wages paycheck (**Check #1**) and an independent out-of-pocket health and dental insurance expense reimbursement transfer (**Check #2**).
3. **401(k) Contribution Splits:** Correctly models pre-tax retirement deferrals as a reduction to income tax bases, while leaving them added back to your underlying FICA/FUTA wage tracking pools.
4. **New Mexico Executive Exclusion:** Implements the New Mexico Workers' Compensation fee exemption rules for small businesses with fewer than 3 employees and greater than 10% owners, with a live toggle to clear the quarterly assessment fees down to a clean $0.00.

---

## 💾 Relational SQLite Database Schema Layout

Data entries are preserved permanently inside an 18-column relational `payroll_ledger (payroll_ledger.db)` table:

| Column Name | Data Type | Operational Bookkeeping Target Description |
| :--- | :--- | :--- |
| `id` | INTEGER | Auto-incrementing primary key marker. |
| `employee_id` | TEXT | Unique employee alphanumeric identifier (`EE-SCORP-01`). |
| `pay_period_ending` | TEXT | ISO calendar pay cycle date string (`YYYY-MM-DD`). |
| `gross_wages` | REAL | Total blended gross earnings pool (Salary + HIP + DIP). |
| `hip_wages` | REAL | Isolated monthly S-Corp Medical Insurance Premium. |
| `dip_wages` | REAL | Isolated monthly S-Corp Dental Insurance Premium. |
| `pre_tax_401k` | REAL | Traditional retirement salary deferral cash deduction. |
| `ee_ss` / `er_ss` | REAL | Employee / Matching Employer Social Security Tax (6.2% on base). |
| `ee_medicare` / `er_medicare` | REAL | Employee / Matching Employer Medicare Tax (1.45% on base). |
| `ee_fed_tax` | REAL | Progressive Federal Income Tax withheld. |
| `ee_nm_tax` | REAL | Progressive New Mexico State Income Tax withheld. |
| `ee_wc_fee` / `er_wc_fee` | REAL | Employee ($2.25) / Employer ($2.55) NM WC quarterly assessment fee fraction. |
| `net_pay` | REAL | Blended residual cash package remaining after deductions. |
| `er_futa` | REAL | Employer Federal Unemployment Tax (0.6% on capped $7k base). |
| `er_nm_sui` | REAL | Employer NM State Unemployment Tax (0.33% on capped $34.8k base). |
| `total_company_cost`| REAL | Complete corporate out-of-pocket transaction capital cash outlay. |
| `timestamp` | DATETIME | Automatic ledger insert transaction event log tracking marker. |

---

## 🚀 Execution Instructions & Workflow

1. Execute **Cell B** first to initialize global tax constants and load the relational database schema architecture into active notebook memory.
2. Clear any corrupt test data rows using **Cell E**, then use **Cell H** with `EXECUTE_SEEDING = True` to populate your ledger history with actual QuickBooks Online pay stubs.
3. Run **Cell A & B** together to access your slider interface, review live pie charts, and use the green **"Commit Pay Run"** button to log real corporate pay milestones going forward.
4. Run reporting **Cells D, F, G, I, and J** at any time to generate immediate tax compliance forecasts, quarterly filings, journal entries, or structured PDF ledger sheets.

*Disclaimer: This calculator platform serves as an internal tracking and financial modeling ledger tool. All outputs should be reconciled alongside certified payroll statements prior to final corporate tax return signatures.*
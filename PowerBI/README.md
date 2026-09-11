# Power BI Dashboard Delivery — Financial Services Directory (FSD) Part-2

## Overview
This folder contains the complete, production-ready Power BI assets for **Part-2 of the Internship Assignment**. The dashboard is built exclusively from the authoritative Part-1 dataset: `Submission File (Naga).xlsx` (Sheet: `FSD directory`, 40,430 rows).

## Deliverable Files & Formats

| File / Folder | Type | Description |
| :--- | :--- | :--- |
| `FSD_Advisor_Dashboard.pbix` | Power BI Desktop Report | Primary report file ready to open in Power BI Desktop with full visual layouts, 7 slicers, and measures. |
| `FSD_Advisor_Dashboard.pbit` | Power BI Template | Clean, unpopulated template file containing TMSL schema, Power Query M code, and visual layouts without cached data rows. Prompts for parameter on open. |
| `FSD_Advisor_Dashboard.pbip` | Power BI Developer Project | Modern modular project folder format for source control integration with TMDL/BIM semantic model and report JSON. |
| `FSD_Advisor_Dashboard.SemanticModel/` | Semantic Model Folder | Contains `model.bim` (TMSL JSON schema with `SourceFilePath` parameter) and `definition.pbism`. |
| `FSD_Advisor_Dashboard.Report/` | Report Folder | Contains `report.json` (all 4 pages and visual container configs) and `definition.pbir`. |

---

## Data Source Portability Strategy

To ensure seamless execution across different machines without manual M code editing, the project implements a **Power Query Parameter**:
- **Parameter Name:** `SourceFilePath`
- **Default Value:** `c:\Users\pvnag\Desktop\FSD-Part-2-Dashboard\Submission File (Naga).xlsx`
- **Repointing in 2 Clicks:**
  1. Open the `.pbix` or `.pbit` file.
  2. Click **Home > Transform Data > Edit Parameters**.
  3. Browse/paste the path to `Submission File (Naga).xlsx` on your machine and click **Apply Changes**.

---

## 4-Page Dashboard Structure
1. **Page 1 — Executive Overview:** Contains **all 7 required slicers** (Country, State, City, ZIP Code, Website Available, LinkedIn Available, Facebook Available), top 8 KPI cards, state volume distribution, digital channel adoption, and directory overview.
2. **Page 2 — Geographic Analysis:** Detailed state density ranking, top 10 metropolitan hubs, territory filters, and area coverage KPIs.
3. **Page 3 — Digital Presence:** Channel readiness comparison (Website, LinkedIn, Facebook) and multi-channel outreach prioritization table (319 full-stack digital firms).
4. **Page 4 — Advisor Directory:** Full-width interactive prospecting workbench with sorting, location/channel slicers, and contact details. Missing Part-1 attributes (`CRD Number`, `Email Address`, `Minimum Investable Assets`) are transparently labeled *"Not available in Part-1 dataset"*.

---

## Core DAX Measures (`_Measures` Table)
- `[Total Advisor Firms] = DISTINCTCOUNT('FSD Directory'[firm])` (36,207 distinct firms)
- `[Filtered Advisor Count] = COUNTROWS('FSD Directory')` (40,430 total records)
- `[Unique States] = CALCULATE(DISTINCTCOUNT('FSD Directory'[state]), 'FSD Directory'[state] <> BLANK(), 'FSD Directory'[state] <> "")` (54 distinct entries)
- `[Unique Cities] = CALCULATE(DISTINCTCOUNT('FSD Directory'[city]), 'FSD Directory'[city] <> BLANK(), 'FSD Directory'[city] <> "")` (4,093 distinct cities)
- `[Unique ZIP Codes] = CALCULATE(DISTINCTCOUNT('FSD Directory'[zip]), 'FSD Directory'[zip] <> BLANK(), 'FSD Directory'[zip] <> "")` (6,905 distinct ZIPs)
- `[Firms with Website] = CALCULATE(DISTINCTCOUNT('FSD Directory'[firm]), 'FSD Directory'[Website Available] = "Yes")` (21,260 distinct firms)
- `[Firms with LinkedIn] = CALCULATE(DISTINCTCOUNT('FSD Directory'[firm]), 'FSD Directory'[LinkedIn Available] = "Yes")` (515 distinct firms)
- `[Firms with Facebook] = CALCULATE(DISTINCTCOUNT('FSD Directory'[firm]), 'FSD Directory'[Facebook Available] = "Yes")` (846 distinct firms)
- `[Firms with All Channels] = CALCULATE(DISTINCTCOUNT('FSD Directory'[firm]), 'FSD Directory'[Website Available] = "Yes", 'FSD Directory'[LinkedIn Available] = "Yes", 'FSD Directory'[Facebook Available] = "Yes")` (319 distinct firms)
- `[Website Coverage %] = DIVIDE([Firms with Website], [Total Advisor Firms], 0)` (58.7% firm-level)
- `[LinkedIn Coverage %] = DIVIDE([Firms with LinkedIn], [Total Advisor Firms], 0)` (1.4% firm-level)
- `[Facebook Coverage %] = DIVIDE([Firms with Facebook], [Total Advisor Firms], 0)` (2.3% firm-level)

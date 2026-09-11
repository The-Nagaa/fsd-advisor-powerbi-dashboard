# Part-2 Dashboard Project Documentation: Financial Services Directory (FSD)

**Project Name:** FSD Advisor Prospecting & Outreach Intelligence Dashboard  
**Assignment Stage:** Part-2 (Power BI Dashboard Delivery)  
**Author:** Senior BI Developer, Data Analyst & UX Designer  
**Date:** September 2026  
**Authoritative Source:** `Submission File (Naga).xlsx` (Worksheet: `FSD directory`)  

---

## 1. Executive Objective

The objective of Part-2 is to transform the completed Part-1 advisor web scraping dataset into a high-performance, interactive, multi-page Power BI dashboard tailored for **Sales**, **Marketing**, **Research**, **Advisor Prospecting**, and **Targeted Outreach Activities**.

The dashboard enables commercial teams to:
1. Instantly assess market concentration across 54 U.S. states and territories, 4,093 cities, and 6,905 ZIP codes.
2. Segment advisor firms by digital readiness across corporate websites, LinkedIn profiles, and Facebook company pages.
3. Identify high-priority outreach prospects with multi-channel contact infrastructure.
4. Execute granular filtering across location, digital channels, and firm names with instantaneous, dynamic visual updates.

---

## 2. Authoritative Data Source

The **sole authoritative data source** for this project is:
- **File Name:** `Submission File (Naga).xlsx`
- **Worksheet:** `FSD directory`
- **Location:** Project root directory (`c:\Users\pvnag\Desktop\FSD-Part-2-Dashboard\Submission File (Naga).xlsx`)

### Strict Data Integrity Guarantees:
- **No Source File Modification:** The source Excel workbook was read in read-only mode and has not been modified, overwritten, or reformatted.
- **No Part-1 Modifications:** No Part-1 scrapers, databases, or original datasets were altered or regenerated.
- **Sole Source Architecture:** No external enrichment, Google search, LinkedIn API scraping, Facebook querying, or synthetic data generation was conducted.
- **Transparent Missing Values:** Missing attributes requested by business users (`CRD Number`, `Email Address`, `Minimum Investable Assets`) are explicitly displayed as `"Not available in Part-1 dataset"` in the dashboard interface and are never fabricated.

---

## 3. Dataset Inspection & Sizing

A thorough pre-modeling audit of `Submission File (Naga).xlsx` revealed the following exact metrics:

| Metric | Source Value | Audit Notes |
| :--- | :--- | :--- |
| **Total Rows / Records** | **40,430** | Exactly 40,430 advisor profile records. |
| **Total Columns** | **14** | `profile_url`, `firm`, `position`, `street_address`, `city`, `state`, `country`, `zip`, `phone`, `website`, `facebook`, `linkedin`, `pdf_profile_link`, `scraped_by` |
| **Unique `profile_url`** | **40,430** | 100% unique primary key; 0 duplicates. |
| **Unique `firm` Names** | **36,207** | Distinct legal firm entities (36,131 case-insensitive/trimmed). |
| **`country` Coverage** | **40,430 (100%)** | All 40,430 records represent "United States". |
| **`state` Coverage** | **40,373 (99.86%)** | 57 null/blank rows; 54 distinct non-empty values (50 States + DC + PR + VI + 1 outlier). |
| **`city` Coverage** | **40,370 (99.85%)** | 60 null/blank rows; 4,093 distinct non-empty cities. |
| **`zip` Coverage** | **32,139 (79.49%)** | 8,291 null/blank rows; 6,905 distinct ZIP codes. |
| **`street_address` Coverage** | **34,813 (86.11%)** | 5,617 null/blank rows. |
| **`phone` Coverage** | **27,944 (69.12%)** | 12,486 null/blank rows. |
| **`website` Coverage** | **24,627 (60.91%)** | 15,803 null/blank rows. |
| **`facebook` Coverage** | **900 (2.23%)** | 39,530 null/blank rows. |
| **`linkedin` Coverage** | **556 (1.38%)** | 39,874 null/blank rows. |
| **`pdf_profile_link` Coverage** | **114 (0.28%)** | 40,316 null/blank rows. |
| **`scraped_by`** | **40,430 (100%)** | Uniformly populated with "Naga". |

### Key Digital Presence Overlap Insights:
- **100% Website Inclusion for Social Profiles:** Every firm that possesses a LinkedIn profile (`556` records) also possesses a corporate website (`556` intersection). Similarly, every firm with Facebook (`900` records) also has a website (`900` intersection).
- **All-Channel Tier:** Exactly **352** advisor records (representing **319** distinct firms) have all three digital channels: Website, LinkedIn, and Facebook.
- **Single/No Channel:** 23,523 records have Website only (no LinkedIn, no Facebook); 15,803 records have no digital footprint recorded.

---

## 4. Power Query ETL & Portable Data Source Parameter

To ensure zero hardcoded path failures across different environments, a Power Query parameter `SourceFilePath` is implemented:

```powerquery
// Parameter Query: SourceFilePath
"c:\Users\pvnag\Desktop\FSD-Part-2-Dashboard\Submission File (Naga).xlsx" meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

// Main Table Query: FSD Directory
let
    Source = Excel.Workbook(File.Contents(SourceFilePath), null, true),
    #"FSD directory_Sheet" = Source{[Item="FSD directory",Kind="Sheet"]}[Data],
    #"Promoted Headers" = Table.PromoteHeaders(#"FSD directory_Sheet", [PromoteAllScalars=true]),
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{
        {"profile_url", type text},
        {"firm", type text},
        {"position", type text},
        {"street_address", type text},
        {"city", type text},
        {"state", type text},
        {"country", type text},
        {"zip", type text},
        {"phone", type text},
        {"website", type text},
        {"facebook", type text},
        {"linkedin", type text},
        {"pdf_profile_link", type text},
        {"scraped_by", type text}
    }),
    #"Added Website Available" = Table.AddColumn(#"Changed Type", "Website Available", 
        each if [website] <> null and Text.Trim(Text.From([website])) <> "" then "Yes" else "No", type text),
    #"Added LinkedIn Available" = Table.AddColumn(#"Added Website Available", "LinkedIn Available", 
        each if [linkedin] <> null and Text.Trim(Text.From([linkedin])) <> "" then "Yes" else "No", type text),
    #"Added Facebook Available" = Table.AddColumn(#"Added LinkedIn Available", "Facebook Available", 
        each if [facebook] <> null and Text.Trim(Text.From([facebook])) <> "" then "Yes" else "No", type text),
    #"Added Clean State" = Table.AddColumn(#"Added Facebook Available", "Clean State", 
        each if [state] = null or Text.Trim(Text.From([state])) = "" then "(Blank / Unknown)" else Text.Trim(Text.From([state])), type text),
    #"Added Clean City" = Table.AddColumn(#"Added Clean State", "Clean City", 
        each if [city] = null or Text.Trim(Text.From([city])) = "" then "(Blank / Unknown)" else Text.Trim(Text.From([city])), type text),
    #"Added Clean ZIP" = Table.AddColumn(#"Added Clean City", "Clean ZIP", 
        each if [zip] = null or Text.Trim(Text.From([zip])) = "" then "(Blank)" else Text.Trim(Text.From([zip])), type text),
    #"Added Display Address" = Table.AddColumn(#"Added Clean ZIP", "Display Address", 
        each Text.Combine(List.Select({[street_address], [city], [state], [zip]}, each _ <> null and Text.Trim(Text.From(_)) <> ""), ", "), type text),
    #"Added CRD Number" = Table.AddColumn(#"Added Display Address", "CRD Number", 
        each "Not available in Part-1 dataset", type text),
    #"Added Email Address" = Table.AddColumn(#"Added CRD Number", "Email Address", 
        each "Not available in Part-1 dataset", type text),
    #"Added Min Assets" = Table.AddColumn(#"Added Email Address", "Minimum Investable Assets", 
        each "Not available in Part-1 dataset", type text)
in
    #"Added Min Assets"
```

---

## 5. Data Model Architecture

The data model follows an optimized star/flat architecture:
- **Fact / Entity Table:** `'FSD Directory'` (40,430 rows, 24 fields).
- **Measures Table:** `'_Measures'` (dedicated calculation container isolating all DAX measures).
- **Parameter:** `SourceFilePath` (M expression parameter query).
- **Data Categories:** Assigned (`City` -> City, `StateOrProvince` -> State, `PostalCode` -> ZIP, `WebURL` -> URLs).

---

## 6. DAX Measures Specification

Per user instructions, KPI measures targeting firm-level entities use `DISTINCTCOUNT('FSD Directory'[firm])`:

```dax
[Total Advisor Firms] = 
DISTINCTCOUNT('FSD Directory'[firm])

[Filtered Advisor Count] = 
COUNTROWS('FSD Directory')

[Total Advisor Profiles] = 
COUNTROWS('FSD Directory')

[Unique States] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[state]),
    'FSD Directory'[state] <> BLANK(),
    'FSD Directory'[state] <> ""
)

[Unique Cities] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[city]),
    'FSD Directory'[city] <> BLANK(),
    'FSD Directory'[city] <> ""
)

[Unique ZIP Codes] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[zip]),
    'FSD Directory'[zip] <> BLANK(),
    'FSD Directory'[zip] <> ""
)

[Firms with Website] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[firm]),
    'FSD Directory'[Website Available] = "Yes"
)

[Firms with LinkedIn] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[firm]),
    'FSD Directory'[LinkedIn Available] = "Yes"
)

[Firms with Facebook] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[firm]),
    'FSD Directory'[Facebook Available] = "Yes"
)

[Website Coverage %] = 
DIVIDE([Firms with Website], [Total Advisor Firms], 0)

[LinkedIn Coverage %] = 
DIVIDE([Firms with LinkedIn], [Total Advisor Firms], 0)

[Facebook Coverage %] = 
DIVIDE([Firms with Facebook], [Total Advisor Firms], 0)

[Firms with All Channels] = 
CALCULATE(
    DISTINCTCOUNT('FSD Directory'[firm]),
    'FSD Directory'[Website Available] = "Yes",
    'FSD Directory'[LinkedIn Available] = "Yes",
    'FSD Directory'[Facebook Available] = "Yes"
)
```

---

## 7. Multi-Page Dashboard UX Structure

### Page 1 — Executive Overview
- **Header:** Dashboard Title, Strategic Subtitle, Data Scope (`40,430 Records | 36,207 Firms`).
- **All 7 Required Slicers on Page 1:**
  1. `Country` (Dropdown)
  2. `State` (Dropdown)
  3. `City` (Dropdown)
  4. `ZIP Code` (Dropdown)
  5. `Website Available` (Yes / No list)
  6. `LinkedIn Available` (Yes / No list)
  7. `Facebook Available` (Yes / No list)
- **KPI Ribbon (8 Cards):** `Filtered Advisors`, `Total Advisor Firms`, `Unique States`, `Unique Cities`, `Unique ZIPs`, `Firms w/ Website`, `Firms w/ LinkedIn`, `Firms w/ Facebook`.
- **Visuals:** Top 10 States Bar Chart, Digital Channel Adoption, and Preview Table.

### Page 2 — Geographic Analysis
- **Territory Slicers:** Country, State, City, ZIP Code.
- **KPI Cards:** Advisors in Selected Area, Covered States, Covered Cities, Covered ZIPs.
- **Visuals:** State Density Hierarchy, Top 10 Metropolitan Hubs (New York 818, Atlanta 782, Chicago 554, Los Angeles 313, Houston 291, San Francisco 278).

### Page 3 — Digital Presence
- **Channel Slicers:** Website Available, LinkedIn Available, Facebook Available, State.
- **KPI Cards:** Firms with Website, Firms with LinkedIn, Firms with Facebook, Firms w/ All 3 Channels.
- **Visuals:** Social Presence by Top States (LinkedIn vs Facebook counts), Premier Multi-Channel Outreach Target Sample Table (319 firms / 352 profiles).

### Page 4 — Advisor Directory
- **Header:** Advisor Prospecting & Outreach Master Directory.
- **Quick Filters:** State, City, Website, LinkedIn, Facebook.
- **Operational Data Table:** Firm Name, Position, CRD Number, Street Address, City, State, ZIP Code, Phone Number, Email Address, Website URL, LinkedIn URL, Facebook URL, Minimum Investable Assets. Missing Part-1 attributes are transparently displayed as `"Not available in Part-1 dataset"`.

---

## 8. Final Comprehensive Validation Results (Tests A – K)

The entire reporting system was verified against ground truth computed directly from the source workbook across 11 distinct testing scenarios:

| Test | Scenario | Filtered Advisors | Distinct Firms | States | Cities | ZIPs | Firms w/ Web | Firms w/ LI | Firms w/ FB | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **A** | Baseline (Unfiltered) | 40,430 | 36,207 | 54 | 4,093 | 6,905 | 21,260 | 515 | 846 | **PASS** |
| **B** | Country = United States | 40,430 | 36,207 | 54 | 4,093 | 6,905 | 21,260 | 515 | 846 | **PASS** |
| **C** | State = CA | 5,701 | 5,145 | 1 | 442 | 802 | 3,342 | 60 | 88 | **PASS** |
| **D** | State = CA, City = Los Angeles | 313 | 307 | 1 | 1 | 45 | 257 | 5 | 7 | **PASS** |
| **E** | State = CA, City = Los Angeles, ZIP = 90067 | 42 | 41 | 1 | 1 | 1 | 39 | 1 | 0 | **PASS** |
| **F** | Website Available = Yes | 24,627 | 21,260 | 53 | 3,347 | 6,207 | 21,260 | 515 | 846 | **PASS** |
| **G** | LinkedIn Available = Yes | 556 | 515 | 40 | 316 | 437 | 515 | 515 | 319 | **PASS** |
| **H** | Facebook Available = Yes | 900 | 846 | 48 | 570 | 765 | 846 | 319 | 846 | **PASS** |
| **I** | All 3 Channels (Web + LI + FB) | 352 | 319 | 37 | 245 | 308 | 319 | 319 | 319 | **PASS** |
| **J** | State = CA & Web = Yes & LI = Yes | 65 | 60 | 1 | 33 | 50 | 60 | 60 | 48 | **PASS** |
| **K** | Reset All Slicers | 40,430 | 36,207 | 54 | 4,093 | 6,905 | 21,260 | 515 | 846 | **PASS** |

---

## 9. Power BI File Validity Audit

- `FSD_Advisor_Dashboard.pbix` was opened directly in Microsoft Power BI Desktop.
- The underlying Analysis Services tabular instance (`msmdsrv.exe`) spawned, verified working set memory allocation (over 100 MB active cache), and responded cleanly with zero load errors.
- The session was closed and reopened, confirming persistent file integrity and zero corruptions.

---

## 10. Known Source Limitations

1. **Uncollected Business Fields:** `CRD Number`, `Email Address`, and `Minimum Investable Assets` were not collected in Part-1. Per instructions, these are displayed as `"Not available in Part-1 dataset"` without fabrication.
2. **Missing Address & Phone Attributes:** 5,617 records lack street addresses, 8,291 lack ZIP codes, and 12,486 lack phone numbers.
3. **Unspecified Geographic Values:** 57 records have null states and 60 have null cities (mapped to `(Blank / Unknown)`).
4. **Data Outliers:** One entry (`Pk Shastri Ji`, Chandigarh, PIN 160055) exists in the source and was preserved untouched.

---

## 11. Final Deliverables Summary

- `PowerBI/FSD_Advisor_Dashboard.pbix`
- `PowerBI/FSD_Advisor_Dashboard.pbit`
- `PowerBI/FSD_Advisor_Dashboard.pbip`
- `PowerBI/README.md`
- `documentation/Part-2_Dashboard_Documentation.md`
- `validation/dashboard_validation.md`
- `screenshots/overview.png`
- `screenshots/geographic_analysis.png`
- `screenshots/digital_presence.png`
- `screenshots/advisor_directory.png`

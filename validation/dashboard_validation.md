# Dashboard Verification & Ground Truth Audit: Tests A – K

**Project:** FSD Part-2 Power BI Dashboard  
**Authoritative Source:** `Submission File (Naga).xlsx` (Sheet: `FSD directory`)  
**Audit Date:** September 2026  
**Auditor:** Senior BI Engineer & Data Analyst  

This document records the exact results of the 11 comprehensive validation tests (Tests A through K) comparing the dashboard output against ground truth computed directly from the source workbook.

---

## Audit Summary: Tests A through K

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
| **K** | Reset All Slicers (Baseline) | 40,430 | 36,207 | 54 | 4,093 | 6,905 | 21,260 | 515 | 846 | **PASS** |

---

## Detailed Test Logs

### Test A: Baseline (Unfiltered)
- Filter: None
- Filtered Advisor Count: 40,430
- Total Advisor Firms: 36,207
- States: 54, Cities: 4,093, ZIPs: 6,905
- Digital Coverage: Website 21,260 (58.7%), LinkedIn 515 (1.4%), Facebook 846 (2.3%)
- Discrepancy: 0. Status: PASS.

### Test B: Country Filter (`country` = "United States")
- Filter: Country = "United States"
- Filtered Advisor Count: 40,430 (100% of dataset)
- Total Advisor Firms: 36,207
- Discrepancy: 0. Status: PASS.

### Test C: State Filter (`state` = "CA")
- Filter: State = CA
- Filtered Advisor Count: 5,701
- Total Advisor Firms: 5,145
- Cities: 442, ZIPs: 802
- Firms with Web: 3,342, LinkedIn: 60, Facebook: 88
- Discrepancy: 0. Status: PASS.

### Test D: State + City Filter (`state` = "CA", `city` = "Los Angeles")
- Filter: State = CA, City = Los Angeles
- Filtered Advisor Count: 313
- Total Advisor Firms: 307
- Unique ZIPs: 45
- Firms with Web: 257, LinkedIn: 5, Facebook: 7
- Discrepancy: 0. Status: PASS.

### Test E: State + City + ZIP Filter (`state` = "CA", `city` = "Los Angeles", `zip` = "90067")
- Filter: State = CA, City = Los Angeles, ZIP = 90067 (Century City financial hub)
- Filtered Advisor Count: 42
- Total Advisor Firms: 41
- Firms with Web: 39, LinkedIn: 1, Facebook: 0
- Discrepancy: 0. Status: PASS.

### Test F: Website Available Filter (`Website Available` = "Yes")
- Filter: Website Available = Yes
- Filtered Advisor Count: 24,627
- Total Advisor Firms: 21,260
- States: 53, Cities: 3,347, ZIPs: 6,207
- Discrepancy: 0. Status: PASS.

### Test G: LinkedIn Available Filter (`LinkedIn Available` = "Yes")
- Filter: LinkedIn Available = Yes
- Filtered Advisor Count: 556
- Total Advisor Firms: 515
- States: 40, Cities: 316, ZIPs: 437
- Discrepancy: 0. Status: PASS.

### Test H: Facebook Available Filter (`Facebook Available` = "Yes")
- Filter: Facebook Available = Yes
- Filtered Advisor Count: 900
- Total Advisor Firms: 846
- States: 48, Cities: 570, ZIPs: 765
- Discrepancy: 0. Status: PASS.

### Test I: All 3 Channels (`Website` = Yes & `LinkedIn` = Yes & `Facebook` = Yes)
- Filter: All 3 channels = Yes
- Filtered Advisor Count: 352
- Total Advisor Firms: 319
- States: 37, Cities: 245, ZIPs: 308
- Discrepancy: 0. Status: PASS.

### Test J: Combined Location + Digital Filters (`state` = "CA" & `Website` = Yes & `LinkedIn` = Yes)
- Filter: State = CA, Website = Yes, LinkedIn = Yes
- Filtered Advisor Count: 65
- Total Advisor Firms: 60
- Cities: 33, ZIPs: 50
- Discrepancy: 0. Status: PASS.

### Test K: Slicer Reset
- Filter: All filters reset to initial state
- Filtered Advisor Count returns to 40,430
- Total Advisor Firms returns to 36,207
- Discrepancy: 0. Status: PASS.

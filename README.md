# Part 1 — Business Data Cleaning, Validation & Excel Reporting

**Analyst:** Harshwardhan Srivastava (bitsom_ba_2511581)
**Repository:** harshwardhan_srivastava_bitsom_ba_2511581_part1_data_cleaning

---

## 1. Problem Summary

A retail company exported order-level sales data from multiple internal systems. The raw file contains inconsistent text, mixed date formats, duplicate records, missing values, invalid discounts, sales/profit calculation mismatches, and order-status inconsistencies. The goal is a clean, validated, analysis-ready dataset plus evaluator-friendly Excel summary reports.

## 2. Dataset Description

| Field                                            | Value                                            |
| ------------------------------------------------ | ------------------------------------------------ |
| Raw file                                         | data/raw_orders.xlsx (932 rows x 21 columns)     |
| Cleaned file                                     | data/cleaned_orders.xlsx (912 rows x 33 columns) |
| Date range                                       | 2024-01-01 to 2025-10-12                         |
| Regions / Segments / Categories / Sub-categories | 5 / 4 / 3 / 13                                   |
| Data completeness                                | 99.93%                                           |

## 3. Tools Used

- Excel (.xlsx) deliverables produced programmatically with **Python (pandas, numpy, openpyxl)**.
- Cleaning operations map to standard Excel techniques: TRIM -> strip, SUBSTITUTE/Find-Replace -> regex replace, Text-to-Columns/Flash-Fill -> parsing, PivotTables -> groupby aggregations.
- **matplotlib** for screenshot visuals.

## 4. Cleaning Steps Performed — by Required Task

### Task 1 — Preserve Raw Data

The original file is kept untouched at `data/raw_orders.xlsx`; all work is written to a separate `data/cleaned_orders.xlsx`.

### Task 2 — Clean Text Fields

Standardized 10 fields (customer_name, segment, region, state, city, category, sub_category, ship_mode, payment_status, order_status): removed extra/leading/trailing spaces, fixed case, and unified differently-written category names. **148 cells changed.** Collapsing internal whitespace merged "Office  Supplies" into "Office Supplies", so categories correctly resolve to **3**.

### Task 3 — Clean & Validate Dates

Converted `order_date` and `ship_date` to a consistent **YYYY-MM-DD** format using a multi-format parser; **1824 date cells, 100% parsed** (0 unrecognized, 0 missing). Flagged ship-before-order (21). Added **shipping_delay_days** (days between order and ship).

### Task 4 — Handle Duplicates

Found and removed **20 exact duplicate rows** (first kept). Identified **12 order_ids** with conflicting info (24 rows); these are **flagged, not deleted** (duplicate_flag). Duplicate handling is summarized in `outputs/data_quality_report.xlsx`.

### Task 5 — Apply Business Rules

| Rule                          | Action                       | Count   |
| ----------------------------- | ---------------------------- | ------- |
| Missing region / ship_mode    | Fill "Unknown" + flag        | 25 / 21 |
| Missing discount              | Treat as 0                   | 26      |
| Negative discount / above 50% | Flag Invalid                 | 15 / 7  |
| Cancelled orders              | Exclude from completed sales | 145     |
| Failed payments               | Exclude from completed sales | 69      |
| Refunded orders               | Summarize separately         | 71      |
| Ship date before order date   | Flag invalid shipping        | 21      |

Every decision is documented in `outputs/cleaning_log.md`. **Completed sales** (excluding cancelled/failed/refunded) = **$6,930,122.60**.

### Task 6 — Create Calculated Columns

Added in `cleaned_orders.xlsx`: cleaned_discount, calculated_sales (qty x unit_price x (1 - discount)), calculated_profit (sales - cost), profit_margin, shipping_delay_days, order_month, order_year, and data_quality_flag (Clean/Warning/Invalid). Supporting flags: date_flag, discount_flag, duplicate_flag, sales_mismatch_flag.

### Task 7 — Create Data Quality Report

`outputs/data_quality_report.xlsx` includes the required sections: (1) Missing value summary, (2) Duplicate summary, (3) Invalid discount summary, (4) Date issue summary, (5) Order status issue summary, (6) Sales/profit mismatch summary, (7) Final clean vs flagged count (plus an executive summary and calculated-columns reference).

### Task 8 — Create Pivot Summary Report

`outputs/pivot_summary.xlsx` contains: (1) Sales & profit by region, (2) Sales & profit by category & sub-category, (3) Order count by ship mode, (4) Profit margin by segment, (5) Refunded/cancelled/failed orders by region, (6) Monthly sales trend. Pivots 1, 2 and 3 are sorted.

### Task 9 — Write Cleaning Log

`outputs/cleaning_log.md` records issues found, cleaning actions, business rules, assumptions, records removed, records flagged, and limitations.

## 5. Business Rules Applied

See the Task 5 table above; full decisions in `outputs/cleaning_log.md`.

## 6. Summary of Data Quality Issues Found

| Issue                                 | Count | Action           |
| ------------------------------------- | ----- | ---------------- |
| Ship date before order date           | 21    | Flagged Invalid  |
| Negative discounts                    | 15    | Flagged Invalid  |
| Discounts above 50%                   | 7     | Flagged Invalid  |
| Missing region                        | 25    | Filled "Unknown" |
| Missing ship_mode                     | 21    | Filled "Unknown" |
| Missing discount                      | 26    | Filled 0         |
| Exact duplicate rows                  | 20    | Removed          |
| Distinct repeated order_ids           | 12    | Flagged          |
| Sales reported vs recomputed mismatch | 61    | Flagged          |

Final: Clean 805 (88.3%), Warning 64 (7.0%), Invalid 43 (4.7%).

## 7. Summary of Final Pivot Reports

- **By region:** South leads on net sales.
- **By category:** Technology is the largest at 35.0% of net sales (across 13 sub-categories).
- **By ship mode:** Standard Class is the most used.
- **By segment:** Consumer has the highest average profit margin.
- **Issues by region & monthly trend:** included for operations review.

## 8. Key Business Insights

1. 88.3% of records are clean; 11.7% need attention.
2. Technology dominates revenue at 35.0% — concentration risk worth monitoring.
3. 21 impossible shipping records should be reviewed by operations.
4. 145 cancelled and 69 failed-payment orders are excluded from completed sales ($6,930,123); 71 refunds are tracked separately.
5. 61 sales mismatches suggest rounding or manual overrides in the source system.

## 9. Assumptions & Limitations

- Slash dates read as US MM/DD/YYYY, dash dates as DD-MM-YYYY (documented convention; data alone is ambiguous).
- "Unknown" used for missing categoricals to preserve totals.
- Reported `sales` validated against qty x price x (1 - discount); recomputed value used.
- Conflicting duplicate order_ids are flagged, not resolved.

## 10. Screenshots Included

| File                                 | Shows                                                     |
| ------------------------------------ | --------------------------------------------------------- |
| screenshots/raw_data_preview.png     | Raw dataset and issues before cleaning                    |
| screenshots/cleaned_data_preview.png | Cleaned dataset with calculated columns and quality split |
| screenshots/pivot_summary_1.png      | Region/category/segment/monthly pivots                    |
| screenshots/pivot_summary_2.png      | Ship mode, issues-by-region and process summary           |

---

### Folder Structure

```
harshwardhan-srivastava-bitsom_ba_2511581-part1-data-cleaning/
├── README.md
├── data/
│   ├── raw_orders.xlsx
│   └── cleaned_orders.xlsx
├── outputs/
│   ├── data_quality_report.xlsx
│   ├── pivot_summary.xlsx
│   └── cleaning_log.md
└── screenshots/
    ├── raw_data_preview.png
    ├── cleaned_data_preview.png
    ├── pivot_summary_1.png
    └── pivot_summary_2.png
```

**Status:** Complete and verified. Final column count = 33; cleaned records = 912; distinct categories = 3.

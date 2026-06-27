# Cleaning Log — Part 1

**Dataset:** raw_orders.xlsx | **Analyst:** Harshwardhan Srivastava (bitsom_ba_2511581)  
All figures are computed directly from the data.

## 1. Issues Found
- Exact duplicate rows: 20
- Repeated order_ids (conflicting): 12 ids across 24 rows
- Missing values: region 25, ship_mode 21, discount 26 (total 72)
- Mixed date formats across 1824 date cells (4+ styles)
- Ship date before order date: 21
- Negative discounts: 15; discounts above 50%: 7
- Reported vs recomputed sales mismatch: 61
- Order-status issues: cancelled 145, failed 69, refunded 71, returned 163

## 2. Cleaning Actions Performed
- Removed 20 exact duplicate rows (kept first): 932 -> 912.
- Standardized 10 text fields (collapse internal spaces, strip, Title Case); 148 cells changed. Merged "Office  Supplies" -> "Office Supplies", so categories resolve to 3.
- Parsed order_date and ship_date to YYYY-MM-DD (100% of 1824 cells).
- Filled missing region/ship_mode with "Unknown" and missing discount with 0.
- Added 7 calculated columns and 5 quality flags.

## 3. Business Rules Applied
| Rule | Action | Count |
|---|---|---|
| Missing region | Fill "Unknown" + flag | 25 |
| Missing ship_mode | Fill "Unknown" + flag | 21 |
| Missing discount | Treat as 0 | 26 |
| Negative discount | Flag Invalid | 15 |
| Discount above 50% | Flag Invalid | 7 |
| Cancelled orders | Exclude from completed sales | 145 |
| Failed payments | Exclude from completed sales | 69 |
| Refunded orders | Summarize separately | 71 |
| Ship date before order date | Flag invalid shipping | 21 |

Completed sales (excludes cancelled, failed, refunded): $6,930,122.60 | Completed profit: $2,036,321.80.

## 4. Assumptions Made
- Ambiguous slash dates read as US MM/DD/YYYY; dash dates as DD-MM-YYYY (consistent with unambiguous samples).
- "Unknown" used for missing categorical values instead of dropping rows, to preserve totals.
- Reported `sales` validated against quantity x unit_price x (1 - discount); recomputed value used for analysis.
- Refunded orders excluded from completed sales and summarized separately.

## 5. Records Removed
- 20 exact duplicate rows.

## 6. Records Flagged
- Invalid: 43 (ship-before-order 21, negative discount 15, over-50% discount 7).
- Warning: 64 (missing region/ship_mode or repeated order_id).
- Sales mismatch flagged: 61.

## 7. Limitations
- Slash/dash date ambiguity cannot be fully resolved from data alone; a documented convention was applied.
- Root cause of the 61 sales mismatches cannot be diagnosed from this dataset alone.
- Conflicting duplicate order_ids are flagged, not resolved, pending source-system confirmation.

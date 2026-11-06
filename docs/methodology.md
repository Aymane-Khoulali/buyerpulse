# BuyerPulse — Data Quality & Preprocessing Methodology

## 1. Project Objective

BuyerPulse aims to predict whether an e-commerce customer will make another qualifying purchase within 60 days of a historical observation point.

The project uses the UCI Online Retail II dataset and prioritizes reliable preprocessing, customer-level feature engineering, and leakage-free temporal evaluation.

## 2. Dataset Overview

The original Excel workbook contains two worksheets:

| Worksheet | Transaction Rows | Date Range |
|---|---:|---|
| Year 2009-2010 | 525,461 | 2009-12-01 to 2010-12-09 |
| Year 2010-2011 | 541,910 | 2010-12-01 to 2011-12-09 |

**Total raw transaction lines:** 1,067,371.

Both worksheets contain eight original fields: `Invoice`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `Price`, `Customer ID`, and `Country`.

A `SourceYear` column was added during exploratory analysis to preserve worksheet provenance.

## 3. Missing-Value Analysis

The initial audit identified:

| Field | Missing Records | Percentage |
|---|---:|---:|
| Customer ID | 243,007 | 22.77% |
| Description | 4,382 | 0.41% |

Other original fields showed no missing values under the initial Pandas inspection.

Missing customer identifiers are particularly important because the prediction task requires linking historical orders to individual customers.

Records without reliable customer identifiers are candidates for exclusion from customer-level model construction.

## 4. Transaction Anomalies

| Anomaly | Affected Rows |
|---|---:|
| Cancellation-marked invoice | 19,494 |
| Negative quantity | 22,950 |
| Zero quantity | 0 |
| Negative price | 5 |
| Zero price | 6,202 |

Among cancellation-marked lines, 19,493 also have negative quantities.

An additional 3,457 negative-quantity records do not use the cancellation prefix.

The five negative-price records are labeled as bad-debt adjustments.

These observations indicate that invoice identifiers alone are insufficient for distinguishing completed purchases from other transaction activity.

## 5. Zero-Price Investigation

The 6,202 zero-price lines include:

- 2,745 positive-quantity lines.
- 3,457 negative-quantity lines.
- 6,131 lines with missing customer identifiers.
- 71 lines with identifiable customers.

All 4,382 records with missing product descriptions also have zero prices.

An invoice-level inspection of positive-quantity zero-price lines with known customer identifiers found:

- 62 distinct invoices.
- 56 invoices also containing qualifying paid lines.
- 6 invoices without qualifying paid lines.

**Provisional policy:** Define paid purchases at the invoice level using qualifying positive-quantity, positive-price lines. Do not discard an otherwise valid invoice solely because it contains a zero-price product line.

## 6. Duplicate Investigation

An initial exact-record analysis found:

- 34,335 duplicate occurrences when comparing the combined dataset without `SourceYear`.
- 12,133 duplicate occurrences within individual worksheets.

Exact duplicate lines are not automatically considered erroneous because the dataset lacks unique invoice-line identifiers.

The effect of within-sheet deduplication on monetary and quantity features should be investigated separately.

## 7. Cross-Worksheet Overlap

Both worksheets contain records dated December 1–9, 2010.

A multiplicity-aware comparison, matching all eight original fields and handling missing values, verified:

- 22,523 overlapping rows in each worksheet.
- 22,202 distinct matching record patterns.
- Zero unmatched record patterns.
- Zero differences in occurrence counts.

**Validated overlap-removal policy:**

Retain the overlapping records from `Year 2009-2010` and exclude their redundant copy from `Year 2010-2011`.

This removes 22,523 redundant rows without automatically eliminating identical lines within the retained worksheet.

**Expected row count after overlap removal:** 1,044,848.

The cleaning pipeline must implement and test this transformation independently.

## 8. Proposed Valid-Purchase Definition

The initial purchase-event definition requires:

- A known customer identifier.
- An invoice that is not cancellation-marked.
- At least one qualifying line with positive quantity and positive unit price.

Customer purchase frequency should count distinct qualifying invoices, not transaction lines.

Zero-price lines should not contribute to monetary totals. Cancellations, returns, and adjustments should not create additional completed purchase events.

These rules remain subject to implementation, testing, and review.

## 9. Reproducibility and Evidence

The exploratory analysis is maintained in `notebooks/01_data_exploration.ipynb`.

The consolidated data-quality statistics are exported to `reports/tables/data_quality_summary.csv`.

Raw transaction files are excluded from Git tracking.

The cleaning pipeline will preserve audit counts and test each transformation to support reproducibility.

## 10. Pending Investigations

- Final decision on within-sheet exact duplicates.
- Validation of customer identifiers and invoice-level assumptions.
- Impact of the cleaning rules on customer coverage.
- Historical observation dates and 60-day label construction.
- Temporal train/validation/test methodology.
- Comparison of feature representations and classification models.

**Status:** Exploratory data-quality audit completed; cleaning implementation and model construction pending.
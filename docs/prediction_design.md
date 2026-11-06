# BuyerPulse — Prediction & Temporal Labeling Design

**Status:** Draft — Phase 3

## 1. Prediction Objective

Predict whether an existing e-commerce customer will make at least one additional qualifying purchase within the next 60 days.

This is a binary classification task.

## 2. Prediction Unit

Each prediction corresponds to a `(CustomerID, ObservationDate)` pair.

A customer may appear at multiple historical observation dates, provided those examples are generated using a time-aware protocol.

## 3. Target Definition

- `1`: The customer completes at least one qualifying purchase strictly after the observation cutoff and no later than 60 days afterward.
- `0`: No qualifying purchase is observed within that fully observed 60-day window.

A qualifying purchase is determined by the validated data-cleaning policy.

## 4. Eligible Customers

A customer must have a valid identifier and at least one qualifying historical purchase available at the observation cutoff.

Customers without sufficient historical evidence for the agreed feature definitions may be handled separately.

## 5. Historical Feature Boundary

All features must use only information available at the observation cutoff.

Future purchases, returns, and later customer activity must never enter feature construction.

## 6. Label Observation Boundary

A label may only be generated when the dataset contains a complete 60-day observation period after the cutoff.

The final eligible observation date must therefore be at least 60 days before the end of available transaction history.

## 7. Proposed Modeling Approach

We will investigate customer purchasing behavior using:

- Recency, Frequency, Monetary (RFM)
- Historical purchase intervals
- Average order value
- Recent purchasing activity
- Product diversity

Feature definitions will be finalized after Ghizlane's cleaning pipeline is reviewed.

## 8. Observation Schedule and Temporal Evaluation

### 8.1 Observation Schedule

BuyerPulse will initially use monthly observation snapshots.

- First eligible snapshot: April 1, 2010.
- Last eligible snapshot: October 1, 2011.
- Total candidate snapshots: 19.
- Prediction horizon: 60 days.
- Minimum dataset history before the first snapshot: 90 days.

The 90-day minimum is a provisional design choice and does not require every customer to have 90 days of purchasing activity.

### 8.2 Chronological Partitioning

| Partition | Observation Dates | Snapshots |
|---|---|---:|
| Training | April 2010 – January 2011 | 10 |
| Embargo | February – March 2011 | 2 |
| Validation | April – May 2011 | 2 |
| Embargo | June – July 2011 | 2 |
| Testing | August – October 2011 | 3 |

The embargoed snapshots are excluded from the initial modeling dataset.

All labels in an earlier partition must be fully observable before the following partition begins.

These boundaries were checked programmatically in `notebooks/02_temporal_design.ipynb`.

### 8.3 Repeated Customer Observations

The same customer may appear at multiple historical observation dates.

This is intentional because BuyerPulse predicts customer behavior at different points in time.

We will:

- Preserve chronological ordering.
- Generate features independently at each observation cutoff.
- Never randomly split customer-snapshot rows.
- Report the number of unique customers and snapshots per partition.
- Investigate repeated-customer dependence when interpreting model performance.

### 8.4 Model Selection and Evaluation

Training data will be used to fit candidate models.

Validation data will support model selection, feature decisions, and threshold tuning.

Testing data will be reserved for a final evaluation after model selection is complete.

Initial evaluation metrics will include:

- Precision
- Recall
- F1-score
- ROC-AUC
- PR-AUC
- Confusion matrix

We will also compare against simple baseline classifiers.

### 8.5 Leakage Prevention

- Features must use only transactions available at the relevant observation cutoff.
- Labels must use qualifying purchases strictly after that cutoff and within the following 60 days.
- Preprocessing parameters requiring fitting must be estimated using training data only.
- Validation and test labels must never influence model fitting or feature selection.
- No customer-snapshot row may appear in multiple partitions.

### 8.6 Pending Implementation Decisions

- Final cleaning and qualifying-purchase policies.
- Exact feature lookback windows.
- Treatment of sparse customer histories.
- Class balance and baseline performance.
- Final feature-generation and labeling tests.

The split is provisional until the cleaned data is available and customer-level sample counts have been verified.

## 9. Reproducibility

The implementation will store temporal parameters in `configs/config.yaml`, validate time boundaries through automated tests, and document the final labeling and evaluation protocol.

No modeling results have been produced at this stage.